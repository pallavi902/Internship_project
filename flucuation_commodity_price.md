# Internship_project
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
msp_mandi = pd.read_csv('msp_mandi.csv', index_col = 'year')
msp_mandi.head()
msp_mandi = msp_mandi.iloc[:, 1:-1]
msp_mandi.head()
monthly_data = pd.read_csv('monthly_data_cleaned.csv')
monthly_data.head()
monthly_data['cluster'] = monthly_data.Commodity + " at " + monthly_data.APMC
monthly_data.head()
relevant_data = monthly_data[["APMC","Commodity","modal_price","Year","cluster"]].groupby(["APMC","Commodity","Year", "cluster"],as_index=False).count().rename(columns={"modal_price":"Count"}).reset_index(drop=True)
relevant_data.head()
relevant_data.shape
clusters = relevant_data["cluster"].unique().tolist()
relevant = monthly_data[monthly_data["cluster"].isin(clusters)].reset_index(drop=True)
relevant = relevant.set_index('date', drop = True)
relevant.index = pd.to_datetime(relevant.index)
relevant.head()
fluctuation_list = []
clusters = relevant_data.cluster.unique().tolist()
from scipy import stats
for cluster in clusters:
    working_df = relevant[relevant['cluster'] == cluster]
    variation = stats.variation(working_df.modal_price)
    fluctuation_list.append((cluster, variation))
fluctuation_list
variation_data = pd.DataFrame(fluctuation_list)
variation_data.columns = ['cluster', 'var_coeff']
variation_data.head()
variation_data = variation_data.sort_values(by = 'var_coeff', ascending = False)
variation_data.head()    
variation_data = variation_data.reset_index(drop = True)    
variation_data.head()    
figure = plt.plot(variation_data.var_coeff)
fluctuation_clusters = variation_data.iloc[0:100, :]
plt.plot(fluctuation_clusters.var_coeff)
fluctuation_clusters = fluctuation_clusters.iloc[0:20, :]
cluster_list = fluctuation_clusters.cluster.tolist()
cluster_list
fluctuation_df
fluctuation_df.cluster.unique().tolist()
fluctuation_df.to_csv('price_fluctuation_values.csv')
pd.DataFrame(fluctuation_df.cluster.unique().tolist()).to_csv('fluctuation_clusters.csv')

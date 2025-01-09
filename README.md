# Electric-Vehicle-Data-Analysis-Project---Python

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import ttest_ind

# Load the dataset
dataset_path = 'FEV-data-Excel.xlsx'  # Ensure this file is in the same directory or provide the correct path.
df = pd.read_excel(dataset_path)

# Task 1: Filtering EVs based on budget and range criteria
def filter_evs(budget, min_range):
    filtered = df[(df['Minimal price (gross) [PLN]'] <= budget) & (df['Range (WLTP) [km]'] >= min_range)]
    grouped = filtered.groupby('Make')
    avg_battery_capacity = grouped['Battery capacity [kWh]'].mean()
    return filtered, grouped, avg_battery_capacity

# Task 2: Detecting outliers in energy consumption
def detect_outliers(column):
    Q1 = df[column].quantile(0.25)
    Q3 = df[column].quantile(0.75)
    IQR = Q3 - Q1
    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR
    outliers = df[(df[column] < lower_bound) | (df[column] > upper_bound)]
    return outliers

# Task 3: Analyzing the relationship between battery capacity and range
def plot_battery_vs_range():
    plt.figure(figsize=(10, 6))
    sns.scatterplot(x='Battery capacity [kWh]', y='Range (WLTP) [km]', data=df)
    plt.title('Battery Capacity vs Range')
    plt.xlabel('Battery Capacity (kWh)')
    plt.ylabel('Range (WLTP) (km)')
    plt.grid(True)
    plt.show()

# Task 4: EV Recommendation Class
class EVRecommendation:
    def __init__(self, data):
        self.data = data

    def recommend(self, budget, min_range, min_battery_capacity):
        filtered = self.data[(self.data['Minimal price (gross) [PLN]'] <= budget) &
                             (self.data['Range (WLTP) [km]'] >= min_range) &
                             (self.data['Battery capacity [kWh]'] >= min_battery_capacity)]
        top_3 = filtered.sort_values(by=['Range (WLTP) [km]', 'Minimal price (gross) [PLN]'], ascending=[False, True]).head(3)
        return top_3

# Task 5: Hypothesis Testing

def perform_t_test():
    tesla = df[df['Make'] == 'Tesla']['Engine power [KM]']
    audi = df[df['Make'] == 'Audi']['Engine power [KM]']
    t_stat, p_value = ttest_ind(tesla, audi, equal_var=False)  # Welch's t-test
    return t_stat, p_value

# Run analysis for each task
# Task 1
filtered_evs, grouped_evs, avg_battery_capacity = filter_evs(350000, 400)
print("Filtered EVs:\n", filtered_evs)
print("Average Battery Capacity by Manufacturer:\n", avg_battery_capacity)

# Task 2
outliers = detect_outliers('Mean - Energy consumption [kWh/100 km]')
print("Outliers in Energy Consumption:\n", outliers)

# Task 3
plot_battery_vs_range()

# Task 4
recommender = EVRecommendation(df)
recommendations = recommender.recommend(350000, 400, 50)
print("Top 3 EV Recommendations:\n", recommendations)

# Task 5
t_stat, p_value = perform_t_test()
print(f"T-test Results:\nT-statistic: {t_stat}, P-value: {p_value}")

# Recommendations and conclusions will be detailed in the markdown section.

## Project Introduction

Since 2008, Airbnb has offered travelers and hosts more travel possibilities, presenting a more unique and personalized way to experience the world. This dataset describes the activity and metrics of Airbnb listings in New York City (NYC) for the year 2019.

## Data Content

The dataset contains information about hosts, geographical locations, and necessary metrics, facilitating predictions and conclusions.

## Dataset Source

This open dataset is part of Airbnb, and the original source can be found at this [Website](https://www.airbnb.com).

## Project Inspiration

- What can we learn from different hosts and areas?
- What can we learn from predictions? (e.g., location, price, reviews, etc.)
- Which hosts are the busiest, and why?
- Is there a noticeable difference in traffic across different areas? What could be the possible reasons?

## Data Import

First, we need to import the data:

```python
import pandas as pd

# Read the CSV file
df = pd.read_csv('./AB_NYC_2019.CSV')

# Display the first few rows of the data
print(df.head())

## Exploratory Data Analysis

plt.figure(figsize=(10, 6))
sns.histplot(df['price'], bins=50, kde=True)
plt.title('Price Distribution')
plt.xlabel('Price')
plt.ylabel('Frequency')
plt.show()

**Price Distribution Analysis**
  
Based on the histogram, we can observe that the price distribution of Airbnb listings in New York City in 2019 shows a skewed distribution. That is, there are more listings at lower prices and fewer listings at higher prices. Specifically, the number of listings is highest in the price range of $0 to $200, and then gradually decreases as the price range increases. This distribution pattern indicates that the Airbnb market in New York City is primarily composed of mid-to-low-priced listings, with relatively few high-priced listings. This finding is significant for understanding the price structure of the New York City Airbnb market and for formulating pricing strategies.

plt.figure(figsize=(10, 6))
sns.countplot(data=df, x='room_type', order=df['room_type'].value_counts().index)
plt.title('Distribution of Listing Types')
plt.xlabel('Listing Types')
plt.ylabel('Quantity')
plt.savefig('house_type.png')
plt.show()

**Type Analysis**

This bar chart provides a detailed depiction of the distribution of listing types. The horizontal axis clearly lists the three types of listings: "Entire home/apt," "Private room," and "Shared room." The vertical axis visually displays the number of each type of listing, ranging from 0 to 25,000.

The chart shows that the number of entire home/apartment listings is the largest, nearing 25,000, indicating a significant market share. This is followed by private rooms, with about 20,000 listings, also demonstrating strong market demand. In contrast, shared rooms are the least numerous, with only around 1,000 listings, indicating their niche status in the market.

plt.figure(figsize=(10, 6))
sns.countplot(data=df, x='neighbourhood_group', order=df['neighbourhood_group'].value_counts().index)
plt.title('Distribution by Neighborhood Group')
plt.xlabel('Neighborhood Group')
plt.ylabel('Quantity')
plt.savefig('groupbar.png')
plt.show()

plt.figure(figsize=(10, 6))
sns.scatterplot(data=df, x='longitude', y='latitude', hue='neighbourhood_group', palette='viridis', alpha=0.5)
plt.title('Geographical Distribution of Listings')
plt.xlabel('Longitude')
plt.ylabel('Latitude')
plt.savefig('groupscatter.png')
plt.show()

**Data Analysis**

    Manhattan leads with over 20,000 neighborhood groups, highlighting its central role in the city, likely due to its high-density commercial, financial, and residential activities. Brooklyn follows closely with nearly 20,000 neighborhood groups, indicating a similarly active community and diverse economic activities.
    
    Queens and the Bronx have significantly fewer neighborhood groups, with approximately 10,000 and 5,000 respectively. This reflects a relative sparsity in these areas, possibly related to factors such as population density and development level. Staten Island is at the far right of the chart with the fewest neighborhood groups, almost negligible, indicating its marginal status in neighborhood group distribution, possibly constrained by geographical location and transportation connectivity.

In summary, the distribution of neighborhood groups in New York City shows a clear imbalance, with Manhattan and Brooklyn dominating, while Queens, the Bronx, and Staten Island lag behind. This distribution pattern reflects differences in economic development, population density, and geographical location across the boroughs.

# 1. Insights on Different Hosts and Regions
## Host Analysis
# Number of Listings per Host and Average Price
host_summary = df.groupby('host_name').agg({
    'id': 'count',
    'price': 'mean',
    'number_of_reviews': 'sum'
}).rename(columns={'id': 'number_of_listings'})

plt.figure(figsize=(12, 8))
sns.histplot(host_summary['number_of_listings'], bins=50, kde=True)
plt.title('Distribution of Listings per Host')
plt.xlabel('Number of Listings')
plt.ylabel('Frequency')
plt.savefig('houselistbarh.png')
plt.show()

**Problem Analysis**

We can learn the following points from the perspectives of hosts and regions:：

    High Concentration of Listings: The chart reveals a high concentration of listings among hosts, with a few hosts owning a large number of listings while the majority have only a few. This indicates a significant concentration of resources in certain areas or markets, where a few large hosts have considerable influence over the market.

    Market Structure Imbalance: The high market concentration, where a few hosts control a large number of listings, can lead to uneven competition. Large hosts have more influence over pricing and supply, potentially impacting overall rental prices and the quality of listings in the market.

    Diversity of Host Types: Although most hosts have only a few listings, this reflects the diversity of host types. It likely includes many part-time hosts who occasionally rent out their properties rather than doing it as a full-time profession. This diversity positively contributes to the market's flexibility and adaptability.

    Potential Regional Differences: While the chart does not directly show regional information, the uneven distribution of listings may imply differences between regions. Market conditions, policy support, and economic development levels in different areas could influence the number and distribution of listings per host.


features = ['latitude', 'longitude', 'minimum_nights', 'number_of_reviews', 'reviews_per_month']
X = df[features]
y = df['price']

# Handling Missing Values
X.fillna(X.median(), inplace=True)
y.fillna(y.median(), inplace=True)

# Splitting the Dataset into Training and Testing Sets
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# Training the Model
model = LinearRegression()
model.fit(X_train, y_train)

# Prediction and Evaluation
y_pred = model.predict(X_test)
mse = mean_squared_error(y_test, y_pred)
print(f'Mean Squared Error of Price Prediction: {mse}')

## Regional Analysis
# Prices and Number of Reviews for Listings in Each Area
neighborhood_summary = df.groupby('neighbourhood_group').agg({
    'price': 'mean',
    'number_of_reviews': 'sum'
}).sort_values(by='price', ascending=False)

neighborhood_summary.plot(kind='bar', figsize=(12, 8), subplots=True, layout=(2, 1), legend=True)
plt.suptitle('Prices and Number of Reviews for Listings in Different Areas')
plt.savefig('Prices and Number of Reviews for Listings in Different Regions.png')
plt.show()

# IMDB Movie Ratings Analysis

This project performs exploratory data analysis (EDA) on IMDB movie ratings data. The analysis explores various aspects of movie ratings, including vote counts, average ratings, and derived popularity scores to uncover insights about movie popularity and critical reception.

## Dataset

Source: [IMDB Ratings](https://www.kaggle.com/datasets/umitka/imdb-ratings/data)

Record: 67,408 rows

File type: CSV

## Tools

- Programming language: Python

    Dependencies:
  - Pandas
  - Numpy
  - Matplotlib
  - Seaborn

- Jupyter Notebook: important for executing python scripts.
- Git & Github: essential for version control and project tracking.

## Data Preparation and Initial Exploration

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from matplotlib.ticker import FuncFormatter

df = pd.read_csv('imdb_ratings.csv')

df.info()
```

## Most Voted Movies

- Identified the top 10 movies by number of votes
- Created a bar plot showing vote counts with custom formatting (K/M notation)

```python
plt.figure(figsize=(12,6))
sns.barplot(data=most_voted_df,
            x='numVotes',
            y='title',
            hue='numVotes',
            palette='flare',
            legend=False)
plt.title('Top 10 Most Voted Movies on IMDB')
plt.xlabel('Votes')
plt.ylabel('')

def xticks_format(x, pos):
    if x >= 1_000_000:
        return f'{x/1_000_000:.1f}M'
    elif x >= 1_000:
        return f'{x/1_000:.0f}K'
    else:
        return str(int(x))

ticks_x = plt.FuncFormatter(xticks_format)
plt.gca().xaxis.set_major_formatter(ticks_x)
plt.savefig('top_voted.png')
plt.show()
```

![Top 10 Most Voted Movies](/images/top_voted.png)

**Insights:** The bar chart highlights movies with the highest voter engagement, often mainstream blockbusters that have achieved widespread cultural recognition. This indicates that vote counts are more reflective of a film's popularity and accessibility than its critical acclaim, with some titles receiving millions of votes due to their broad appeal across demographics.

## Highest Rated Movies

- Filtered movies with at least 1,000 votes
- Ranked top 10 movies by average rating
- Visualized ratings on a bar chart with rating scale from 9-10

```python
sns.barplot(data=highest_rated_df,
            x='averageRating',
            y='title',
            hue='averageRating',
            palette='crest',
            legend=False)

plt.xlim(9,10)
plt.title('Top 10 Highest Rated Movies (min 1,000 Votes)')
plt.xlabel('Average Rating')
plt.ylabel('')
plt.savefig('highest_rated.png')
plt.show()
```

![Top 10 Highest Rated Movies](/images/highest_rated.png)

**Insights:** This visualization showcases critically acclaimed films that have earned near-perfect scores from dedicated viewers. The narrow rating range (9-10) suggests these movies represent the pinnacle of cinematic achievement, often recognized for their artistic merit, storytelling excellence, and lasting cultural impact rather than mass-market appeal.

## Rating Distribution

- Analyzed the distribution of average ratings across all movies
- Created a histogram with kernel density estimation

```python
plt.figure(figsize=(10,5))
sns.histplot(df['averageRating'], bins=20, kde=True)
plt.title('Distribution of IMDb Ratings')
plt.xlabel('Average Rating')
plt.ylabel('Count')
plt.savefig('rating_dist.png')
plt.show()
```

![Distribution of IMDb Ratings](/images/rating_dist.png)

**Insights:** The histogram reveals a normal distribution of ratings centered around 6-7, with a slight left skew indicating that while most movies receive moderate ratings, there are fewer extremely low-rated films. This suggests a tendency toward positive bias in ratings, possibly due to self-selection where dissatisfied viewers are less likely to rate movies.

## Vote Count Distribution

- Examined the distribution of vote counts using a logarithmic scale
- Revealed the skewed nature of voting patterns

```python
plt.figure(figsize=(10,5))
sns.histplot(df['numVotes'], bins=20, log_scale=True)
plt.title('Distribution of Vote Counts (Log Scale)')
plt.xlabel('Votes')
plt.ylabel('Count')
plt.savefig('vote_dist.png')
plt.show()
```

![Distribution of Vote Counts](/images/vote_dist.png)

**Insights:** The log-scaled histogram demonstrates the extreme inequality in voter participation, with most movies receiving relatively few votes while a small elite group attracts massive audiences. This power-law distribution underscores the winner-takes-all nature of online voting systems and the importance of visibility in driving engagement.

## Rating vs. Votes Relationship

- Created a scatter plot to explore the relationship between average rating and number of votes
- Used logarithmic scaling for votes to handle wide range

```python
plt.figure(figsize=(10,6))
sns.scatterplot(
    data=df,
    x='numVotes',
    y='averageRating',
    alpha= 0.4
)
plt.xscale('log')
plt.title('Average Rating vs Number of votes')
plt.xlabel('Number of Votes (Log Scale)')
plt.ylabel('Average Rating')
plt.savefig('rating_vs_votes.png')
plt.show()
```

![Average Rating vs Number of Votes](/images/rating_vs_votes.png)

**Insights:** The scatter plot shows a positive correlation between ratings and vote counts, suggesting that higher-rated movies tend to attract more voters. However, the relationship is not perfectly linear, indicating that while quality drives some engagement, other factors like marketing, cultural relevance, and accessibility also play significant roles in voter participation.

## Popularity Score Creation

- Engineered a popularity metric combining vote count and rating:

```df['popularity'] = np.log(df['numVotes'] + 1) * (df['averageRating'] / 10)```

- Normalized the score to a 0-100 scale:

```df['popularity'] = ((df['popularity'] - df['popularity'].min()) / (df['popularity'].max() - df['popularity'].min()) * 100)```

## Most Popular Movies

- Ranked top 10 movies by the custom popularity score
- Visualized with a bar chart

```python
plt.figure(figsize=(10, 6))
sns.barplot(data=most_popular,
            x='popularity',
            y='title',
            hue='popularity',
            palette='flare',
            legend=False)
plt.title('Top 10 Most Popular Movies')
plt.xlabel('Popularity Score')
plt.ylabel('')
plt.savefig('most_popular.png')
plt.show()
```

![Top 10 Most Popular Movies](/images/most_popular.png)

**Insights:** By combining rating quality with voter engagement, this popularity metric provides a more balanced assessment of cinematic success. The resulting top movies represent the sweet spot where critical acclaim meets widespread audience approval, offering a comprehensive view of films that excel in both artistic merit and cultural impact.

## Statistical Summaries

- Created box plots for both average rating and vote counts
- Used logarithmic scaling for votes to show distribution characteristics

```python
plt.figure(figsize=(12,5))

plt.subplot(1,2,1)
sns.boxplot(x=df["averageRating"])
plt.title("Boxplot of Average Rating")
plt.xlabel("Average Rating")

plt.subplot(1,2,2)
sns.boxplot(x=df["numVotes"])
plt.xscale("log")
plt.title("Boxplot of Number of Votes (Log Scale)")
plt.xlabel("Number of Votes")

plt.suptitle("Statistical Summaries of IMDb Movie Ratings and Votes")
plt.tight_layout()
plt.savefig('boxplots.png')
plt.show()
```

![Boxplots of Rating and Votes](/images/boxplots.png)

**Insights:** The box plots reveal the central tendency and variability in ratings and votes. Ratings show a relatively symmetric distribution with few outliers, while vote counts exhibit extreme positive skewness with numerous high-vote outliers, confirming the presence of viral hits that dominate the voting landscape.

## Conclusive Summary and Findings

1. **Vote Distribution**: Movie vote counts follow a highly skewed distribution, with most movies having relatively few votes and a small number receiving massive vote counts.

2. **Rating Patterns**: Average ratings are generally high, with most movies rated between 6-8. The distribution shows a slight left skew.

3. **Rating vs. Popularity**: There's a positive relationship between ratings and vote counts, though not perfectly linear. Higher-rated movies tend to attract more votes.

4. **Popularity Metric**: The custom popularity score provides a balanced view combining both critical reception (rating) and audience engagement (votes).

## Visualizations

The analysis includes several key visualizations:

- Bar charts for top movies by votes, ratings, and popularity
- Histograms for rating and vote distributions
- Scatter plot showing rating-vote relationship
- Box plots for statistical summaries

## Usage

1. Ensure all dependencies are installed
2. Run the EDA.ipynb notebook to reproduce the analysis
3. The notebook contains all code and visualizations

## Conclusion

This EDA provides valuable insights into IMDB movie data patterns, revealing the complex interplay between critical ratings and audience engagement. The custom popularity metric offers a more nuanced view of movie success beyond simple ratings or vote counts alone.

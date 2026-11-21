# Elasticsearch Data Loading and Query Examples

This README documents how to load a news dataset into Elasticsearch and run basic queries to explore search relevance and aggregation results.

## 📥 Data Import

1. Download the News Category Dataset from [Kaggle](https://www.kaggle.com/datasets/rmisra/news-category-dataset?resource=download).
2. Extract the downloaded file. The dataset file is named `News_Category_Dataset_v3.json`.
3. Open Kibana and go to **Getting Started**.
4. Select **Add Data**, then choose **Upload a file**.
5. Upload `News_Category_Dataset_v3.json` and select **Import**.
6. Confirm that a new index containing the imported data is available.

### 📄 Sample Data

```json
{
  "date": "2016-10-04",
  "short_description": "Sign him up!",
  "@timestamp": "2016-10-04T00:00:00.000+05:30",
  "link": "https://www.huffingtonpost.com/entry/watch-a-ball-boy-make-the-sickest-catch-of-the-nfl-season_us_57f3876ae4b0d0e1a9a97df6",
  "category": "SPORTS",
  "headline": "Watch A Ball Boy Make The Sickest Catch Of The NFL Season",
  "authors": "Ron Dicker"
}
```

## 🎯 Precision and Recall

Elasticsearch ranks search results based on relevance. Precision and recall describe two important—and often competing—ways to evaluate a search.

- **Precision:** Returns the most relevant documents, but some relevant documents may not be included.
- **Recall:** Returns as many matching documents as possible, but some results may not be relevant.

## ⭐ Relevance and Scoring

Elasticsearch calculates a relevance score for each document using several factors, including:

- **Term Frequency (TF):** The number of times a search word appears in a document.
- **Inverse Document Frequency (IDF):** How common or rare a word is across all documents. Rare words usually provide more information.

Elasticsearch combines these and other signals to rank the most relevant results first.

## 🔍 Queries and Aggregations

Elasticsearch provides two main ways to work with indexed data.

### Queries

Queries retrieve documents that match specific search criteria.

```http
GET enter_name_of_the_index_here/_search
```

### Aggregations

Aggregations summarize data using metrics, statistics, and grouped analytics.

```http
GET enter_name_of_the_index_here/_search
{
  "aggs": {
    "name_your_aggregation_here": {
      "specify_aggregation_type_here": {
        "field": "name_the_field_to_aggregate",
        "size": "number_of_buckets_to_return"
      }
    }
  }
}
```

## 📊 Viewing Search Data

### Get News Documents

```http
GET news-data/_search
```

This returns the first 10 documents and the total hit count. The search is limited to 10,000 hits by default. A total result with `"relation": "gte"` means there may be more results than the returned count.

```json
"hits": {
  "total": {
    "value": 10000,
    "relation": "gte"
  }
}
```

### Get the Exact Document Count

```http
GET news-data/_count
```

Example response:

```json
{
  "count": 209527,
  "_shards": {
    "total": 1,
    "successful": 1,
    "skipped": 0,
    "failed": 0
  }
}
```

## 📅 Filter by Date Range

Use a `range` query to retrieve records between two dates.

```http
GET news-data/_search
{
  "query": {
    "range": {
      "date": {
        "gte": "2016-10-01",
        "lte": "2016-10-31"
      }
    }
  }
}
```

This example returns approximately 2,757 records.

## 🗂️ Group Data by Category

Use a `terms` aggregation to group documents by category.

```http
GET news-data/_search
{
  "aggs": {
    "category-based": {
      "terms": {
        "field": "category",
        "size": 100
      }
    }
  }
}
```

- `field` identifies the field used for grouping.
- `size` controls how many category buckets are returned.

Example result:

```json
"buckets": [
  {
    "key": "POLITICS",
    "doc_count": 35602
  },
  {
    "key": "WELLNESS",
    "doc_count": 17945
  }
]
```

## 🧩 Combine a Query and an Aggregation

The query result becomes the input for the aggregation. This example finds the most significant words in headlines from the `POLITICS` category.

```http
GET news-data/_search
{
  "query": {
    "match": {
      "category": "POLITICS"
    }
  },
  "aggs": {
    "most_significant_words": {
      "significant_text": {
        "field": "headline"
      }
    }
  }
}
```

Example result:

```json
"buckets": [
  {
    "key": "trump",
    "doc_count": 7589,
    "score": 0.6671440675331257,
    "bg_count": 21630
  },
  {
    "key": "donald",
    "doc_count": 3107,
    "score": 0.24353031477906451,
    "bg_count": 9648
  },
  {
    "key": "gop",
    "doc_count": 1805,
    "score": 0.21605359322479248,
    "bg_count": 4038
  }
]
```

## 🔎 Improve Recall with the OR Operator

A `match` query uses the `OR` operator by default. This search checks whether any one of the words appears in the headline.

```http
GET news-data/_search
{
  "query": {
    "match": {
      "headline": {
        "query": "trump donald gop election"
      }
    }
  }
}
```

This query returns more than 10,000 matching documents.

Example response:

```json
"hits": {
  "total": {
    "value": 10000,
    "relation": "gte"
  },
  "max_score": 15.061684,
  "hits": [
    {
      "_index": "news-data",
      "_id": "LTEW9aEB2zHhIrL5yKJt",
      "_score": 15.061684,
      "_source": {
        "date": "2016-03-01",
        "short_description": "\"Trump is a bully, unworthy of our nomination.\"",
        "@timestamp": "2016-03-01T00:00:00.000+05:30",
        "link": "https://www.huffingtonpost.com/entry/scott-rigell-donald-trump_us_56d5d5dfe4b03260bf7837ac",
        "category": "POLITICS",
        "headline": "GOP Congressman Says He Won't Back Donald Trump In The General Election",
        "authors": "Amanda Terkel"
      }
    },
    {
      "_index": "news-data",
      "_id": "oDEW9aEB2zHhIrL5yahV",
      "_score": 14.191684,
      "_source": {
        "date": "2016-02-11",
        "short_description": "The betting markets now say that the most likely Republican nominee for president is a man who mocks women, insults Latinos",
        "@timestamp": "2016-02-11T00:00:00.000+05:30",
        "link": "https://www.huffingtonpost.comhttp://www.nytimes.com/2016/02/11/opinion/the-gop-created-donald-trump.html",
        "category": "POLITICS",
        "headline": "The GOP Created Donald Trump",
        "authors": ""
      }
    }
  ]
}
```

## 🎯 Improve Precision with the AND Operator

Set the match operator to `and` to return headlines containing all four search words.

```http
GET news-data/_search
{
  "query": {
    "match": {
      "headline": {
        "query": "trump donald gop election",
        "operator": "and"
      }
    }
  }
}
```

This increases precision but decreases recall. For example, a headline containing only three of the four words will not be returned.

This query returns only one document:

```json
"hits": [
  {
    "_index": "news-data",
    "_id": "LTEW9aEB2zHhIrL5yKJt",
    "_score": 15.061684,
    "_source": {
      "date": "2016-03-01",
      "short_description": "\"Trump is a bully, unworthy of our nomination.\"",
      "@timestamp": "2016-03-01T00:00:00.000+05:30",
      "link": "https://www.huffingtonpost.com/entry/scott-rigell-donald-trump_us_56d5d5dfe4b03260bf7837ac",
      "category": "POLITICS",
      "headline": "GOP Congressman Says He Won't Back Donald Trump In The General Election",
      "authors": "Amanda Terkel"
    }
  }
]
```

## ✅ Match a Custom Number of Words

Use `minimum_should_match` to set the minimum number of search words that must appear in a headline.

```http
GET news-data/_search
{
  "query": {
    "match": {
      "headline": {
        "query": "trump donald gop election",
        "minimum_should_match": 3
      }
    }
  }
}
```

This example returns 169 headlines containing at least 3 of the 4 search words.

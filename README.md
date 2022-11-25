# Clean Pandas

This repository contains the supporting code for my Medium article, [The ultimate reference for clean Pandas code](https://towardsdatascience.com/the-ultimate-reference-for-clean-pandas-code-413df676e63c). 

## Description

In just one brief Jupyter notebook, I use the Twitter API v2 to scrape tweets of US Senators (117th Congress) and clean them in Pandas using method chaining and pipes. The result is a curated collection of clean Pandas methods that I use on a daily basis to clean, investigate, aggregate, and analyze text data.

## Snippet

```python
# Let's clean some tweets! 
tweets_df = (
    tweets_raw_df
    .filter(["created_at", "id", "full_text", "user.screen_name"])                                   # Keep only relevant columns
    .assign(id=tweets_raw_df["id"].astype(str),                                                      # Convert id to string
            created_at=tweets_raw_df["created_at"].apply(pd.to_datetime),                            # Convert datetime to timestamp with Pandas method
            text=tweets_raw_df["full_text"].apply(p.clean).str.replace("&amp;", "and "),             # Clean tweets using preprocessor and replace &amp; with "and "
            text_length=tweets_raw_df["full_text"].apply(len))                                       # Calculate length of tweets
    .merge(usernames_df[["username", "party"]], left_on="user.screen_name", right_on="username")     # Merge with usernames_df to get politcal affiliations                                                                                                      # Get usernames, party affiliations
    .drop(columns=["username"])                                                                      # Drop extra username column added after merge
    .drop_duplicates(["text"])                                                                       # Drop duplicate tweets
    .rename({"user.screen_name": "username"}, axis=1)                                                # Rename columns
    .sort_values(by="created_at", ascending=False)                                                   # Sort tweets by date
    .reindex(columns=["created_at", "id", "username", "text", "text_length", "party"])               # Reorder columns
    .reset_index(drop=True)                                                                          # Reset index
    .query('text_length > 0')                                                                        # Remove rows with empty tweets
)
```


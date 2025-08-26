# Crawler for Business Analysis

This Jupyter Notebook uses the **Google Custom Search API (Programmable Search Engine)** to discover Instagram accounts related to nail artists in different cities.  
It queries Google with prompts like:

```
site:instagram.com HAIRSTYLE KOREA
```

…and extracts **username**, **followers (when available)**, and **bio (from snippet text)** from the search results.

---

## Features
- Query Google for Instagram profiles by location.
- Extract basic profile info from SERP snippets (no profile clicks needed).
- Filter accounts by minimum follower count (configurable).
- Load lists of cities from a **JSON file**, so non-technical collaborators can edit cities easily.
- Export results to CSV/JSON.

---

## Project Structure
```yaml
├── notebooks/
│   └── 01_search_instagram_serp.ipynb   # Jupyter Notebook (PyCharm Pro)
├── src/
│   └── serp_cse.py                      # Helper functions
├── cities.json                          # Cities grouped by continent + top cities
├── requirements.txt                     # Python dependencies
├── .env.example                         # Template for secrets
├── .gitignore
└── README.md
```

---

## Setup

### 1. Python environment
- Open the project in **PyCharm Pro**.
- Create a new virtual environment (Python 3.11+).
- Install dependencies:
    ```bash
     pip install -r requirements.txt
    ```
### 2. Configure Google API 
- Create a Programmable Search Engine. 
- Enable Search the entire web and bias toward instagram.com/*. 
- Copy the Search engine ID (CX_ID). 
- In Google Cloud Console:
	•	Create an API key.
	•	Enable the Custom Search API.
	•	(Optional but recommended) Restrict the key to Custom Search API.
- Copy .env.example to .env and fill in:
```dotenv
GOOGLE_API_KEY=your_api_key
GOOGLE_CX=your_search_engine_id
PAGES=num_of_pages
FOLLOWER_MIN=follower_restrictions
# add more filter criterias here
```
### 3. City list
- All cities are stored in cities.json.
- It has two sections:
  - all_major_cities: grouped by continent. 
  - top_major_cities: smaller set of priority cities.
- Example:
```json
{
  "all_cities": {
    "Asia": ["Tokyo", "Seoul", "Shanghai"],
    "Europe": ["London", "Paris"]
  },
  "top_cities": ["Tokyo", "London"]
}
```
- Non-technical collaborators can safely edit this file without touching Python code.
### Usage
1. Open the notebook:
`notebooks/01_search_instagram_serp.ipynb`
2. Run through the cells:
   - Load .env keys. 
   - Fetch search results from Google. 
   - Parse usernames, bios, and followers.
   - (Optional) Loop through cities.json for multiple queries. 
3. Filter results (e.g., accounts with ≥2000 followers). 
4. Export to out/filtered.csv and out/filtered.json.
5. Example code (loading cities):

```python
import json

with open("queries/cities.json", "r", encoding="utf-8") as f:
    city_data = json.load(f)

# Flatten all continents into one list
all_cities = [city for cities in city_data["all_cities"].values() for city in cities]

# Or just use the priority list
top_cities = city_data["top_cities"]
```
### Notes
1. Be mindful of API quotas: free tier allows 100 queries/day. 
2. Each query returns up to 10 results. To fetch multiple pages, set PAGES=2 or higher in .env or code. 
3. Google snippets don’t always contain follower counts. Some rows may have None.
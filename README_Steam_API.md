# Steam Gaming Behaviour Analysis

A personal data project that pulls live data from the **Steam Web API** and **Steam Store API** to analyse your own gaming habits — playtime, genres, and account history — and translate them into business-relevant insights.

---

## Overview

This notebook answers questions like:

1. **What games do I play the most?**
2. **Which games did I abandon vs. still actively play?**
3. **What genres dominate my playtime?**
4. **How long have I been on Steam?**
5. **As a seller/marketer — what genre campaigns would work on this player?**

---

## Setup

### Requirements

```bash
pip install requests pandas matplotlib
```

| Library | Purpose |
|---|---|
| `requests` | Call the Steam Web API and Store API |
| `pandas` | Structure and clean the data |
| `matplotlib` | Visualise playtime and genre data |

### Getting Your Credentials

You need two things before running the notebook:

**1. Steam API Key**
- Go to [https://steamcommunity.com/dev/apikey](https://steamcommunity.com/dev/apikey)
- Log in and register to get your key
- Paste it into `API_KEY` at the top of the notebook

**2. Steam ID**
- Go to your Steam profile → right-click → Copy Page URL
- Or use [https://www.steamidfinder.com](https://www.steamidfinder.com)
- Paste it into `STEAM_ID`

```python
API_KEY = "your_key_here"
STEAM_ID = "your_steam_id_here"
```

> ⚠️ Your Steam profile **must be set to Public** for the API to return game data.

---

## Workflow

### 1. Player Profile Check
- Calls `GetPlayerSummaries` to verify the API connection
- Returns basic profile info: display name, avatar, account creation timestamp

### 2. Owned Games Fetch
- Calls `GetOwnedGames` with `include_appinfo=true` to get the full game library
- Converts `playtime_forever` (minutes) → `playtime_hours`
- Sorts by most played

### 3. Top 10 Visualisation
- Horizontal bar chart of the **top 10 most-played games**
- Quick visual for identifying your top titles at a glance

### 4. Last Played Analysis
- Converts `rtime_last_played` (Unix timestamp) → readable date
- Calculates `days_since_played` for every game with at least 1 minute of playtime
- Sorted ascending — most recently played games first
- Helps identify **active games vs. abandoned ones**

### 5. Account Age Calculation
- Uses `timecreated` from the player profile
- Outputs account creation date and total age in days and years

### 6. Genre Enrichment (via Steam Store API)
- Loops through every played game and calls the **Steam Store API** per `appid`
- Extracts the genre labels (e.g., Action, RPG, Free to Play)
- Rate-limited with `time.sleep(2)` between calls to avoid hitting API limits
- Appends genres back to the main dataframe

> ⚠️ This step is slow — it makes one API call per game with a 2-second delay. For a library of 50 games, expect ~2 minutes runtime.

### 7. Genre Count Analysis
- Explodes multi-genre entries (e.g., "Action, RPG" → two separate rows)
- Counts how many games fall into each genre

### 8. Genre Playtime Analysis
- Groups by genre and sums total playtime hours
- Pie chart: **Playtime Distribution by Genre**
- Stacked bar chart: **Top 10 Games broken down by Genre**

---

## Insights Produced

| Analysis | Business Takeaway |
|---|---|
| Top 10 most-played games | Player's core interests and loyalty |
| Genre frequency count | Which genres to target in campaigns |
| Genre playtime distribution | Where the player actually spends time (not just what they own) |
| Last played recency | Identifies active vs. lapsed engagement |
| Free to Play share | Signals spending behaviour (e.g., budget-conscious player) |
| Account age | Indicates how experienced/invested the player is |

**Example insight from this notebook:**
> Action dominates playtime largely because Steam labels fighting games under Action. High Free to Play playtime suggests a low-spending profile — useful context for pricing or campaign targeting.

---

## Output Visuals

| Chart | Description |
|---|---|
| Horizontal bar chart | Top 10 games by playtime hours |
| Pie chart | Total playtime split by genre |
| Stacked bar chart | Top 10 games with genre colour breakdown |

---

## File Structure

```
├── Steam_API.ipynb       # Main analysis notebook
```

No local data file needed — all data is fetched live from the Steam API at runtime.

---

## Limitations

- **Public profile required** — private profiles return empty game lists
- **Genre enrichment is slow** — one Store API call per game with rate limiting
- **Genre labels are Steam's own** — fighting games are often categorised as Action
- **No historical data** — playtime reflects all-time totals, not recent trends
- **API key is personal** — do not share or commit your key to version control

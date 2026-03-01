# Getting Started with Data Scraping

If you want to start scraping data, here's a practical path that keeps things simple and safe.

## 1) Start with the rules first

- Check the website Terms of Service.
- Check `robots.txt` (for example: `https://example.com/robots.txt`).
- Avoid scraping personal/sensitive data.
- Add polite delays and avoid sending too many requests.

## 2) Pick a basic stack

For most beginner projects, this is enough:

- `requests` for downloading HTML
- `BeautifulSoup` for parsing HTML
- `pandas` for storing/exporting data

Install:

```bash
pip install requests beautifulsoup4 pandas
```

## 3) Build a tiny scraper first

```python
import time
import requests
import pandas as pd
from bs4 import BeautifulSoup

url = "https://example.com"
headers = {"User-Agent": "Mozilla/5.0 (compatible; learning-scraper/1.0)"}

resp = requests.get(url, headers=headers, timeout=20)
resp.raise_for_status()

soup = BeautifulSoup(resp.text, "html.parser")
rows = []

for item in soup.select("article"):
    title = item.select_one("h2")
    link = item.select_one("a")
    rows.append(
        {
            "title": title.get_text(strip=True) if title else None,
            "url": link["href"] if link and link.has_attr("href") else None,
        }
    )

time.sleep(1)  # polite delay for multi-page scraping
pd.DataFrame(rows).to_csv("output.csv", index=False)
print(f"Saved {len(rows)} rows to output.csv")
```

## 4) Add pagination and resilience

- Find the "next page" URL pattern.
- Loop pages until no results remain.
- Handle retries/timeouts (e.g., exponential backoff).
- Deduplicate results by a stable key (URL or ID).

## 5) When pages are JavaScript-heavy

If `requests` returns empty content but data appears in browser:

- Use Playwright or Selenium.
- Or inspect browser network calls and hit the underlying JSON endpoint directly (usually faster/cleaner).

## 6) Keep your project maintainable

- Save raw HTML or JSON samples for debugging.
- Log request URLs/status codes.
- Write small parser functions and test them.
- Version your schema (`title`, `price`, `date`, etc.) so downstream code is stable.

---

If you want, I can next generate a targeted starter scraper for a specific site structure (list page, detail page, pagination, and CSV/JSON export).

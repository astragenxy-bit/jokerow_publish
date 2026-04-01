# Job Market Data Scraper & Aggregator

Automated system to crawl and aggregate Vietnam job market data from multiple sources.

## Features

- **Web Scraping**: Crawls TopCV, ITviec for job postings and salary data
- **Data Aggregation**: Merges data from multiple sources intelligently
- **Auto-Update**: Runs daily via GitHub Actions
- **Validation**: Ensures data integrity before publishing
- **GSO Integration**: Incorporates official labor statistics

## Data Sources

### 1. **TopCV** (https://topcv.vn)
- Job categories and posting counts
- Salary ranges by position
- Company insights

### 2. **ITviec** (https://itviec.com)
- IT job postings
- Salary data
- Company reviews

### 3. **GSO** (Tổng Cục Thống kê)
- Official labor force statistics
- Employment rates by sector
- Workforce demographics

## Components

### `scraper.py`
Main web scraper that:
- Crawls job posting sites
- Extracts salary, location, experience data
- Parses salary ranges
- Outputs `scraper_output.json`

### `data_aggregator.py`
Data pipeline that:
- Loads scraped data
- Merges with historical data
- Validates consistency
- Generates final `data-hierarchical.json`

### `.github/workflows/daily-update.yml`
GitHub Actions workflow that:
- Runs scraper daily at 2 AM UTC (9 AM VN time)
- Aggregates data
- Commits changes automatically
- Deploys to live visualization

## Local Usage

### Install Dependencies
```bash
pip install -r requirements.txt
```

### Run Scraper
```bash
python scraper.py
```

Output: `scraper_output.json`

### Run Aggregator
```bash
python data_aggregator.py
```

Input: `scraper_output.json` + `data-hierarchical.json`
Output: Updated `data-hierarchical.json`

### Full Pipeline
```bash
python scraper.py && python data_aggregator.py
```

## Data Update Flow

```
TopCV/ITviec → scraper.py → scraper_output.json
                                     ↓
data-hierarchical.json ← data_aggregator.py
         ↓
    Visualization
```

## Configuration

### Update Frequency
Edit `.github/workflows/daily-update.yml` line 7:
```yaml
- cron: '0 2 * * *'  # Change time here
```

### Weighted Averaging
In `data_aggregator.py`:
- New data: 70%
- Historical data: 30%

Change weights in `update_postings_count()` and `update_salary_data()`

## Data Quality

✓ **Validation Checks**
- Required fields present
- Data type correctness
- Value ranges reasonable
- Sector name matching

✓ **Source Attribution**
- Each data point tracks sources
- Traceable to original site
- Timestamp on every update

## Metrics Updated

| Metric | Source | Frequency |
|--------|--------|-----------|
| postings | TopCV, ITviec | Daily |
| pay | TopCV, ITviec | Daily |
| outlook | Calculated | Daily |
| competition | Calculated | Daily |
| growth | Historical + Trend | Weekly |

## Error Handling

- Non-critical errors logged but don't stop pipeline
- Failed scrapes use previous data
- Validation warnings don't block publishing
- All errors reported in workflow summary

## API Integration (Future)

```python
# Can extend scraper to use APIs instead of scraping:

# TopCV API
topcv_api = "https://api.topcv.vn/..."

# ITviec API  
itviec_api = "https://api.itviec.com/..."

# GSO API
gso_api = "https://api.gso.gov.vn/..."
```

## Troubleshooting

**Scraper returns no data**
- Check internet connection
- Verify URLs are accessible
- Check if site layout changed

**Data merge fails**
- Ensure `data-hierarchical.json` exists
- Check JSON format validity
- Verify scraper output format

**GitHub Actions fails**
- Check workflow logs
- Verify Python version compatibility
- Test locally first

## Notes

- Respects robots.txt and rate limits
- Adds random delays between requests
- Uses proper User-Agent headers
- Data is publicly available information only

## Next Steps

1. ✅ Basic scraper operational
2. ⏳ API integration (TopCV, ITviec official APIs)
3. ⏳ Add more data sources (LinkedIn, JobStreet)
4. ⏳ ML predictions for salary trends
5. ⏳ Dashboard for data quality metrics

## Support

For issues or improvements:
- Check GitHub Issues
- Review workflow logs
- Test locally before deploying

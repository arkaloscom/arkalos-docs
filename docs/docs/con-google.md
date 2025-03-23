
# Python Google Extractor API


## Create a Google OAuth Key

(1)

Create a new [Google Cloud Console](https://console.cloud.google.com/) project, e.g. "My Drive"



(2)

Open "APIs & Services", find and enable these APIs:

- Google Drive API
- Google Sheets API	
- Google Forms API
- Google Search Console API	
- Google Analytics Admin API
- Google Analytics Data API	



(3)

Setup OAuth consent screen and select User Type = Internal.

Enter your gmail into User support email, Developer contact information and/or Testing Users.

Into any Domain, URL, Authorized JavaScript origins, redirect URIs, etc, enter - http://localhost


(4)

Go to "Credentials" and create new credentials for OAuth client ID.

Select Application type - Desktop app, and provide a name for your app, e.g. "My Personal Drive Desktop OAuth"

A pop-up will display your Client ID and Client Secret. Download them as a JSON file by clicking Download JSON.

(5)

Rename the file as `google_oauth.json` and put it into `data/keys/` folder.



## Examples

Create a new notebook and try the GoogleExtractor out!

In the first cell, create a GoogleExtractor.

```python
import polars as pl

from arkalos.utils import MimeType
from arkalos.data.extractors import GoogleExtractor

google = GoogleExtractor()
```

Run a cell. It will output a link for you to open in the browser.

Enable all scopes.

New `data/tokens/google_access_token.json` file will be then created. Next time authorization won't be required.

If you need to change scopes or update the token - delete this file.



### Search and List Google Drive Files, Spreadsheets and Forms

```python

folder_id = 'folder_id'

# List files and their metadata in a Google Drive folder
files = google.drive.listFiles(folder_id)

# Search for files with regex and by type
files = google.drive.listFiles(folder_id, name_pattern='report', file_types=[MimeType.DOC_PDF])

print(pl.DataFrame(files))
```

### List All the Spreadsheets Recursively With Their Tabs (Sheets) Info

```python
files = google.drive.listSpreadsheets(folder_id, name_pattern='report', recursive_depth=1, with_meta=True, do_print=True)

for file in files:
    google.drive.downloadFile(file['id'], do_print=True)
```

### Download, Export Files and Spreadsheets or Google Form Responses

```python
google.drive.getFormMetadata(form_id)

google.drive.getFormResponses(form_id)

google.drive.getFormQuestions(form_id)

# Export Google Form responses as CSV
google.drive.downloadFile(form_id)

# Export Google Spreadsheet as LibreOffice Calc
google.drive.downloadFile(spreadsheet_id, 'my_folder/spreadsheet_name', as_mime_type=MimeType.SHEET_LIBRE_CALC)
```

### Get Data from Google Analytics 4

```python
# Past 28 days (minus 2 days of delay)
start_date = (datetime.now() - timedelta(days=29)).strftime('%Y-%m-%d')
end_date = (datetime.now() - timedelta(days=2)).strftime('%Y-%m-%d')

props = google.analytics.listProperties()

property_id = 'property_id'

google.analytics.fetchPages(property_id, start_date, end_date)

google.analytics.fetchTrafficStats(property_id, start_date, end_date)

google.analytics.fetchInternalSiteSearch(property_id, start_date, end_date)
```

### And From Google Search Console (GSC)

```python
google.analytics.listGSCSites()

site = 'sc-domain:arkalos.com'

google.analytics.fetchTopGSCPages(site, start_date, end_date)

queries = google.analytics.fetchTopGSCQueries(site, start_date, end_date)

# Sort by a built-in CTR Opportunity Score
pl.Config(tbl_rows=100)
pl.DataFrame(queries).select(pl.exclude('site', 'page_url', 'page_path')).sort('ctr_os', descending=True)

# Fetch top pages first and then their top queries (Page-first)
google.analytics.fetchTopGSCPagesThenQueries(site, start_date, end_date)

# Query-first
google.analytics.fetchTopGSCQueriesThenPages(site, start_date, end_date)

# Or as sections, instead of a single table
google.analytics.fetchTopGSCPagesThenQueries(site, start_date, end_date, with_sections=True)
```










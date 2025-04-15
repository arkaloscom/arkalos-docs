
# Python Web Extractor, Scraper & Crawler

Before you start, make sure the [browser is configured properly](/docs/browser/).

Arkalos includes a powerful built-in **WebExtractor** tool that allows you to automate the process of gathering data from websites, whether it's downloading full articles, scraping specific information, or crawling multiple pages linked from a start page.

- **Crawling** means starting from a base URL and automatically following internal links to extract data from every page it finds.
- **Scraping** means extracting specific data (like a title or price) from a specific page or part of a page.



## 🕸️ Crawling a Website: Saving Web Pages in Markdown

Want to download a website’s content for offline reading or AI training? Here's how to crawl the Arkalos documentation and save each page in Markdown format:

```python title="notebooks/web_extractor.ipynb"
from arkalos.data.extractors import WebExtractor

arkalos = WebExtractor('https://arkalos.com', True, 'article')
arkalos.crawl()
```

This creates a folder at `data/drive/crawl/arkalos.com/` containing one Markdown file per page.

**WebExtractor parameters:**

1. **Base URL** – Starting page.
2. **Trailing slash flag** – Set to `True` if URLs on the site end with a slash (e.g. `/docs/`).
3. **CSS selector for content** – For example `main`, or a specific `.class` or `#id` where the content is located.

> [!NOTE]
> Arkalos waits for the page and main content area to fully load before scraping.

Calling `crawl()` starts from the base page, discovers internal links, and saves every visited page as Markdown.



## 🎯 Scraping Specific Pages Only

You don’t always need the whole website. If you want to scrape specific pages, use:

```python title="notebooks/web_extractor.ipynb"
await arkalos.crawlSpecificPages(['docs/installation'])
```

Pass a list of relative URLs. Arkalos will visit each and extract the content using the same content selector.



## 🔍 Extracting Specific Details From Pages

Want to extract things like titles, prices, tags, or ratings from multiple articles on a page? You can create your own structured extractor.

### Step 1: Define what data to extract

Here’s an example website that has this HTML:

```html
<article data-id="6IF3WGP8V">
  <a href="...">Title of the article</a>
  <div data-item="description">Article text</div>
  <div class="rating">
    <img ...>
    5
  </div>
  <div class="tags">
    <span data-item="tag">Category A</span>
    <span data-item="tag">Category B</span>
  </div>
</article>
```

### Step 2: Create a `WebDetails` class

```python title="app/data/extractors/my_website_web_extractor.py"
from arkalos.data.extractors import WebExtractor, WebDetails, _
from dataclasses import dataclass
import polars as pl

@dataclass
class ArticleDetails(WebDetails):
    CONTAINER = 'article[data-id]'

    id: _[str, None, 'data-id']           # Attribute from container
    url: _[str, 'a', 'href']              # Link
    title: _[str, 'a']                    # Text from <a>
    description: _[str, '[data-item="description"]']
    tags: _[list[str], '[data-item="tag"]']
    rating: _[int, '.rating', 1]          # Second child (after image)
```

The `_` is a special alias for `Annotated` to provide typing and selector info in one line.



### Step 3: Create your custom extractor

```python title="app/data/extractors/my_website_web_extractor.py"
class MyWebsiteWebExtractor(WebExtractor):
    BASE_URL = 'https://mywebsite.com'
    PAGE_CONTENT_SELECTOR = 'main'
    SCROLL = True
    DETAILS = ArticleDetails

    async def crawlTechArticles(self):
        return await self.crawlSpecificDetails(['/category/tech'])
```



### Step 4: Run your crawler

```python title="notebooks/my_web_crawler.ipynb"
from app.data.extractors.my_website_web_extractor import MyWebsiteWebExtractor

mywebsite = MyWebsiteWebExtractor()
data = await mywebsite.crawlTechArticles()

df = pl.DataFrame(data)
df
```

You can view the results directly in VS Code using the **Data Wrangler** extension.



## 🧩 WebDetails and Annotations

To extract structured data from pages, you define a subclass of `WebDetails` and annotate each property using a custom annotation format. These annotations tell the scraper **what** to extract and **how**.

The annotation format is:

```python
_[data_type, 'CSS selector' | None, optional_extraction_instruction]
```

- `data_type`: The expected Python type of the result. This can be:
    - `str`, `int`, `float`
    - `list[str]` — to extract multiple values as a list using `querySelectorAll()`

- `'CSS selector'`: A standard CSS selector string for locating the desired element inside the container. If set to `None`, the container itself will be used.

- `optional_extraction_instruction`: Tells how to extract the data. It could be:
    - An attribute name (e.g. `'href'`)
    - A `slice`, like `1:` or `:3`
    - A regular expression string with **one capture group**
    - An integer — to get a specific child node (e.g. the second text node)

**Default Behavior:**

If you only provide the data type and a CSS selector — without any extras — the **text content** of the element is extracted using `.textContent`.

If the CSS selector doesn't match anything inside the container, the default value for the data type is returned (`""`, `0`, etc.).


**Supported Extraction Instructions (the 3rd argument)**

1. **Attribute Name**  
   ```python
   url: _[str, 'a', 'href']
   ```
   Extracts the value of the `href` attribute from the `<a>` element.

2. **Slice**  
   ```python
   price: _[int, '.price', '1:']  # Skips the currency symbol
   ```
   If `.price` contains text like `"$99"`, the slice will extract `"99"` and convert it to `int`.

3. **Regular Expression**  
   ```python
   sku: _[str, '.sku', r'ID-(\w{8})']
   ```
   Extracts matched group from a string like `"Product ID-ABC12345"`.

4. **Child Node Index**  
   ```python
   rating: _[int, '.rating', 1]
   ```
   Useful when the target number is inside a sibling text node, and you want to skip over images or other tags.



**Lists with querySelectorAll()**

When the type is `list[str]`, `querySelectorAll()` is used instead of `querySelector()`, and all matched elements’ text contents are extracted into a list:

```python
tags: _[list[str], '[data-item="tag"]']
# ⟶ ['Category A', 'Category B']
```

**If You Want to Extract from the Container Itself**

Set the selector to `None` and use an attribute name or other instruction:

```python
id: _[str, None, 'data-id']
```



## 🧱 WebExtractor Constants

Your custom WebExtractor can set these config values:

Required:

- `BASE_URL`: Starting point for crawling
- `PAGE_CONTENT_SELECTOR`: The main content selector

Optional:

- `BROWSER_TYPE`: Choose browser type (default is `EDGE_HEADLESS`)
- `TRAIL_SLASH`: Add a trailing slash to URLs (default `False`)
- `WAIT_FOR_SELECTOR`: Wait for this selector to appear before scraping
- `CLICK_TEXT`: Auto-click this button (e.g. cookie popup) before scraping
- `SCROLL`: Scroll the page to load dynamic content (default `False`)

For structured details:

- `DETAILS`: Your custom `WebDetails` class; or
- `JSON_DETAILS`: or JS code to extract data from JSON blobs on the page




## 📦 Scraping JSON Data From Web Pages

Some websites store content as embedded JSON inside `<script>` tags.

You can extract it using JavaScript instead of HTML selectors:

```python title="app/data/extractors/my_website_web_extractor.py"
class MyWebsiteWebExtractor(WebExtractor):
    BASE_URL = 'https://mywebsite.com'
    PAGE_CONTENT_SELECTOR = 'main'
    JSON_DETAILS = 'JSON.stringify(JSON.parse(document.querySelector("script#__DATA__").textContent).props.articles)'

    async def crawlTechArticles(self):
        return await self.crawlSpecificDetailsJSON(['/category/tech'])
```

Use `crawlSpecificDetailsJSON()` instead of `crawlSpecificDetails()` when using JSON extraction.




## 🧾 Get Raw HTML or a Parsed HTML Document

Arkalos can fetch the raw HTML or a parsed document using `HTMLDocumentParser`.

```python title="notebooks/crawl.ipynb"
html = await extractor.getPageHTML(url)
doc = await extractor.getPageHTMLDoc(url)
```

The `doc` object has many useful properties:

```python
doc.url
doc.baseURL
doc.domain
doc.path
doc.charset
doc.title
doc.meta
doc.metaProps
doc.links
doc.pageLinks
doc.internalPageLinks
doc.externalPageLinks
doc.cleanHTML
doc.contentHTML
doc.markdown
```

You can import `HTMLDocumentParser` from:

```python
from arkalos.utils.html_utils import HTMLDocumentParser
```

It uses **BeautifulSoup4** (with **lxml**) and **markdownify** for conversion to clean Markdown.



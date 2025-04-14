# Python Browser Automation

## Configuration

Arkalos makes it easy to automate the web using a real browser. You can use it for scraping, crawling, or saving web pages as Markdown. It’s built on top of Microsoft’s [Playwright](https://playwright.dev/python/) engine — a modern and fast automation library that supports all major browsers.

Playwright works by controlling a real browser (like Edge or Chrome), either in a headless mode (without UI) or with a visible window. It follows web standards and provides a powerful and clean API, like `query_selector_all()` to get multiple elements from the page.

You can use Arkalos to:

- Automate your browser while you're logged in
- Interact with dynamic websites
- Extract specific data
- Scroll pages like a real user

Before using browser features, make sure to install Playwright’s browsers and system dependencies:

> [!NOTE]  
> Chrome and Edge will be installed globally and might overwrite your current browser installation. If you already have them installed, you can install only specific browsers. If you are inside WSL or server, install all of them.

```bash title="VS Code terminal - inside the Arkalos project"
playwright install --with-deps
```

If you don’t have the Playwright Python module yet, add it to your project:

```bash
uv add playwright
```

## For Windows and WSL Users

If you're using **WSL (Windows Subsystem for Linux)**, Playwright may not be able to connect to your browser running in Windows. You might see timeout errors when launching a browser.

To fix this, switch WSL's networking mode to **mirrored**, which lets your WSL environment access services running on Windows.

Follow these steps:

1. Create a `.wslconfig` file in your Windows user directory (e.g., `C:\Users\YourUsername`)
2. Add this content:
```ini title="C:\Users\YourUsername\.wslconfig"
[wsl2]
networkingMode=mirrored
```

3. Close all running VS Code and WSL terminals.
4. Restart WSL from the **Windows Terminal (Admin)**:
```bash
wsl --shutdown
```

5. Reopen VS Code and your project inside WSL.
6. Verify that WSL is using the mirrored networking mode:
```bash
wslinfo --wsl-version
wslinfo --networking-mode
```

## Connecting to and Automating Your Real Browser

Sometimes, you want to automate a **live browser that's already running**, for example to:

- Skip login steps
- Avoid CAPTCHAs
- Access pages where you're already authenticated

Arkalos supports this by connecting to browsers using the **Chromium DevTools Protocol (CDP)**—a remote debugging protocol supported by Edge and Chrome.

> [!NOTE]  
> Only Chromium-based browsers like **Chrome** or **Edge** support CDP.

### Launching Your Browser in CDP Mode

To enable CDP, you need to start your browser with a special flag that opens a remote debugging port.

**On Windows:**

```bash title="Windows Terminal (Admin)"
start msedge --remote-debugging-port=9222

# or Chrome
start chrome --remote-debugging-port=9222
```

**On macOS:**

```bash title="Mac Terminal"
sudo /Applications/Microsoft\ Edge.app/Contents/MacOS/Microsoft\ Edge --remote-debugging-port=9222

# or Chrome
sudo /Applications/Google\ Chrome.app/Contents/MacOS/Google\ Chrome --remote-debugging-port=9222
```

## Creating a WebBrowser and Running an Action

Once your browser is running in CDP mode and you're logged in, you can connect to it using Arkalos.

Use the `WebBrowser` class to launch or connect to a browser. By default, it creates a headless Edge browser. To connect to your live browser, use `REMOTE_CDP` type.

Available browser types are defined inside `WebBrowser.TYPE`:

- `REMOTE_CDP` – Connects to a running Chrome/Edge instance
- `EDGE_HEADLESS` (default)  
- `EDGE_UI`  
- `CHROME_HEADLESS`  
- `CHROME_UI`  
- `FIREFOX_HEADLESS`  
- `FIREFOX_UI`  
- `SAFARI_HEADLESS`  
- `SAFARI_UI`  

```python title="notebooks/my_live_browser_automation.ipynb"
from arkalos.browser import WebBrowser, WebBrowserTab

browser = WebBrowser(WebBrowser.TYPE.REMOTE_CDP)

async def search_google(tab: WebBrowserTab):
    await tab.goto('https://www.google.com')
    search_input = tab.get_by_role('combobox', name='Search')
    await search_input.click()
    await search_input.fill('cats')
    await search_input.press('Enter')
    images_tab = tab.get_by_role('link', name='Images', exact=True)
    await images_tab.click()

await browser.run(search_google)
```

> [!NOTE]  
> Arkalos uses **async/await** syntax. If you're working in a Jupyter notebook, you can use `await` directly. For regular Python scripts, wrap it with `asyncio.run()`.

This example will open your real browser and run a search on Google, just like a user would.

You can pass more arguments to the `run()` callback and return data from it. The `tab` parameter gives you full control of the browser page.

## Impersonation

When you create a `WebBrowser`, Arkalos automatically sets:

- Correct headers
- User agent strings
- Browser behaviors

This helps you avoid detection or errors when interacting with modern websites.

## Testing Headers, User Agent, IP, and Cookies

To debug or inspect what your browser is sending, use this utility:

```python title="notebooks/browser.ipynb"
test = await WebBrowser().testHeadersCookiesIP()
headers = test['headers']
cookies = test['cookies']
ip = test['origin']
```

This tells you:

- What headers are being sent
- The cookies stored
- Your current IP address

Useful for testing VPNs, proxies, or impersonation setup.

## Scrolling

Many modern websites load content as you scroll. Arkalos supports smooth, human-like scrolling with `WebBrowser.scroll(tab)`.

Use it inside your automation callback:

```python title="notebooks/browser.ipynb"
async def handle_page(tab: WebBrowserTab):
    WebBrowser.scroll(tab)
    ...
```

This helps trigger lazy-loaded content or infinite scroll.

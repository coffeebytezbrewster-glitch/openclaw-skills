# Playwright Browser Automation Skill

Browser automation using Playwright via NodeScript.

## When to Use

Use this skill when:
- Taking screenshots of web pages
- Automating form submissions
- Testing web applications
- Scraping dynamic content
- Any browser automation task

## Important: Use NodeScript for Viewport

The standard Playwright MCP tool has viewport limitations. **Use NodeScript** for reliable viewport control:

```javascript
// NodeScript script for viewport screenshots
const { chromium } = require('playwright');

async function screenshot(url, options = {}) {
  const {
    width = 1280,
    height = 800,
    fullPage = false,
    output = 'screenshot.png'
  } = options;

  const browser = await chromium.launch({ headless: true });
  const context = await browser.newContext({
    viewport: { width, height }
  });
  
  const page = await context.newPage();
  await page.goto(url, { waitUntil: 'networkidle' });
  
  await page.screenshot({
    path: output,
    fullPage,
    viewport: { width, height }
  });

  await browser.close();
  return output;
}

// Usage
screenshot('https://example.com', {
  width: 1280,
  height: 800,
  output: 'screenshot.png'
});
```

## Quick Start

### Install Playwright

```bash
npm install playwright
npx playwright install chromium
```

### Basic Screenshot Script

```javascript
// screenshot.js
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();
  
  await page.goto('https://example.com');
  await page.screenshot({ path: 'screenshot.png' });
  
  await browser.close();
})();
```

Run:
```bash
node screenshot.js https://example.com screenshot.png --width 1280 --height 800
```

## NodeScript CLI Usage

The `screenshot.js` script supports:

```bash
node screenshot.js <url> <output.png> [--width=1280] [--height=800] [--full] [--element=selector]
```

| Option | Description |
|--------|-------------|
| `--width` | Viewport width (default: 1280) |
| `--height` | Viewport height (default: 800) |
| `--full` | Full page screenshot |
| `--element` | Screenshot specific element |
| `--scrollTo` | Scroll to element before screenshot |

### Desktop Viewport

```bash
node screenshot.js "https://example.com" desktop.png --width 1280 --height 800
```

### Mobile Viewport

```bash
node screenshot.js "https://example.com" mobile.png --width 390 --height 844
```

### Element Screenshot

```bash
node screenshot.js "https://example.com" header.png --element "#header"
```

### Full Page Screenshot

```bash
node screenshot.js "https://example.com" fullpage.png --full
```

## Browser Automation Examples

### Navigate and Click

```javascript
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();
  
  await page.goto('https://example.com');
  await page.click('#submit-button');
  await page.waitForURL('**/success');
  
  await browser.close();
})();
```

### Fill Form

```javascript
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({ headless: true });
  const context = await browser.newContext({
    viewport: { width: 1280, height: 800 }
  });
  const page = await context.newPage();
  
  await page.goto('https://example.com/form');
  await page.fill('#email', 'test@example.com');
  await page.fill('#password', 'secret123');
  await page.click('button[type="submit"]');
  
  await browser.close();
})();
```

### Handle Dialogs

```javascript
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();
  
  // Handle alert dialog
  page.on('dialog', async dialog => {
    console.log(`Dialog: ${dialog.message()}`);
    await dialog.accept();
  });
  
  await page.goto('https://example.com');
  await page.click('#alert-button');
  
  await browser.close();
})();
```

### Wait for Element

```javascript
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage();
  
  await page.goto('https://example.com');
  await page.waitForSelector('#dynamic-content', { timeout: 10000 });
  const content = await page.$eval('#dynamic-content', el => el.textContent);
  
  console.log(content);
  await browser.close();
})();
```

### Screenshot with Wait

```javascript
const { chromium } = require('playwright');

(async () => {
  const browser = await chromium.launch({ headless: true });
  const page = await browser.newPage({
    viewport: { width: 1280, height: 800 }
  });
  
  await page.goto('https://example.com');
  
  // Wait for network idle
  await page.waitForLoadState('networkidle');
  
  // Wait for specific element
  await page.waitForSelector('.content-loaded', { timeout: 5000 });
  
  await page.screenshot({ path: 'page.png' });
  
  await browser.close();
})();
```

## Viewport Reference

| Device | Width | Height |
|--------|-------|--------|
| Desktop | 1280 | 800 |
| Laptop | 1440 | 900 |
| Tablet | 768 | 1024 |
| iPhone 14 | 390 | 844 |
| iPhone 15 Pro | 393 | 852 |
| Pixel 7 | 412 | 915 |

## Headless vs Headed

```javascript
// Headless (default, faster)
const browser = await chromium.launch({ headless: true });

// Headed (visible browser)
const browser = await chromium.launch({ headless: false });
```

## Common Issues

### Viewport Not Working

**Use NodeScript** instead of MCP tool:

```bash
# Wrong (viewport may not work)
mcporter call playwright.browser_take_screenshot type:png

# Right (reliable viewport control)
node screenshot.js "https://url" output.png --width 1280 --height 800
```

### Element Not Found

```javascript
// Wait for element to be visible
await page.waitForSelector('#element', { state: 'visible' });

// Scroll into view first
await page.locator('#element').scrollIntoViewIfNeeded();
```

### Screenshot Timeout

```javascript
await page.goto(url, { waitUntil: 'networkidle', timeout: 30000 });
```

## Complete NodeScript Example

```javascript
// screenshot.js
const { chromium } = require('playwright');

async function screenshot(url, outputPath, options = {}) {
  const {
    width = 1280,
    height = 800,
    fullPage = false,
    element = null,
    waitFor = 1000
  } = options;

  const browser = await chromium.launch({ headless: true });
  const context = await browser.newContext({
    viewport: { width, height }
  });
  
  const page = await context.newPage();
  await page.goto(url, { waitUntil: 'networkidle' });
  await page.waitForTimeout(waitFor);

  if (element) {
    await page.locator(element).screenshot({ path: outputPath });
  } else {
    await page.screenshot({ path: outputPath, fullPage });
  }

  await context.close();
  await browser.close();
  return outputPath;
}

// CLI
const args = process.argv.slice(2);
if (args.length < 2) {
  console.log('Usage: node screenshot.js <url> <output.png> [--width=1280] [--height=800] [--full] [--element=selector]');
  process.exit(1);
}

const url = args[0];
const output = args[1];
const options = {
  fullPage: args.includes('--full'),
  width: parseInt(args.find(a => a.startsWith('--width='))?.split('=')[1] || 1280),
  height: parseInt(args.find(a => a.startsWith('--height='))?.split('=')[1] || 800),
  element: args.find(a => a.startsWith('--element='))?.split('=')[1] || null
};

screenshot(url, output, options)
  .then(() => console.log(`Screenshot saved: ${output}`))
  .catch(console.error);
```

## Resources

- **Playwright**: [playwright.dev](https://playwright.dev)
- **NodeScript**: [nodescript.dev](https://nodescript.dev)
- **GitHub**: [github.com/microsoft/playwright](https://github.com/microsoft/playwright)

---

*Note: For reliable viewport control, use NodeScript instead of the MCP tool. The MCP browser tool may not respect viewport settings in some configurations.*

*Last updated: February 3, 2026*

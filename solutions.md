# 🚀 Playwright + JavaScript – 50 Advanced Programming Solutions

This document contains practical coding solutions for 50 advanced Playwright + JavaScript interview questions.

---

# 🔹 Section 1: Core Playwright Coding

## 1️⃣ Wait and Click Safely
```js
async function safeClick(page, selector) {
  const element = page.locator(selector);
  await element.waitFor({ state: 'visible' });
  await element.click();
}
```

## 2️⃣ Dynamic Locator Function
```js
const getButton = (page, text) => page.locator(`button:has-text("${text}")`);
await getButton(page, "Submit").click();
```

## 3️⃣ Retry Click 3 Times
```js
async function retryClick(locator) {
  for (let i = 0; i < 3; i++) {
    try {
      await locator.click();
      return;
    } catch (error) {
      if (i === 2) throw error;
    }
  }
}
```

## 4️⃣ Conditional Visibility Handling
```js
const popup = page.locator('#popup');
if (await popup.isVisible()) {
  await popup.locator('.close').click();
}
```

## 5️⃣ Scroll Until Element Visible
```js
await page.locator('#element').scrollIntoViewIfNeeded();
```

## 6️⃣ Check If Element Exists
```js
const exists = (await page.locator('#id').count()) > 0;
```

## 7️⃣ Extract All Texts From Elements
```js
const texts = await page.locator('.items').allTextContents();
```

## 8️⃣ Handle Multiple Elements
```js
const items = page.locator('.item');
await items.nth(0).click();
```

## 9️⃣ Select Dropdown by Label
```js
await page.selectOption('#dropdown', { label: 'Option 1' });
```

## 🔟 Checkbox State Validation
```js
const checkbox = page.locator('#agree');
if (!(await checkbox.isChecked())) {
  await checkbox.check();
}
```

---

# 🔹 Section 2: Async / Await & Promises

## 11️⃣ Why Async/Await?
All Playwright actions return Promises → must use `await` to ensure proper execution order.

## 12️⃣ Parallel Execution
```js
await Promise.all([
  page.waitForNavigation(),
  page.click('#login')
]);
```

## 13️⃣ Wait for Multiple API Responses
```js
await Promise.all([
  page.waitForResponse(res => res.url().includes('/api1')),
  page.waitForResponse(res => res.url().includes('/api2'))
]);
```

## 14️⃣ Measure Page Load Time
```js
const start = Date.now();
await page.goto('https://example.com');
console.log(`Load Time: ${Date.now() - start}ms`);
```

## 15️⃣ Handle Popup Window
```js
const [popup] = await Promise.all([
  page.waitForEvent('popup'),
  page.click('#open')
]);
```

## 16️⃣ Validate API Status
```js
const response = await page.waitForResponse('/api');
expect(response.status()).toBe(200);
```

## 17️⃣ Capture JSON Response
```js
const data = await response.json();
```

## 18️⃣ Mock API Response
```js
await page.route('/api', route =>
  route.fulfill({
    status: 200,
    body: JSON.stringify({ success: true })
  })
);
```

## 19️⃣ Avoid Race Conditions
```js
await page.waitForSelector('#loaded');
```

## 20️⃣ Retry Flaky Tests
```js
test.retry(2);
```

---

# 🔹 Section 3: Network & API Automation

## 21️⃣ Block API
```js
await page.route('**/ads/**', route => route.abort());
```

## 22️⃣ Modify Request Headers
```js
await page.route('/api', route => {
  route.continue({
    headers: {
      ...route.request().headers(),
      test: 'true'
    }
  });
});
```

## 23️⃣ Validate API vs UI
```js
const apiData = await response.json();
await expect(page.locator('#name')).toHaveText(apiData.name);
```

## 24️⃣ Capture Network Requests
```js
page.on('request', req => console.log(req.url()));
```

## 25️⃣ Validate Response Time
```js
expect(response.timing().responseEnd).toBeLessThan(2000);
```

## 26️⃣ Simulate Slow Network
```js
await page.route('/api', route =>
  setTimeout(() => route.continue(), 3000)
);
```

## 27️⃣ Mock 500 Error
```js
await page.route('/api', route =>
  route.fulfill({ status: 500 })
);
```

## 28️⃣ File Upload via API
```js
await request.post('/upload', {
  multipart: { file: 'file.txt' }
});
```

## 29️⃣ GraphQL Request
```js
await request.post('/graphql', {
  data: { query: '{ users { id name } }' }
});
```

## 30️⃣ Schema Validation (Using AJV)
```js
const valid = ajv.validate(schema, data);
expect(valid).toBeTruthy();
```

---

# 🔹 Section 4: Advanced DOM Handling

## 31️⃣ Nested Iframes
```js
const frame = page.frameLocator('#frame1').frameLocator('#frame2');
await frame.locator('#btn').click();
```

## 32️⃣ Shadow DOM
```js
await page.locator('custom-element >> button').click();
```

## 33️⃣ Validate Sorting
```js
const values = await page.locator('.price').allTextContents();
expect(values).toEqual([...values].sort());
```

## 34️⃣ Select Row by Text
```js
await page.locator('tr', { hasText: 'John' }).click();
```

## 35️⃣ Pagination Loop
```js
while (await nextBtn.isEnabled()) {
  await nextBtn.click();
}
```

## 36️⃣ Infinite Scroll
```js
await page.evaluate(() => window.scrollTo(0, document.body.scrollHeight));
```

## 37️⃣ Compare Two Tables
Extract rows as arrays → compare using `expect(array1).toEqual(array2);`

## 38️⃣ Click Hidden Element
```js
await page.locator('#hidden').click({ force: true });
```

## 39️⃣ Validate Tooltip
```js
await page.hover('#info');
await expect(page.locator('.tooltip')).toHaveText('Details');
```

## 40️⃣ Drag and Drop
```js
await page.dragAndDrop('#source', '#target');
```

---

# 🔹 Section 5: Framework & Architecture

## 41️⃣ Page Object Model
```js
class LoginPage {
  constructor(page) {
    this.page = page;
    this.username = page.locator('#user');
  }
}
```

## 42️⃣ Base Page Class
```js
class BasePage {
  constructor(page) {
    this.page = page;
  }
}
```

## 43️⃣ Custom Fixture
```js
import { test as base } from '@playwright/test';

export const test = base.extend({
  loginPage: async ({ page }, use) => {
    await use(new LoginPage(page));
  }
});
```

## 44️⃣ Environment Configuration
```js
const baseURL = process.env.BASE_URL;
```

## 45️⃣ Parallel Execution
```js
test.describe.configure({ mode: 'parallel' });
```

## 46️⃣ Tagging Tests
```js
test('Login Test @smoke', async ({ page }) => {});
```

## 47️⃣ Dynamic Tests from JSON
```js
data.forEach(user => {
  test(`Login for ${user.name}`, async () => {});
});
```

## 48️⃣ Custom Reporter
Create a class implementing Playwright Reporter interface and configure in playwright.config.js.

## 49️⃣ Fail Test on Console Error
```js
page.on('console', msg => {
  if (msg.type() === 'error') {
    throw new Error(msg.text());
  }
});
```

## 50️⃣ Scalable Framework Design
- Page Object Model
- Custom Fixtures
- Environment Config
- Parallel Execution
- CI/CD Integration
- Reporting
- Retry Mechanism
- Test Data Management

---

# 👩‍💻 Author

Roshni Panarajan  
Automation Engineer | Playwright | JavaScript | QA

---

⭐ If this repository helps you, please consider starring it.

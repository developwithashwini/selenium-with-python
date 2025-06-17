##🔹 4.1 Dynamic Elements
🧠 What Are They?
Dynamic elements are those that:

-Load asynchronously (via JavaScript)

-Change frequently (IDs, classes)

-Appear after delays

✅ Common Examples:
Search results

Modal pop-ups

Notifications (toasts)

###🔧 Handling Dynamic Elements

-Use Explicit Waits

-Use stable locators (like XPath contains, CSS attributes)

✅ Example:

from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

driver.get("https://example.com")

element = WebDriverWait(driver, 10).until(
    EC.presence_of_element_located((By.XPATH, "//button[contains(text(), 'Submit')]"))
)
element.click()
⚠️ Avoid:
time.sleep() — unreliable and slows tests

🔹 4.2 Web Tables & Pagination
🧠 Why Important?
Used in dashboards, admin panels, and reports.
You may need to verify data, click rows, or handle pages.

✅ Extracting Table Data

rows = driver.find_elements(By.XPATH, "//table[@id='data']//tr")

for row in rows:
    cols = row.find_elements(By.TAG_NAME, "td")
    data = [col.text for col in cols]
    print(data)
🔁 Handling Pagination

while True:
    scrape_table_data()
    next_btn = driver.find_element(By.LINK_TEXT, "Next")
    if "disabled" in next_btn.get_attribute("class"):
        break
    else:
        next_btn.click()

##🔹 4.3 Auto-Suggestions
🧠 Where It's Used?
Google search

E-commerce filters

Typeahead inputs

✅ Selecting Auto Suggestion

search = driver.find_element(By.ID, "search")
search.send_keys("python")

WebDriverWait(driver, 10).until(
    EC.presence_of_all_elements_located((By.XPATH, "//ul[@role='listbox']/li"))
)

suggestions = driver.find_elements(By.XPATH, "//ul[@role='listbox']/li")

for s in suggestions:
    if "python tutorial" in s.text.lower():
        s.click()
        break
🔹 4.4 Upload/Download Files
📤 File Upload
Use <input type="file"> elements.

upload_input = driver.find_element(By.ID, "file-upload")
upload_input.send_keys("C:/Users/Ashwini/Documents/test.pdf")
📥 File Download
Set preferences in ChromeOptions:

from selenium.webdriver.chrome.options import Options

options = Options()
options.add_experimental_option("prefs", {
    "download.default_directory": "C:/Downloads",
    "download.prompt_for_download": False,
    "plugins.always_open_pdf_externally": True
})
driver = webdriver.Chrome(options=options)
📝 Some downloads (like PDFs or CSVs) need auto-confirm download, which is browser-specific.

##🔹 4.5 Logging
🧠 Why Logging?
  Better than print()
  Used for:

  -Debugging
  
  -Test reports
  
  -Failures

✅ Logging Setup

import logging

logging.basicConfig(level=logging.INFO, filename="test.log",
                    format="%(asctime)s - %(levelname)s - %(message)s")

logging.info("Test started")
logging.warning("Button not clickable")
💡 Use different log levels: DEBUG, INFO, WARNING, ERROR, CRITICAL

##🔹 4.6 WebDriverManager
🧠 Why Use It?
No more manual .exe downloads or PATH configuration.

✅ Setup

pip install webdriver-manager
✅ Usage:

from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager

driver = webdriver.Chrome(ChromeDriverManager().install())

##🔹 4.7 Headless Mode
🧠 Why Headless?

  -CI/CD pipelines
  
  -Faster tests
  
  -No UI rendering

✅ Chrome Headless Mode:

options = webdriver.ChromeOptions()
options.add_argument("--headless")
options.add_argument("--window-size=1920,1080")

driver = webdriver.Chrome(options=options)
💡 Still can take screenshots:

driver.save_screenshot("result.png")

##🔹 4.8 Multi-browser Testing
🧠 Purpose
Ensure app works in:

-Chrome

-Firefox

-Edge

✅ Cross-Browser Setup

from selenium.webdriver import Chrome, Firefox
from webdriver_manager.chrome import ChromeDriverManager
from webdriver_manager.firefox import GeckoDriverManager

browser = "firefox"

if browser == "chrome":
    driver = Chrome(ChromeDriverManager().install())
elif browser == "firefox":
    driver = Firefox(executable_path=GeckoDriverManager().install())
✅ Cross-Browser in Pytest
Use pytest fixture to run tests on multiple browsers.

@pytest.fixture(params=["chrome", "firefox"])
def driver(request):
    if request.param == "chrome":
        return webdriver.Chrome(ChromeDriverManager().install())
    else:
        return webdriver.Firefox(executable_path=GeckoDriverManager().install())


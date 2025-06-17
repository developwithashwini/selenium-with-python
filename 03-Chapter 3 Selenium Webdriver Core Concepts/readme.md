
📘 Chapter 3: Selenium WebDriver – Core Concepts

🔹 3.1 Working with Web Elements

✅ find_element() and find_elements()

These are the most commonly used Selenium methods for locating HTML elements.

find_element(by, value) → Returns one WebElement

find_elements(by, value) → Returns a list of WebElements

Example:

from selenium import webdriver
from selenium.webdriver.common.by import By

driver = webdriver.Chrome()
driver.get("https://example.com")

# Single element
username_input = driver.find_element(By.ID, "username")

# Multiple elements
all_links = driver.find_elements(By.TAG_NAME, "a")

🔹 Why needed?

You can’t interact with a page unless you can locate the HTML element.

✅ click(), send_keys(), text, get_attribute()

click() – Simulates a mouse click

send_keys() – Types into an input box

.text – Gets the visible text of an element

get_attribute() – Gets the value of any HTML attribute

Example:

username_input.send_keys("admin")
login_button = driver.find_element(By.ID, "submit")
login_button.click()

🔹 3.2 Handling Radio Buttons and Checkboxes

✅ Select using click()

radio = driver.find_element(By.ID, "genderMale")
radio.click()

✅ Verify if selected:

is_selected = radio.is_selected()

🔹 3.3 Handling Dropdowns

Use the Select class.

from selenium.webdriver.support.ui import Select

dropdown = Select(driver.find_element(By.ID, "country"))
dropdown.select_by_visible_text("India")

🔹 3.4 Handling Alerts & Popups

alert = driver.switch_to.alert
print(alert.text)
alert.accept()  # or alert.dismiss()

🔹 3.5 Handling Windows and Tabs

original = driver.current_window_handle
all_windows = driver.window_handles

driver.switch_to.window(all_windows[1])  # Go to new tab
driver.switch_to.window(original)  # Come back

🔹 3.6 Handling Frames and iFrames

driver.switch_to.frame("frameName")
# Interact inside the frame
driver.switch_to.default_content()  # Exit frame

🔹 3.7 Browser Navigation

driver.get("https://google.com")
driver.back()
driver.forward()
driver.refresh()

🔹 3.8 Get Current URL and Title

print(driver.current_url)
print(driver.title)

🔹 3.9 Waits: Implicit & Explicit

✅ Implicit Wait

driver.implicitly_wait(10)

✅ Explicit Wait

from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

wait = WebDriverWait(driver, 10)
wait.until(EC.presence_of_element_located((By.ID, "username")))

🔹 3.10 JavaScript Execution

driver.execute_script("alert('Hello from JS!')")

Used when:

Element is hidden

You want to scroll, click via JS, or fetch values not exposed in DOM

🔹 3.11 Taking Screenshots

driver.save_screenshot("screenshot.png")

🔹 3.12 Working with ActionChains

from selenium.webdriver.common.action_chains import ActionChains

actions = ActionChains(driver)
element = driver.find_element(By.ID, "hoverMenu")

actions.move_to_element(element).perform()  # Hover
actions.context_click(element).perform()    # Right click

For drag and drop:

source = driver.find_element(By.ID, "dragMe")
target = driver.find_element(By.ID, "dropHere")
actions.drag_and_drop(source, target).perform()

🔹 3.13 Working with Cookies

# Get cookies
print(driver.get_cookies())

# Add cookie
driver.add_cookie({'name': 'mycookie', 'value': '123456'})

# Delete cookies
driver.delete_all_cookies()

🧠 Summary

Topic          Key Methods
---
Web Elements  find_element, click(), send_keys()

Dropdowns     Select class

Alerts        switch_to.alert

Windows/Tabs  switch_to.window()

Frames        switch_to.frame()

Waits         implicitly_wait(), WebDriverWait

JavaScript    execute_script()

Screenshots   save_screenshot()

ActionChains  move_to_element(), context_click(), drag_and_drop()

Cookies       get_cookies(), add_cookie(), delete_all_cookies()


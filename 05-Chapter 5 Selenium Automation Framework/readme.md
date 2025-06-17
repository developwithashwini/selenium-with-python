This chapter transforms your basic test scripts into scalable, maintainable, and reusable automation suites used in real-world projects.

🔹 Sections Covered

Framework Structure

Page Object Model (POM)

Data-Driven Testing

Hybrid Framework

Utilities

Fixtures & conftest.py

How to Run This Framework

🔹 5.1 Framework Structure

🧠 Why a Framework?

Frameworks bring:

Scalability: Handle hundreds of test cases.

Readability: Clean code and folder structure.

Reusability: Shared code = less maintenance.

CI/CD Integration Ready

📂 Sample Directory Structure

SeleniumFramework/
├── tests/                  # Test cases (Pytest-based)
│   └── test_login.py
├── pages/                  # Page Object Model classes
│   └── login_page.py
├── data/                   # Test data (Excel, CSV, JSON)
│   └── users.json
├── utils/                  # Utility files (logger, readers)
│   ├── logger.py
│   ├── read_json.py
│   └── wait_helper.py
├── config/                 # Configs like browser, URL
│   └── config.yaml
├── conftest.py             # Pytest fixtures (setup, teardown)
├── requirements.txt
├── run_tests.py            # Optional runner script
└── README.md

🔹 5.2 Page Object Model (POM)

🧠 What Is It?

POM is a design pattern to keep:

Locators + actions in a class (Page class)

Tests separate and clean

✅ login_page.py

from selenium.webdriver.common.by import By

class LoginPage:
    def __init__(self, driver):
        self.driver = driver
        self.username = (By.ID, "username")
        self.password = (By.ID, "password")
        self.login_btn = (By.ID, "login")

    def login(self, user, pwd):
        self.driver.find_element(*self.username).send_keys(user)
        self.driver.find_element(*self.password).send_keys(pwd)
        self.driver.find_element(*self.login_btn).click()

✅ test_login.py

import pytest
from pages.login_page import LoginPage

def test_valid_login(driver):
    login = LoginPage(driver)
    login.login("admin", "admin123")
    assert "Dashboard" in driver.title

🔹 5.3 Data-Driven Testing

🧠 Why?

Run the same test with different data

Used for form testing, login testing, etc.

✅ Using @pytest.mark.parametrize

@pytest.mark.parametrize("username, password", [
    ("admin", "admin123"),
    ("user1", "wrongpass"),
])
def test_login_data(driver, username, password):
    login = LoginPage(driver)
    login.login(username, password)

✅ Using JSON File

users.json

[
  {"username": "admin", "password": "admin123"},
  {"username": "user", "password": "pass"}
]

read_json.py

import json

def get_users():
    with open("data/users.json") as f:
        return json.load(f)

test_login.py

import pytest
from utils.read_json import get_users

@pytest.mark.parametrize("user", get_users())
def test_login_json(driver, user):
    login = LoginPage(driver)
    login.login(user["username"], user["password"])

🔹 5.4 Hybrid Framework

🧠 What Is It?

Hybrid = POM + Data-Driven + Utilities + Logging + Reporting

This is what most enterprise teams use.

Components of a Hybrid Framework

Component

Example Use

POM

Separate locator logic

Data-Driven

Pull from JSON, Excel

Utils

Logging, wait helpers

Config

Base URLs, browser setup

Reporting

Generate HTML/Allure reports

📂 Folder Snapshot

├── reports/              # HTML or Allure reports
├── drivers/              # Browser drivers (if not using WDM)
├── run_tests.py          # Master runner

You can trigger the full test suite using:

pytest --html=reports/report.html

🔹 5.5 Utilities

🛠️ Logger Utility: utils/logger.py

import logging

def get_logger(name):
    logger = logging.getLogger(name)
    logger.setLevel(logging.INFO)
    handler = logging.FileHandler("logs/test.log")
    logger.addHandler(handler)
    return logger

🛠️ Wait Utility: utils/wait_helper.py

from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

def wait_for_element(driver, locator, timeout=10):
    return WebDriverWait(driver, timeout).until(EC.presence_of_element_located(locator))

🛠️ Config Reader: utils/config_reader.py

config.yaml

browser: chrome
url: https://example.com

config_reader.py

import yaml

def read_config():
    with open("config/config.yaml") as f:
        return yaml.safe_load(f)

🚀 5.6 Fixtures and conftest.py

🧠 What Are Fixtures?

Fixtures in Pytest are reusable components that run before or after a test. Common use cases:

Set up and tear down the browser

Initialize configurations

Database connections or mocks

✅ Example: conftest.py

import pytest
from selenium import webdriver
from webdriver_manager.chrome import ChromeDriverManager
from utils.config_reader import read_config

@pytest.fixture
def driver():
    config = read_config()
    options = webdriver.ChromeOptions()
    driver = webdriver.Chrome(ChromeDriverManager().install(), options=options)
    driver.get(config['url'])
    driver.maximize_window()
    yield driver
    driver.quit()

🚀 Advanced Fixture Usage

scope: Defines how often fixture is invoked (function, class, module, session)

autouse: Automatically apply a fixture to every test

yield: Used for teardown logic after test completes

conftest.py: Common file where shared fixtures live, auto-detected by pytest

@pytest.fixture(scope="session", autouse=True)
def setup_logging():
    print("Setting up logs once per session")

📸 Screenshot on Failure (Advanced Hook)

@pytest.hookimpl(hookwrapper=True)
def pytest_runtest_makereport(item, call):
    outcome = yield
    rep = outcome.get_result()
    if rep.when == "call" and rep.failed:
        driver = item.funcargs['driver']
        driver.save_screenshot(f"reports/{item.name}.png")

🚀 5.7 How to Run This Framework

✅ Step 1: Set Up Environment

python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows

✅ Step 2: Install Requirements

# requirements.txt
selenium
pytest
webdriver-manager
pytest-html
pyyaml

pip install -r requirements.txt

✅ Step 3: Prepare Config

# config/config.yaml
browser: chrome
url: https://example.com

✅ Step 4: Create Test Case

import pytest
from pages.login_page import LoginPage
from utils.read_json import get_users

@pytest.mark.parametrize("user", get_users())
def test_login(driver, user):
    login = LoginPage(driver)
    login.login(user['username'], user['password'])
    assert "Dashboard" in driver.title

✅ Step 5: Run the Tests

pytest
pytest --html=reports/report.html --self-contained-html

✅ Step 6: Optional Runner Script

# run_tests.py
import pytest
pytest.main(["--html=reports/report.html", "--self-contained-html"])

python run_tests.py



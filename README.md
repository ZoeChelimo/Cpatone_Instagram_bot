# Twitter Automation Script

This script automates the process of logging into Twitter, searching for tweets with a specific hashtag, and liking those tweets. It is built using Selenium for web automation.

## Prerequisites

Before running the script, ensure you have the following installed:

- Python 3.x
- pip (Python package installer)
- Chrome browser
- ChromeDriver (compatible with your Chrome browser version)

## Installation

1. **Clone the repository** (if you have this in a repository):
    ```bash
    git clone https://github.com/yourusername/twitter-automation.git
    cd twitter-automation
    ```

2. **Install the required Python packages**:
    ```bash
    pip install selenium
    ```

3. **Download ChromeDriver**:
    - Download ChromeDriver from [here](https://sites.google.com/a/chromium.org/chromedriver/downloads).
    - Ensure it matches your Chrome version.
    - Place the `chromedriver` executable in a directory that is in your system's `PATH`, or specify its path in the script.

## Configuration

Update the script with your Twitter login credentials and the hashtag you want to search for:

```python
# Set up credentials
username = "your_email"
password = "your_password"

# Set up hashtag
hashtag = "python"

Usage

	1.	Run the script:

python twitter_automation.py


	2.	Manual Interaction:
	•	When prompted, enter the 2FA confirmation code in the browser if needed.
	3.	Script Resumes:
	•	The script will continue to search for tweets with the specified hashtag and like them.

Important Notes

	•	Security: Do not hard-code sensitive information such as passwords and API keys directly in your script. Consider using environment variables or a secure vault.
	•	CAPTCHA Handling: This script does not handle CAPTCHAs. You may need to solve them manually if prompted.
	•	Rate Limits: Be aware of Twitter’s rate limits and terms of service. Excessive automated actions can result in your account being flagged or suspended.
	•	Debugging: For debugging purposes, you can remove the --headless argument to see the browser actions.

Example Script

import time
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.keys import Keys

# Set up credentials
username = "your_email"
password = "your_password"

# Set up hashtag
hashtag = "python"

# Set up Chrome driver
options = webdriver.ChromeOptions()
options.add_argument("--disable-notifications")
driver = webdriver.Chrome(options=options)

try:
    # Navigate to Twitter login page
    driver.get("https://twitter.com/login")
    print("Navigated to Twitter login page")

    # Wait for username input field to be available and enter username
    username_input = WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.NAME, "text"))
    )
    username_input.send_keys(username)
    username_input.send_keys(Keys.RETURN)
    print("Entered username")

    # Wait for password input field to be available and enter password
    password_input = WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.NAME, "password"))
    )
    password_input.send_keys(password)
    password_input.send_keys(Keys.RETURN)
    print("Entered password")

    # Wait for 2FA confirmation code input field to be available (if present)
    try:
        confirmation_input = WebDriverWait(driver, 20).until(
            EC.presence_of_element_located((By.NAME, "text"))
        )
        print("Please enter the confirmation code manually in the browser.")
        
        # Wait for the user to input the confirmation code manually
        while not confirmation_input.get_attribute('value'):
            time.sleep(1)
        print("Confirmation code entered manually")
        
        confirmation_input.send_keys(Keys.RETURN)
    except Exception as e:
        print("No confirmation code needed or error occurred:", e)

    # Wait for the home page to load
    WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.XPATH, '//div[@aria-label="Home timeline"]'))
    )
    print("Logged into Twitter")

    # Search for the hashtag
    search_input = WebDriverWait(driver, 20).until(
        EC.presence_of_element_located((By.XPATH, '//input[@aria-label="Search query"]'))
    )
    search_input.send_keys(f'#{hashtag}')
    search_input.send_keys(Keys.RETURN)
    print(f"Searched for #{hashtag}")

    # Switch to the 'Latest' tab
    latest_tab = WebDriverWait(driver, 20).until(
        EC.element_to_be_clickable((By.LINK_TEXT, 'Latest'))
    )
    latest_tab.click()
    print("Switched to 'Latest' tab")

    # Wait for search results to load
    time.sleep(5)

    # Like tweets
    tweets = driver.find_elements_by_xpath('//div[@data-testid="like"]')
    for tweet in tweets[:10]:  # Like the first 10 tweets
        try:
            tweet.click()
            print("Liked a tweet")
            time.sleep(2)  # Wait a bit to avoid being flagged as a bot
        except Exception as e:
            print(f"Error liking tweet: {e}")

finally:
    # Close the driver
    driver.quit()
    print("Closed the driver")

Disclaimer

This script is for educational purposes only. Use it responsibly and in compliance with Twitter’s terms of service. The author is not responsible for any misuse of this script.



## Solving reCAPTCHA with Python, Selenium, and CapSolver

CAPTCHA challenges are essential for securing online activities, whether it's logging in to accounts, submitting forms, or making online payments. However, they often become roadblocks to automation, hindering processes like automated testing, data collection, and efficiency. In this guide, we’ll explore how to solve reCAPTCHA using Python and Selenium, providing developers and data scientists with practical solutions to overcome these challenges.

### What is reCAPTCHA?

reCAPTCHA, a security service developed by Google, protects websites from spam and abuse by distinguishing human users from bots. It’s commonly used in form submissions, account creations, and login attempts. Various versions of reCAPTCHA exist, each with different levels of interaction and complexity:

1. **reCAPTCHA v2 (Checkbox)**: Presents a checkbox labeled "I’m not a robot." Users might be prompted with an image-based challenge if the system detects suspicious activity.
2. **reCAPTCHA v2 (Invisible)**: Runs in the background and triggers challenges only if suspicious activity is detected.
3. **reCAPTCHA v3**: Assigns a score based on user behavior, allowing site admins to decide on the necessary action without interrupting the user.
4. **reCAPTCHA Enterprise**: A more advanced version designed for large-scale businesses, offering enhanced security and customizability.

### Why Solve reCAPTCHA?

In certain legitimate scenarios, solving reCAPTCHA becomes necessary:
- **Automated Testing**: Facilitates automated testing of web applications.
- **Data Scraping**: Essential for tasks on sites where you have permission.
- **Accessibility**: Helps automate repetitive tasks or provide alternative access for users with disabilities.
- **Efficiency**: Automating interactions on websites that use reCAPTCHA can significantly improve productivity.

### Solving reCAPTCHA with CapSolver

To solve reCAPTCHA challenges, you can use [CapSolver](https://www.capsolver.com/?utm_source=github&utm_medium=repo&utm_campaign=recaptchapython), a third-party service that simplifies CAPTCHA-solving, ensuring smooth automation.

#### Prerequisites

1. **Identify the Target Site**: Look for reCAPTCHA indicators on the page or in the request logs (e.g., `https://www.google.com/recaptcha****`).
2. **Obtain the Site Key**: In the browser request logs, search for `/recaptcha/api2/reload?k=YOUR_SITE_KEY`.
3. **Differentiate Between V2 and V3**: V2 usually involves image recognition, while V3 operates with behavior-based scoring. The handling methods differ accordingly.

#### CapSolver API Integration

Below are examples of how to solve reCAPTCHA V2 and V3 using Python with CapSolver:

```python
# pip install requests
import requests
import time

# Configuration
api_key = "YOUR_API_KEY"
site_key = "YOUR_SITE_KEY"
site_url = "YOUR_TARGET_URL"

def capsolver():
    payload = {
        "clientKey": api_key,
        "task": {
            "type": 'ReCaptchaV2TaskProxyLess',  # For V2
            "websiteKey": site_key,
            "websiteURL": site_url
        }
    }
    res = requests.post("https://api.capsolver.com/createTask", json=payload)
    task_id = res.json().get("taskId")
    if not task_id:
        print("Failed to create task:", res.text)
        return
    print(f"Task created: {task_id}, waiting for solution...")

    while True:
        time.sleep(3)
        res = requests.post("https://api.capsolver.com/getTaskResult", json={"clientKey": api_key, "taskId": task_id})
        if res.json().get("status") == "ready":
            return res.json().get('solution', {}).get('gRecaptchaResponse')
        elif res.json().get("status") == "failed":
            print("Task failed:", res.text)
            return

token = capsolver()
print(token)
```

### Additional Setup for Your Project

Three dependencies are required to run this script. You can install them using the following command:

```bash
pip install -r requirements.txt
```

You also need to install `ffmpeg`:

```bash
sudo apt-get install ffmpeg
```

#### Usage Example

To implement the CAPTCHA-solving functionality in your project, follow the approach below:

```python
from DrissionPage import ChromiumPage 
from RecaptchaSolver import RecaptchaSolver

driver = ChromiumPage()
recaptchaSolver = RecaptchaSolver(driver)
driver.get("https://www.google.com/recaptcha/api2/demo")
recaptchaSolver.solveCaptcha()
```

For a complete demonstration, check out the `test.py` file included in the repository, which shows the script in action.

### Conclusion

Solving reCAPTCHA using Python, Selenium, and services like CapSolver offers an effective solution for legitimate automation tasks. By understanding different reCAPTCHA types, utilizing CapSolver, and following the provided scripts, you can streamline your automation processes while maintaining compliance with legal and ethical standards.

Always ensure you use these techniques responsibly, adhering to website terms of service.

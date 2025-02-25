# PNM Portal API Analysis: A Case Study

This section demonstrates how to analyze the API used by the PNM (Pamantasan ng Montalban / Colegio de Montalban) student portal. We will cover the login process, data retrieval, and document generation.


!!! warning "Important Note"
    This example is for educational purposes only. Accessing or using this API without authorization is illegal and unethical. Respect website terms of service and avoid any actions that could harm the system or violate student privacy.

!!! warning "Important Note"
    The information provided here is based on publicly accessible data and observations of network traffic. It should not be used to access or manipulate student data without explicit permission.

## 1. Understanding the Login Process

The login process involves the following steps:

1.  **Submitting Credentials:** The user submits their student ID, email address, and password to the `https://portal.pnm.edu.ph/v2/login.php` endpoint using a POST request.
2.  **Accessing the Dashboard:** Upon successful authentication (as determined by cookies), the user is redirected to the main dashboard at `https://portal.pnm.edu.ph/index`.  The intermediate redirect to `auth-redirect` can be skipped.

**Replicating the Login with Python:**

```python
import requests
from bs4 import BeautifulSoup

def login_to_pnm(student_id, email, password):
    """Logs into the PNM portal and returns the session object if successful,
    otherwise returns None.
    """
    login_url = "https://portal.pnm.edu.ph/v2/login.php"
    headers = {
        "accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
        "accept-language": "en-US,en;q=0.9",
        "cache-control": "max-age=0",
        "content-type": "application/x-www-form-urlencoded",
        "dnt": "1",
        "origin": "https://portal.pnm.edu.ph",
        "priority": "u=0, i",
        "referer": "https://portal.pnm.edu.ph/v2/login",
        "sec-ch-ua": '"Not(A:Brand";v="99", "Google Chrome";v="133", "Chromium";v="133"',
        "sec-ch-ua-mobile": "?0",
        "sec-ch-ua-platform": '"Linux"',
        "sec-fetch-dest": "document",
        "sec-fetch-mode": "navigate",
        "sec-fetch-site": "same-origin",
        "sec-fetch-user": "?1",
        "upgrade-insecure-requests": "1",
        "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
    }
    data = {
        "studentid": student_id,
        "emailaddress": email,
        "password": password,
        "login": ""
    }

    session = requests.Session()  # Use a session to persist cookies
    response = session.post(login_url, headers=headers, data=data, allow_redirects=False)

    # Check for successful login by examining the response from the index page
    index_url = "https://portal.pnm.edu.ph/index"
    index_response = session.get(index_url, headers=headers) # Get the index page
    if index_response.status_code == 200:
        soup = BeautifulSoup(index_response.text, 'html.parser')
        welcome_message = soup.find("div", class_="sidenav-footer-title") # Look for the welcome msg on the index page

        if welcome_message:
            print("Login Successful!")
            return session
        else:
            print("Login Failed: Could not find welcome message on the index page.")
            print("HTML Content of Index Response:\n", index_response.text) # Print for debug
            return None
    else:
        print("Login Failed: Could not retrieve index page (status code:", index_response.status_code, ")")
        return None

# Example usage (replace with valid credentials):
session = login_to_pnm("YOUR_STUDENT_ID", "YOUR_EMAIL", "YOUR_PASSWORD")

if session:
    # Now you can use the session to access other API endpoints
    pass  # Proceed to the dashboard and other data retrieval
```

**Explanation:**

*   `login_to_pnm()` handles login with student ID, email, and password.
*   Sets headers/data for the POST request to the login URL.
*   `requests.Session()` is used to store login cookies.
*   Code retrieves the `/index` page and checks for a welcome message to confirm login success. Returns the session if successful, otherwise `None`.
*   Illustrates basic login. More complex systems may use CSRF tokens or CAPTCHAs.


## 2. Retrieving Enrolled Courses

Once logged in, you can retrieve the enrolled courses by sending a POST request to `https://portal.pnm.edu.ph/getEnrolledCourses.php` with the student ID. The server returns a JSON response containing the course information.

**Python Code:**

```python
if session:  # Assuming you have a valid session from the login
    courses_url = "https://portal.pnm.edu.ph/getEnrolledCourses.php"
    courses_headers = {
        "accept": "application/json, text/javascript, */*; q=0.01",
        "accept-language": "en-US,en;q=0.9",
        "content-type": "application/x-www-form-urlencoded; charset=UTF-8",
        "dnt": "1",
        "origin": "https://portal.pnm.edu.ph",
        "priority": "u=1, i",
        "referer": "https://portal.pnm.edu.ph/enrollment3",
        "sec-ch-ua": '"Not(A:Brand";v="99", "Google Chrome";v="133", "Chromium";v="133"',
        "sec-ch-ua-mobile": "?0",
        "sec-ch-ua-platform": '"Linux"',
        "sec-fetch-dest": "empty",
        "sec-fetch-mode": "cors",
        "sec-fetch-site": "same-origin",
        "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36",
        "x-requested-with": "XMLHttpRequest"
    }
    courses_data = {
        "student_id": "YOUR_STUDENT_ID"  # Replace with the actual student ID
    }

    courses_response = session.post(courses_url, headers=courses_headers, data=courses_data)

    if courses_response.status_code == 200:
        courses_json = courses_response.json()
        print("Enrolled Courses:", courses_json)
    else:
        print("Failed to retrieve enrolled courses.")
```

**Explanation:**

*   This code assumes you have a valid `session` object from the login function.
*   It constructs the headers and data for the `getEnrolledCourses.php` POST request.
*   It uses `session.post()` to send the request, ensuring that the cookies are included.
*   It parses the JSON response and prints the course information.

## 3. Downloading Documents (OVRF, COE, SOG)

The API provides endpoints for generating and downloading documents such as the OVRF, Certificate of Enrollment (COE), and Summary of Grades (SOG). These endpoints typically return PDF files.

*   **Generating OVRF:** POST request to `https://portal.pnm.edu.ph/generateOVRF.php` with `student_id`.
*   **Generating COE:** POST request to `https://portal.pnm.edu.ph/generateCOE.php` with `student_id` and `purpose`. The `purpose` parameter accepts values like "Scholarship Applications", "Internship Requirements", etc.
*   **Generating SOG:** POST request to `https://portal.pnm.edu.ph/generateSOG.php` with `student_id` and `purpose`. The `purpose` parameter is similar to the COE endpoint.

**Python Code (Example for COE):**

```python
if session:  # Assuming a valid session
    coe_url = "https://portal.pnm.edu.ph/generateCOE.php"
    coe_headers = {
        "accept": "*/*",
        "accept-language": "en-US,en;q=0.9",
        "content-type": "application/x-www-form-urlencoded",
        "dnt": "1",
        "origin": "https://portal.pnm.edu.ph",
        "priority": "u=1, i",
        "referer": "https://portal.pnm.edu.ph/enrollment3",
        "sec-ch-ua": '"Not(A:Brand";v="99", "Google Chrome";v="133", "Chromium";v="133"',
        "sec-ch-ua-mobile": "?0",
        "sec-ch-ua-platform": '"Linux"',
        "sec-fetch-dest": "empty",
        "sec-fetch-mode": "cors",
        "sec-fetch-site": "same-origin",
        "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
    }
    coe_data = {
        "student_id": "YOUR_STUDENT_ID",  # Replace
        "purpose": "Scholarship Applications"  # Or other valid purpose
    }

    coe_response = session.post(coe_url, headers=coe_headers, data=coe_data)

    if coe_response.status_code == 200:
        with open("certificate_of_enrollment.pdf", "wb") as f:
            f.write(coe_response.content)
        print("COE downloaded successfully as certificate_of_enrollment.pdf")
    else:
        print("Failed to download COE.")
```

**Python Code (Example for OVRF):**

```python
if session:  # Assuming a valid session
    ovrf_url = "https://portal.pnm.edu.ph/generateOVRF.php"
    ovrf_headers = {
        "accept": "*/*",
        "accept-language": "en-US,en;q=0.9",
        "content-type": "application/x-www-form-urlencoded",
        "dnt": "1",
        "origin": "https://portal.pnm.edu.ph",
        "priority": "u=1, i",
        "referer": "https://portal.pnm.edu.ph/enrollment3",
        "sec-ch-ua": '"Not(A:Brand";v="99", "Google Chrome";v="133", "Chromium";v="133"',
        "sec-ch-ua-mobile": "?0",
        "sec-ch-ua-platform": '"Linux"',
        "sec-fetch-dest": "empty",
        "sec-fetch-mode": "cors",
        "sec-fetch-site": "same-origin",
        "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36"
    }
    ovrf_data = {
        "student_id": "YOUR_STUDENT_ID"  # Replace
    }

    ovrf_response = session.post(ovrf_url, headers=ovrf_headers, data=ovrf_data)

    if ovrf_response.status_code == 200:
        with open("ovrf.pdf", "wb") as f:
            f.write(ovrf_response.content)
        print("OVRF downloaded successfully as ovrf.pdf")
    else:
        print("Failed to download OVRF.")
```

**Explanation:**

*   This code shows how to download the COE.  The process is similar for OVRF and SOG.
*   It sets the appropriate headers and data for the POST request.
*   It uses `session.post()` to send the request.
*   If the request is successful, it saves the PDF content to a file.

## 4. Retrieving Grades

Grades can be retrieved via a POST request to `https://portal.pnm.edu.ph/test2.php` with `student_id`. The response is a JSON array of objects, each containing subject code, description, and grade.

**Python Code:**

```python
if session:
    grades_url = "https://portal.pnm.edu.ph/test2.php"
    grades_headers = {
        "accept": "application/json, text/javascript, */*; q=0.01",
        "accept-language": "en-US,en;q=0.9",
        "content-type": "application/x-www-form-urlencoded; charset=UTF-8",
        "dnt": "1",
        "origin": "https://portal.pnm.edu.ph",
        "priority": "u=1, i",
        "referer": "https://portal.pnm.edu.ph/grades",
        "sec-ch-ua": '"Not(A:Brand";v="99", "Google Chrome";v="133", "Chromium";v="133"',
        "sec-ch-ua-mobile": "?0",
        "sec-ch-ua-platform": '"Linux"',
        "sec-fetch-dest": "empty",
        "sec-fetch-mode": "cors",
        "sec-fetch-site": "same-origin",
        "user-agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/133.0.0.0 Safari/537.36",
        "x-requested-with": "XMLHttpRequest"
    }
    grades_data = {
        "student_id": "YOUR_STUDENT_ID"
    }

    grades_response = session.post(grades_url, headers=grades_headers, data=grades_data)

    if grades_response.status_code == 200:
        grades_json = grades_response.json()
        print("Grades:", grades_json)
    else:
        print("Failed to retrieve grades.")
```

## 5. Other Endpoints and Data

*   **Evaluated Instructors:**  `https://portal.pnm.edu.ph/fetchEval.php?student_id=...`. This endpoint requires no authentication and returns a list of instructors to be evaluated.
*   **E-books:**  `https://portal.pnm.edu.ph/getEbooks.php`. This endpoint requires no authentication and returns a list of available e-books.

## 6. Important Notes

*   **Session Management:** The `PHPSESSID`, `session_iv`, and `session_id` cookies are crucial for maintaining the session. The `requests.Session()` object automatically handles these cookies.
*   **Headers:** Pay close attention to the headers, especially `Content-Type`, `Referer`, and `User-Agent`.
*   **Authentication:** The code assumes you have valid credentials. Gaining access to credentials without authorization is illegal.
*   **Rate Limiting:** Be careful not to overload the server with too many requests. Implement delays if necessary.
*   **Legal and Ethical Considerations:** Unauthorized access to student data is a serious offense. Only perform these actions with explicit permission and for legitimate educational purposes.
*   **Cloudflare:** The presence of `cf_clearance` cookie suggests Cloudflare protection. This may require additional handling to bypass.

## 7. Identifying Logged-In State

Several indicators can determine if a user is logged in:

*   The presence of specific cookies (`PHPSESSID`, `session_iv`, `session_id`).
*   Redirection to the dashboard (`https://portal.pnm.edu.ph/index`).
*   The presence of the welcome message and student name in the dashboard HTML.
*   The existence of menu items such as "Enrolled Courses," "Grades," and "Evaluation."

## Disclaimer

This document is intended for educational purposes only and should not be used for any illegal or unethical activities. Accessing or manipulating student data without authorization is strictly prohibited. Always respect website terms of service and avoid any actions that could harm the system or violate student privacy. The author assumes no responsibility for any misuse of the information provided herein.

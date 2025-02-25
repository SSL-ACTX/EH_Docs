# API Analysis Example: Replicating an Anilist Profile Data Request

This example demonstrates how to use the browser developer tools to analyze API requests and then replicate them using Python. We'll use the Anilist API as an example to fetch profile data. **Important:** This example is for educational purposes onlyapi-analysis-example.md

!!! warning "Warning"
    Respect API rate limits and terms of service. Do not use this information for malicious purposes.

### Step 1: Inspecting the Network Traffic

1.  Open the Anilist User Profile: Go to an Anilist user profile page (e.g., `https://anilist.co/user/random/`) in your browser.
2.  Open Developer Tools (Network Tab): Open the developer tools and navigate to the Network tab.
3.  Reload the Page: Reload the page to capture all network requests.
4.  Filter by `graphql`:** In the filter box of the Network tab, type `graphql` to isolate the API request responsible for fetching the user's data. Look for a request to `https://graphql.anilist.co`.
5.  Examine Request Details: Click on the `graphql` request to inspect its details.
    *   Headers Tab: View the request headers. Pay attention to headers like `Content-Type`, `Origin`, `User-Agent`, and any cookies being sent.
    *   Payload Tab (or Request Body): See the data being sent to the API. This is often in JSON format and contains the GraphQL query and variables.
    *   Response Tab: View the response from the API. This will be a JSON object containing the user's profile data.

### Step 2: Identifying the CSRF Token

Many web applications use CSRF (Cross-Site Request Forgery) tokens to protect against malicious attacks. These tokens are typically embedded in the HTML page and must be included in any POST requests to the server. In this Anilist example, we will identify and extract the CSRF token for API authentication.

1.  Examine the HTML Source: Go to the Elements tab and inspect the HTML source code of the Anilist user profile page (`https://anilist.co/user/random/`).
2.  Search for `al_token`:** Use the search function (`Ctrl + F` or `Cmd + F`) to find `window.al_token`. You should find a JavaScript variable that assigns a CSRF token: `window.al_token = "YOUR_CSRF_TOKEN_HERE";`
3.  Note the Cookie Value: Inspect the `Cookie` request header in the Network Tab. Note the value of the `laravel_session` cookie.

### Step 3: Replicating the API Request with Python

Now that we've analyzed the API request and identified the CSRF token and the cookie value, we can replicate the request using Python and the `requests` library.

```python
import requests
import re

# Function to fetch CSRF token from the user page
def get_csrf_token():
    url = "https://anilist.co/user/random/"
    headers = {
        "Cookie": "laravel_session=YOUR_COOKIE_VALUE",  # Replace with your cookie
        "User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36"
    }

    response = requests.get(url, headers=headers)
    match = re.search(r'window\.al_token = "([^"]+)"', response.text)
    return match.group(1) if match else None

# Function to fetch account information using GraphQL query
def fetch_account_info(csrf_token):
    headers = {
        "Cookie": "laravel_session=YOUR_COOKIE_VALUE",  # Replace with your cookie
        "X-Csrf-Token": csrf_token,
        "User-Agent": "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36",
        "Dnt": "1",
        "Content-Type": "application/json",
        "Origin": "https://anilist.co"
    }

    data = {
        "query": "query($id:Int,$name:String){User(id:$id,name:$name){id name previousNames{name updatedAt}avatar{large}bannerImage about isFollowing isFollower donatorTier donatorBadge createdAt moderatorRoles isBlocked bans options{profileColor restrictMessagesToFollowing}mediaListOptions{scoreFormat}statistics{anime{count meanScore standardDeviation minutesWatched episodesWatched genrePreview:genres(limit:10,sort:COUNT_DESC){genre count}}manga{count meanScore standardDeviation chaptersRead volumesRead genrePreview:genres(limit:10,sort:COUNT_DESC){genre count}}}stats{activityHistory{date amount level}}favourites{anime{edges{favouriteOrder node{id type status(version:2)format isAdult bannerImage title{userPreferred}coverImage{large}startDate{year}}}}manga{edges{favouriteOrder node{id type status(version:2)format isAdult bannerImage title{userPreferred}coverImage{large}startDate{year}}}}characters{edges{favouriteOrder node{id name{userPreferred}image{large}}}}staff{edges{favouriteOrder node{id name{userPreferred}image{large}}}}studios{edges{favouriteOrder node{id name}}}}}}",
        "variables": {"name": "random"}
    }

    return requests.post("https://graphql.anilist.co", headers=headers, json=data).json()

# Main function to drive the script
def main():
    csrf_token = get_csrf_token()
    if csrf_token:
        print("CSRF Token:", csrf_token)
        account_info = fetch_account_info(csrf_token)
        print(account_info)
    else:
        print("CSRF Token not found.")

if __name__ == "__main__":
    main()
```

### Step 4: Running the Python Script

1.  Install the `requests` Library:

    ```bash
    pip install requests
    ```

2.  Run the Script: Execute the Python script.

    ```bash
    python your_script_name.py
    ```

The script will print the CSRF token and the Anilist profile data to the console.

### Disclaimer

This section is intended for educational purposes only. Replicating API requests without authorization may violate the terms of service of the target website or API. Always obtain explicit permission before testing or interacting with APIs. This example is intended to demonstrate how browser developer tools can be used to analyze API requests and how those requests can be replicated using Python.

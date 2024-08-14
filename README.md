# 🚨 Infringement URL Analyzer
This repository contains a Python script designed to flatten nested data structures, extract and analyze infringing URLs, and generate insightful summaries. The project utilizes parallel processing to efficiently handle large datasets, making it suitable for high-performance environments.

## ✨ Features
1. 🗂️ Data Flattening: Automatically flattens nested JSON or similar data structures.
2. 🔗 Infringing URL Extraction: Extracts infringing URLs and processes each URL individually.
3. 🌐 Domain and IP Address Identification: Retrieves the domain and corresponding IP address for each infringing URL.
4. ⚡ Parallel Processing: Leverages multiple CPUs to accelerate domain and IP address extraction.
5. 📊 Data Summarization: Generates key insights from the data, including the most frequently infringed domains and their associated IP addresses.

## 📥 Input:
Nested data structure containing multiple infringing URLs.

## 📤 Output:
A flattened CSV file where each row corresponds to an infringing URL, along with its domain and IP address.

## Sample Summaries📈 :
- Top 10 domains with the most infringements.
- Top 10 IP addresses hosting infringing content.
- Summary table showing the number of infringements per domain.
## Performance Considerations 🚀 
The script is designed to run efficiently on systems with multiple CPUs. It leverages Python's multiprocessing library to parallelize the extraction of domains and IP addresses. The default configuration uses 4 CPUs, but this can be adjusted to fit the capabilities of your system.

## Code Overview

The analysis script includes the following key components:

1. **Data Flattening** (Python): 

    ```python
    df = json_normalize(data['notices'], record_path=['works', 'infringing_urls'], 
                        meta=['id', 'type', 'title', 'date_sent', 'date_received', 
                              ['works', 'description']], 
                        record_prefix='infringing_')
    # Rename the columns for clarity
    df = df.rename(columns={
        'infringing_url': 'infringing URL',
        'works.description': 'work_description'
    })

    # Saving the flattened data to CSV
    df.to_csv('flattened_json_data.csv', index=False)
    ```
Note: Even R, SQL can also be used for flattening JSON but for simplicity purposes i have used python.

2. **Domain and IP Address Extraction** (R/Python):

    ```python
    def extract_domain(url):
        try:
            return urlparse(url).netloc
        except Exception as e:
            return None

    def get_ip(domain):
        try:
            return socket.gethostbyname(domain)
        except socket.gaierror:
            return None
    ```

    
    ```R
    # Function to extract domain from a URL
    extract_domain <- function(url) {
      parsed <- parse_url(url)
      return(parsed$hostname)
    }

    # Function to extract domain from a URL
    extract_domain <- function(url) {
      parsed <- parse_url(url)
      # Return hostname or NA if hostname is missing
      if (is.null(parsed$hostname)) {
        return(NA)
      } else {
        return(parsed$hostname)
      }
    }

    # Function to get IP address for a domain
    get_ip <- function(domain) {
      ip <- tryCatch({
        ip <- resolve(domain)$ip_address
      }, error = function(e) {
        return(NA)
      })
      return(ip)
    }

    
    # Extract domain and IP address
    df <- df %>%
      mutate(Domain = future_map_chr(infringing.URL, extract_domain, .progress = TRUE),
             IP_Address = future_map_chr(Domain, get_ip, .progress = TRUE))

    ```
3. **Parallel Processing**:

    ```python
    with ThreadPoolExecutor(max_workers=4) as executor:
        df['IP address'] = list(executor.map(get_ip, df['domain']))
    ```
    ```R
    # Parallel processing setup
    plan(multicore, workers = 4)
    ```
## Data Summarization & Analysis:

The analysis includes generating the following summarizations:

1. **Count of Infringing URLs per Domain**:

    <img width="1183" alt="Screenshot 2024-08-14 at 9 44 45 AM" src="https://github.com/user-attachments/assets/86fac7b4-5221-46f8-ab65-e440af4ad1b2">
2. **Top 10 most frequent infringing URLs**

    The below are most used urls for pirated content mostly are from **google drives**
                                          
    https://drive.google.com/file/d/1tqRi2DiMjmeb_1KidMMcglWNEiJI4GIt     2
    https://drive.google.com/file/d/1TJekGwVHJxoSSbgPzf3WV53DFx4yic2Y     2
    https://drive.google.com/file/d/1tibAet-OUlazYuUOh0tFOtQfQr3X-7W8     2
    https://drive.google.com/file/d/1SIr1jOKgD26TBRo7uOnCXHRTVJ6l8jG0     2
    https://drive.google.com/file/d/1rv-89CQwtAv5nGT3tu9ZGYV48cknkvyo     2
    https://drive.google.com/file/d/1RGbzMLTQw4zgy7YPjt15Qi8Seh7NSEHY     2
    https://drive.google.com/file/d/1qMqeDOW5dGz1wnXZybp_f8Nf3u1hbtsf     2
    https://drive.google.com/file/d/1qL3zla1W-PWkQEr0G7oS6ca3L1xF5i1W     2
    https://drive.google.com/file/d/1QGMMbqy63N8KBdskK9DP-YIR2E8_9VsJ     2
    

    **Caution:** Links are clickable but most of them doesn't display content. Proceed with care!
   





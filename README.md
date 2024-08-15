# Infringement URL Analysis 🚨
This repository contains a Python script designed to flatten nested data structures, extract and analyze infringing URLs, and generate insightful summaries. The project utilizes parallel processing to efficiently handle large datasets, making it suitable for high-performance environments.

## Workflow 🛠️
![workflow](https://github.com/user-attachments/assets/8593f3be-a99a-4488-acde-44dcedd17319)


## ✨ Features
1. 🗂️ Data Flattening: Automatically flattens nested JSON or similar data structures.
2. 🔗 Infringing URL Extraction: Extracts infringing URLs and processes each URL individually.
3. 🌐 Domain and IP Address Identification: Retrieves the domain and corresponding IP address for each infringing URL.
4. ⚡ Parallel Processing: Leverages multiple CPUs to accelerate domain and IP address extraction.
5. 📊 Data Summarization: Generates key insights from the data, including the most frequently infringed domains and their associated IP addresses.

## 📥 Source:
Nested data structure containing multiple infringing URLs, which is response.json file (Given by professor)

## 📤 Output:
A flattened CSV file where each row corresponds to an infringing URL, along with its domain and IP address.

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
    (R code)
    ```R
        # Flatten the 'notices' list into a data frame
    notices_df <- data$notices

        # Unnest the 'works' column
        notices_df <- notices_df %>%
          mutate(works = map(works, ~ as.data.frame(.))) %>%
          unnest(works)
    
        # Unnest the 'infringing_urls' within 'works'
        notices_df <- notices_df %>%
          mutate(infringing_urls = map(infringing_urls, ~ as.data.frame(.))) %>%
          unnest(infringing_urls)

    ```
Note: Even SQL can also be used for flattening JSON.

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
      domain(url)
    }

    # Function to get IP address for a domain using nslookup
    get_ip <- function(domain) {
      tryCatch({
        result <- system(paste("nslookup", domain), intern = TRUE)
        ip_lines <- grep("Address:", result, value = TRUE)
        if (length(ip_lines) > 1) {
          ip <- sub("Address: ", "", ip_lines[2])  # Take the second Address if multiple
        } else if (length(ip_lines) == 1) {
          ip <- sub("Address: ", "", ip_lines[1])
        } else {
          ip <- NA
        }
        return(ip)
      }, error = function(e) {
        return(NA)
      })
    }
    
    # Extract domain from infringing URLs
    df <- df %>%
      mutate(domain = sapply(`infringing.URL`, extract_domain))
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

Handling Missing Values:
![missing-values](https://github.com/user-attachments/assets/be305bf3-884d-4c3c-8786-7b324cb60ff8)

ipaddresses and domain have missing values- Approach used is given below:

- Missing values for domain are removed - since, the count of values are small usually 19 which doesn't effect large datasets.
- Missing values for ipaddress are imputed with Not Available since count is large (3320 large missing values) 



The analysis includes generating the following summarizations:

1. **Count of Infringing URLs per Domain**:

    <img width="1183" alt="Screenshot 2024-08-14 at 9 44 45 AM" src="https://github.com/user-attachments/assets/86fac7b4-5221-46f8-ab65-e440af4ad1b2">
    
    This graph shows the distribution of infringing URLs across different domains. It helps identify which domains are most frequently used for hosting infringing content.
    
2. **Top 10 most frequent infringing URLs**

    The below are most used urls for pirated content mostly are from **google drives**
                                          
    - https://drive.google.com/file/d/1tqRi2DiMjmeb_1KidMMcglWNEiJI4GIt     2
    - https://drive.google.com/file/d/1TJekGwVHJxoSSbgPzf3WV53DFx4yic2Y     2
    - https://drive.google.com/file/d/1tibAet-OUlazYuUOh0tFOtQfQr3X-7W8     2
    - https://drive.google.com/file/d/1SIr1jOKgD26TBRo7uOnCXHRTVJ6l8jG0     2
    - https://drive.google.com/file/d/1rv-89CQwtAv5nGT3tu9ZGYV48cknkvyo     2
    - https://drive.google.com/file/d/1RGbzMLTQw4zgy7YPjt15Qi8Seh7NSEHY     2
    - https://drive.google.com/file/d/1qMqeDOW5dGz1wnXZybp_f8Nf3u1hbtsf     2
    - https://drive.google.com/file/d/1qL3zla1W-PWkQEr0G7oS6ca3L1xF5i1W     2
    - https://drive.google.com/file/d/1QGMMbqy63N8KBdskK9DP-YIR2E8_9VsJ     2

   The analysis revealed that Google Drive links are among the most commonly used for sharing pirated content. Here are the top 10 most frequent infringing URLs (note: links have been deactivated for security) 

    **Caution:** Links are clickable but most of them doesn't display content. Proceed with care!


4. **Top 10 Most Frequent IP Addresses:**
    ![ipaddresses](https://github.com/user-attachments/assets/43e215df-89a4-4fdc-be33-68c5275edee0)
   This graph showcases the IP addresses most commonly associated with infringing content. It can help identify potential hotspots of piracy activity.

6. **Most pirated works**
   ![work_desc](https://github.com/user-attachments/assets/85ee143b-10bb-407c-9f33-2266d9b3ae07)
   This visualization presents the works that are most frequently pirated, based on the descriptions in the dataset. It provides insights into which types of content are most targeted by infringers.
   
8. **Fake Urls received per day**
   ![fake_url_days](https://github.com/user-attachments/assets/666507aa-ee99-4210-aece-f7f0e329921c)
   This time series graph shows the number of potentially fake or malicious URLs reported each day. It can help identify trends or spikes in fraudulent activity over time.

## 🤝 Contributing
Contributions, issues, and feature requests are welcome! Feel free to check issues page if you want to contribute.

## 📝 License
This project is MIT licensed.

   

   





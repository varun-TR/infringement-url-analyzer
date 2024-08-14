# Infringement URL Analyzer
This repository contains a Python script designed to flatten nested data structures, extract and analyze infringing URLs, and generate insightful summaries. The project utilizes parallel processing to efficiently handle large datasets, making it suitable for high-performance environments.

# Features
- Data Flattening: Automatically flattens nested JSON or similar data structures.
- Infringing URL Extraction: Extracts infringing URLs and processes each URL individually.
- Domain and IP Address Identification: Retrieves the domain and corresponding IP address for each infringing URL.
- Parallel Processing: Leverages multiple CPUs to accelerate domain and IP address extraction.
- Data Summarization: Generates key insights from the data, including the most frequently infringed domains and their associated IP addresses.

# Input:
Nested data structure containing multiple infringing URLs.

# Output:
A flattened CSV file where each row corresponds to an infringing URL, along with its domain and IP address.

# Sample Summaries:
Top 10 domains with the most infringements.
Top 10 IP addresses hosting infringing content.
Summary table showing the number of infringements per domain.
Performance Considerations
The script is designed to run efficiently on systems with multiple CPUs. It leverages Python's multiprocessing library to parallelize the extraction of domains and IP addresses.
The default configuration uses 4 CPUs, but this can be adjusted to fit the capabilities of your system.

## Code Overview

The analysis script includes the following key components:

1. **Data Flattening**: 

    ```python
    df = json_normalize(data['notices'], record_path=['works', 'infringing_urls'], 
                        meta=['id', 'type', 'title', 'date_sent', 'date_received', 
                              ['works', 'description']], 
                        record_prefix='infringing_')
    ```


# learn-this-amazing-beginner-code-of-python-
The program provides the basic web scraping for beginners!


# Importing necessary libraries
import requests
from bs4 import BeautifulSoup
import csv

# Function to fetch and parse web page content
def fetch_webpage(url):
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.3'}
    try:
        response = requests.get(url, headers=headers)
        if response.status_code == 200:
            return BeautifulSoup(response.text, 'html.parser')
        elif response.status_code == 404:
            print(f"Page not found (404). Please check the URL: {url}")
        else:
            print(f"Failed to retrieve the webpage. Status code: {response.status_code}")
    except requests.exceptions.RequestException as e:
        print(f"Error fetching the webpage: {e}")
    return None

# Function to extract data from the parsed HTML
def extract_data(soup):
    data = []
    table = soup.find('table', {'class': 'wikitable'})  # Find the first table with class 'wikitable'
    if table:
        rows = table.find_all('tr')  # Find all table rows
        for row in rows:
            cells = row.find_all(['td', 'th'])  # Find all table data cells and header cells
            if len(cells) > 1:  # Ensure the row is not empty
                data.append([cell.text.strip() for cell in cells])  # Extract and clean the text from each cell
    return data

# Function to save the extracted data to a CSV file
def save_to_csv(data, filename):
    with open(filename, 'w', newline='', encoding='utf-8') as file:
        writer = csv.writer(file)
        writer.writerows(data)

# Main function to orchestrate the web scraping process
def main(url, filename):
    soup = fetch_webpage(url)
    if soup:
        data = extract_data(soup)
        save_to_csv(data, filename)
        print(f"Data successfully saved to {filename}")
    else:
        print("No data extracted")

# URL of the Wikipedia page to scrape
url = 'https://www.worldometers.info/geography/alphabetical-list-of-countries/'
# Filename to save the extracted data
filename = 'countries_population.csv'

# Run the main function
if __name__ == "__main__":
    main(url, filename)

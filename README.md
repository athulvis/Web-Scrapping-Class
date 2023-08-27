

# Data Scrapping using Python

## Prerequisites
- Python 3
    - Lists and dictionary
    - string manipulation
    - dataframes
    - loops and conditions
    - file handling
- Libraries used
    - Requests
    - BeautifulSoup
    - Pandas
    - [Optional] Re
    - [Optional] Matplotlib
- Basics of HTML

## Collecting data using API

If available, API requests are the bestway to collect data from websites. Most of the websites now provide paid as well as free API according to value of data.

### Tutorial 1 : Using API from [Open-Meteo](https://open-meteo.com) website

- To get temperature data, goto https://open-meteo.com/en/docs.
- Enter your city name or input latitude and longitude.
- Choose required parameters (here we choose temperature at 2 meter altitude only).
- You can also choose number of forecast days (by default it will be 7 days).
- [Optional] Now you may get the CSV or XML from their website.
- Copy the API Url provided below.
- Using the API, create a python GET request to the website and fetch the data in JSON format

> ``` python
> url = "https://api.open-meteo.com/v1/forecast"
>
> param = {"latitude": 8.4855, "longitude": 76.9492,           "hourly":"temperature_2m"}
> head = {"User-Agent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0"}
>
> data = requests.get(url, params=param, headers = head)
> output = data.json()
> ```

Now you can import this data to a dataframe and use as you wish.

## Webscraping
It is very essential to know the structure of a webpage.

Structure of our local html file: 
```
├── html
│   ├── head
│   │   ├── title
│   ├── body
│       ├── table
│           ├── row
│           |   ├── head
│           ├── row
│           |   ├── column
│           ├── row
│           |   ├── column
│           ├── row
│           |   ├── column
│           ├── row
│               ├── column

```
### Tutorial 2: Scraping a Local Webpage

*The following data is extracted from [UPSCSuccess](https://www.upscsuccess.com/rivers-india/) website.* 

- Go through `index.html` filw which contains the following table:

| Rivers      | Length (KM) | Origin                                                            | End                                                                 |
|-------------|-------------|-------------------------------------------------------------------|---------------------------------------------------------------------|
| Kaveri      | 765         | Talakaveri in the Western Ghats in Karnataka                      | Ends in the Bay of Bengal                                           |
| Krishna     | 1400        | Originates in the Western Ghats near Mahabaleshwar in Maharashtra | Ends in the Bay of Bengal near Andhra Pradesh                       |
| Godavari    | 1465        | Starts in Maharashtra and passes through 7 Indian states          | Empties in the Bay of Bengal                                        |
| Tungabhadra | 531         | Tributary of Krishna river staring at Karnataka                   | Joins Krishna river along the border of Telangana and Andhra Prades |

- open the file in Python

> ``` python
> with open('index.html','r') as f:
>   content = f.read()
> ```

- Convert it to a BeautifulSoup object. BeautifulSoup intakes the type of parser to use (for html websites, we can use `html.parser`).

> ```python
> soup = BeautifulSoup(content, 'html.parser')
> ```
- Using `find_all` extracts all attributes with the given html tag (here `tr`) and converts it into a list.

> ``` python
> rows = soup.find_all("tr")
> ```

- Extract titles first.

> ``` python
> title = soup.find_all('th')
> head = [k.text for k in title]
> ```

- Parse all column data into a list

> ``` python
> river_data = []
> for n, i in enumerate(rows):
>    cols = i.find_all('td')
>    col_data = [j.text for j in cols]
>    if col_data:
>        river_data.append(col_data)   
> ```

- Convert the whole list into a dataframe

> ``` python
> df = pd.DataFrame(river_data, columns= head)
> ```

### Ethics in Webscrapping
-  Check if `robots.txt` present in the website and they allow scrapping pages.
- `robots.txt` file structure:

    ```
    User-agent: *
    Disallow:
    ```
    Means you can crawl all pages in the website.

    ```
    User-agent: *

    Disallow: /
    ```
    Means you are not allowed to crawl through the website.

    ```
    User-agent: *

    Disallow: /blog/

    User-agent: *

    Disallow: /test.html
    ```
    Means apart from these pages, you can crawl through other pages.

### Tutorial 2 : Scrapping a Live Website

- Instead of opening a file, here we are using `requests.get()` we used above to fetch the content of a live website and then parse it using BeautifulSoup.

> ``` python
> upsc_url = "https://www.upscsuccess.com/rivers-india/"
> head = {"User-Agent": "Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0"}
>
> riv_page = requests.get(upsc_url, headers = head)
> soup2 = BeautifulSoup(riv_page.content)
> ```

- We can also specify extact `class` or `id` name of the object we have to scrap.
- Here we first scrap the figure and then the table is prsed using find function. `find` extracts only the first attribute of the input tag.

> ``` python
> fig = soup2.find('figure', attrs={'class':'wp-block-table'})
> table = fig.find('table')
> ```
After that using previous steps, we can extract the table and convert it into the pandas dataframe.


## References:
- https://beautiful-soup-4.readthedocs.io/en/latest/
- https://realpython.com/beautiful-soup-web-scraper-python/
- https://www.cloudflare.com/learning/bots/what-is-robots-txt/
- https://www.promptcloud.com/blog/how-to-read-and-respect-robots-file/
- https://www.upscsuccess.com/rivers-india/
- https://open-meteo.com/en/docs

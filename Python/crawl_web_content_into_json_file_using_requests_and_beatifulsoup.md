# Crawl Web Content Into Json File Using requests and BeatifulSoup

using requests to get html then use BeatifulSoup to parse it.

> See Also:
>- [Requests](http://docs.python-requests.org/en/master/)
>- [Beautiful Soup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
>- [json — JSON encoder and decoder](https://docs.python.org/3/library/json.html)

---

1. make request by requests.
    ```python
    # use requests to get page
    page = requests.get(BASE_URL)
    page_text = page.text
    ```

2. use BeatifulSoup to parse html.
  ```python
  # parse html by BeautifulSoup
  page_html = BeautifulSoup(page_text, 'html.parser')
  ```

3. use `find` or `find_all` to find elements you need.
  ```python
  # find the table contents the list of all pokemon
  table = page_html.find('table', class_='some-class')
  rows = table.find_all('tr')
  ```
  > Use class_='some-class' to find specific class.
  > Use attrs={"data-foo":"value"} to find elements with attributes.

  > See Also: [find_all() keyword arguments](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#the-keyword-arguments)

4. Navigate elements by dot '.'
  ```python
  row.td.attrs
  ```

5. Get attributes like a dict.
  ```python
  link = elements.a['href']
  ```

6. Dump into json
    ```python
    # Add encodeing='utf-8' for Chinese names
    with open('output.json', 'w', encoding='utf8') as json_file:
        # Add ensure_ascii=False to avoid Chinese character got convert to unicode.
        json.dump(data_list, json_file, ensure_ascii=False)
    ```
    > See Also: [[python] json 與中文](https://dotblogs.com.tw/rickyteng/2012/05/30/72492)

### [Example - Pokemon Crawler](https://github.com/TomazWang/pokemon-crawler/blob/master/pokemon-crawler.py)

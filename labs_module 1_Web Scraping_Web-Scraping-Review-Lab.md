<center>
    <img src="https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0101EN-SkillsNetwork/IDSNlogo.png" width="300" alt="cognitiveclass.ai logo">
</center>


# **Web Scraping Lab**


Estimated time needed: **40** minutes


## Objectives


After completing this lab you will be:

*   Familiar with the basics of the `BeautifulSoup` Python library
*   Be able to scrape webpages for data and filter the data


<h2>Table of Contents</h2>
<div class="alert alert-block alert-info" style="margin-top: 20px">
    <ul>
        <li>
            <a href="#Beautiful-Soup-Object">Beautiful Soup Object</a>
            <ul>
                <li><a href="#Tags">Tags</a></li>
                <li><a href="#Children,-Parents,-and-Siblings">Children, Parents, and Siblings</a></li>
                <li><a href="#HTML-Attributes">HTML Attributes</a></li>
                <li><a href="#Navigable-String">Navigable String</a></li>
            </ul>
        </li>
     </ul>
    <ul>
        <li>
            <a href="#Filter">Filter</a>
            <ul>
                <li><a href="#find_All">find_All</a></li>
                <li><a href="#find">find</a></li>
            </ul>
        </li>
     </ul>
     <ul>
        <li>
            <a href="#Downloading-And-Scraping-The-Contents-Of-A-Web-Page">Downloading And Scraping The Contents Of A Web Page</a></li>
         <li> <a href="#Scraping-tables-from-a-Web-page-using-Pandas">Scraping tables from a Web page using Pandas</a></li>
    </ul>

</div>

<hr>


For this lab, we are going to be using Python and several Python libraries. Some of these libraries might be installed in your lab environment or in SN Labs. Others may need to be installed by you. The cells below will install these libraries when executed.



```python
!pip install html5lib
```

    Requirement already satisfied: html5lib in /home/jupyterlab/conda/envs/python/lib/python3.7/site-packages (1.1)
    Requirement already satisfied: six>=1.9 in /home/jupyterlab/conda/envs/python/lib/python3.7/site-packages (from html5lib) (1.16.0)
    Requirement already satisfied: webencodings in /home/jupyterlab/conda/envs/python/lib/python3.7/site-packages (from html5lib) (0.5.1)


**Note:- After running the above code cell, restart the kernel and don't run the above code cell after restarting the kernel.**



```python
# !pip install requests
!pip install bs4
```

    Collecting bs4
      Downloading bs4-0.0.2-py2.py3-none-any.whl (1.2 kB)
    Requirement already satisfied: beautifulsoup4 in /home/jupyterlab/conda/envs/python/lib/python3.7/site-packages (from bs4) (4.11.1)
    Requirement already satisfied: soupsieve>1.2 in /home/jupyterlab/conda/envs/python/lib/python3.7/site-packages (from beautifulsoup4->bs4) (2.3.2.post1)
    Installing collected packages: bs4
    Successfully installed bs4-0.0.2


Import the required modules and functions



```python
from bs4 import BeautifulSoup # this module helps in web scrapping.
import requests  # this module helps us to download a web page
```

## Beautiful Soup Object


Beautiful Soup is a Python library for pulling data out of HTML and XML files, we will focus on HTML files. This is accomplished by representing the HTML as a set of objects with methods used to parse the HTML.  We can navigate the HTML as a tree, and/or filter out what we are looking for.

Consider the following HTML:



```python
%%html
<!DOCTYPE html>
<html>
<head>
<title>Page Title</title>
</head>
<body>
<h3><b id='boldest'>Lebron James</b></h3>
<p> Salary: $ 92,000,000 </p>
<h3> Stephen Curry</h3>
<p> Salary: $85,000, 000 </p>
<h3> Kevin Durant </h3>
<p> Salary: $73,200, 000</p>
</body>
</html>
```


<!DOCTYPE html>
<html>
<head>
<title>Page Title</title>
</head>
<body>
<h3><b id='boldest'>Lebron James</b></h3>
<p> Salary: $ 92,000,000 </p>
<h3> Stephen Curry</h3>
<p> Salary: $85,000, 000 </p>
<h3> Kevin Durant </h3>
<p> Salary: $73,200, 000</p>
</body>
</html>



We can store it as a string in the variable HTML:



```python
html = "<!DOCTYPE html><html><head><title>Page Title</title></head><body><h3> \
<b id='boldest'>Lebron James</b></h3><p> Salary: $ 92,000,000 </p> \
<h3>Stephen Curry</h3><p> Salary: $85,000,000</p> \
<h3>Kevin Durant</h3><p> Salary: $73,200,000</p></body></html>"
```

To parse a document, pass it into the <code>BeautifulSoup</code> constructor. The <code>BeautifulSoup</code> object represents the document as a nested data structure:



```python
soup = BeautifulSoup(html, 'html5lib')
```

First, the document is converted to Unicode (similar to ASCII) and HTML entities are converted to Unicode characters. Beautiful Soup transforms a complex HTML document into a complex tree of Python objects. The <code>BeautifulSoup</code> object can create other types of objects. In this lab, we will cover <code>BeautifulSoup</code> and <code>Tag</code> objects, that for the purposes of this lab are identical. Finally, we will look at <code>NavigableString</code> objects.


We can use the method <code>prettify()</code> to display the HTML in the nested structure:



```python
print(soup.prettify())
```

    <!DOCTYPE html>
    <html>
     <head>
      <title>
       Page Title
      </title>
     </head>
     <body>
      <h3>
       <b id="boldest">
        Lebron James
       </b>
      </h3>
      <p>
       Salary: $ 92,000,000
      </p>
      <h3>
       Stephen Curry
      </h3>
      <p>
       Salary: $85,000,000
      </p>
      <h3>
       Kevin Durant
      </h3>
      <p>
       Salary: $73,200,000
      </p>
     </body>
    </html>


## Tags


Let's say we want the  title of the page and the name of the top paid player. We can use the <code>Tag</code>. The <code>Tag</code> object corresponds to an HTML tag in the original document, for example, the tag title.



```python
tag_object = soup.title
print("tag object:", tag_object)
```

    tag object: <title>Page Title</title>


we can see the tag type <code>bs4.element.Tag</code>



```python
print("tag object type:", type(tag_object))
```

    tag object type: <class 'bs4.element.Tag'>


If there is more than one <code>Tag</code> with the same name, the first element with that <code>Tag</code> name is called. This corresponds to the most paid player:



```python
tag_object = soup.h3
tag_object
```




    <h3> <b id="boldest">Lebron James</b></h3>



Enclosed in the bold attribute <code>b</code>, it helps to use the tree representation. We can navigate down the tree using the child attribute to get the name.


### Children, Parents, and Siblings


As stated above, the <code>Tag</code> object is a tree of objects. We can access the child of the tag or navigate down the branch as follows:



```python
tag_child = tag_object.b
tag_child
```




    <b id="boldest">Lebron James</b>



You can access the parent with the <code>parent</code>.



```python
parent_tag = tag_child.parent
parent_tag
```




    <h3> <b id="boldest">Lebron James</b></h3>



this is identical to:



```python
tag_object
```




    <h3> <b id="boldest">Lebron James</b></h3>



<code>tag_object</code> parent is the <code>body</code> element.



```python
tag_object.parent
```




    <body><h3> <b id="boldest">Lebron James</b></h3><p> Salary: $ 92,000,000 </p> <h3>Stephen Curry</h3><p> Salary: $85,000,000</p> <h3>Kevin Durant</h3><p> Salary: $73,200,000</p></body>



<code>tag_object</code> sibling is the <code>paragraph</code> element.



```python
sibling_1 = tag_object.next_sibling
sibling_1
```




    <p> Salary: $ 92,000,000 </p>



`sibling_2` is the `header` element, which is also a sibling of both `sibling_1` and `tag_object`



```python
sibling_2 = sibling_1.next_sibling
sibling_2
```




    ' '



<h3 id="first_question">Exercise: <code>next_sibling</code></h3>


Use the object <code>sibling\_2</code> and the method <code>next_sibling</code> to find the salary of Stephen Curry:



```python

```

<details><summary>Click here for the solution</summary>

```
sibling_2.next_sibling

```

</details>


### HTML Attributes


If the tag has attributes, the tag <code>id="boldest"</code> has an attribute <code>id</code> whose value is <code>boldest</code>. You can access a tag's attributes by treating the tag like a dictionary:



```python
tag_child['id']
```




    'boldest'



You can access that dictionary directly as <code>attrs</code>:



```python
tag_child.attrs
```




    {'id': 'boldest'}



You can also work with Multi-valued attributes. Check out <a href="https://www.crummy.com/software/BeautifulSoup/bs4/doc/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2021-01-01">\[1]</a> for more.


We can also obtain the content of the attribute of the <code>tag</code> using the Python <code>get()</code> method.



```python
tag_child.get('id')
```




    'boldest'



### Navigable String


A string corresponds to a bit of text or content within a tag. Beautiful Soup uses the <code>NavigableString</code> class to contain this text. In our HTML we can obtain the name of the first player by extracting the string of the <code>Tag</code> object <code>tag_child</code> as follows:



```python
tag_string = tag_child.string
tag_string
```




    'Lebron James'



we can verify the type is Navigable String



```python
type(tag_string)
```




    bs4.element.NavigableString



A NavigableString is similar to a Python string or Unicode string. To be more precise, the main difference is that it also supports some <code>BeautifulSoup</code> features. We can convert it to string object in Python:



```python
unicode_string = str(tag_string)
unicode_string
```




    'Lebron James'



## Filter


Filters allow you to find complex patterns, the simplest filter is a string. In this section we will pass a string to a different filter method and Beautiful Soup will perform a match against that exact string. Consider the following HTML of rocket launches:



```python
%%html
<table>
  <tr>
    <td id='flight' >Flight No</td>
    <td>Launch site</td> 
    <td>Payload mass</td>
   </tr>
  <tr> 
    <td>1</td>
    <td><a href='https://en.wikipedia.org/wiki/Florida'>Florida</a></td>
    <td>300 kg</td>
  </tr>
  <tr>
    <td>2</td>
    <td><a href='https://en.wikipedia.org/wiki/Texas'>Texas</a></td>
    <td>94 kg</td>
  </tr>
  <tr>
    <td>3</td>
    <td><a href='https://en.wikipedia.org/wiki/Florida'>Florida<a> </td>
    <td>80 kg</td>
  </tr>
</table>
```


<table>
  <tr>
    <td id='flight' >Flight No</td>
    <td>Launch site</td> 
    <td>Payload mass</td>
   </tr>
  <tr> 
    <td>1</td>
    <td><a href='https://en.wikipedia.org/wiki/Florida'>Florida</a></td>
    <td>300 kg</td>
  </tr>
  <tr>
    <td>2</td>
    <td><a href='https://en.wikipedia.org/wiki/Texas'>Texas</a></td>
    <td>94 kg</td>
  </tr>
  <tr>
    <td>3</td>
    <td><a href='https://en.wikipedia.org/wiki/Florida'>Florida<a> </td>
    <td>80 kg</td>
  </tr>
</table>



We can store it as a string in the variable <code>table</code>:



```python
table = "<table><tr><td id='flight'>Flight No</td><td>Launch site</td> \
<td>Payload mass</td></tr><tr> <td>1</td> \
<td><a href='https://en.wikipedia.org/wiki/Florida'>Florida<a></td> \
<td>300 kg</td></tr><tr><td>2</td> \
<td><a href='https://en.wikipedia.org/wiki/Texas'>Texas</a></td> \
<td>94 kg</td></tr><tr><td>3</td> \
<td><a href='https://en.wikipedia.org/wiki/Florida'>Florida<a> </td> \
<td>80 kg</td></tr></table>"
```


```python
table_bs = BeautifulSoup(table, 'html5lib')
```

## find_All


The <code>find_all()</code> method looks through a tag's descendants and retrieves all descendants that match your filters.

<p>
The Method signature for <code>find_all(name, attrs, recursive, string, limit, **kwargs)<c/ode>
</p>


### Name


When we set the <code>name</code> parameter to a tag name, the method will extract all the tags with that name and its children.



```python
table_rows = table_bs.find_all('tr')
table_rows
```




    [<tr><td id="flight">Flight No</td><td>Launch site</td> <td>Payload mass</td></tr>,
     <tr> <td>1</td> <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a></a></td> <td>300 kg</td></tr>,
     <tr><td>2</td> <td><a href="https://en.wikipedia.org/wiki/Texas">Texas</a></td> <td>94 kg</td></tr>,
     <tr><td>3</td> <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a> </a></td> <td>80 kg</td></tr>]



The result is a Python iterable just like a list, each element is a <code>tag</code> object:



```python
first_row = table_rows[0]
first_row
```




    <tr><td id="flight">Flight No</td><td>Launch site</td> <td>Payload mass</td></tr>



The type is <code>tag</code>



```python
print(type(first_row))
```

    <class 'bs4.element.Tag'>


we can obtain the child



```python
first_row.td
```




    <td id="flight">Flight No</td>



If we iterate through the list, each element corresponds to a row in the table:



```python
for i, row in enumerate(table_rows):
    print("row", i, "is", row)
```

    row 0 is <tr><td id="flight">Flight No</td><td>Launch site</td> <td>Payload mass</td></tr>
    row 1 is <tr> <td>1</td> <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a></a></td> <td>300 kg</td></tr>
    row 2 is <tr><td>2</td> <td><a href="https://en.wikipedia.org/wiki/Texas">Texas</a></td> <td>94 kg</td></tr>
    row 3 is <tr><td>3</td> <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a> </a></td> <td>80 kg</td></tr>


As <code>row</code> is a <code>cell</code> object, we can apply the method <code>find_all</code> to it and extract table cells in the object <code>cells</code> using the tag <code>td</code>, this is all the children with the name <code>td</code>. The result is a list, each element corresponds to a cell and is a <code>Tag</code> object, we can iterate through this list as well. We can extract the content using the <code>string</code> attribute.



```python
for i, row in enumerate(table_rows):
    print("row", i)
    cells = row.find_all('td')
    for j, cell in enumerate(cells):
        print('colunm', j, "cell", cell)
```

    row 0
    colunm 0 cell <td id="flight">Flight No</td>
    colunm 1 cell <td>Launch site</td>
    colunm 2 cell <td>Payload mass</td>
    row 1
    colunm 0 cell <td>1</td>
    colunm 1 cell <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a></a></td>
    colunm 2 cell <td>300 kg</td>
    row 2
    colunm 0 cell <td>2</td>
    colunm 1 cell <td><a href="https://en.wikipedia.org/wiki/Texas">Texas</a></td>
    colunm 2 cell <td>94 kg</td>
    row 3
    colunm 0 cell <td>3</td>
    colunm 1 cell <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a> </a></td>
    colunm 2 cell <td>80 kg</td>


If we use a list we can match against any item in that list.



```python
list_input = table_bs.find_all(name=["tr", "td"])
list_input
```




    [<tr><td id="flight">Flight No</td><td>Launch site</td> <td>Payload mass</td></tr>,
     <td id="flight">Flight No</td>,
     <td>Launch site</td>,
     <td>Payload mass</td>,
     <tr> <td>1</td> <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a></a></td> <td>300 kg</td></tr>,
     <td>1</td>,
     <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a></a></td>,
     <td>300 kg</td>,
     <tr><td>2</td> <td><a href="https://en.wikipedia.org/wiki/Texas">Texas</a></td> <td>94 kg</td></tr>,
     <td>2</td>,
     <td><a href="https://en.wikipedia.org/wiki/Texas">Texas</a></td>,
     <td>94 kg</td>,
     <tr><td>3</td> <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a> </a></td> <td>80 kg</td></tr>,
     <td>3</td>,
     <td><a href="https://en.wikipedia.org/wiki/Florida">Florida</a><a> </a></td>,
     <td>80 kg</td>]



### Attributes


If the argument is not recognized it will be turned into a filter on the tag's attributes. For example with the <code>id</code> argument, Beautiful Soup will filter against each tag's <code>id</code> attribute. For example, the first <code>td</code> elements have a value of <code>id</code> of <code>flight</code>, therefore we can filter based on that <code>id</code> value.



```python
table_bs.find_all(id="flight")
```




    [<td id="flight">Flight No</td>]



We can find all the elements that have links to the Florida Wikipedia page:



```python
list_input = table_bs.find_all(href="https://en.wikipedia.org/wiki/Florida")
list_input
```




    [<a href="https://en.wikipedia.org/wiki/Florida">Florida</a>,
     <a href="https://en.wikipedia.org/wiki/Florida">Florida</a>]



If we set the <code>href</code> attribute to True, regardless of what the value is, the code finds all anchor tags with <code>href</code> value:



```python
table_bs.find_all('a', href=True)
```




    [<a href="https://en.wikipedia.org/wiki/Florida">Florida</a>,
     <a href="https://en.wikipedia.org/wiki/Texas">Texas</a>,
     <a href="https://en.wikipedia.org/wiki/Florida">Florida</a>]



There are other methods for dealing with attributes and other related methods. Check out the following <a href='https://www.crummy.com/software/BeautifulSoup/bs4/doc/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2021-01-01#css-selectors'>link</a>


<h3 id="exer_type">Exercise: <code>find_all</code></h3>


Using the logic above, find all the anchor tags without <code>href</code> value



```python
# Add response here
```

<details><summary>Click here for the solution</summary>

```
table_bs.find_all('a', href=False)

```

</details>


Using the soup object <code>soup</code>, find the element with the <code>id</code> attribute content set to <code>"boldest"</code>.



```python
# Add your answer here
```

<details><summary>Click here for the solution</summary>

```
soup.find_all(id="boldest")

```

</details>


### string


With string you can search for strings instead of tags, where we find all the elments with Florida:



```python
table_bs.find_all(string="Florida")
```




    ['Florida', 'Florida']



## find


The <code>find_all()</code> method scans the entire document looking for results. It’s useful if you are looking for one element, as you can use the <code>find()</code> method to find the first element in the document. Consider the following two tables:



```python
%%html
<h3>Rocket Launch </h3>

<p>
<table class='rocket'>
  <tr>
    <td>Flight No</td>
    <td>Launch site</td> 
    <td>Payload mass</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Florida</td>
    <td>300 kg</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Texas</td>
    <td>94 kg</td>
  </tr>
  <tr>
    <td>3</td>
    <td>Florida </td>
    <td>80 kg</td>
  </tr>
</table>
</p>
<p>

<h3>Pizza Party</h3>
  
    
<table class='pizza'>
  <tr>
    <td>Pizza Place</td>
    <td>Orders</td> 
    <td>Slices </td>
   </tr>
  <tr>
    <td>Domino's Pizza</td>
    <td>10</td>
    <td>100</td>
  </tr>
  <tr>
    <td>Little Caesars</td>
    <td>12</td>
    <td >144 </td>
  </tr>
  <tr>
    <td>Papa John's </td>
    <td>15 </td>
    <td>165</td>
  </tr>

```


<h3>Rocket Launch </h3>

<p>
<table class='rocket'>
  <tr>
    <td>Flight No</td>
    <td>Launch site</td> 
    <td>Payload mass</td>
  </tr>
  <tr>
    <td>1</td>
    <td>Florida</td>
    <td>300 kg</td>
  </tr>
  <tr>
    <td>2</td>
    <td>Texas</td>
    <td>94 kg</td>
  </tr>
  <tr>
    <td>3</td>
    <td>Florida </td>
    <td>80 kg</td>
  </tr>
</table>
</p>
<p>

<h3>Pizza Party</h3>


<table class='pizza'>
  <tr>
    <td>Pizza Place</td>
    <td>Orders</td> 
    <td>Slices </td>
   </tr>
  <tr>
    <td>Domino's Pizza</td>
    <td>10</td>
    <td>100</td>
  </tr>
  <tr>
    <td>Little Caesars</td>
    <td>12</td>
    <td >144 </td>
  </tr>
  <tr>
    <td>Papa John's </td>
    <td>15 </td>
    <td>165</td>
  </tr>



We store the HTML as a Python string and assign <code>two_tables</code>:



```python
two_tables="<h3>Rocket Launch </h3> \
<p><table class='rocket'> \
<tr><td>Flight No</td><td>Launch site</td><td>Payload mass</td></tr> \
<tr><td>1</td><td>Florida</td><td>300 kg</td></tr> \
<tr><td>2</td><td>Texas</td><td>94 kg</td></tr> \
<tr><td>3</td><td>Florida </td><td>80 kg</td></tr></table></p>\
<p><h3>Pizza Party</h3> \
<table class='pizza'> \
<tr><td>Pizza Place</td><td>Orders</td><td>Slices </td></tr> \
<tr><td>Domino's Pizza</td><td>10</td><td>100</td></tr> \
<tr><td>Little Caesars</td><td>12</td><td >144 </td></tr> \
<tr><td>Papa John's</td><td>15 </td><td>165</td></tr>"
```

We create a <code>BeautifulSoup</code> object  <code>two_tables_bs</code>



```python
two_tables_bs = BeautifulSoup(two_tables, 'html.parser')
```

We can find the first table using the tag name table



```python
two_tables_bs.find("table")
```




    <table class="rocket"> <tr><td>Flight No</td><td>Launch site</td><td>Payload mass</td></tr> <tr><td>1</td><td>Florida</td><td>300 kg</td></tr> <tr><td>2</td><td>Texas</td><td>94 kg</td></tr> <tr><td>3</td><td>Florida </td><td>80 kg</td></tr></table>



We can filter on the class attribute to find the second table, but because class is a keyword in Python, we add an underscore to differentiate them.



```python
two_tables_bs.find("table", class_='pizza')
```




    <table class="pizza"> <tr><td>Pizza Place</td><td>Orders</td><td>Slices </td></tr> <tr><td>Domino's Pizza</td><td>10</td><td>100</td></tr> <tr><td>Little Caesars</td><td>12</td><td>144 </td></tr> <tr><td>Papa John's</td><td>15 </td><td>165</td></tr></table>



<h2 id="DSCW">Downloading And Scraping The Contents Of A Web Page</h2> 


We Download the contents of the web page:



```python
url = "http://www.ibm.com"
```

We use <code>get</code> to download the contents of the webpage in text format and store in a variable called <code>data</code>:



```python
data = requests.get(url).text
```

We create a <code>BeautifulSoup</code> object using the <code>BeautifulSoup</code> constructor



```python
soup = BeautifulSoup(data, "html5lib")  # create a soup object using the variable 'data'
```

Scrape all links



```python
for link in soup.find_all('a', href=True):  # in html anchor/link is represented by the tag <a>
    print(link.get('href'))
```

    https://www.ibm.com/cloud?lnk=hpUSbt1


### Scrape all images Tags



```python
for link in soup.find_all('img'):  # in html image is represented by the tag <img>
    print(link)
    print(link.get('src'))
```

### Scrape data from HTML tables



```python
# The below url contains an html table with data about colors and color codes.
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/labs/datasets/HTMLColorCodes.html"
```

Before proceeding to scrape a web site, you need to examine the contents and the way data is organized on the website. Open the above url in your browser and check how many rows and columns there are in the color table.



```python
# get the contents of the webpage in text format and store in a variable called data
data = requests.get(url).text
```


```python
soup = BeautifulSoup(data, "html5lib")
```


```python
# find a html table in the web page
table = soup.find('table')  # in html table is represented by the tag <table>
```


```python
# Get all rows from the table
for row in table.find_all('tr'):  # in html table row represented by tag <tr>
    # Get all columns in each row.
    cols = row.find_all('td')  # in html a column is represented by tag <td>
    color_name = cols[2].string  # store the value in column 3 as color_name
    color_code = cols[3].text  # store the value in column 4 as color_code
    print("{}--->{}".format(color_name, color_code))
```

    Color Name--->Hex Code#RRGGBB
    lightsalmon--->#FFA07A
    salmon--->#FA8072
    darksalmon--->#E9967A
    lightcoral--->#F08080
    coral--->#FF7F50
    tomato--->#FF6347
    orangered--->#FF4500
    gold--->#FFD700
    orange--->#FFA500
    darkorange--->#FF8C00
    lightyellow--->#FFFFE0
    lemonchiffon--->#FFFACD
    papayawhip--->#FFEFD5
    moccasin--->#FFE4B5
    peachpuff--->#FFDAB9
    palegoldenrod--->#EEE8AA
    khaki--->#F0E68C
    darkkhaki--->#BDB76B
    yellow--->#FFFF00
    lawngreen--->#7CFC00
    chartreuse--->#7FFF00
    limegreen--->#32CD32
    lime--->#00FF00
    forestgreen--->#228B22
    green--->#008000
    powderblue--->#B0E0E6
    lightblue--->#ADD8E6
    lightskyblue--->#87CEFA
    skyblue--->#87CEEB
    deepskyblue--->#00BFFF
    lightsteelblue--->#B0C4DE
    dodgerblue--->#1E90FF


## Scraping tables from a Web page using Pandas


Particularly for extracting tabular data from a web page, you may also use the `read_html()` method of the Pandas library. 



```python
# The below url contains an html table with data about colors and color codes.
url = "https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBM-DA0321EN-SkillsNetwork/labs/datasets/HTMLColorCodes.html"
```

You may extract all the tables from the given webpage simply by using the following commands.



```python
import pandas as pd

tables = pd.read_html(url)
tables
```




    [         0      1               2                3                    4
     0   Number  Color      Color Name  Hex Code#RRGGBB  Decimal Code(R,G,B)
     1        1    NaN     lightsalmon          #FFA07A     rgb(255,160,122)
     2        2    NaN          salmon          #FA8072     rgb(250,128,114)
     3        3    NaN      darksalmon          #E9967A     rgb(233,150,122)
     4        4    NaN      lightcoral          #F08080     rgb(240,128,128)
     5        5    NaN           coral          #FF7F50      rgb(255,127,80)
     6        6    NaN          tomato          #FF6347       rgb(255,99,71)
     7        7    NaN       orangered          #FF4500        rgb(255,69,0)
     8        8    NaN            gold          #FFD700       rgb(255,215,0)
     9        9    NaN          orange          #FFA500       rgb(255,165,0)
     10      10    NaN      darkorange          #FF8C00       rgb(255,140,0)
     11      11    NaN     lightyellow          #FFFFE0     rgb(255,255,224)
     12      12    NaN    lemonchiffon          #FFFACD     rgb(255,250,205)
     13      13    NaN      papayawhip          #FFEFD5     rgb(255,239,213)
     14      14    NaN        moccasin          #FFE4B5     rgb(255,228,181)
     15      15    NaN       peachpuff          #FFDAB9     rgb(255,218,185)
     16      16    NaN   palegoldenrod          #EEE8AA     rgb(238,232,170)
     17      17    NaN           khaki          #F0E68C     rgb(240,230,140)
     18      18    NaN       darkkhaki          #BDB76B     rgb(189,183,107)
     19      19    NaN          yellow          #FFFF00       rgb(255,255,0)
     20      20    NaN       lawngreen          #7CFC00       rgb(124,252,0)
     21      21    NaN      chartreuse          #7FFF00       rgb(127,255,0)
     22      22    NaN       limegreen          #32CD32       rgb(50,205,50)
     23      23    NaN            lime          #00FF00         rgb(0.255.0)
     24      24    NaN     forestgreen          #228B22       rgb(34,139,34)
     25      25    NaN           green          #008000         rgb(0,128,0)
     26      26    NaN      powderblue          #B0E0E6     rgb(176,224,230)
     27      27    NaN       lightblue          #ADD8E6     rgb(173,216,230)
     28      28    NaN    lightskyblue          #87CEFA     rgb(135,206,250)
     29      29    NaN         skyblue          #87CEEB     rgb(135,206,235)
     30      30    NaN     deepskyblue          #00BFFF       rgb(0,191,255)
     31      31    NaN  lightsteelblue          #B0C4DE     rgb(176,196,222)
     32      32    NaN      dodgerblue          #1E90FF      rgb(30,144,255)]



`tables` is now a list of dataframes representing the tables from the web page, in the sequence of their appearance. In the current  URL, there is only a single table, so the same can be accessed as shown below.



```python
tables[0]
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Number</td>
      <td>Color</td>
      <td>Color Name</td>
      <td>Hex Code#RRGGBB</td>
      <td>Decimal Code(R,G,B)</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>NaN</td>
      <td>lightsalmon</td>
      <td>#FFA07A</td>
      <td>rgb(255,160,122)</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>NaN</td>
      <td>salmon</td>
      <td>#FA8072</td>
      <td>rgb(250,128,114)</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>NaN</td>
      <td>darksalmon</td>
      <td>#E9967A</td>
      <td>rgb(233,150,122)</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>NaN</td>
      <td>lightcoral</td>
      <td>#F08080</td>
      <td>rgb(240,128,128)</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>NaN</td>
      <td>coral</td>
      <td>#FF7F50</td>
      <td>rgb(255,127,80)</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>NaN</td>
      <td>tomato</td>
      <td>#FF6347</td>
      <td>rgb(255,99,71)</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>NaN</td>
      <td>orangered</td>
      <td>#FF4500</td>
      <td>rgb(255,69,0)</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>NaN</td>
      <td>gold</td>
      <td>#FFD700</td>
      <td>rgb(255,215,0)</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>NaN</td>
      <td>orange</td>
      <td>#FFA500</td>
      <td>rgb(255,165,0)</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>NaN</td>
      <td>darkorange</td>
      <td>#FF8C00</td>
      <td>rgb(255,140,0)</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>NaN</td>
      <td>lightyellow</td>
      <td>#FFFFE0</td>
      <td>rgb(255,255,224)</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>NaN</td>
      <td>lemonchiffon</td>
      <td>#FFFACD</td>
      <td>rgb(255,250,205)</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>NaN</td>
      <td>papayawhip</td>
      <td>#FFEFD5</td>
      <td>rgb(255,239,213)</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>NaN</td>
      <td>moccasin</td>
      <td>#FFE4B5</td>
      <td>rgb(255,228,181)</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>NaN</td>
      <td>peachpuff</td>
      <td>#FFDAB9</td>
      <td>rgb(255,218,185)</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>NaN</td>
      <td>palegoldenrod</td>
      <td>#EEE8AA</td>
      <td>rgb(238,232,170)</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>NaN</td>
      <td>khaki</td>
      <td>#F0E68C</td>
      <td>rgb(240,230,140)</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>NaN</td>
      <td>darkkhaki</td>
      <td>#BDB76B</td>
      <td>rgb(189,183,107)</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>NaN</td>
      <td>yellow</td>
      <td>#FFFF00</td>
      <td>rgb(255,255,0)</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20</td>
      <td>NaN</td>
      <td>lawngreen</td>
      <td>#7CFC00</td>
      <td>rgb(124,252,0)</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21</td>
      <td>NaN</td>
      <td>chartreuse</td>
      <td>#7FFF00</td>
      <td>rgb(127,255,0)</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22</td>
      <td>NaN</td>
      <td>limegreen</td>
      <td>#32CD32</td>
      <td>rgb(50,205,50)</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23</td>
      <td>NaN</td>
      <td>lime</td>
      <td>#00FF00</td>
      <td>rgb(0.255.0)</td>
    </tr>
    <tr>
      <th>24</th>
      <td>24</td>
      <td>NaN</td>
      <td>forestgreen</td>
      <td>#228B22</td>
      <td>rgb(34,139,34)</td>
    </tr>
    <tr>
      <th>25</th>
      <td>25</td>
      <td>NaN</td>
      <td>green</td>
      <td>#008000</td>
      <td>rgb(0,128,0)</td>
    </tr>
    <tr>
      <th>26</th>
      <td>26</td>
      <td>NaN</td>
      <td>powderblue</td>
      <td>#B0E0E6</td>
      <td>rgb(176,224,230)</td>
    </tr>
    <tr>
      <th>27</th>
      <td>27</td>
      <td>NaN</td>
      <td>lightblue</td>
      <td>#ADD8E6</td>
      <td>rgb(173,216,230)</td>
    </tr>
    <tr>
      <th>28</th>
      <td>28</td>
      <td>NaN</td>
      <td>lightskyblue</td>
      <td>#87CEFA</td>
      <td>rgb(135,206,250)</td>
    </tr>
    <tr>
      <th>29</th>
      <td>29</td>
      <td>NaN</td>
      <td>skyblue</td>
      <td>#87CEEB</td>
      <td>rgb(135,206,235)</td>
    </tr>
    <tr>
      <th>30</th>
      <td>30</td>
      <td>NaN</td>
      <td>deepskyblue</td>
      <td>#00BFFF</td>
      <td>rgb(0,191,255)</td>
    </tr>
    <tr>
      <th>31</th>
      <td>31</td>
      <td>NaN</td>
      <td>lightsteelblue</td>
      <td>#B0C4DE</td>
      <td>rgb(176,196,222)</td>
    </tr>
    <tr>
      <th>32</th>
      <td>32</td>
      <td>NaN</td>
      <td>dodgerblue</td>
      <td>#1E90FF</td>
      <td>rgb(30,144,255)</td>
    </tr>
  </tbody>
</table>
</div>



## Authors


Ramesh Sannareddy


### Other Contributors


Rav Ahuja

Abhishek Gagneja


## Change Log


| Date (YYYY-MM-DD) | Version | Changed By            |          Change Description         |
| ----------------- | ------- | ----------------------| ----------------------------------- |
| 2023-11-02 | 1.1 | Abhishek Gagneja | Updated instructions, added web scraping using Pandas |
| 2023-06-11        | 1.0     | Akansha Yadav         |   Spell check                       |
| 2020-10-17        | 0.1     | Joseph Santarcangelo  |  Created initial version of the lab |


Copyright © 2023 IBM Corporation. This notebook and its source code are released under the terms of the [MIT License](https://cognitiveclass.ai/mit-license/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursesIBMDeveloperSkillsNetworkPY0101ENSkillsNetwork19487395-2021-01-01).


- building the spider in the virtual environment within the CTF web scraping workspace 

- all work is done under the tutorial folder

https://docs.scrapy.org/en/latest/intro/tutorial.html

NOTES:
-------------

- spiders are classes defined that scrapy uses to scrap information from websites
- must be a subclass of `spider` and all define the initial requests to be made
- optionally, can be used to define how to follow links in pages and parsing downloaded pages to extract specific data
- create spiders by creating a python file under `.../spiders` directory of the project
- **Questions I've asked myself while looking at the tutorial spider code:**

| Question                                                                                    | Answer                                                                                                                                                                        |
| ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "What is pathlib?"                                                                          | standard python library module (comes pre-installed) that lets you work with file and directory paths                                                                         |
| "when I do `from pathlib import x` am I saying to import specific parts from that library?" | doing so imports the specific class specified in the import clause. if you only need a few parts from the library, you can import just the specifics.                         |
| "what is a class?"                                                                          | a template defining the attributes and functions that the object within that class has. In essence, **a class defines what something is and what it can do.**                 |
| "what is class inheritance?"                                                                | when a new class is created based on a pre-existing one, allowing it to automatically receive the parent class's attributed and behaviors while adding or overriding its own. |
| what is the 'name' attribute in the spider class?                                           | whenever scrapy refers to the spider, it refers to it by the value defined by the name variable inside of the class. it's the unique identifier inside of the project.        |
| what is `async` ?                                                                           | keyword in python that marks a function as "asynchronous". this means that it can pause and resume while waiting for something slow                                           |
| what is `async def` ?                                                                       | defining a function as asynchronous                                                                                                                                           |
| what is a method?                                                                           | a function assigned to a class.                                                                                                                                               |
| what is `yield` ?                                                                           | keyword used in python to return a list of values stored in a function. `return` stops further execution from the function, but `yield` continues                             |


Title: Trafilatura Starter
Date: 2023-09-10
Tags: Trafilatura

In a previous post I mentioned using Trafilatura in an AWS Lambda function. Trafilatura's main purpose is to easily extract metadata from websites. All web crawling info and web document traversal are abstracted away from the user.

Basic usage:

```python
from trafilatura import fetch_url
from trafilatura import extract, extract_metadata

url = ""
document = fetch_url(url)

text = extract(document)
print(text)

metadata = extract_metadata(document)
print(metadata)
print(metadata.title)
```

The [documentation](https://trafilatura.readthedocs.io/en/latest/corefunctions.html#trafilatura.extract_metadata) for extract_metadata states that it returns a "trafilatura.metadata.Document". Unfortunately the only way to truly understand what metadata is available is to look into the source code.


`trafilatura/metadata.py`
```python
class Document:
    "Defines a class to store all necessary data and metadata fields for extracted information."
    __slots__ = [
    'title', 'author', 'url', 'hostname', 'description', 'sitename',
    'date', 'categories', 'tags', 'fingerprint', 'id', 'license',
    'body', 'comments', 'commentsbody', 'raw_text', 'text',
    'language', 'image', 'pagetype'  # 'locale'?
    ]
```
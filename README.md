# Goal

- Create a simple importable Python module which will produce parsed
  WHOIS data for a given domain.
- Able to extract data for all the popular TLDs (com, org, net, ...)
- Query a WHOIS server directly instead of going through an
  intermediate web service like many others do.

# Example

```python
>>> import whois
>>> w = whois.whois('example.com')
>>> w.expiration_date  # dates converted to datetime object
datetime.datetime(2022, 8, 13, 4, 0)
>>> w.text  # the content downloaded from whois server
u'\nDomain Name: EXAMPLE.COM
Registry Domain ID: 2336799_DOMAIN_COM-VRSN
...'

>>> print(w)  # print values of all found attributes
{
  "creation_date": "1995-08-14 04:00:00",
  "expiration_date": "2022-08-13 04:00:00",
  "updated_date": "2021-08-14 07:01:44",
  "domain_name": "EXAMPLE.COM",
  "name_servers": [
      "A.IANA-SERVERS.NET",
      "B.IANA-SERVERS.NET"
  ],
  ...
```

# Install

Install from pypi:

```bash
pip install python-whois
```

Or checkout latest version from repository:

```bash
git clone git@github.com:richardpenman/whois.git
pip install -r requirements.txt
```

Run test cases:

```bash
python -m pytest
```

# Using a proxy.

Set your environment SOCKS variable
```bash
export SOCKS="username:password@proxy_address:port"
```

# Problems?

Pull requests are welcome!

Thanks to the many who have sent patches for additional TLDs. If you want to add or fix a TLD it's quite straightforward.
See example domains in [whois/parser.py](https://github.com/richardpenman/whois/blob/master/whois/parser.py)

Basically each TLD has a similar format to the following:

```python
class WhoisOrg(WhoisEntry):
  """Whois parser for .org domains
  """
  regex = {
    'domain_name':      'Domain Name: *(.+)',
    'registrar':        'Registrar: *(.+)',
    'whois_server':     'Whois Server: *(.+)',
    ...
  }

  def __init__(self, domain, text):
    if text.strip() == 'NOT FOUND':
      raise WhoisDomainNotFoundError(text)
    else:
      WhoisEntry.__init__(self, domain, text, self.regex)
```

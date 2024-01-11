# hakoriginfinder

Tool for discovering the origin host behind a reverse proxy. Useful for bypassing WAFs and other reverse proxies.

## How does it work?

This tool will first make a HTTP request to the hostname/URL that you provide and store the response, then it will make a request to every IP address that you provide via HTTP (80) and HTTPS (443) by default (more ports can be given via option), with the `Host` header set to the original host(:port). Each response is then compared to the original using the Levenshtein algorithm to determine similarity. If the response is similar, it will be deemed a match.

## Usage

Provide the list of IP addresses via stdin, and the original hostname via the -h option. For example:

```
prips 93.184.216.0/24 | hakoriginfinder -h https://example.com:443/foo
```

You may set the Levenshtein distance threshold with `-l`. The lower the number, the more similar the matches need to be for it to be considered a match, the default is 5.

The number of threads may be set with `-t`, default is 32.

The hostname is set with `-h`, there is no default.

The ports to use for the IP addresses supplied via stdin is set with `-p`, the default is 80,443.

## Output

The output is 3 columns, separated by spaces. The first column is either "MATCH" or "NOMATCH" depending on whether the Levenshtein threshold was reached or not. The second column is the URL being tested, and the third column is the Levenshtein score.

### Output example

```
hakluke$ prips 1.1.1.0/24 | hakoriginfinder -h https://one.one.one.one/index.html
Redirect 308 to: https://one.one.one.one/
NOMATCH http://1.1.1.13:443/ 56290
NOMATCH http://1.1.1.21:443/ 56290
NOMATCH http://1.1.1.14:443/ 56290
NOMATCH http://1.1.1.22:443/ 56290
NOMATCH http://1.1.1.30:443/ 56290
NOMATCH http://1.1.1.27:443/ 56290
NOMATCH http://1.1.1.23:443/ 56290
NOMATCH http://1.1.1.0:443/ 56290
... snipped for brevity ...
NOMATCH http://1.1.1.252:443/ 56290
NOMATCH http://1.1.1.253:443/ 56290
NOMATCH http://1.1.1.254:443/ 56290
NOMATCH http://1.1.1.251:443/ 56290
MATCH https://1.1.1.1:443/ 0
```

## Installation

Install golang, then run:

```
go install github.com/hakluke/hakoriginfinder@latest
```

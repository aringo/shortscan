# 🌀 Shortscan

An IIS short filename enumeration tool with multi-URL scanning capabilities.

## Functionality

Shortscan is designed to quickly determine which files with short filenames exist on an IIS webserver. Once a short filename has been identified the tool will try to automatically identify the full filename.

In addition to standard discovery methods Shortscan also uses a unique checksum matching approach to attempt to find the long filename where the short filename is based on Windows' proprietary shortname collision avoidance checksum algorithm.

## Installation

### Quick install

Using a recent version of [go](https://golang.org/):

```
go install github.com/aringo/shortscan/cmd/shortscan@latest
```

### Manual install

To build (and optionally install) locally:

```
git clone https://github.com/aringo/shortscan.git
cd shortscan
go build ./cmd/shortscan
go install ./cmd/shortscan
```

## Usage

### Basic usage

Shortscan is easy to use with minimal configuration. Basic usage looks like:

```
$ shortscan https://example.org/
```

### Multiple URLs

You can scan multiple URLs at once:

```
$ shortscan https://example1.com https://example2.com https://example3.com
```

### URL List from File

You can also specify a file containing a list of URLs to be scanned:

```
$ shortscan @urls.txt
```

The file can contain comments (lines starting with #) and empty lines, which will be ignored.

### Concurrency Options

```
# Process 5 URLs concurrently
shortscan -M 5 @urls.txt

# Make 30 requests at once per URL
shortscan -c 30 https://example.com

# Combined URL and request concurrency
shortscan -M 5 -c 30 @urls.txt
```

### Batch Processing

```
# Process URLs in batches of 50 (default is 100)
shortscan --batch-size 50 @urls.txt
```

### Examples

This example sets multiple custom headers by using `--header`/`-H` multiple times:
```
shortscan -H 'Host: gibson' -H 'Authorization: Basic ZGFkZTpsMzN0'
```

To check whether a site is vulnerable without performing file enumeration use:
```
shortscan --isvuln
```

### Advanced features

The following options allow further tweaks:

```
🌀 Shortscan v1.0.0 · an IIS short filename enumeration tool
Usage: shortscan [--wordlist FILE] [--header HEADER] [--concurrency CONCURRENCY] [--timeout SECONDS] [--output format] [--verbosity VERBOSITY] [--fullurl] [--norecurse] [--stabilise] [--patience LEVEL] [--characters CHARACTERS] [--autocomplete mode] [--isvuln] [--url-concurrency URL-CONCURRENCY] [--batch-size BATCH-SIZE] URL [URL ...]

Positional arguments:
  URL                    url to scan (multiple URLs can be provided; a file containing URLs can be specified with an «at» prefix, for example: @urls.txt)

Options:
  --wordlist FILE, -w FILE
                         combined wordlist + rainbow table generated with shortutil
  --header HEADER, -H HEADER
                         header to send with each request (use multiple times for multiple headers)
  --concurrency CONCURRENCY, -c CONCURRENCY
                         number of requests to make at once [default: 20]
  --timeout SECONDS, -t SECONDS
                         per-request timeout in seconds [default: 10]
  --output format, -o format
                         output format (human = human readable; json = JSON) [default: human]
  --verbosity VERBOSITY, -v VERBOSITY
                         how much noise to make (0 = quiet; 1 = debug; 2 = trace) [default: 0]
  --fullurl, -F          display the full URL for confirmed files rather than just the filename [default: false]
  --norecurse, -n        don't detect and recurse into subdirectories (disabled when autocomplete is disabled) [default: false]
  --stabilise, -s        attempt to get coherent autocomplete results from an unstable server (generates more requests) [default: false]
  --patience LEVEL, -p LEVEL
                         patience level when determining vulnerability (0 = patient; 1 = very patient) [default: 0]
  --characters CHARACTERS, -C CHARACTERS
                         filename characters to enumerate [default: JFKGOTMYVHSPCANDXLRWEBQUIZ8549176320-_()&'!#$%@^{}~]
  --autocomplete mode, -a mode
                         autocomplete detection mode (auto = autoselect; method = HTTP method magic; status = HTTP status; distance = Levenshtein distance; none = disable) [default: auto]
  --isvuln, -V           bail after determining whether the service is vulnerable [default: false]
  --url-concurrency URL-CONCURRENCY, -M URL-CONCURRENCY
                         number of URLs to scan concurrently [default: 1]
  --batch-size BATCH-SIZE
                         number of URLs to process in each batch [default: 100]
  --help, -h             display this help and exit
  --version              display version and exit
```

## Utility

The shortscan project includes a utility named `shortutil` which can be used to perform various short filename operations and to make custom rainbow tables for use with the tool.

### Examples

You can create a rainbow table from an existing wordlist like this:

```
shortutil wordlist input.txt > output.rainbow
```

To generate a one-off checksum for a file:

```
shortutil checksum index.html
```

### Usage

Run `shortutil <command> --help` for a definitive list of options for each command.

```
Shortutil v0.4 · a short filename utility
Usage: shortutil <command> [<args>]

Options:
  --help, -h             display this help and exit

Commands:
  wordlist               add hashes to a wordlist for use with, for example, shortscan
  checksum               generate a one-off checksum for the given filename
```

## Wordlist

A custom wordlist was built for shortscan. For full details see [pkg/shortscan/resources/README.md](pkg/shortscan/resources/README.md)

## Credit

Original IIS short filename [research](https://soroush.secproject.com/downloadable/microsoft_iis_tilde_character_vulnerability_feature.pdf) by Soroush Dalili.

Additional research and original project by [bitquark](https://github.com/bitquark).

Multi-URL scanning and concurrency improvements by [aringo](https://github.com/aringo).

# rel-me

A set of PHP functions for discovering, consuming and verifying the rel-me microformat.

## Usage

Install using [composer](https://getcomposer.org) with `./composer.phar require indieweb/rel-me:dev-master`

### Resolve and test a profile URL

```php
<?php

// Register the composer autoloader (assumed in future code samples.)
require 'vendor/autoload.php';

// This is a profile URL a user has given you, or you have parsed from a page somewhere.
$givenProfileUrl = 'http://waterpigs.co.uk';

list($resolvedProfileUrl, $isSecure, $redirectChain) = IndieWeb\relMeDocumentUrl($givenProfileUrl);

if ($isSecure) {
	// $resolvedProfileUrl is the final resolved profile URL derived from the given one.
} else {
	echo 'Your profile URL redirected insecurely (changed protocols)';
	// Here you might use the $redirectChain (list of URLs) to present a more useful error message.
}
```

### Find all `rel=me` links on a page

```php
// this should be derived and checked using relMeDocumentUrl()
$resolvedProfileUrl = 'http://waterpigs.co.uk';

$relMeLinks = IndieWeb\relMeLinks($resolvedProfileUrl);
```

### Test whether or not a backlinking `rel=me` URL can be securely considered to link

```php
// a rel=me link from a silo profile page
$inboundRelMeUrl = 'http://t.co/qhZqdUcTbQ';
// the derived profile document URL to test for matches of
$meUrl = 'http://waterpigs.co.uk';

list($matches, $secure, $previous) = IndieWeb\backlinkingRelMeUrlMatches($inboundRelMeUrl, $meUrl);

if ($matches) {
	if ($secure) {
		echo "{$inboundRelMeUrl} is a secure, valid link to {$meUrl}";
	} else {
		echo "{$inboundRelMeUrl} isn’t a secure link to {$meUrl} because it redirects insecurely (changes protocols)";
		// Here you might use the $redirectChain (list of URLs) to present a more useful error message.
	}
} else {
	echo "None of that silo backlink’s redirect chain match {$meUrl}";
}
```

### Utility Functions

* `string $url = IndieWeb\unparseUrl(array $parsedUrl)` takes the output of core `parse_url()` and makes it into a URL again
* `string $url = IndieWeb\normaliseUrl($url)` normalises a URL by parsing, then unparsing it
* `list(string $body, array $headers, array $info) = IndieWeb\httpGet($url)` performs a basic HTTP GET on a URL
* `string $nextUrl | null = IndieWeb\followOneRedirect($url)` fetches a URL and returns the redirect URL if it’s a redirect, else `null`
* `bool $match = IndieWeb\urlsMatchOtherThanScheme($url1, $url2)` compares two URLs and returns whether or not they are the same, ignoring differences in their scheme

## Testing

Run the test suite with `./vendor/bin/phpunit`

There’s also an experimental HTML+microformats test suite which can be run with  `./tests/html-test-runner.php`

## Version History

### 0.1.0 2014-01-04
* Initial extraction from indiewebify.me, conversion into a composer package
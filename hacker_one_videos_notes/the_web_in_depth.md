  ~*~*~*~* THE WEB IN DEPTH *~*~*~*~*~*~*~


HTTP Basics

every basic HTTP request follows:
	VERB /resource/locator HTTP/1.1
	Header1: value
	Header2: value

Request Headers:
	Host, Accept (indicates what MIME types), Cookie, Referer (page leading to this request), Authorization

Cookies are just key value pairs of data, and only exist on a server for a fixed period of time

each cookie has a domain pattern that it applies to and they're passed with eachc request the lcient makes to matching hosts

if you can set cookies on domains you're not supposed to ..... DUN DUN DUN


Cookie Security:
	cookies added for any domain can be read by subdomains of that domain
	cookies added for subdomain can only be read by that subdomain and down (follow the tree)
	subdomain can set cookies for its subdomains and parent, but NOT for sibling domains

	two important flags for cookies:
		Secure: the cookie is only accessible to HTTPS pages
		HTTPOnly: the cookie cannot be read by Javascript
	 - the server indicates these flags in the set-cookie header


HTML
	- different to consider HTML with a security mindset

	Parsing:
		not only parsed by a browser, also by firewalls and other sources

	sometimes you can inject a <script/xss src=http://eviljs.com> and it won't be caught

	browsers automatically close open script tags at the end of a page
	often all tags will do that


MIME Sniffing
	aka content sniffing
	send data to the browser without telling it what you're sending 
	the browser will often not just look at the Content-Type header, but the contents of the page
		(if it looks enough like HTML, it'll be parsed as HTML)

	upload a picture that has a lot of html for it to be sniffed and parsed as that, but secretly it carries malicious shit

	If you don't specify encoding on a browser, it will apply heuristics to determine it
		- put UTF-7 text into XSS payloads:
			+ADw-script+AD4-alert(1);ADw-/script+AD4-
		--- ^^^ this will go cleanly through HTML encoding, as there are no 'unsafe' characters

	try different UTF types and different XML encodings to deliver payloads


	- THE RULE IS:
		always specify an encoding, know what encoding the browser should be parsing it in, so the server and client don't have discrepancy
		- similarly, always set MIME types

Same-Origin Policy
	- the backbone of security as it stands today
	
	== what domains you can contact via XMLHttpRequest
		- access to th eDOM across separate frames/windows
	
	- ANYTIME this is circumvented in some way, havoc can be wreaked

	Origin Matching 
		- Much more strict for SOP than cookies
		- Protocol MUST match, no crossing HTTP/HTTPS boundaries,
		- port numbers must match
		- domain names must be an exact match (no wildcarding, etc

	It's possible for developers to loosen SOP grip
		- change document.domain
		- use CORS
	

CORS
	cross origin resource sharing
	still very new! lots of risky stuff :)
	It's possible to have it pass the receiving domains cookies, allowing attackers to potentially compromise logged-in users

	LARGELY UNEXPLORED, MANY SECURITY PROSPECTS

CSRF
	cross site request forgery
	not uncommon to find one and that will lead to many more
	
	basically when an attacker can make a victim go to a page that they control, which then submits data *as the user*

	canonical example is a bank transfer site
	
	best way to mitigate this bug is through CSRF tokens
		- generate random token and embed to user's session, that will be passed in every form that is generated
	whenever any server gets a post request, it should check for CSRF token is present and matches token associated with user's session

	
	


wrap up:
	cookie domain scoping is often a source of trouble
	SOP is strict, but complex
	CSRF is when an attacker tricks a victim into going to a page that triggers requests on other sites
		- ALWAYS USE CSRF TOKENS
			- no matter what, if a form doesn't have csrf tokens on it, that's a finding





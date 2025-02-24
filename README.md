# Bypassing Cloudflare: Best Practices

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/) 

This guide explains how to bypass Cloudflare’s security and successfully scrape websites without getting blocked.

- [Using Proxy Solutions](#using-proxy-solutions)
- [Spoofing HTTP Headers](#spoofing-http-headers)
- [Implementing CAPTCHA-Solving Services](#implementing-captcha-solving-services)
- [Using a Fortified Headless Browser](#using-a-fortified-headless-browser)
- [Using Cloudflare Solvers](#using-cloudflare-solvers)
- [Advanced Techniques](#advanced-techniques)
- [Incorporating Bright Data Solutions](#incorporating-bright-data-solutions)

## Understanding Cloudflare’s Mechanisms

Cloudflare’s [web application firewall](https://www.cloudflare.com/application-services/products/waf/) (WAF) protects web apps from DDoS and zero-day attacks. Running on its global network, it stops attacks in real time and uses a proprietary algorithm to identify and block malicious bots based on several traits:

- **TLS fingerprints**: JA3 fingerprints identify the clients and their capabilities and configurations and verify if a client is genuine.
- **[HTTP/2 fingerprints](https://www.blackhat.com/docs/eu-17/materials/eu-17-Shuster-Passive-Fingerprinting-Of-HTTP2-Clients-wp.pdf)**: Uses HTTP/2 parameters to match against known bot signatures.
- **HTTP details**: Inspects headers and cookies for bot-like configurations.
- **JavaScript fingerprints**: Gathers browser, OS, and hardware details to distinguish bots.
- **Behavior analysis**: Monitors request rates, mouse movements, and idle times with machine learning to detect bots.

If bot-like activity is detected, Cloudflare issues a background JavaScript challenge; failure results in a CAPTCHA.

## Techniques to Bypass Cloudflare

Cloudflare’s proprietary bot detection isn’t foolproof, so you'll need to experiment to find the best approach for your needs.

### Using Proxy Solutions

Cloudflare detects bots by flagging too many requests from a single IP. To avoid this, use rotating premium residential proxies. However, if user-agent checks are in play, you'll need to spoof the user agent.

### Spoofing HTTP Headers

HTTP headers reveal client details. Cloudflare checks these to differentiate real browsers, which send many headers, from scrapers that send few. Most scraper tools let you modify headers to mimic a genuine browser. Common headers include:

#### User-Agent Header

The `User-Agent` header reveals the browser and OS. Cloudflare may block bot-like User-Agents, so spoofing it to mimic a real browser (e.g., Chrome, Firefox, Safari) can help avoid detection. For example, in Python’s `requests` library, you can set it like this:

```python
import requests
 
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
}
 
response = requests.get('http://httpbin.org/headers', headers=headers)
 
print(response.status_code)
print(response.text)
```

#### Referrer Header

Cloudflare checks the `Referer` header to verify a request's source. Spoofing it with a valid URL can make the request appear trusted.

```python
import requests
 
headers = {
    'Referer': 'https://trusted-website.com'
}
 
response = requests.get('http://httpbin.org/headers', headers=headers)
 
print(response.status_code)
print(response.text)
```

#### Accept Headers

`Accept` headers specify which content types a client can handle. Mimicking a real browser's detailed `Accept` headers can help avoid detection:

```python
import requests
 
headers = {
    'Accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8'
}
 
response = requests.get('http://httpbin.org/headers', headers=headers)
 
print(response.status_code)
print(response.text)
```

Cloudflare also checks for header mismatches and outdated headers. For instance, using a Firefox user agent with `Sec-CH-UA-Full-Version-List` can result in a block, as Firefox doesn’t support it.

### Implementing CAPTCHA-Solving Services

Cloudflare may show a CAPTCHA to suspicious clients when other detection methods fall short. Its Turnstile system runs lightweight, non-interactive challenges but can switch to interactive CAPTCHAs that challenge scrapers. Additionally, many services employ human solvers to bypass these CAPTCHAs. Read our article about [the best CAPTCHA Solvers]( https://brightdata.com/blog/web-data/best-captcha-solvers) to find the perfect service for your use case. 

### Using a Fortified Headless Browser

To bypass Cloudflare’s JavaScript challenges, your scraper must mimic real browser behavior—executing JavaScript, handling cookies, and simulating actions like scrolling, mouse movements, and clicks. Tools like [Selenium](https://www.selenium.dev/) can do this, but [headless browsers](https://brightdata.com/blog/web-data/best-headless-browsers) often reveal themselves (e.g., via `navigator.webdriver`). Plugins such as [undetected_chromedriver](https://github.com/ultrafunkamsterdam/undetected-chromedriver) and [puppeteer-extra-plugin-stealth](https://brightdata.com/blog/how-tos/avoid-getting-blocked-with-puppeteer-stealth) help mask these traits.

Below is an example using undetected_chromedriver:

```python
import undetected_chromedriver.v2 as uc
driver = uc.Chrome()
with driver:
    driver.get('https://example.com')
```

You can pair the headless browser with [a high-quality proxy service](https://brightdata.com/proxy-types) to more it resilient against Cloudflare:

```python
chrome_options = uc.ChromeOptions()

proxy_options = {
    'proxy': {
        'http': 'HTTP_PROXY_URL',
        'https': 'HTTPS_PROXY_URL'
    }
}

driver = uc.Chrome(
    options=chrome_options,
    seleniumwire_options=proxy_options
)
```

Browsers update often, exposing headless signatures, and Cloudflare’s evolving algorithms can exploit these. Consequently, plugins must be regularly maintained or they may stop working.

### Using Cloudflare Solvers

Dedicated [Cloudflare solver services](https://github.com/luminati-io/cloudflare-captcha-solver) can bypass basic protections temporarily. For example, cloudscraper uses a JavaScript engine to simulate browser support, though its outdated updates may reduce its effectiveness.

### Advanced Techniques

Cloudflare employs multiple bot detection methods, so no single technique is enough. Instead, combine approaches to mimic a real user. For example, use a fortified headless browser, simulate human mouse movements (e.g., [B-spline curves](https://stackoverflow.com/a/48690652)), rotate residential proxies to avoid IP bans, and use tools like [Hazetunnel](https://github.com/daijro/hazetunnel) to replicate genuine browser fingerprints. Adding a CAPTCHA solver further enhances your chances of bypassing Cloudflare's defenses.

## Incorporating Bright Data Solutions

[Bright Data’s Web Unlocker](https://github.com/luminati-io/web-unlocker-api) simplifies bypassing Cloudflare’s bot detection by using AI to overcome anti-bot measures (like browser fingerprinting, CAPTCHA solving, IP rotation, and request retries) with a 99.99% success rate. It automatically selects the best proxies and provides simple credentials, allowing you to use it like any standard proxy server. You can use it like any other proxy server:

```python
import requests


host = 'brd.superproxy.io'
port = 22225

username = 'brd-customer-<customer_id>-zone-<zone_name>'
password = '<zone_password>'

proxy_url = f'http://{username}:{password}@{host}:{port}'

proxies = {
    'http': proxy_url,
    'https': proxy_url
}


url = "http://lumtest.com/myip.json"
response = requests.get(url, proxies=proxies)
print(response.json())
```

[Bright Data's Scraping Browser](https://github.com/luminati-io/scraping-browser) bypasses Cloudflare by running your code on a remote browser that uses multiple proxies to unlock sites. It integrates with [Puppeteer](https://brightdata.com/products/scraping-browser/puppeteer), [Selenium](https://brightdata.com/products/scraping-browser/selenium), and [Playwright](https://brightdata.com/products/scraping-browser/playwright), offering a full headless experience.

## Conclusion

Evading Cloudflare can be complicated, and it comes with varying degrees of success. Instead of duct-taping a solution, consider using [Bright Data’s offerings](https://brightdata.com/products), such as the Web Unlocker, Scraping Browser, and Web Scraper API. With only a few lines of code, you get a higher rate of success without needing to worry about managing complex solutions.

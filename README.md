# Core--Tester
Tool #22/100 - Fast CORS Misconfiguration Tester for Bug Bounty. Detects Origin reflection + Credentials True vulnerability.
#!/usr/bin/env python3
import requests
import sys
from urllib.parse import urlparse

# Colors
R = "\033[91m"
G = "\033[92m"
Y = "\033[93m"
W = "\033[0m"

def check_cors(url):
    if not url.startswith("http"):
        url = "https://" + url

    headers = {
        "Origin": "https://evil.com",
        "User-Agent": "cors-tester by Sourav"
    }
    try:
        r = requests.get(url, headers=headers, timeout=8, verify=False)
        acao = r.headers.get("Access-Control-Allow-Origin", "")
        acac = r.headers.get("Access-Control-Allow-Credentials", "")

        print(f"\n{Y}[*] Testing: {url}{W}")
        print(f" A-C-A-Origin: {acao if acao else 'Not Set'}")
        print(f" A-C-A-Credentials: {acac if acac else 'Not Set'}")

        if acao == "https://evil.com" or acao == "*":
            if acac.lower() == "true":
                print(f" {R}[VULNERABLE] -> CORS misconfig with Credentials True!{W}")
            else:
                print(f" {R}[POTENTIALLY VULN] -> Origin Reflected: {acao}{W}")
        else:
            print(f" {G}[SAFE] -> Not Vulnerable{W}")

    except Exception as e:
        print(f"{R}[-] Error {url}: {e}{W}")

def main():
    print(f"{G}=== CORS Tester - Bug Bounty Tool #22 ==={W}")
    if len(sys.argv) < 2:
        print("Usage: python3 cors-tester.py <url> OR python3 cors-tester.py -f urls.txt")
        return

    if sys.argv[1] == "-f":
        with open(sys.argv[2]) as f:
            for line in f:
                check_cors(line.strip())
    else:
        check_cors(sys.argv[1])

if __name__ == "__main__":
    main()

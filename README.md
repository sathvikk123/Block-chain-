# create_gist.py
import os
import json
import requests
import sys

# Usage: python create_gist.py SimpleToken_Assignment.md "Solidity Assignment"
# Requires: set env var GITHUB_TOKEN to a personal access token (classic) with "gist" scope.

API_URL = "https://api.github.com/gists"

def main():
    if len(sys.argv) < 2:
        print("Usage: python create_gist.py <path_to_md> [gist_description]")
        sys.exit(1)

    file_path = sys.argv[1]
    desc = sys.argv[2] if len(sys.argv) > 2 else "Solidity Assignment"

    token = os.getenv("GITHUB_TOKEN")
    if not token:
        print("Error: Please set GITHUB_TOKEN environment variable (token needs 'gist' scope).")
        sys.exit(1)

    with open(file_path, "r", encoding="utf-8") as f:
        content = f.read()

    payload = {
        "description": desc,
        "public": True,
        "files": {
            os.path.basename(file_path): {"content": content}
        }
    }

    headers = {"Authorization": f"token {token}",
               "Accept": "application/vnd.github+json"}

    resp = requests.post(API_URL, headers=headers, data=json.dumps(payload))
    if resp.status_code in (200, 201):
        data = resp.json()
        print("Gist URL:", data["html_url"])        # Share this
        print("Raw file URL:", list(data["files"].values())[0]["raw_url"])
    else:
        print("Failed:", resp.status_code, resp.text)

if __name__ == "__main__":
    main()

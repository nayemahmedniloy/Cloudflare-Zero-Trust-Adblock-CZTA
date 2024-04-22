# Cloudflare Zero Trust Adblock (CZTA)

![Cloudflare Zero Trust Adblock (CZTA)](https://github.com/nayemahmedniloy/ReactNativeSetup/assets/71997569/13b655bd-97c4-4565-8efb-5cf9733ce164)

Cloudflare Zero Trust Gateway allows it's user to create custom rules to filter HTTP, DNS, and network traffic based on your firewall policies. This is a collection of scripts that can be used to get a similar experience as if you were using Pi-hole to block annoying Ad's, but with Cloudflare Zero Trust Gateway. No need to maintain servers buy a Raspberry Pi!

## About the individual scripts

- `cf_list_delete.js` - Deletes all lists created by CZTA from Cloudflare Zero Trust Gateway. This is useful for subsequent runs.
- `cf_list_create.js` - Takes a blocklist.txt file containing domains and creates lists in Cloudflare Zero Trust Gateway
- `cf_gateway_rule_create.js` - Creates a Cloudflare Zero Trust Gateway rule to block all traffic if it matches the lists created by CZTA.
- `cf_gateway_rule_delete.js` - Deletes the Cloudflare Zero Trust Gateway rule created by CZTA. Useful for subsequent runs.
- `download_lists.js` - Initiates blocklist and whitelist download.

## Features

- Support for basic hosts files
- Full support for domain lists
- Automatically cleans up filter lists: removes duplicates, invalid domains, comments and more
- Works fully unattended
- Whitelist support, allowing you to prevent false positives and breakage by forcing trusted domains to always be unblocked.
- Optional health check: Sends a ping request ensuring continuous monitoring and alerting for the workflow execution.

## Usage 

### Run in GitHub Actions

These scripts has to be run using GitHub Actions so your filters will be automatically updated and pushed to Cloudflare Zero Trust Gateway. This is useful if you are using a frequently updated malware & adblocker's blocklist.

Please note that the GitHub Action downloads the recommended blocklists and whitelist by default. You can change this behavior by editing the files.

1. Create a new public/private repository. You can also fork this repo - although the script never leaks your API keys and GitHub Actions secrets are automatically redacted from the logs, it's better to be safe than sorry.
2. Go to your newly opened repository and from the top bar go to 'Settings'. Then from the left side bar and open drop down menu of 'Secrets and variables' and select 'Actions' and create the following 'New repository secret':

- `CLOUDFLARE_API_KEY`: Your Cloudflare API key. Collect it from: Cloudflare dashboard -> My profile -> API Tokens -> Global API key -> View
- `CLOUDFLARE_ACCOUNT_ID`: Your Cloudflare account ID. Collect it from: Cloudflare dashboard -> Workers & Pages -> Create worker -> Account ID (After creating the worker)
- `CLOUDFLARE_ACCOUNT_EMAIL`: Your Cloudflare account email.
- `CLOUDFLARE_LIST_ITEM_LIMIT`: The maximum number of blocked domains allowed for your Cloudflare Zero Trust plan. Use 300000 for the free plan.
- `PING_URL`: (Optional) The HTTP(S) URL to ping (using curl) after the GitHub Action has successfully updated your filters. Useful for monitoring.

3. (Optional) Create the following GitHub Actions variables in your repository settings if you desire:

- `FAST_MODE`: Enable the scripts to send the requests simultaneously. Beware that there's a rate limit of 1200 requests per five minutes (https://developers.cloudflare.com/fundamentals/api/reference/limits/) so make sure you know what you are doing.
- `ALLOWLIST_URLS`: Uses your own allowlists. One URL per line. Recommended allowlists will be used if this variable is not provided.
- `BLOCKLIST_URLS`: Uses your own blocklists. One URL per line. Recommended blocklists will be used if this variable is not provided.

4. Now go the 'Actions' from the top bar and select 'Update Filter Lists' under workflows.
5. Now select 'Run workflow' and again select 'Run workflow' from the pop up windows.
6. It will take upto 7-8 minutes so wait & after it's complete you can check 'the CZTA Filter List, from: Cloudflare dashboard -> Zero Trust -> Gateway -> Firewall Policies.

### DNS setup for Cloudflare Gateway

Now it's time to setup the DNS in your router or devices.

1. To get the DNS addresses go to: Cloudflare dashboard -> Zero Trust -> Gateway -> DNS Locations. Now add a new location or configure your existing location (make it default loaction) and you will get unique IPv6 DNS address and following IPv4 DNS addresses '172.64.36.1' & '172.64.36.2'. Remeber to add your IPv4 IP address to the DNS Location settings or it might not work if you don't have IPv6.
2. Configure your router or device based on the provided DNS addresses.
3. You can also get 'DNS over TLS' & 'DNS over HTTPS' to use on your devices. You can also use Zero trust enabled Warp Client for mobile and PC.

### Dry runs

To see if e.g. your filter lists are valid without actually changing anything in your Cloudflare account, you can set the `DRY_RUN` environment variable to 1, either in `.env` or the regular way. This will only print info such as the lists that would be created or the amount of duplicate domains to the console.

**Warning:** This currently only works for `cf_list_create.js`.

## License

MIT License. See `LICENSE` for more information.

## Credit

This repository modified from source `kayosotis/lilac-gateway-pihole`.


# Active Directory and DNS Troubleshooting

I think one of the most valuable parts of this project was troubleshooting problems with Active Directory-integrated DNS during the initial domain deployment.

## Initial Problem

The domain was originally deployed as `corp.example`.

After promoting `LAB-DC01` to a domain controller, Active Directory diagnostics reported connectivity problems and the expected DNS zones were not being created correctly.

Symptoms included:

- `dcdiag` connectivity failures
- Missing expected Active Directory DNS zones
- Failure to resolve domain controller records
- DNS zone creation errors
- The Windows 11 client initially being unable to resolve the domain correctly

## Investigation

I used several tools to investigate the problem, including:

- `dcdiag`
- `dcdiag /test:dns`
- `Get-DnsServerZone`
- `Get-DnsClientServerAddress`
- `nslookup`
- DNS Manager
- Windows Event Viewer

During troubleshooting, I verified the server's network configuration, inspected the available DNS zones, tested DNS resolution, and reviewed diagnostic output.

I was able to create a test `.local` DNS zone successfully while attempts involving the original namespace continued to fail.

Rather than continue building the lab on an unreliable DNS configuration, I decided to rebuild the domain using `corp.local`.

## Rebuild and DNS Configuration

I demoted the domain controller and rebuilt the forest as:

`corp.local`

After promotion, the expected Active Directory-integrated DNS zones were created successfully, including:

- `corp.local`
- `_msdcs.corp.local`

I also discovered that the domain controller's DNS client configuration was using the loopback address `127.0.0.1`.

I changed the DNS server address on the domain controller to its static address:

`10.0.0.10`

I then refreshed DNS registration and restarted Netlogon before testing the environment again.

## Validation

After the changes, the domain controller successfully passed the Active Directory DNS diagnostic:

`dcdiag /test:dns`

The final test reported:

- `LAB-DC01 passed test Connectivity`
- `LAB-DC01 passed test DNS`
- `corp.local passed test DNS`

![Successful DNS diagnostic](../screenshots/08-dcdiag-dns-health.png)

The Windows 11 client was also able to resolve the domain and domain controller and successfully join `corp.local`.

## Additional Diagnostic Findings

A full `dcdiag` later reported warnings related to Windows Time and historical system log events.

Because this lab uses a single domain controller on an isolated network without Internet access or an upstream time source, I documented these findings rather than expanding the project infrastructure solely to eliminate non-critical lab-specific warnings.

The core Active Directory and DNS functionality was validated separately with `dcdiag /test:dns`.

## Takeaways

This troubleshooting process reinforced several important lessons:

- Active Directory depends heavily on correctly functioning DNS.
- Diagnostic tools such as `dcdiag` can help narrow down domain health problems.
- DNS client configuration on a domain controller should be verified during troubleshooting.
- A successful configuration should be validated rather than assumed to be working.
- Rebuilding a small lab can be more effective than continuing to troubleshoot a fundamentally unreliable configuration.
- Troubleshooting documentation should distinguish between observed symptoms and a root cause that has been conclusively proven.

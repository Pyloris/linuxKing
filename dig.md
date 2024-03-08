## Dig Command
> This command is used to find DNS records.

```shell
# command format
dig @<dns_ip> <domain> <record_type>

# find A(ipv4) record of facebook.com
dig facebook.com A
# find AAAA(ipv6) record of google.com from cloudflare dns
dig @1.1.1.1 facebook.com AAAA

# types of records
# - A > ipv4
# - AAAA > ipv6
# - CNAME > canonical names
# - MX > mail servers
# - SOA > Start of Authority
# - TXT > TXT records
```
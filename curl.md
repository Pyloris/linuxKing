## CURL Command
> It is used to transfer files over a network. Supports Authentication , Encryption & it can also resume interrupted Transfers.


```shell
# download files while keeping original name
curl -O https://hacker.com/pdf1.pdf -O https://hacker.com/pdf2.pdf
# save file to a custom path
curl https://hacker.com/file.pdf -o /home/shoaib/mypdf.pdf
# to get a progress bar as the download progresses use '-#'
curl https://domain.com/file.pdf -#

# use proxy
curl -x "http://shoaib:paswd@198.8.8.1:8080" https://domain.com/file.pdf -o ./file1

# POST request
curl -X POST https://domain.com/login --data-urlencode username="shoaib" --data-urlencode password="itsme" -d key="hello"

# upload a file to HTTP server
curl --upload-file /path/to/file http://ip:port/

# upload to FTP server in anonymous mode
curl -T /home/file ftp://addr/
# provide credentials
curl -u username:pass -T /path/to/file ftp://ip
```
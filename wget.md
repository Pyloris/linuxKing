## Wget Command
> Used to download files over HTTP, HTTPs and FTP.

```shell
# download a file to current directory
wget https://download.com/helo.txt
# turn off the output
wget -q https://.../
# save with a different name
wget -O my.txt https://download.com/helo.txt
# save to a specific directory with default name
wget -P /tmp/files https://download.com/helo.txt
# limit the download speed [1k, 1m, 1g]
wget --limit-rate=1m https://.../
# resume a download
wget -c https://microsoft.com/win10.iso
# run in background, output will be redirected to
# wget-log file in current directory
wget -b https://.../

# change user agent, as some website may block wget user agent
wget --user-agent="Mozilla ....." https://blocked.com/site.pdf

# download multiple files from links stored in a text file
wget -i files.txt

# read urls from STDIN
wget -i - <(echo "https://google.com/")

# download files from FTP server
wget --ftp-user=FTP_USERNAME --ftp-password=FTP_PASSWORD ftp://ftp.example.com/filename.tar.gz
```
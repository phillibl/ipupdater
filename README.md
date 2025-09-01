# ipupdater
Image runs a script once then exits, used for notifying an ip change


Compose
```
services:
  ipupdate:
    container_name: ipupdate
    image: ghcr.io/phillibl/ipupdate:latest
    volumes:
      - /path/to/scripts:/scripts
    restart: no
```

Script for sending pushover notification
```
#!/bin/sh

PO_TOKEN=#pushover token#
PO_USER=#pushover user#
OLDIP=$(cat /scripts/oldip)
NEWIP=$(curl http://canhazip.com)

if [ "$OLDIP" != "$NEWIP" ]
then
	echo $OLDIP $NEWIP

		rm /scripts/oldip
		echo $NEWIP >> /scripts/oldip
		echo "IP UPDATED" $NEWIP
		curl -s --form-string "token=$PO_TOKEN" --form-string "user=$PO_USER" --form-string "message=IP UPDATED $NEWIP" https://api.pushover.net/1/messages.json

else
	echo "NO UPDATE NEEDED"
fi

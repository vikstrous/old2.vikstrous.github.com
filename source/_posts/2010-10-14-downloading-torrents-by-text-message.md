---
layout: post
title: "Downloading torrents by text message"
date: 2010-10-14 02:10
comments: true
categories: 
---


Inspiration
---

I was often stranded on a bus or somewhere else without wifi and wished I could start a torrent download before I forget to do it. I needed a way to use my cell phone to do it without having access to the internet. Twitter is the best place I could think of which will provide me with a simple way of transcending from the world of SMS into the world of interwebz. So I wrote a little python script that connects twitter, demonoid.com and a torrent server.

Typical use case

1. You need a torrent, let's say "Paintings of Van Gogh Collection"
1. You make sure your cell phone is connected to your twitter accounts so you can tweet through text messages
1. You text twitter with "@torrenter download Paintings of Van Gogh Collection"
1. The torrent server runs this python script periodically, checking for new messages directed at its twitter account (or any twitter account I guess)
1. The torrent server sees a new request, searches demonoid for the most seeded torrent that is relevant and starts downloading it

The setup
---

(It's assumed that you have a linux based server you want to control through your cell phone)

1. Make a twitter account for your torrenter
1. Set up your phone to tweet through text messages
1. Put the python script below on your torrent server
1. Configure the variables inside the script (demonoid username and password, download path and twitter username for the torrenter)
1. Set up cron to run it periodically
1. Set up your torrent client to load torrent files from the folder you chose to download the torrents to in the config

Edit: Demonoid seems to have changed their domain to demonoid.me, so if anyone wants to try this, see if just changing the domain will work. If not I guess you'll have to figure it out yourself.

``` python
import urllib, urllib2, json, re, cookielib
urllib2.install_opener(urllib2.build_opener(urllib2.HTTPCookieProcessor(cookielib.CookieJar())))
 
demonoid_user = ''
demonoid_password = ''
save_folder = ''
torrenter_twitter = ''
 
def torrenter_download(command):
    q = ' '.join(command[2:])
    listing = urllib2.urlopen('http://www.demonoid.com/files/?'+urllib.urlencode({
        'query' : q,
        'category' : 0,
        'subcategory' : 'All',
        'seeded' : 0,
        'external' : 2,
        'uid':0,
        'sort':'S',
    }))
    torrent = re.findall(r'<a href="([/a-zA-Z0-9]+)"><img src="/images/downbg.gif"', listing.read())[0]
    download = open(save_folder + q + '_' + str(torrent.rsplit('/')[4]) + '.torrent', 'wb')
    download.write(
        urllib2.urlopen('http://www.demonoid.com/account_handler.php',
            urllib.urlencode({
                'nickname' : demonoid_user,
                'password' : demonoid_password,
                'returnpath' : torrent
                })
        ).read()
    )
    download.close()
     
try:
    f = open(save_folder+'last_id.txt', 'r')
    last_id = f.read()
except:
    last_id = ''
     
results = json.loads(
    urllib2.urlopen('http://search.twitter.com/search.json', 
        urllib.urlencode({
            'q' : '@'+torrenter_twitter,
            'rpp' : 10,
            'result_type':'recent',
            'since_id':str(last_id)
        })
    ).read()
)['results']
 
for req in results:
    command = req['text'].rsplit(' ')
    commands = {
        'download' : torrenter_download,
    }
    try:
        commands[command[1]](command)
    except:
        pass
         
if len(results) > 0:
    f = open(save_folder+'last_id.txt', 'w')
    f.write(str(results[0]['id']))
    
```

---
layout: post
title: "Automating a PC to behave as a TV"
date: 2010-12-24 17:15
comments: true
categories: 
---


Background
---

My Bulgarian grandparents had nothing to do all day after moving in with us, so my dad bought a subscription to watch Bulgarian TV over the internet. However, my grandparents are very old and can't use computers at all - you can't just say "oh just double click it" because they can't even move the mouse effectively. So I decided to see if I can make it possible to connect the PC to the TV and automate it so that they don't have to do anything other than turn it on.

First attempt
---

The connection was simple - the TV had a VGA port and an audio input so I just connected it like a monitor and speakers.

Automating the boot sequence was a bit more complicated, still easy. I initially set it up like this:

1. The PC turns on
1. Chrome turns on and opens the page with an embedded video
1. Chrome is redirected to the login page
1. LastPass automatically logs in
1. Chrome is back to the video and loads it
1. An autoit script that was run on startup slept 20 seconds from start up and now double clicks the video to make it full screen
1. Tada!

This wasn't enough for me though. They could only watch one channel, but there were 22 others that the site offered. It would be a waste of money to pay for all of them and not be able to watch them. So I looked into how I can make autoit change channels.

Second attempt
---

I realized that autoit can be used for a lot more than double clicking, so I embarked on a 3 hour journey into the mess and madness that is autoit scripting. It seems similar to Visual Basic, but then again, I've never used visual basic. Initially I started writing some really barbaric code, but as time went on I got pretty disgusted with my code and made it more compact and rebust. Initially I tried making autoit trigger on regular key presses of the buttons 0-9, q-p, a-s. That ended badly because the events triggered by these buttons had to type in the url of the next video. That triggered more events and it got into an infinite loop. Apparently there are ways around it, but I didn't find a reliable one and ended up using ctrl+button rather than just the buttons.

Now I had a way to choose one of the 22 channels by just pressing ctrl + the number or letter! I showed it to my grandparents in that state and my grandfather refused to touch it and my grandmother was confused about how to hold ctrl. So, I needed a mechanism like on the remote to change go to the next or previous channel. I assigned the INSERT and HOME keys to do that because they were beside each other and the labels didn't mean anything for my grandparents. My grandmother seemed to hold the button for too long when she presses it so I had to make sure it doesn't trigger again. I think that worked relatively well. At the end I had a pretty good script that works on a few assumptions:

* LastPass is set up to log in automatically
* chrome is used
* the title of the page is "www.djTacho.com"
* the urls of the channels won't change
* the wireless turns on as soon as the pc turns on
* chrome is the default browser
* chrome is always full screen
* buttons are not pressed too fast one after the other

It seems like a long list now that I wrote it out. I'm considering making a JavaScript page that uses an iframe to load the video, which will significantly reduce the chances of the whole thing breaking. I'll still need the autoit script to make it full screen though.

This is the final script:

```
Local $channels[128]
;These correspond to the ascii codes of their shortcuts
$channels[48] = "DIEMA";0
$channels[49] = "";1
$channels[50] = "NOVA";2
$channels[51] = "BTV";3
$channels[52] = "PROBG";4
$channels[53] = "KANAL3";5
$channels[54] = "PLANETA";6
$channels[55] = "DIEMA2";7
$channels[56] = "DIEMAFAMILY";8
$channels[57] = "FOXLIFE";9
$channels[113] = "RINGBG";q
$channels[119] = "TV7";w
$channels[101] = "BTVCOMEDY";e
$channels[114] = "NG";r
$channels[116] = "EUROSPORT";t
$channels[121] = "BTVCINEMA";y
$channels[117] = "TVPLUS";u
$channels[105] = "NOVASPORT";i
$channels[111] = "for-kids";o
$channels[112] = "filmi";p
$channels[97] = "chalga";a
$channels[115] = "pop";s
Local $bound
Local $url = "http://www.djtacho.com/members/tv.php?id="
Local $default_channel = '1'
Local $keys[22] = ['1','2','3','4','5','6','7','8','9','0','q','w','e','r','t','y','u','i','o','p','a','s']
Local $current = $default_channel
Local $pause = False


;wait for the page to open
Run(@Comspec & " /c start " & $url & $channels[Asc($default_channel)])
WinWaitActive("www.djTacho.com - Google Chrome")
Sleep(3000)
ToggleFullscreen()

BindHotKeys()

Func NextChannel()
  ;this stop working at the end anyways so that's good enough
  $found = False
  For $key In $keys
    If $found Then
      ChangeToChannel($key)
      $found = False
      ExitLoop
    EndIf
    If $key == $current Then
      $found = True
    EndIf
  Next
  If $found Then
    ChangeToChannel($keys[0])
  EndIf
EndFunc

Func PrevChannel()
  If $current <> $keys[0] Then ;stop at the beginning
    $last = $keys[0]
    For $key In $keys
      If $key == $current Then
        ChangeToChannel($last)
        ExitLoop
      EndIf
      $last = $key
    Next
  Else
    ChangeToChannel($keys[21])
  EndIf
EndFunc

Func ProcessKey()
  ChangeToChannel(StringTrimLeft(@HotKeyPressed, 1))
EndFunc

Func ToggleFullscreen()
  MouseClick("left", 250, 250)
  MouseClick("left", 250, 250)
EndFunc

Func GoToURL($url)
  MouseClick("left", 700, 700)
  Send("{F6}")
  Send($url)
  Send("{ENTER}")
EndFunc

Func ChangeToChannel($channel)
  If Not $pause Then
    $pause = True
    $current = $channel
    ToggleFullscreen()
    GoToURL($url & $channels[Asc($channel)])
    Sleep(2000)
    ToggleFullscreen();on
    $pause = False
  EndIf
EndFunc

Func BindHotKeys()
  For $key in $keys 
    HotKeySet("^" & $key, "ProcessKey")
  Next
  HotKeySet("{HOME}", "NextChannel")
  HotKeySet("{INSERT}", "PrevChannel")
  HotKeySet("{SPACE}", "ToggleFullscreen")
  HotKeySet("{PAUSE}", "ToggleBindings")
  $bound = True
EndFunc

Func UnbindHotKeys()
  For $key in $keys 
    HotKeySet("^" & $key)
  Next
  HotKeySet("{INSERT}")
  HotKeySet("{HOME}")
  HotKeySet("{SPACE}")
  $bound = False
EndFunc

Func ToggleBindings()
  If $bound Then
    UnbindHotKeys()
  Else
    BindHotKeys()
  EndIf
EndFunc

While 1
  ;
WEnd
```

PS: I made spacebar toggle fullscreen. That way if something breaks, they can try to fix it by pressing space. Somehow I doubt they'll remember that, but it's worth a try.

PPS: If anyone knows how to get the length of an array, please tell me. Then I wouldn't have to hardcode "21" on line 68.

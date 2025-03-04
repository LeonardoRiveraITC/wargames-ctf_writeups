A very straightforward machine

![[dh_ktx.png.png]]

When you create a note it gets stored, and when you download a note it gets downloaded

The download note retrieves files stored in the machine, including those created by us

Given that there are two main functionalities on this app, we can try two things

1. Malicious file uploads
2. Local file inclusion

### Malicious file upload

Gathering some information, we see that this app is built on python
![[dh_ktx_2.png.png]]

But when trying to enumerate, it seems that the only availabe routes are
/
/save
/download

So we have no way of directly accessing the uploaded files

![[dh_ktx3_3.png.png]]

### LFI
When trying a very standard ../../../../etc/shadow we are greeted with the shadow file itself

![[dh_ktx_4.png.png]]


So all we have to do now is look for the flag in the filesystem. Which is located under the parent directory

../flag gives us the flag

![[dh_ktx_5.png.png]]


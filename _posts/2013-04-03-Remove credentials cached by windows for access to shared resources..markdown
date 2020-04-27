---
title:  "Remove credentials cached by windows for access to shared resources."
date:   2013-04-03 15:25:31 +0100
categories:
  - Windows
tags:
  - Credentials
---

Today I was connected to the NAS that I have at home for the first time since my new installation of Windows 8 so I have access to it through the file explorer
He asked me for the user credentials which Windows always saves once the user successfully accesses the resource even if you don't have the save check activated.

Well, as you normally had them slapped in Windows 7 do not investigate how to delete them to access with another user, until today when I skip the credentials request, enter with another NAS user who has less privileges to try it. What happens when you want to enter a folder of my user when you have entered with another, that Windows asks you for the user's credentials and when you enter them you throw this error:

"Multiple connections for a server or user-compatible share, using more than one username, are not allowed. Disconnect previous connections to the server or shares and be new again."

It tells us that it does not allow us to access the owner of that resource again, because we already have the credentials of the previous user saved and we cannot access the new one, which entertains me in fighting a little today to see how to release
these credentials so that I can access with my usual user, that is to say that I would ask for the credentials when I access the NAS.

The solution to avoid having to restart and disconnect network drives:

Just open a command line and see what resources we have busy, with "network use", and then delete them with "net use \\shared resource /del"
where the shared resource is the address that has shown us the "net use"

Well, very simple, no, now I have no excuse to try different logins of the shared resources that I configure.

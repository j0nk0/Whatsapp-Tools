#==# Whatsapp-Tools #==#
Different tools for whatsapp. E.g decrypting messages



#Small tool to display chats from the Android msgstore.db database (crypt12)
    https://github.com/andreas-mausch/whatsapp-viewer

#Simple script to decrypt whatsapp conversations
    https://github.com/ivancduran/whatsapp_decrypt

#Analysis of WhatsApp conversations using bash.
    https://github.com/alialghamdi/whatsapp_analysis_bash

#Script to generate Android App to get all WhatsApp media (images, audios, videos)
    https://github.com/thelinuxchoice/whatshack

#Program made for after having made a backdoor attack using android/meterpreter/reverse_tcp as internal payload, make an intrusion into the WhatsApp of the victim.
    https://github.com/abazad/RemoteWA

#WhatsappKey is a bash script just to get the Whatsapp password from 'pw' and 'me' files.
    https://github.com/toespar/WhatsappKey


#===================# Other Useful Links #===================#
    TripCode for crypt12 support https://github.com/EliteAndroidApps/WhatsApp-Crypt12-Decrypter
    Whatsapp Xtract
    pwncrypt5.py https://github.com/aramosf/pwncrypt5/blob/master/pwncrypt5.py
    SQLite
    MD5 http://bobobobo.wordpress.com/2010/10/17/md5-c-implementation/
    UTF8 http://utfcpp.sourceforge.net/
    AES https://polarssl.org/aes-source-code
    Base64 http://www.adp-gmbh.ch/cpp/common/base64.html
    Zlib http://www.zlib.net/
    RapidJSON http://rapidjson.org/
    mbedtls https://github.com/ARMmbed/mbedtls


#===================# Extract WhatsApp Password on Android #===================#
https://stackpointer.io/security/extract-whatsapp-password-android/374/
#===================# How to Extract Unencrypted Whatsapp Chat Messages #===================#
On Android, WhatsApp stores encrypted copies of the chat messages onto the SD card.
These encrypted copies are denoted by the .crypt, .crypt5, .crypt6 and .crypt7 file extensions.
They are generated from the master database file stored in the following location:
    /data/data/com.whatsapp/databases/msgstore.db

The WhatsApp master message store database, msgstore.db, is a SQLite database containing all of the WhatsApp chat messages. This file is not encrypted in any way.

But, without root, accessing any data from the Android /data partition directly except within the application code is not possible. But there’s a loophole – all the application data or files within 
/data/data/com.whatsapp directory can be extracted using the Android ADB Backup feature.

To use the Android ADB Backup feature, your phone must be on at least Android 4.0. You will also need to install Android ADB installed on your computer and USB Debugging must be enabled on your phone.

Extract WhatsApp Application Data
Connect the phone to your computer’s USB port and perform a backup using ADB.

    $adb backup -f whatsapp.ab -noapk com.whatsapp

This will cause a prompt on your phone as shown below. Click on the “Back up my data” button without providing a password.
adb-backup-prompt

Once the backup has been completed, you will see a file called whatsapp.ab on your computer. The .ab extension stands for Android Backup.
To extract files from the file, we will first need to convert it to a tar archive.

    $ dd if=whatsapp.ab ibs=24 skip=1 | openssl zlib -d > whatsapp.tar

Determine the msgstore.db file location within the tar archive.
    $ tar tvf whatsapp.tar | grep msgstore.db
    -rw------- 10188/10188   14960 2014-06-01 14:03 apps/com.whatsapp/db/msgstore.db-journal
    -rw------- 10188/10188 14642176 2014-06-01 14:03 apps/com.whatsapp/db/msgstore.db

The file is at “apps/com.whatsapp/db/msgstore.db“. Extract the file to the current working directory.
    $ tar xf backup.tar apps/com.whatsapp/db/msgstore.db
    $ mv apps/com.whatsapp/db/msgstore.db .

You can now perform queries on the msgstore.db database file using the sqlite3 command.
    $ sqlite3 msgstore.db

WhatsApp has gone to great lengths to come up with new encryption algorithms to encrypt the database messages saved on the SD card. But, I guess they have not thought about this loophole yet.



Project Inception
-----------------

This project is an incomplete software implementation of the Mifare Desfire protocol for HCE on Android.
The app has to be used with the original HCE patch of Doug Yeager introduced in CyanogenMod 9. Most of the
initial code has now been removed from CyanogenMod and thus a system level patch is needed to make the whole work.

* App will only work on phones with PN544 NXP chip (which are HTC One M7, Nexus S, ..)
* A patched NFC.apk will need to be built from the [packages_Nfc_apps repository](https://github.com/jekkos/android_packages_apps_Nfc) I cloned here on github
* A patched Android framework.jar should be built the [android base framework](https://github.com/jekkos/android_frameworks_base)
* libnfc-nxp should contain the needed fixes in CM12 (no changes required)

Standard HCE emulation in Android 4.4 does not give [complete control](http://stackoverflow.com/questions/20055497/emulate-mifare-card-with-android-4-4) over APDUs sent. Desfire has 3 communication modes which are `native`, `wrapped` and `iso14443-4`.

A standard android HCE app would be able to cope with a wrapped protocol implementation after removing the AID routing functionality using an xposed framework library hook. However for my POC I found the hardware to communicate using native commands and thus needed to have full control over APDUs which at that time was only feasible with the original CM HCE patch.

Project state
-------------

The code contains some testcases that verify a couple of different basic communication scenario's with a DesFire card. 
Doug Yeager's patch was ported to Android 5 which enabled a pn544 to communicate with off the shelve NFC physical security system at Capgemini Belgium's premises.
[A talk was given at Capgemini Belgium](https://github.com/jekkos/android-hce-desfire/blob/master/talk/Android%20internals%20-%20Nfc%20stack%20explorations.pptx?raw=true) in 2015 to present the POC.

You can detect a phone as a Mifare Tag by using an libnfc compqatible reader with [patched](https://github.com/nfc-tools/libfreefare/pull/42/files) [libfreefare](https://github.com/jekkos/libfreefare). One working example here includes the mifare get info command, which can request some general info fields from the Android application and show them in a linux terminal. To reproduce this case it's best to use the `pn532-tamashell` binary that comes by default with libfreefare. 

In that case the raw command bytes for DesFire get info can be issued which should normally yield a valid response from the Android app.

```
LIBNFC_LOG_LEVEL=3 cat << EOF | pn53x-tamashell \
4a 01 00 \
40 01 00 a4 04 00 07 d2 76 00 00 85 01 00 00  \
40 01 00 00 60 00 00 00 \
40 01 00 00 AF 00 00 00 \
40 01 00 00 AF 00 00 00 \
EOF
```

References
----------

This project is based on the following work

* [Kevin Valk's thesis](https://github.com/kevinvalk/android-hce-framework) on HCE in Android and his work on porting JavaCard applets to the Android platform
* Original code from an [academic paper](https://securewww.esat.kuleuven.be/cosic/publications/article-2206.pdf) on [porting DesFire to JavaCard](https://github.com/Dansf/java-card-desfire-emulation). This code contains an implementation for the legacy protocols only (non AES)
* Libfreefare code which contains all the newer DesFire protocol implementation details.

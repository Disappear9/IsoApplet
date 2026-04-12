# Fork differences
* Use Java Card 3.0.5
* Support most usecases of off-card hashing ECDSA even on cards that do not have `ALG_NULL` together with `SIG_CIPHER_ECDSA`.
* API features are not probed anymore, they are supplied with install parameters
* Configuration parameters can be supplied with install parameters instead of recompiling

## Install parameters

* Tag `81`: API features, required, length 1, bitwise OR of the following flags:
    * `0x01`: extended APDU support (required)
    * `0x02`: secure random number generator support
    * `0x04`: ECC support
    * `0x08`: RSA-PSS support
    * `0x20`: RSA 4096 bits support
* Tag `82`: configuration, optional, length 1, bitwise OR of the following flags:
    * `0x01`: use global PIN (not implemented yet)
    * `0x02`: allow private key import
    * `0x04`: PUK must be set (mutually exclusive with global PIN usage)

Examples:

* All features, no private key import, PUK optional, no global PIN: `81012F`
* All features, with private key import, PUK optional, no global PIN: `81012F820102`
* All features except RSA 4096, no private key import, PUK optional, no global PIN: `81010F`

On some NXP JCOP 4 from AliExpress (J3R150, J3R180) RSA 4096 is not available in which case you would use the third example above.

# General Information
The Java Card IsoApplet (e.g. for use with OpenSC).
The Applet is capable of saving a PKCS#15 file structure and performing PKI related operations using the private key, such as signing or decrypting.
Private keys can be generated directly on the smart card or imported from the host computer.
The import of private keys is disabled in the default security configuration.
The applet targets modern smartcards with Java Card 3.0.4 or above.

# IsoApplet Version and Smartcard requirements
IsoApplet is maintained in two different versions: one for newer smartcards and a legacy version for older smartcards.
If your smartcard supports the newer version of IsoApplet, you should prefer it.
For both versions, the support of the "requestObjectDeletion()"-mechanism of the Java Card API is recommended to be able to properly delete files.
Also, the javacardx.crypto.Cipher-package needs to be supported by your smart card.
This is very common among Java Card smartcards.

## New version of IsoApplet (v1)
This version is found on the [main branch](https://github.com/philipWendland/IsoApplet/tree/main).
It targets smartcards with Java Card version >= 3.0.5.
This version requires extended APDUs the be used and supported by your reader and smartcard (javacardx.apdu.ExtendedLength).
If supported by your smart card, the newer version of IsoApplet supports the following additional features:
* RSA keys of 4096 bit length
* RSA PSS signatures
* ECDSA with off-card hashing, which makes ECC actually usable in practice

# Build process
This project uses [ant-javacard](https://github.com/martinpaljak/ant-javacard) to build cap-files.
After cloning the IsoApplet repository, all you have to do is:
* Perform `git submodule init && git submodule update` to retrieve the Java Card SDKs, in case you did not `git clone --recursive` to clone this repository.
* Install Apache `ant`, `openjdk-17-jdk-headless`
* If needed, configure the correct Java SDK version or set the JAVA_HOME environment variable.
* Invoke `ant` to produce the cap file.
If you have build errors on an existing repository, try deleting the ant-javacard.jar file so that the newest version is downloaded.

# Installation
Install the CAP-file (IsoApplet.cap) to your Java Card smart card (e.g. with [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro)).

**Have a look at the wiki for more information:** https://github.com/philipWendland/IsoApplet/wiki


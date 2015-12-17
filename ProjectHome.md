Grey Goo is a tiny, available and reliable remote command execution server and client designed purely for emergency situations.

Typically, you would use it to diagnose an unresponsive machine, reboot it or restart OpenSSH.

The features include:

  * It is extremely memory efficient (no explicit memory allocation after initialization)
  * It supports public-key based client authentication and packet integrity (but not encryption).
  * The server doesn't need any external library other than a libc. It can rely on a tiny, custom crypto library. The daemon is roughly 50kb in total.
  * It relies on pre-forked and pre-initialized workers (process, not threads!) for maximum reliability and availability.
  * It is self contained (one executable file, no configuration file, no dependencies on the file system once running)
  * It uses PBKDF2 for slow key derivation
  * It supports embedded commands (reboot, remote sysrq etc..)

Grey Goo is currently only available for Linux 2.6

Make sure you read the [README](https://code.google.com/p/greygoo/source/browse/README) file, the [PROTOCOL](https://code.google.com/p/greygoo/source/browse/PROTOCOL) file and the [FAQ](FAQ.md).

Feel free to [contact us](mailto:greygoo-dev@googlegroups.com).
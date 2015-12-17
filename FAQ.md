

## How can I make static binaries ? ##
Uncomment `COMPILE_STATICALLY=1` in the Makefile.

## Is it a good idea to make static binaries? ##

It's a trade-off. The good thing with static binaries is that if the libc gets updated with a buggy version, a static Grey Goo server wouldn't be affected when it's restarted (the client would though).
For everything else, using a shared libc is much better:
  1. First, modern Glibc is not really designed to be used as static libraries anymore. Some functionality (like dns resolving) only exists in shared libraries and if you link a program as static, a small dynamic loader stub will be link-ed in statically. The problem is that if the shared libc gets updated, the dynamic loader stub and the new shared libc may end-up with incompatible ABI.
  1. Second, there is something nice about having a **shared** libc. The code pages of this library will end-up being shared by all processes (more precisely, the code pages without relocations). This is a good thing because the pages will most likely have actual page frames in memory (and not depend on on-demand loading).
  1. Furthermore, to be more reliable, Grey Goo mlock() all of its current memory after starting. If the libc's page frames are shared among many processes, this creates little overhead (Grey Goo itself being tiny). But if Grey Goo is statically linked, it becomes a big 500kb blob and mlock() wastes memory.
  1. In contrary to some belief, this does not create a new "external dependency" at run-time. Once the program is loaded, the library is mmaped and even deleting the file would have no effect. The difference is at load-time, but if the libc is completely hosed, nothing will start anyway.

## I have generated keys and tried to process them with OpenSSL but it doesn't work. Why ? ##

The private keys are not encrypted directly with the password you provide, but with a password that is slowly derived from the one you provide.

## What if I _really_ need to process those keys? ##
"make tools/gg-keygen && tools/gg-keygen -t" can convert your password. Manipulating the password could result in it being leaked to swap or to files on the disk more easily.

## Can I test/use Grey Goo without root privileges? ##
Absolutely. The one thing that may not work is creating the default PID file in /var/run, so use something like "./ggd -i $(mktemp)" for a quick test. There are a couple of features that won't work without some privileges, such as the tuning of the OOM score or the priority. See the next question.

## I see "Grey Goo could not become bullet proof. Continuing anyway." in my logs. Why ? ##
Most likely, you ran Grey Goo without root privileges. This is fine but Grey Goo makes use of certain privileges, traditionally associated with root accounts to rise its priority and adjust its OOM score.

## How can I make Grey Goo more resistant to a fork bomb or an otherwise unresponsive machine? ##
For now, the best is to raise its priority (-n). Ideally you would run Grey Goo with "-n -20". Since Grey Goo doesn't do anything else than wait for connections or for workers to die, it won't actually use any CPU while idle.

## Where does the name come from ? ##
An [end of the world scenario involving tiny things](http://en.wikipedia.org/wiki/Grey_goo).

## What operating systems are suppored ? ##
Only Linux 2.6 is supported. Grey Goo uses many things that are Linux specific, but it would be possible to make a portable version without too much effort. Perhaps the biggest change would be switching from ppoll() to pselect() or something else.
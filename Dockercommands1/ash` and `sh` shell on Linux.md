##### difference between `ash` and `sh` shell on Linux?
``````sh
# On current Linux systems, including in containers, there are three common scenarios:

sh is Bash (in sh mode) — this will always be larger and more featureful than ash

sh is dash (Debian derivatives) — in such systems, installing ash will result in a symlink from ash to dash, so ash and sh have the same size

sh is Busybox — the specifics will depend on the options used when building Busybox, but by default now Busybox sh is ash, so ash and sh have the same size
``````

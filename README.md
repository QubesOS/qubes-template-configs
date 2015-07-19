Configuration files used to build Qubes templates
=================================================

This repository contains Qubes Builder configuration files used to build
template packages. 

Directory layout
----------------

1. First level - Qubes release number (R2, R3.0, etc)
2. Second level - `templates-itl` or `templates-community`
3. Third level - configuration files

Those files are either generic configs to build template using most recent
sources (`whonix.conf`, `debian.conf` etc). Or files to repeat build using the
same sources as to build official binary package (those files have template
package version in its name). Note that, until we implement deterministic
builds, the result binary will not be exactly the same.

Note that some of those config files will **automatically import additional GPG
keys** to your keyring (the one used to verify git tag signatures) and **set
them as trusted**. This is done to make possible unattended build in Disposable
VM. But if you are not using such a build mode, you most likely want to at
least check what keys are imported.

Usage
-----

To use such a config file, you need to have [Qubes
Builder](https://www.qubes-os.org/doc/QubesBuilder/) installed (cloned), then
place the file as `builder.conf`, then build the template.

There are two possible ways to build the template:

1. Build directly in the environment you've installed the builder (the same VM,
   or even not Qubes system). To do this, execute:

       make qubes-vm

2. [Build in
   DispVM](https://github.com/QubesOS/qubes-builder/tree/master/doc/TemplateDispVMBuild.md).
   To do that, you will need to run this on Qubes system. Before the build, you
   need to [configure some Qubes RPC
   services](https://github.com/QubesOS/qubes-builder/tree/master/doc/TemplateDispVMBuild.md)
   used during build process. Then execute:

       make template-in-dispvm

   The advantage of this mode is starting with clean environment, so no local
   change can interfere with the build process. Also if your build environment
   got compromised during the build, it will not affect the VM from which
   you've called the build (but the template binary can be compromised). 

In either case, you can set `DISTS_VM` environment variable to select
particular template flavor. To get list of configured flavors, call:

    make VERBOSE=1 build-info

To get list of git tags/commits pointing exactly at the source you currently
use, you can call:

    make build-id

The output will be suitable to paste to `builder.conf` to later download
exactly the same sources. This is how configs for specific template package
version are prepared. `template-in-dispvm` target automatically call `build-id`
and output is available in build log - so even when DispVM used to build is
already destroyed, you can still get information what exact sources were used.
Note that there is only last build log for given template flavor - if you
repeat the build, it will override the log.

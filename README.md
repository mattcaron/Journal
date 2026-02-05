# The Grumpy Old Man Journal Setup

## Design goals

1. Feels old
2. Encrypted
3. Easy to use on a daily basis

### Explicitly out of scope

1. Auto decryption (I *want* it to be manual)
2. Homedir encryption (all of `/home` is decrypted and mounted at boot already, so this is redundant)

The idea here is that, if the machine is lost while powered off, all data is encrypted. This stops other uses from being able to get at the data assuming the system is compromised while on and at rest.

## Dependencies

1. [cool-retro-term](https://github.com/Swordfish90/cool-retro-term)

    Either compile it from source or install from package repositories, e.g.:

        sudo apt install cool-retro-term

1. [edit](https://github.com/microsoft/edit)

    Clone and compile in the standard rust way, e.g.:

       git clone https://github.com/microsoft/edit.git
       cd edit
       cargo build --release

    Then drop it in the path someplace.

    **Important:** Building only works if you have rust tooling installed, which is outside the scope of this document. If you don't feel comfortable compiling it, grab the binaries.

1. All the LUKS crypto stuff

       sudo apt install cryptsetup

## Setup

### Filesystem preparation

1. Create the empty block device.

   **NOTE:** If you ever fill this, you'll need to make another one, mount both, and copy stuff over. So, pick a good size.

       dd if=/dev/zero of=.journal.img bs=1M count=1024

1. Do the initial setup:

       cryptsetup luksFormat .journal.img

   Enter your passphrase, etc.

1. Open it then format it.

       sudo cryptsetup luksOpen .journal.img journal
       sudo mkfs.ext2 -m 0 -L journal /dev/mapper/journal

   Note that ext2 is used here because it's on a real journalling filesystem, so
   there's no need to double-journal that.

1. You can then mount it with:

       sudo mount /dev/mapper/journal $HOME/journal

1. Make sure to fix permissions:

       sudo chown -R $USER $HOME/journal

1. Umount/close/etc:

       sudo umount $HOME/journal
       sudo cryptsetup close journal

### Optional

Import the `journal.json` config file into `cool-retro-term` to get the configuration I prefer.

### Script

1. Edit the script and set `PROFILE` to be the profile you prefer. If you imported `journal.json` you can just use it as-is.

1. Copy or link the script into your path.

1. Run `journal` to do it all automatically.

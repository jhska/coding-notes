Ubuntu
======

Utilities
---------

Permanently add global environment variables
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
.. code-block:: bash

    $ sudo vim /etc/environment

Then add your env var.

Delete a large amount of files
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
When using ``rm -rf `` to remote a large amount of files/dirs in a directory,
you might receive the error of ``argument list too long`` error.

Solution:
use ``rsync``.

.. code-block:: bash

    $ mkdir empty_dir
    $ rsync -a --delete empty_dir/ dir_to_del/


Print directory in terminal
~~~~~~~~~~~~~~~~~~~~~~~~~~~

``find . -type d | sed -e "s/[^-][^\/]*\//  |/g" -e "s/|\([^ ]\)/|-\1/"``

This command will search recursively for directories inside the parent directory and then draw the tree of the founded directories.

You may also try the following to include all of the files as well.

``find | sed 's|[^/]*/|- |g'``

find
~~~~
User ``find`` to find files recursively by file type and copy them to a directory.

Folder ``Enc`` has two sub folders: ``HM`` and ``csv``. You can use below method
to collect all the csv files inside ``HM`` into ``csv`` folder:


.. code-block:: bash

    $ cd Enc/HM
    $ find . -name "*.csv" -type f -exec cp {} ../csv/ \;

Done.

disown
~~~~~~
Use ``disown`` to keep process running after ending ssh session
`Reference <https://askubuntu.com/questions/8653/how-to-keep-processes-running-after-ending-ssh-session>`_

1. ssh [server];
2. command;
3. CTRL + Z;
4. ``bg``;
5. ``disown``;
6. exit.

Done.

rsync
~~~~~

.. note:: rsync -- a fast, versatile, remote (and local) file-copying tool

Example usage:

**Usage #1**: Use ``rsync`` to *locally* copy folders and optionally omit some files/folders.

.. code-block:: bash

    $ rsync -av --progress HM HM-COPY --exclude *.yuv --exclude *.bin

or

.. code-block:: bash

    $ rsync -av --progress Orig ../PA --exclude *.yuv --exclude *.bin --exclude *.txt

.. note:: If folder ``PA`` does not exist before you execute the command above, you need to ``mkdir PA`` first.
        ``rsync`` will not help you create directory.

Explain:

1. ``-v`` means `` -v, --verbose               increase verbosity``

2. ``-a`` is the archive mode, it includes lots of modes, see `here <https://superuser.com/questions/1322108/when-is-av-not-the-appropriate-option-for-rsync>`_ for more.

3. **HM** is the source folder.

4. **HM-COPY** is the destination folder, if it does not exist, it will be created.

5. If we do not specify the full path, **HM-COPY** will be created in the current working directory.

6. You can use ``--exclude`` multiple times, you can also just use it once.

**Usage #2**: Use ``rsync`` to *remotely* push/pull files/folders.

.. code-block:: bash

    $ rsync -av --progress <user-name>@<host-ip>:/home/<user-name>/<folder-to-copy> /Users/<user-name>/Downloads

<folder-to-copy> will be copied to ``Downloads`` folder.

See `rsync manual page <https://linux.die.net/man/1/rsync>`_.

How to use SHA256SUM
--------------------

Suppose now you have a directory, inside which you have several binary data files.
You need to transfer those files via internet, and you want to verify the integrity of data files after transfer.

Here the solution:

First, export SHA256SUM file in your local machine:

.. code-block:: bash

    cd path-to-your-dir
    sha256sum -b * > SHA256SUM  # This will generate a file named SHA256SUM
    # You can checkout the contents of the generated file using
    cat SHA256SUM


Second, after transfer finishes, verify your data files against the SHA256SUM file.

.. code-block:: bash

    cd path-to-your-dir-on-remote-machine-after-transfer
    sha256sum -c SHA256SUM


How to update ubuntu packages on 18.04 Bionic Beaver Linux
----------------------------------------------------------

.. code-block:: bash

    $ sudo apt update
    $ sudo apt list --upgradable
    $ sudo apt upgrade
    $ sudo apt dist-upgrade
    $ sudo apt autoremove


or ``sudo apt update && sudo apt list --upgradable && sudo apt upgrade && sudo apt dist-upgrade && sudo apt autoremove``


check cpu ram
-------------

``cat /proc/meminfo``

``cat /proc/cpuinfo``


Check Version
-------------

old school
~~~~~~~~~~
The below terminal command can help you check the ubuntu release version.

.. code-block:: bash

    $ lsb_release -a

new
~~~

.. code-block:: bash

    $ cat /etc/os-release


Check disk space
~~~~~~~~~~~~~~~~


1. type `df -h` in terminal

2. check a folder

.. code-block:: bash

        $ du -h /usr/local/texlive/2016/


Install latex on ubuntu
-----------------------

``sudo apt-get install texlive-full``



cat in Ubuntu
-------------

:ref: https://www.tecmint.com/13-basic-cat-command-examples-in-linux/

Use ``cat`` to get the contents into a txt file.

.. code-block:: bash

    $ sudo cat /etc/network/interfaces > /tmp/net.txt


How to copy filename or current working dir to clipboard
--------------------------------------------------------
First install **xclip**: ``sudo apt install xclip``.

Then inside ``.bashrc``, add

.. code-block:: bash

    $ alias pbcopy='xclip -selection clipboard'
    $ alias pbpaste='xclip -selection clipboard -o'

After the above two steps, you can

1. copy filename to clipboard: ``ls <filename.ext> | pbcopy``, paste to somewhere ``pbpaste``.
2. copy current working directory to clipboard: ``echo $PWD | pbcopy``, paste to somewhere ``pbpaste``.


How to set static ip address for ubuntu server 16.4 and do port forwarding
--------------------------------------------------------------------------

Find an existing vm ubuntu server, use the ``net.txt`` that you get from the above step.
And type the contents in that ``net.txt`` file to the ``/etc/network/interfaces``. **Then shutdown the vm, refresh the mac address** for several times.

An example provided for downloading:

:download:`network/interfaces <../../_static/downloads/net.txt>`

At this time you can ask the network administrator to help do the port forwarding.

You need to provide:

1. static ip: e.g., 192.168.0.157 (maybe mac address also needed)
2. local port 8080 mapping to the public port 8080 [or other ports]


How to enable remote ssh
------------------------

:ref: https://help.ubuntu.com/lts/serverguide/openssh-server.html
:ref: https://help.ubuntu.com/community/SSH/OpenSSH/Configuring

**Remember to change the network adapter to ``Bridged`` instead of ``NAT``.**

If you want quick remote access using password only:

.. code-block:: bash

    $ sudo nano /etc/ssh/sshd_config

Then uncomment this line of codes::

    #PasswordAuthentication yes


Then restart ssh::

    $ sudo restart ssh

If you get the error, "Unable to connect to Upstart", restart ssh with the following::

    $ sudo systemctl restart ssh


If you want to use key pair auth, please refer to links above.

Permission denied (publickey)
-----------------------------

for ssh
~~~~~~~
If you see a warning like ``Permission denied (publickey)``, try to supply
private key.

.. code-block:: bash

    $ ssh root@www.mywebsite.com
    # Permission denied (publickey).

    $ ssh -i ~/.ssh/my_private_key root@www.mywebsite.com
    # success!

Or if you can configure ``~/.ssh/config`` file, based on your configuration,
you can directly type ``ssh mywebsite``.

for git pull
~~~~~~~~~~~~

:ref: https://confluence.atlassian.com/bitbucket/troubleshoot-ssh-issues-271943403.html

if you see ``Permission denied(publickey)`` when doing git pull,
please first type

.. code-block:: bash

    $ eval `ssh-agent`

in the terminal to
start `ssh agent <https://linux.die.net/man/1/ssh-agent>`_,
which is the authentication agent.

Then you can use ``ssh-add ~/.ssh/<private_key_file>`` to add your keys.

ssh-add
-------

`Could not open a connection to your authentication agent <https://stackoverflow.com/questions/17846529/could-not-open-a-connection-to-your-authentication-agent>`_

If you cannot successfully perform ``ssh-add``, you can do this:

.. code-block:: bash

    $ eval `ssh-agent -s`
    $ ssh-add


what is the eval command in bash
--------------------------------

ref: `What is the “eval” command in bash? <https://unix.stackexchange.com/questions/23111/what-is-the-eval-command-in-bash>`_

eval - construct command by concatenating arguments


Configuring Iptables on Ubuntu 14.04
------------------------------------

:ref: https://www.upcloud.com/support/configuring-iptables-on-ubuntu-14-04/

Save
~~~~

.. code-block:: bash

    $ sudo iptables-save > /etc/iptables/rules.v4

Restore
~~~~~~~

* Overwrite the current one

.. code-block:: bash

    $ sudo iptables-restore < /etc/iptables/rules.v4


* Add the new rules while keeping the current one


.. code-block:: bash

    $ sudo iptables-restore -n < /etc/iptables/rules.v4

Apply
~~~~~
.. code-block:: bash

    $ sudo iptables-apply iptables.txt

.. note:: ``iptables-apply`` shall be used with ``iptables.txt``
        while ``iptable-restore`` shall be used with ``rules.v4``
        with the symbol of ``<``.



Persistent Iptables
~~~~~~~~~~~~~~~~~~~

You can automate the restore process at the reboot by installing an  additional package for iptables which takes over the loading of the saved rules.

.. code-block:: bash

    $ sudo apt-get install iptables-persistent


After the installation the initial setup will ask to save the current rules for IPv4 and IPv6, just select Yes and press enter for both.
If you make further changes to your iptables rules, remember to save them again using the same command as above. The iptables-persistent looks for the files rules.v4 and rules.v6 under /etc/iptables.


How to solve the issue of Filezilla permission denied
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

To allow user ``ubuntu`` write access to the remote root directory, enter those commands via terminals as root user ``sudo``:

.. code-block:: bash

    $ sudo chown -R ubuntu /etc/supervisor
    # make sure permissions on that entire folder were correct:
    $ sudo chmod -R 755 /etc/supervisor


Give specific user permission to write to a folder using +w notation
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

ref: https://askubuntu.com/questions/487527/give-specific-user-permission-to-write-to-a-folder-using-w-notation

If you want to change the user owning this file or
directory (folder), you will have to use the command
``chown``. For instance, if you run

.. code-block:: bash

    sudo chown username: myfolder/file

the user owning myfolder will be the username. Then you can execute

.. code-block:: bash

    sudo chmod u+w myfolder

to add the write permission to the username user.

tar compress
------------

Basics
~~~~~~

**compress**

.. code-block:: bash

    $ cd /path/to/the/folder/directory
    # e.g., if you want to compress folder of `myProj`, its path is `/home/ubuntu/myProj`, then you need to $ cd /home/ubuntu
    #
    $ tar -zcvf name-of-archive.tar.gz foldername
    # e.g., $ tar -zcvf myProj.tar.gz myproj
    # the compressed tar ball will be in the /home/ubuntu/ directory
    #
    $ tar -zcvf /tmp/myProj.tar.gz foldername
    # the compressed tar ball will be in the /tmp/ directory

**extract**
.. code-block:: bash

    $ tar -zxvf archive.tar.gz

Notice that it must be a capital letter c.

If you want to extract files to a specified directry, you can use: `$ tar -zxvf archive.tar.gz -C /tmp`

Advanced
~~~~~~~~
Exclude files matching patterns listed in `exclude.txt`

.. code-block:: bash

    $ touch exclude.txt
    $ vim exclude.txt
    # press I button and type somthing
    # press esc button and : button, then type x, then press enter to save and exit vim
    # the file will be something like:
    #
    # abc
    # xyz
    # *.bak
    # backup2017*.sql
    #

    $ tar -zcvf /tmp/mybak.tar.gz -X exclude.txt /home/me


Download/Upload files from/to server
------------------------------------
.. code-block:: bash

    # download: remote -> local
    $ scp user@remote_host:remote_file local_file

    # upload: local -> remote
    $ scp local_file user@remote_host:remote_file

    # ***************** Detailed Example *******************
    # ******************************************************
    #
    ### --> Download:
    #
    $ scp root@zwap:/tmp/pl.sql ~/Downloads/pl.sql
    #
    ### --> Upload:
    #
    $ scp ~/Downloads/pl.sql ubuntu@zwap_server_on_iMac:/tmp/pl.sql
    #
    #
    # ******************************************************

    # ----> example
    $ scp -i ~/.ssh/myprivatekey root@www.mywebsite.com:/home/ubuntu/example.sql /tmp/example.sql
    # if you have `~/.ssh/config` file configured
    $ scp mywebsite:/home/ubuntu/example.sql /tmp/example.sql


what is the difference between .bash_profile and .bashrc
--------------------------------------------------------

ref: `What is the difference between .bash_profile and .bashrc? <https://apple.stackexchange.com/questions/51036/what-is-the-difference-between-bash-profile-and-bashrc>`_

``.bash_profile`` is executed for login shells, while ``.bashrc`` is executed for interactive non-login shells.

When you login (type username and password) via console, either sitting at the machine, or remotely via ssh: .bash_profile is executed to configure your shell before the initial command prompt.

But, if you’ve already logged into your machine and open a new terminal window (xterm) then ``.bashrc`` is executed before the window command prompt. ``.bashrc`` is also run when you start a new bash instance by typing ``/bin/bash`` in a terminal.

On OS X, Terminal by default runs a login shell every time, so this is a little different to most other systems, but you can configure that in the preferences.


How to execute a bash script at system Startup/Shutdown/Reboot
--------------------------------------------------------------

:ref: http://www.upubuntu.com/2015/08/how-to-executerun-bash-script-at-system.html

1. ``chmod +x script_file`` can turn your script executable

2. if you want to run a bash script at system startup, go edit ``/etc/rc.local``

3. if you want to run a script at system reboot, go put it in ``/etc/rc0.d``

4. if you want to run a script at system shutdown, go put it in ``/etc/rc6.d``


Check the size of a folder
--------------------------

ref: https://unix.stackexchange.com/questions/185764/how-do-i-get-the-size-of-a-directory-on-the-command-line

Jump to the directory, type: ``du -sh`` and wait for results.


Install Ubuntu 18.04.1 LTS with Kernel 4.19
-------------------------------------------
This guide shows how to install

1. Ubuntu 18.04.01 LTS Bionic with update-to-date kernel 4.19,
2. CUDA 10 and nvidia drivers for Gigabyte Geforce RTX 2080ti.
3. TensorFlow 1.12 with CUDA 10 support.

.. note::
    We want to use *ubuntu 18.04.01 LTS Bionic* and *TensorFlow*.
    Currently *Tensorflow* only support up to *CUDA 9*.
    Nvidia only support *CUDA 10* for *Bionic*. Meaning if we want to use *TensorFlow* in *Bionic*,
    we have to install *CUDA 10* and compile *TensorFlow* from source for it to work with *CUDA 10*.

    The *nouveau* driver comes with ubuntu installation will not work for 2080ti. Hence we need
    to install driver for 2080ti after ubuntu has been installed.

    I am using *z390 AORUS PRO WIFI* motherboard, and the wifi module will only work under the condition that
    we update the default 4.15 kernel to a later stable version. In this guide *linux kernel 4.19* has been chosen,
    which is the latest stable one.

    The correct installation order is:

    1. Install Bionic (with a bootable USB stick)

    2. Update kernel version to 4.19 (which contains the driver for the wifi module on *z390 AORUS PRO WIFI*)

    3. ``sudo apt install build-essential``

    4. Disable *nouveau* driver (for the purpose of installing the driver that works for RTX 2080ti)

    5. Download Nvidia ``.run`` file from official website, install the 2080ti driver and CUDA 10 at the same time.


Detailed Guide:

.. note::
    Before starting the installation process, the ``.deb`` files required for updating kernel to 4.19
    and the ``.run`` file required for install CUDA10 and nvidia driver for 2080ti have been downloaded to
    a mountable disk.

1. Boot from USB stick, erase disk and install Bionic, at the end the installation when the restart prompt come up just click the "Restart" button.

2. Wait a moment until you see "Please remove the installation medium, then reboot". Remove installation medium physically then press "ctrl + c".

3. Make sure you have the ``.deb`` files required for updating kernel to 4.19 and run below commands

.. code-block:: bash

    sudo dpkg -i linux-*.deb
    sudo update-grub
    sudo reboot

4. Use *software updater* to update Bionic, enter password if prompted, after that ``sudo reboot``.

5. ``sudo apt install build-essential``

6. Disable *nouveau* driver.

.. code-block:: bash

    sudo bash -c "echo blacklist nouveau > /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
    sudo bash -c "echo options nouveau modeset=0 >> /etc/modprobe.d/blacklist-nvidia-nouveau.conf"
    # verify:
    cat /etc/modprobe.d/blacklist-nvidia-nouveau.conf
    # blacklist nouveau
    # options nouveau modeset=0
    sudo update-initramfs -u
    sudo reboot

7. stop the current display server ``sudo telinit 3``. Then press ``CTRL + ALT + F1``, and login to open a new TTY1 session.

8. Start the installation by ``sudo bash xxx.run``. (Normally in Ubuntu , we use ``./xxx.run``, but here is different.). Press space button until you are asked to accept or decline or quit the EULA. Type *accept* if you wish continue. Then answer questions for installing nvidia driver and CUDA10.

9. ``sudo reboot``.

.. note::
        If you want to boot into GRUB mode when you are in BIOS, first press ``F12`` to
        choose the ubuntu disk to boot from. Then immediately hit ``shift`` until GRUB
        actually appears on the screen.

10. ``sudo dpkg -i xxxx.deb`` to install chrome.

11. ``sudo apt install curl git vim htop``.

.. code-block:: bash

    cd ~ && touch .vimrc
    vim .vimrc

Insert below contents to ``.vimrc``.

.. code-block:: bash

    set number
    set ruler
    set nocindent
    set nosmartindent
    set noautoindent
    set indentexpr=
    filetype indent off
    filetype plugin indent off

12. Install zsh shell by following https://github.com/robbyrussell/oh-my-zsh/wiki/installing-ZSH

13. install oh-my-zsh. Uncomment the ``export PATH`` line.

14. visit nvidia CUDA installation guide from browser, follow the post installation actions. Additionally, as indicated in tensorflow GPU support installation guide, add ``export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda/extras/CUPTI/lib64`` to ``.zshrc``. After that verify installation as indicated in nvidia guide. Close the current terminal and open a new one.

15. Open cudnn installation guide website, install cudnn and verify it.

16. Install dependencies for installing python from source.

.. code-block:: bash

    sudo apt install libssl-dev zlib1g-dev libncurses5-dev libncursesw5-dev libreadline-dev libsqlite3-dev
    sudo apt install libgdbm-dev libdb5.3-dev libbz2-dev libexpat1-dev liblzma-dev tk-dev

17. Download python 3.6.7 from official python download page. Then ``./configure --enable-optimizations``, ``make -j 8``, and ``sudo make altinstall``. Then exit shell and start a new one.

18. ``snap remove gnome-system-monitor`` and ``sudo apt install gnome-system-monitor``.

19. Install cmake.

.. code-block:: bash

    sudo apt install curl libcurl4-gnutls-dev
    cd /tmp && mkdir cmake && cd cmake
    wget https://cmake.org/files/v3.12/cmake-3.12.2.tar.gz
    tar xvf cmake-3.12.2.tar.gz && cd cmake-3.12.2
    ./bootstrap --parallel=$(nproc) --system-curl
    make -j $(nproc)
    sudo make install

20. Install opencv

.. code-block:: bash

    cd /tmp && mkdir repo && cd repo && mkdir opencv-installation
    wget https://github.com/opencv/opencv/archive/3.4.3.zip -O opencv-3.4.3.zip && unzip opencv-3.4.3.zip && mv opencv-3.4.3 opencv
    wget https://github.com/opencv/opencv_contrib/archive/3.4.3.zip -O opencv_contrib-3.4.3.zip && unzip opencv_contrib-3.4.3.zip && mv opencv_contrib-3.4.3 opencv_contrib
    cd opencv && mkdir build && cd build
    cmake -G "Unix Makefiles" -DCMAKE_CXX_COMPILER=/usr/bin/g++ CMAKE_C_COMPILER=/usr/bin/gcc -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local -D OPENCV_EXTRA_MODULES_PATH=/tmp/repo/opencv-installation/opencv_contrib/modules -DWITH_TBB=ON -DBUILD_NEW_PYTHON_SUPPORT=ON -DWITH_V4L=ON -DINSTALL_C_EXAMPLES=ON -DINSTALL_PYTHON_EXAMPLES=ON -DBUILD_EXAMPLES=ON -DWITH_OPENGL=ON -DBUILD_FAT_JAVA_LIB=ON -DINSTALL_TO_MANGLED_PATHS=ON -DINSTALL_CREATE_DISTRIB=ON -DINSTALL_TESTS=ON -DENABLE_FAST_MATH=ON -DWITH_IMAGEIO=ON -DBUILD_SHARED_LIBS=OFF -DWITH_GSTREAMER=ON ..
    make all -j$(nproc)
    sudo make install
    sudo apt install python3-opencv
    pkg-config --modversion opencv

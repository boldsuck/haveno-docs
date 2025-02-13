# Haveno using OS provided Tor aka little-t-tor

Since Haveno v1.0.10 there is the [DirectBindTor](https://github.com/haveno-dex/haveno/commit/3d44f3777c7bdf56707ca5d0768877535ac854c5) option.
This means that we can create and use a HiddenService (aka Onion Service) with portforward 9999 in Tor provided by the operating system.

## Whonix

We need to configure 2 files on the Whonix system. In the different Whonix types, the two files to be edited are in different places. If using Qubes-Whonix read how to get your IP.

1. [Create a HiddenService on Whonix-Gateway](https://www.whonix.org/wiki/Onion_Services#Step_2:_Edit_Tor_Configuration)
2. [Open Whonix-Workstation Firewall Port 9999](https://www.whonix.org/wiki/Onion_Services#Step_2:_Open_Whonix-Workstation_Firewall_Port)

### 1. Create a HiddenService on the Whonix-Gateway

File paths are of non-Qubes Whonix running in VirtualBox or KVM - Whonix with Xfce graphical user interface (GUI)

!!! info
    There is a provided `Tor Examples` Button for torrc.examples & `Tor User Config` in the Whisker Menu `Application` -> `System`<br>
    Please open torrc.examples in your Whonix VM and look at the IP in the webserver example!
    You can use `Tor User Config` Button or `sudoedit` to edit `50_user.conf`

`sudoedit /usr/local/etc/torrc.d/50_user.conf`

```
# Haveno incoming anonymity connections
HiddenServiceDir /var/lib/tor/haveno_service/
HiddenServicePort 9999 10.152.152.11:9999
```
and save the file

??? info
    `HiddenServiceVersion 3` as in the examples of the Whonix wiki is **not** required, this is the Tor default. v2 hasn't been supported in Tor for years.

Reload Tor config to create the HiddenService with: `sudo systemctl reload tor`<br>
Heck, there's even a GUI button for Reload Tor ;-)<br>
Get Your_HiddenService_address `sudo cat /var/lib/tor/haveno_service/hostname`<br>
Copy it for your Whonix-Workstation

Whonix-Gateway is ready, switch to Whonix-Workstation

### 2. Edit Whonix-Workstation firewall configuration to open port 9999

!!! info
    There is `Global Firewall Settings` in the Whisker Menu `Application` -> `System` where it says:<br>
    Please use `/etc/whonix_firewall.d/50_user.conf` for your custom configuration, which will override the defaults found here.

`sudoedit /etc/whonix_firewall.d/50_user.conf`

```
# Open TCP port on all network interfaces, gateway as well as (if any) tunnel (VPN) interfaces.
EXTERNAL_OPEN_PORTS+=" 9999 "
```
and save the file

That was all to configure a HiddenService for our app in Whonix.

### Haveno Download & Install

1. On Whonix-Workstation, download the latest version of the .deb & .sig version of Haveno-reto (now renamed RetoSwap) from https://github.com/retoaccess1/haveno-reto/releases/ or https://RetoSwap.com (eg: for RetoSwap v1.0.18, download https://github.com/retoaccess1/haveno-reto/releases/download/v1.0.18/haveno-linux-deb.zip & https://github.com/retoaccess1/haveno-reto/releases/download/v1.0.18/haveno-linux-deb.zip.sig).
It should download automatically to `/home/user/.tb/tor-browser/Browser/Downloads/`

2. Verify the signature TODO

Download RetoSwap Public Key `wget https://retoswap.com/reto_public.asc`
List Fpr `gpg --show-keys --with-fingerprint reto_public.asc`
`gpg --import reto_public.asc`
`gpg --edit-key DAA24D878B8D36C90120A897CA02DAC12DAE2D0F`
`trust` <- You may chose 3 or 4
CTRL+C

`gpg --verify haveno-linux-deb.zip.sig && sha512sum haveno-linux-deb.zip`

3. Extract the archive: right-click on the downloaded .zip (eg: /home/user/.tb/tor-browser/Browser/Downloads/haveno-linux-deb.zip), click “Extract Here”
4. Install the .deb: open the newly extracted folder `/home/user/.tb/tor-browser/Browser/Downloads/haveno-linux-deb/`, and in a terminal window on Whonix-Workstation, type sudo dpkg -i (with a trailing space) and then drag the .deb installer from the folder into the terminal to complete the filepath (eg: for Haveno-reto v1.0.18, it should be<br>
`sudo dpkg -i '/home/user/.tb/tor-browser/Browser/Downloads/haveno-linux-deb/haveno-v1.0.18-linux-x86_64-installer.deb`.
Press enter, Haveno-reto should be installed to /opt/haveno/. If it fails because of missing dependencies, run the command `sudo apt install -f` to download and install the missing dependencies, and then try the original sudo dpkg -i '[...].deb' command again.

??? info
    Alternative install in a terminal window<br>
    `cd /home/user/.tb/tor-browser/Browser/Downloads/haveno-linux-deb`<br>
    `sudo dpkg -i haveno-v*-linux-x86_64-installer.deb`

Haveno Launcher should be in `Applications` -> `Internet` You must edit it to<br>
`/opt/haveno/bin/Haveno --hiddenServiceAddress=Your_HiddenService_address.onion --nodePort=9999`

List all available haveno-desktop options for cmdline:<br>
`/opt/haveno/bin/Haveno -h`<br>
or to use in `/home/user/.local/share/Haveno-reto/haveno.properties`

!!! info
    Your_HiddenService_address is the saved output from Whonix-Gateway<br>
    `sudo cat /var/lib/tor/haveno_service/hostname`

If not create a desktop shortcut: copy (or drag) `/opt/haveno/lib/haveno-Haveno.desktop to your desktop and add the cmdline options like in the launcher above.

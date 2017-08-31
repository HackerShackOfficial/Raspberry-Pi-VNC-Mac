# Raspberry-Pi-VNC-Mac
How to Setup Raspberry Pi VNC Screensharing on a Mac

Credits to http://4dc5.com/2012/06/12/setting-up-vnc-on-raspberry-pi-for-mac-access/ for developing a guide to base this tutorial on.

```
sudo apt-get install tightvncserver
```
Then run
```
vncserver :1
```
and create a password.

Then run:

```
sudo apt-get install netatalk
```

Now from the Mac, open Finder, and hit ⌘K. Enter afp://192.168.0.22 (using the IP address of your Pi).

```
sudo apt-get install avahi-daemon
sudo update-rc.d avahi-daemon defaults
```
```
sudo vim /etc/avahi/services/afpd.service
```
then add

```
<?xml version="1.0" standalone='no'?><!--*-nxml-*-->
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
<service-group>
   <name replace-wildcards="yes">%h</name>
   <service>
      <type>_afpovertcp._tcp</type>
      <port>548</port>
   </service>
</service-group>
```
Next, run
```
sudo /etc/init.d/avahi-daemon restart
```

Will show the Pi in the sidebar on your Mac by default.

```
cd /etc/init.d
sudo vim tightvncserver
```
Add the following content and change `1440x900` to `1920x1080`:

```
#!/bin/bash
# /etc/init.d/tightvncserver
#

# Carry out specific functions when asked to by the system
case "$1" in
start)
    su pi -c '/usr/bin/vncserver -geometry 1440x900'
    echo "Starting VNC server "
    ;;
stop)
    pkill vncserver
    echo "VNC Server has been stopped (didn't double check though)"
    ;;
*)
    echo "Usage: /etc/init.d/blah {start|stop}"
    exit 1
    ;;
esac

exit 0
```

Change the permissions of the file you just created

```
sudo chmod +x tightvncserver
```
kill the server

```
sudo pkill Xtightvnc
```

```
sudo /etc/init.d/tightvncserver start
cd /etc/init.d
sudo update-rc.d tightvncserver defaults
```
Create one more file
```
sudo vim /etc/avahi/services/rfb.service
```
and add this content:

```
<?xml version="1.0" standalone='no'?>
<!DOCTYPE service-group SYSTEM "avahi-service.dtd">
<service-group>
  <name replace-wildcards="yes">%h</name>
  <service>
    <type>_rfb._tcp</type>
    <port>5901</port>
  </service>
</service-group>
```
Finally, run

```
sudo /etc/init.d/avahi-daemon restart
```

Now you should be able to see the Screen Share option for the Pi in your Finder sidebar.

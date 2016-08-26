Irssi: IRC Client on Atomic Host
================================

`Irssi <https://irssi.org/>`_ is a Terminal Based IRC Client for Unix/Linux based systems. This page shows how to run Irssi on Atomic Host.

Steps
-----

**First you will need to boot up an atomic host**

- Copy the **Sources** down from here `Fedora Dockerfile for Irssi <https://github.com/fedora-cloud/Fedora-Dockerfiles/tree/master/irssi/>`_.
- Perform the build with the following command:
    ``docker build -t fedora/irssi .``
- After the build is successful run the container with the following command:
    ``atomic run fedora/irssi``

This will start Irssi.


Irssi Commands
--------------

To Connect to Server:

``/connect irc.freenode.net``

To Connect to Channel:

``/join #fedora``

To leave Channel:

``/part #fedora``

To Set nick:

``/set nick username``

To Identify nick:

``/msg nickserv identify password``

To Change nick:

``/nick username1``


More Commands
-------------

+----------------+--------------------------------------------------------------------+
| **Command**    | **Description**                                                    |
+----------------+--------------------------------------------------------------------+
| /ban           | Sets or lists bans for a channel                                   |
+----------------+--------------------------------------------------------------------+
| /clear         | Clears a channel buffer                                            |
+----------------+--------------------------------------------------------------------+
| /disconnect    | Disconnects from the network that has focus                        |
+----------------+--------------------------------------------------------------------+
| /exit          | Disconnects client from all networks and returns to shell prompt   |
+----------------+--------------------------------------------------------------------+
| /join          | Joins a channel                                                    |
+----------------+--------------------------------------------------------------------+
| /kick          | Kicks a user out                                                   |
+----------------+--------------------------------------------------------------------+
| /kickban       | Kickbans a user                                                    |
+----------------+--------------------------------------------------------------------+
| /msg           | Sends a private message to a user                                  |
+----------------+--------------------------------------------------------------------+
| /names         | Lists the users in the current channel                             |
+----------------+--------------------------------------------------------------------+
| /query         | Opens a query window with a user or closes a current query window  |
+----------------+--------------------------------------------------------------------+
| /topic         | Displays/edits the current topic                                   |
+----------------+--------------------------------------------------------------------+
| /unban         | Unbans everyone                                                    |
+----------------+--------------------------------------------------------------------+
| /whois         | Displays user information                                          |
+----------------+--------------------------------------------------------------------+
| /window close  | Forces closure of a window                                         |
+----------------+--------------------------------------------------------------------+



**Further Reading:** To know more about Irssi visit `https://irssi.org <https://irssi.org/>`_.

Atomic Command Cheat Sheet
==========================

This chapter contains cheat sheet for atomic commands

**The atomic command ``/usr/bin/atomic`` defines the entrypoint of Project Atomic hosts**


- ``atomic host upgrade`` upgrades to a newer version.
- ``atomic host rollback`` rollbacks to the previous version.
- ``atomic host status`` shows the current status of the installed atomic host.
- ``atomic run <name>`` executes container image run method.
- ``atomic install <name>`` installs a container on atomic host with systemd unit file to run it as service.
- ``atomic uninstall <name>`` uninstalls the container from the atomic host.
- ``atomic info <name>`` returns LABEL Information of the image.
- ``atomic images`` displays all the container image present on the atomic host.
- ``atomic scan <name>`` scans the image or container
- ``atomic stop <name>`` executes container image stop method
- ``atomic mount`` mounts container image to a specific directory
- ``atomic diff`` shows difference between to container images, RPMs or file diff
- ``atomic push`` pushes (upload) the latest image to the repository
- ``atomic pull`` pulls the latest image from the repository
- ``atomic top`` shows stats about processes running inside container
- ``atomic storage`` manages container storage
- ``atomic unmount`` unmount container image
- ``atomic update`` updates to the latest container image of the repository
- ``atomic version`` displays "Name Version Release" Label of the image
- ``atomic verify`` verifies that the image is fully updated

We have blog post for few commands. Check `this <https://trishnag.wordpress.com/2016/08/11/getting-started-with-atomic-commands/>`_.

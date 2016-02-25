Docker related queries
======================

SELinux rule for volumes on host
::

    # chcon -Rt svirt_sandbox_file_t /path/to/volume

Without this you will see permission error on a Fedora host.

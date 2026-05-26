Quick Reference (Cheat Sheets)
==============================

Login and data transfer
-----------------------

+---------------------------------------------------------------+--------------------------------------------------------------------------+
| *ssh USERNAME@wayland.hec.lancs.ac.uk*                        | login from a linux desktop                                               |
+---------------------------------------------------------------+--------------------------------------------------------------------------+
| *scp /path/to/my/file USERNAME@wayland.hec.lancs.ac.uk*       | upload a file to the HEC from a                                          |
|                                                               | linux desktop                                                            |
+---------------------------------------------------------------+--------------------------------------------------------------------------+
| scp -r /path/to/my/directory USERNAME@wayland.hec.lancs.ac.uk | upload a directory to the HEC from a linux desktop                       |
+---------------------------------------------------------------+--------------------------------------------------------------------------+
| smbclient -k -D folder-path //luna/faculty-code               | Connect to research folder on LUNA. User put and get to upload/download  |
+---------------------------------------------------------------+--------------------------------------------------------------------------+

Test 8

.. list-table::
  :class: tight-table
 
  * - *scp -r /path/to/my/directory USERNAME@wayland.hec.lancs.ac.uk*
    -  upload a directory to the HEC from a linux desktop 
  * - *scp -r /path/to/my/directory USERNAME@wayland.hec.lancs.ac.uk*
    - upload a directory to the HEC from a linux desktop
  * - *smbclient -k -D folder-path //luna/faculty-code
    - Connect to research folder on LUNA. User put and get to upload/download

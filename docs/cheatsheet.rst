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

Test tight-table

.. list-table:: Comparison
   :widths: 20 10 10 15 20 
   :header-rows: 1
   :class: tight-table   
 
   * - Platform
     - scp -r /path/to/my/directory USERNAME@wayland.hec.lancs.ac.uk upload a directory to the HEC from a linux desktop 

Reference markup table

+------------------------+------------+----------+----------+
| Header row, column 1   | Header 2   | Header 3 | Header 4 |
| (header rows optional) |            |          |          |
+========================+============+==========+==========+
| body row 1, column 1   | column 2   | column 3 | column 4 |
+------------------------+------------+----------+----------+
| body row 2             | Cells may span columns.          |
+------------------------+------------+---------------------+
| body row 3             | Cells may  | - Table cells       |
+------------------------+ span rows. | - contain           |
| body row 4             |            | - body elements.    |
+------------------------+------------+----------+----------+
| body row 5             | Cells may also be     |          |
|                        | empty: ``-->``        |          |
+------------------------+-----------------------+----------+

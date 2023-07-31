Logging in to the HEC
=====================

Once registered for an account, you can access the High 
End Computing (HEC) cluster from on campus using any 
application offering support for Secure Shell (ssh).

.. note::
  If you are not using a Secure PC, MyLab or MyDesktop, 
  you will need to connect to the VPN in order to 
  access the HEC when off campus.

From the Interactive Unix Service (IUS) or any local Linux desktop, 
you can access the HEC by typing the following in a session window:

.. code-block:: console

  ssh -X username@wayland.hec.lancaster.ac.uk

From a local Windows PC, the HEC can be accessed using PuTTY or any 
ssh (Secure Shell) capable application using the same hostname: 
wayland.hec.lancs.ac.uk. The PuTTY application is available on 
AppsAnywhere to enable ssh connections on university PCs.

For Windows PC users who wish to use applications on the HEC which 
require graphics, we recommend using an Ubuntu instance of
`mylab <https://mylab.lancaster.ac.uk/>`_ and using the instruction
for a Linux desktop HEC login as described above.

When logging in for the first time you may see a message similar to 
the following:

.. code-block:: console

  Host key not found from the list of known hosts.
  Are you sure you want to continue connecting (yes/no)?
  yes>

Reply yes to the question and you will then be prompted for your 
password. When you next log in, you will only be prompted for 
your username and password.

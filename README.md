---
Purpose:

Create a Post-processor plugin that will:
  - Store new Vagrant boxes created with packer to a central repository
  - Version the boxes

Ideas:
  - Central repo is a shared Samba dir
  - Updates the Vagrant .json file
  - Each new box file, will be have the version appended to the file name

Examples:
  - Assume a new ubuntu vagrant box was created using packer

  --> packer build ubuntu-15.04.json

==> Builds finished. The artifacts of successful builds are:
--> virtualbox-iso: 'virtualbox' provider box: ubuntu-15.04.box

  - Upload the new box to the central repository

  --> vboxrepo ubuntu 0.1 ubuntu-15.04.box "Ubuntu 15.04 box"


Reference:
 https://github.com/hollodotme/Helpers/blob/master/Tutorials/vagrant/self-hosted-vagrant-boxes-with-versioning.md

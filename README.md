---
Purpose:
Create a Post-processor plugin that will:
  - Store new Vagrant boxes created with packer to a central repository
  - Version the boxes
Ideas:
  - Central repo is a shared Samba dir
  - Updates the Vagrant .json file
  - Each new box file, will be have the version appended to the file name

Reference:
 https://github.com/hollodotme/Helpers/blob/master/Tutorials/vagrant/self-hosted-vagrant-boxes-with-versioning.md

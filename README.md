# crypto-container
An easy way to create an encrypted container.
This is an Ansible playbook to create encrypted containers on the local machine to protect all your confidential files automatically mounted at login.

## Usage
1. Launch the script as a usual Ansible playbook :
```
        $ ansible-playbook cryptocontainer.yml --ask-sudo-pass
```
2. Answer the questions :
  - container password : to encrypt the container
  - session password : to encrypt the container key

## Architecture
The container is just a regular file 50 MB, fill with random data and mounted as a loop device. It is accessed through dm-crypt which encrypt seamlessly every file that will be written in it.
To mount the container at login it must be linked in some way to the session password. The main issue is when the user wants to change his session password (you are doing it regularly, right?) then you should re-encrypt your container. To avoid such scenario we use a dedicated password stored in the key file that is encrypted with your session password. At login, pam-mount use your session password to decrypt the key file and use the content (ie: the password container) to unlock your container.

## Security
- This script works on Linux with [LUKS](https://en.wikipedia.org/wiki/Linux_Unified_Key_Setup)
- The container is encrypted with a cipher aes-xts-plain64 and a key size of 256 bits.
- **Warning** : In order to use passwords (container and session) through cryptsetup and openssl, they are written into temporary files. Even if rights and deletions are as much secure as it can, it is still required that your local machine is secure and that your filesystem behave nicely with the command shred.

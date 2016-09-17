# "Users" role for initial server configuration  
  
## General
This role appliable for the Debian/Ubuntu and RedHat/CentOS operation systems.  
It's do the following:  
* checks if required packages installed (```shadow-utils``` for RedHat and ```passwd``` - for Debian)  
* adds ```sudo``` group if required  
* creates users according to the ```users``` variable with default password  
* adds public keys to the each user (all keys must be stored in the ```files/``` directory and named like ```id_rsa.pub_<username>```)  
* forces newly created users to change their passwords at the first login  
  
## Variables  
Here is a list of all the default variables for this role, which are also available in ```defaults/main.yml.example```.  
```yml
users:
  - name: Alexander Lebedev
    username: ad
    shell: /bin/zsh
    groups: wheel, sudo
```  
  
## Password  
According to [ansible documentstion](http://docs.ansible.com/ansible/faq.html#how-do-i-generate-crypted-passwords-for-the-user-module), 
password must be stored in the SHA512 format, so we need to use ```mkpasswd``` utility to generate SHA512 from plain password:  
```bash
mkpasswd --method=sha-512 qwmkzxlp
```  
Sample output:   
```
$6$2.HvHb23jV$bw0mKuDsFGA/G855W4XUpJsllay1azk.POcNUwMzYB5Rb85ffEGgYVdUsTDB9RSLUz7jFCXexYsbeAPXXcbQc0
```
To store default password securely, we can use ```ansible-vault``` utility:  
* create new encripted vault:  

```bash
ansible-vault create vars/main.yml
New Vault password:
Confirm New Vault password:
```  
* write to the file something similar to this:  

```yml
---  
default_password: $6$2.HvHb23jV$bw0mKuDsFGA/G855W4XUpJsllay1azk.POcNUwMzYB5Rb85ffEGgYVdUsTDB9RSLUz7jFCXexYsbeAPXXcbQc0
```  

* save and close file  
* edit existing vault with:  

```bash
ansible-vault edit vars/main.yml
```  

## Usage with other roles  
To use this role with other roles in different playbooks, you can override ```users``` variable 
in few other places (```defaults/main.yml```, ```vars/main.yml```, in role ```vars``` in the playbook etc).  

### Oh-my-zsh
To adds ```oh-my-zsh``` for some user, you need to extend required element of ```users``` array with specific values or use default values from [zsh role](https://git.itgene.ru/ansible/zsh).  
Example configuration with user-specific oh-my-zsh settings:  
```yml
users:
  - name: Alexander Lebedev
    username: ad
    shell: /bin/zsh
    groups: sudo
    oh_my_zsh:
      theme: bira
      plugins: git
      case_sensitive: true
      hyphen_insensitive: true
      disable_update_prompt: true
      disable_auto_update: true
      update_days: 13
      disable_ls_colors: true
      disable_auto_title: true
      disable_untracked_files_dirty: true
      disable_correction: true
      completion_waiting_dots: false
```  

### PyEnv  
To install ```pyenv``` for some user and adds specific Python versions, you need to extend required element of ```users``` array with specific values.  
Example configuration:  
```yml 
users:
 - name: Webmaster
    username: webmaster
    shell: /bin/zsh
    groups: sudo,redis
    pyenv: true
    pyenv_python:
      - {version: 2.7.10}
      - {version: 3.4.3}
```  
See [pyenv]() and [pyenv-python]() roles repositories for detaied information about it's actions.  
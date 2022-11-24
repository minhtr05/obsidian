Clone using regular http on github desktop, open resultant folder in obsidian and manage *obsidian git* plugin settings, e.g. enable auto-push/commit for seamless syncing.

To enable SSH-Auth use `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` with github private email to generate public, private key pair which will be stored in ~/.ssh/ take the public key from this (one ending in .pub) open it with text editor, copy its contents and add it into github keys section in settings. the private key that was created as well shouldn't be uploaded anywere, send that to all computers that need to modify/access the obsidian repo/vault, usually good idea to also store it in ~/.ssh/ but not necessary, 

run `ssh-add ~/.ssh/id_<name>`

to actually make this vault use the SSH Auth go into github desktop, repository from top bar, properties, and change the URL to the SSH Url,


https://gist.github.com/mortenege/42b12a82f7d79877171af84c7d0a0714
Just use the comment version for reference.

#### Cant ssh-add
add the `sshCommand = "ssh -i ~/.ssh/github_key"` to the .git/config file inside the [core] section, the .git file is in the local project repo itself, just do the initial clone with http, if messed up before and your getting [[#Host Key Validation Failed]] see below.
The Private Key that is referenced must be the same one on all computers since that is the one linked to the public key added in github.
should look something like this (on windows only)
![[Pasted image 20221125014758.png]]
 
This is only necessary if there is no access to ssh-agent, this can be manually if administrator privalledges are allowed
```powershell
# By default the ssh-agent service is disabled.
Get-Service -Name ssh-agent | Set-Service -StartupType Automatic

Start-Service ssh-agent
```
But if no admin privs go for the other method with Git Bash and putting the sshCommand in .git/config

on linux u can literally just do `ssh-add ~/.ssh/id_whateverkey` which is all you do on windows too if you can enable the ssh-agent normally without git bash, allegedly might have to sometimes do `eval "$(ssh-agent -s)"` to start it on startup but hearsay


##### Host Key Validation Failed
https://stackoverflow.com/questions/13363553/git-error-host-key-verification-failed-when-connecting-to-remote-repository

Essentially comes down to running this command:
```bash
ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
```
Even though it's formatted like Linux/Mac it works on windows through the GIT Bash terminal, the ~ just gets translated to windows home folder. Standard Bash Syntax just running an ssh key scan for github, then piping ( | ) that result into the known_hosts file so that the new key is the known_host instead of the old one so that ssh no longer thinks the key is invalid and your getting man in the middle'd

if not using Git Bash just look for the known_hosts and wipe its ass
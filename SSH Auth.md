Clone using regular http on github desktop, open resultant folder in obsidian and manage *obsidian git* plugin settings, e.g. enable auto-push/commit for seamless syncing.

To enable create Public Private Key Pair for ssh-auth use `ssh-keygen -t name -C "your_email@example.com"` with github private email subbed in.
Resultant keys in ~/.ssh/ with the format id_name (private) and id_name.pub (Public)

take the public key from this (one ending in .pub) open it with text editor, copy its contents and paste it into github keys section in github settings. the private key that was created shouldn't be uploaded anywere, send that to all computers that need to modify/access the obsidian repo/vault, usually good idea to also store it in ~/.ssh/ on each computer but not necessary, since it is just added to `ssh-agent` by: 

running `ssh-add ~/.ssh/id_<name>`

to actually make this vault use the SSH Auth go into github desktop, repository from top bar, properties, and change the URL to the SSH Url, then all is good

### Tl;dr
Generate Public / Private Key Pair
```
ssh-keygen -t name -C "your_email@example.com"
stored in ~/.ssh/ which = C:/Users/%USERNAME%/.ssh/
```
Open id_name.pub (**Public**) in notepad, copy it stuff, paste in github --> settings --> keys --> ssh keys
```
Create New Repo in github Desktop / HTTPS Clone existing one
Run: ssh-add ~/.ssh/id_name (Private)
Github Desktop --> Top Bar --> Repository --> Repository Settings --> Remote --> Copy Paste in SSH URL instead of HTTP one from github.com
Open the Github Desktop Folder (likely ~/Documents/Github/RepositoryName) in Obsidian
```
Download Obisidan-git plugin
Profit

iiii
(oh also copy this .gitignore template otherwise you get conflcits with the workplace file trying to sync itself every two seconds cus it changes every two seconds)

```shell
# to exclude Obsidian's settings (including plugin and hotkey configurations)

#.obsidian/

# OR only to exclude workspace cache

.obsidian/workspace.json

# Add below lines to exclude OS settings and caches

.trash/

.DS_Store
```


#### Cant ssh-add
[Careful only listen to the comment main post is aids](https://gist.github.com/mortenege/42b12a82f7d79877171af84c7d0a0714)
Add the `sshCommand = "ssh -i ~/.ssh/github_key"` to the .git/config file inside the [core] section, of the .git file is in the local project repo itself, just do the initial clone with http, if messed up before and your getting [[#Host Key Validation Failed]] see below.
The Private Key that is referenced in the snippet below must be the same one on all computers since that is the one linked to the public key added in github.
should look something like this 
(on windows this is the same though this looks like bash syntax, ~ translated to user folder don't try to use a C: path)
![[Pasted image 20221125014758.png]]
Obviously replace "github_key" with the private key FILE that was generated earleir.
 
This is only necessary if there is no access to ssh-agent **AND** no admin perms, `ssh-agent ` can be manually enabled if administrator privalledges are allowed (powershell snippet 4 windows)
```powershell
# By default the ssh-agent service is disabled.
Get-Service -Name ssh-agent | Set-Service -StartupType Automatic

Start-Service ssh-agent
```
But if no admin privs go for the other method with Git Bash installed and putting the sshCommand in .git/config.

on linux u can literally just do `ssh-add ~/.ssh/id_<private_key>` which is all you do on windows too if you can enable the ssh-agent normally without git bash, allegedly might have to sometimes do `eval "$(ssh-agent -s)"` to start it on startup but hearsay

ffffffff

##### Host Key Validation Failed
https://stackoverflow.com/questions/13363553/git-error-host-key-verification-failed-when-connecting-to-remote-repository

Essentially comes down to running this command:
```bash
ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
```
Even though it's formatted like Linux/Mac it works on windows through the GIT Bash terminal, the ~ just gets translated to windows home folder. Standard Bash Syntax just running an ssh key scan for github, then piping ( | ) that result into the known_hosts file so that the new key is the known_host instead of the old one so that ssh no longer thinks the key is invalid and your getting man in the middle'd

if not using Git Bash just look for the known_hosts and wipe its ass
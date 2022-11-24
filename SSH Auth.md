https://gist.github.com/mortenege/42b12a82f7d79877171af84c7d0a0714
Just use the comment version for reference.

add the `sshCommand = "ssh -i ~/.ssh/github_key"` to the .git/config file inside the [core] section, the .git file is in the local project repo itself, just do the initial clone with http, if messed up before make sure to use GIT Bash on windows to remove the previous host signed key thing, so that it can trust the new host.
The key that is referenced must be the same one on all computers since that is the one linked to the public key added in github.
should look something like this (on windows only)
![[Pasted image 20221125014758.png]]

This is only necessary if there is no access to ssh-agent, this can be manually if administrator privalledges are allowed
```powershell
# By default the ssh-agent service is disabled.
Get-Service -Name ssh-agent | Set-Service -StartupType Automatic

Start-Service ssh-agent
```
But if no admin go for the other method with Git Bash and putting the sshCommand in .git/config

on linux u can literally just do `ssh-add ~/.ssh/id_whateverkey` which is all you do on windows too if you can enable the ssh-agent normally without git bash


(If host key validation error)
https://stackoverflow.com/questions/13363553/git-error-host-key-verification-failed-when-connecting-to-remote-repository

Essentially comes down to running this command:
```bash
ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
```
Even though it's formatted like Linux/Mac it works on windows through the GIT Bash terminal, the ~ just gets translated to windows home folder.

if not using Git Bash just look for the known_hosts and wipe its ass
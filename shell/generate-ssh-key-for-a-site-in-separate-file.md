# Generate a SSH key

```bash
cd ~/.ssh
# -C: comment
# -f: file
ssh-keygen -t rsa -b 4096 -C "<site.user>" -f "<site.user>"
ssh-add ~/.ssh/<file>

touch config
```

in config file

```
Host bitbucket.org
  HostName bitbucket.org
  IdentityFile ~/.ssh/id_rsa

Host github.com
  HostName github.com
  IdentityFile ~/.ssh/<file>
```

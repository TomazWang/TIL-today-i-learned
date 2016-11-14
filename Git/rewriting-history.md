# Git edit UN-PUSHED commint message

Before push, it's great to check your commit message one more time. Check if your message is clear for others or is your message contents smart commit keywords as you wish(which is the reason why I usually need to edit my commit message before push).

__Notice__: methods below are only work before you push commits up to remote branch.

## Edit last commit message

Edit last commit is easy. Simply commit again with tag `--amend` or `-a` and you can overwrite your last commit.

```
$ git commit --amend
```

## Edit commit message before

`git rebase` can take you back to commits farther back. Use `git rebase -i` can open rebase tool interactively. Give the command a parameter to specify how far you want to go back. Let's say we want to edit previous 3 message, the parameter is `HEAD~3` or `HEAD~2^`.(The first one is easier to remember)

```
$ git rebase -i HEAD~3
```

```
$ git rebase -i HEAD~2^
```

After running this command, rebase tool will show you a list of commits look like this.

![rebase][rebase]

By changing the first word of each commit, you can mark which commit you want to edit.

![rebase-mark][rebase-mark]

> __NOTE:__
> If all you want to change is the commit message, you can use command `reword` instead of `edit` which only edit the message.

After marking  the commit, run a series of `git rebase --continue` and `git commit --amend` and rebase tool will take you all the way to edit the commits.


## Remember

If this is your first time using `rebase` command. You probably want to create a temporary branch before you do all this time-traveling things. It might delete all your works if you don't handle it right.


[rebase]:http://i.imgur.com/IJdqw3U.png
[rebase-mark]:http://i.imgur.com/Bw4fzyQ.png

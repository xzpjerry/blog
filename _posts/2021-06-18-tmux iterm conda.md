---
layout: post
published: true
tags: casual
---

For some reason, when entering tmux from iterm, the $PATH was somehow reset which leads to mismatching of Conda's Python from the system's one.

![Without entering tmux](/Users/dev/Library/Application Support/typora-user-images/image-20210618161947909.png)

*without entering tux*

---

![entered tmux](/Users/dev/Library/Application Support/typora-user-images/image-20210618162614027.png)

*tmux entered*

---



## Solution

Added the following code to ~/.zshrc

```bash
# tmux conda issue
[[ -z $TMUX ]] || conda deactivate; conda activate base
```


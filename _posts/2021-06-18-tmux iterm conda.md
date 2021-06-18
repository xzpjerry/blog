---
layout: post
published: true
tags:
- casual
- tmux
- iterm
---

For some reason, when entering tmux from iterm, the $PATH was somehow reset which leads to mismatching of Conda's Python from the system's one.

![image-20210618161947909](../images/2021-06-18-tmux iterm conda/image-20210618161947909.png)

*without entering tux*

---

![image-20210618162614027](../images/2021-06-18-tmux iterm conda/image-20210618162614027.png)

*tmux entered*

---



## Solution

Added the following code to ~/.zshrc

```bash
# tmux conda issue
[[ -z $TMUX ]] || conda deactivate; conda activate base
```


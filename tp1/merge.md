# Merge

I tried to push changes to the remote repository but there were already some other uploaded modofications that affected the same file/lines. This produced a conflict.

![Merge error](https://github.com/FranPitri/ing_soft_3/blob/master/tp1/assets/merge_00.png?raw=true)

To fix this, I had to merge both versions using a merging tool. I opted to use the simple git built-in extension for VSCode.

![Merging](https://github.com/FranPitri/ing_soft_3/blob/master/tp1/assets/merge_01.png?raw=true)

To handle merge conflicts, git uses a "three-way" aproach. Therefore, three files are generated.

`LOCAL` is the "ours" side of the conflict. It will hold the results of the merge.

`REMOTE` is the "theirs" side of the conflict. i.e the branch you are merging into `LOCAL`.

`BASE` is a common ancestor of these two.
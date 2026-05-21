# gitsemver-test

Repository used in [gitsemver] tests.

## master Branch

This branch is not used for tests.

It was created using https://github.com/giantswarm/template template repository
and then following commands were applied:

```bash
git commit --allow-empty -m "release 1.0.0"
sleep 1 # To add some time between commits.
git tag v1.0.0 HEAD
git checkout -b branch-of-1.0.0
git commit --allow-empty -m "in branch-of-1.0.0"
sleep 1 # To add some time between commits.
git checkout master
git commit --allow-empty -m "after release 1.0.0"
sleep 1 # To add some time between commits.
git commit --allow-empty -m "release 2.0.0"
sleep 1 # To add some time between commits.
git tag -a -m "Test tag message" v2.0.0 HEAD
git commit --allow-empty -m "after release 2.0.0"
sleep 1 # To add some time between commits.
git checkout branch-of-1.0.0
git merge --no-edit master
git checkout master
git checkout -b branch-of-2.0.0
git commit --allow-empty -m "in branch-of-2.0.0"
git checkout master
git checkout -d $(git rev-list --max-parents=0 HEAD) # Checkout to the first commit.
git checkout -b complex-tree
for i in {1..300}; do
    git commit --allow-empty -m "commit number $i" > /dev/null
done
for k in {1..5}; do
    for i in {1..5}; do
        git checkout -b complex-tree-$k-$i
        git commit --allow-empty -m "commit in branch complex-tree-$k-$i"
        sleep 1
    done
    for i in {1..5}; do
        git checkout complex-tree
        git merge --no-edit --no-ff complex-tree-$k-$i
        git b -d complex-tree-$k-$i
        sleep 1
    done
done
git checkout master

# After that a single commit updating this README.md was pushed.
```

## branch-of-2.0.0 Branch

https://github.com/giantswarm/gitsemver-test/tree/branch-of-2.0.0

This branch is created from v2.0.0 tag without any merging whatsoever. All
commits have single parent. This allows to test resolving versions for commits
with single parents.

```
$ git log --graph --pretty=format:'%H %ci %d %s'
* 3901da4b6b4cf68e3d11a10f60916107828fa9a7 2019-11-08 11:45:06 +0000  (HEAD -> branch-of-2.0.0) in branch-of-2.0.0
* 0c57573cece531f840a167aa0ccc29b178b6de42 2019-11-08 11:45:05 +0000  (master) after release 2.0.0
* 22b04802cd5ee933de078344fa53a3e37b826913 2019-11-08 11:45:04 +0000  (tag: v2.0.0) release 2.0.0
* 5ff7013b7a5f43d39b8da62361cfbfd4d3bf9a50 2019-11-08 11:45:03 +0000  after release 1.0.0
* 02995edb3e6f14b8f9a83b84e3b8c7c8d9f60f86 2019-11-08 11:45:01 +0000  (tag: v1.0.0) release 1.0.0
* 2091354c7b8659f1846a876fbe2032fd1390d569 2019-11-08 11:42:00 +0000  (origin/master, origin/HEAD) Initial commit
```

## branch-of-1.0.0 Branch

https://github.com/giantswarm/gitsemver-test/tree/branch-of-1.0.0

This branch is created from v1.0.0 tag with merged master afterwards. This
allows to test resolving versions for commits with multiple parents on long
living branches where master branch merged after a release.

```
$ git log --graph --pretty=format:'%H %ci %d %s'
*   c3726de44a2bb1bd898fdbe5632a90841636fa82 2019-11-08 11:45:06 +0000  (HEAD -> branch-of-1.0.0) Merge branch 'master' into branch-of-1.0.0
|\
| * 0c57573cece531f840a167aa0ccc29b178b6de42 2019-11-08 11:45:05 +0000  (master) after release 2.0.0
| * 22b04802cd5ee933de078344fa53a3e37b826913 2019-11-08 11:45:04 +0000  (tag: v2.0.0) release 2.0.0
| * 5ff7013b7a5f43d39b8da62361cfbfd4d3bf9a50 2019-11-08 11:45:03 +0000  after release 1.0.0
* | 543f61abf7f3091240e53fdec75a8a2687195921 2019-11-08 11:45:02 +0000  in branch-of-1.0.0
|/
* 02995edb3e6f14b8f9a83b84e3b8c7c8d9f60f86 2019-11-08 11:45:01 +0000  (tag: v1.0.0) release 1.0.0
* 2091354c7b8659f1846a876fbe2032fd1390d569 2019-11-08 11:42:00 +0000  (origin/master, origin/HEAD) Initial commit
```

## complex-tree Branch

https://github.com/giantswarm/gitsemver-test/tree/complex-tree

This is branch created from initial commit with a hundreds commits followed by
series non-fast-forward merges. There are no tagged commits in this branch so
resolving version has to check all the commits. The purpose of this branch is
to test potential performance issues when resolving version.

```
$ git log --graph --pretty=format:'%H %ci %d %s'
*   a42e026e60b4c191ffb29430f439ad4b3aced71b 2019-11-18 12:08:09 +0000  (HEAD -> complex-tree, origin/complex-tree) Merge branch 'complex-tree-5-5' into complex-tree
|\
| * f59528ff1e195678e277757497bb801590dc05a9 2019-11-18 12:08:04 +0000  commit in branch complex-tree-5-5
* |   d1b6116035771b32d3c28b083b7e26c878b61825 2019-11-18 12:08:08 +0000  Merge branch 'complex-tree-5-4' into complex-tree
|\ \
| |/
| * 5e1fd199ca62e69e5374bc1d533b4a1574492780 2019-11-18 12:08:03 +0000  commit in branch complex-tree-5-4
* |   abd42da00a1ca546e59c91479cdf9b8177449aa4 2019-11-18 12:08:07 +0000  Merge branch 'complex-tree-5-3' into complex-tree
|\ \
| |/
| * 670b173f6a9f43d17020e92ac89fe532b8b88ff3 2019-11-18 12:08:02 +0000  commit in branch complex-tree-5-3
* |   0adfabaae26533099920187a32de45a6e5abff9b 2019-11-18 12:08:06 +0000  Merge branch 'complex-tree-5-2' into complex-tree
|\ \
| |/
| * 54c7d6a03e4651e6046ab32ebabc21a9640a2c06 2019-11-18 12:08:01 +0000  commit in branch complex-tree-5-2
* |   01590a73563c985fdf7b4dc3353dbc6ac648cb19 2019-11-18 12:08:05 +0000  Merge branch 'complex-tree-5-1' into complex-tree
|\ \
| |/
| * 871bc71e27e283bab8cfd5f6ad5edc0fe6aa3e8c 2019-11-18 12:08:00 +0000  commit in branch complex-tree-5-1
|/
*   db0fe404ab87ba36c7b735fb48e6270b29ca0913 2019-11-18 12:07:59 +0000  Merge branch 'complex-tree-4-5' into complex-tree
|\
| * 36579af0484883a73b794c6fc1340656266ebe75 2019-11-18 12:07:54 +0000  commit in branch complex-tree-4-5
* |   b13b63c73cb299134f35bd63c36829b667b3a959 2019-11-18 12:07:58 +0000  Merge branch 'complex-tree-4-4' into complex-tree
|\ \
| |/
| * 98a43cf8d48e2bbb0be6db5faccfb302347c3e65 2019-11-18 12:07:53 +0000  commit in branch complex-tree-4-4
* |   6342c254d59ce2893e561399103a47ad7f46e519 2019-11-18 12:07:57 +0000  Merge branch 'complex-tree-4-3' into complex-tree
|\ \
| |/
| * 8e6179f364a2f52dd5eb4215637b13926889043a 2019-11-18 12:07:52 +0000  commit in branch complex-tree-4-3
* |   8508aacdc35629d760fb21075b5a996bb65f8280 2019-11-18 12:07:56 +0000  Merge branch 'complex-tree-4-2' into complex-tree
|\ \
| |/
| * f53990d66f70dea37d6c5c3ecc522d22450f2d39 2019-11-18 12:07:51 +0000  commit in branch complex-tree-4-2
* |   05a786c9a769e5f36bf3628b4765657e792fb6e2 2019-11-18 12:07:55 +0000  Merge branch 'complex-tree-4-1' into complex-tree
|\ \
| |/
| * 7c22911e74e8a5baf2962bfd10097882e585d485 2019-11-18 12:07:50 +0000  commit in branch complex-tree-4-1
|/
*   bf117c937c08edeb7c887f3346d67b6d28c8aa60 2019-11-18 12:07:49 +0000  Merge branch 'complex-tree-3-5' into complex-tree
|\
| * 3087d5d73356493e6b2d43449202b064d6608577 2019-11-18 12:07:43 +0000  commit in branch complex-tree-3-5
* |   220502b0c640275229de770275cfd965cb96787b 2019-11-18 12:07:48 +0000  Merge branch 'complex-tree-3-4' into complex-tree
|\ \
| |/
| * 3b37d9598cc0afa02b384ec5235f49c323b3b29c 2019-11-18 12:07:42 +0000  commit in branch complex-tree-3-4
* |   573f07a2f683f69b87c4678f72fa205dffd2924b 2019-11-18 12:07:46 +0000  Merge branch 'complex-tree-3-3' into complex-tree
|\ \
| |/
| * 7e38688b1f1c587f06792e5719870bf4c795fbb1 2019-11-18 12:07:41 +0000  commit in branch complex-tree-3-3
* |   db2353e19a51f9e793b8b7c92027a5e05fac4697 2019-11-18 12:07:45 +0000  Merge branch 'complex-tree-3-2' into complex-tree
|\ \
| |/
| * 986023a9849fe6c43560ef7fc79ebd02657e8447 2019-11-18 12:07:40 +0000  commit in branch complex-tree-3-2
* |   4d12c3993abf3b47b13f999a0f719bb78224abe9 2019-11-18 12:07:44 +0000  Merge branch 'complex-tree-3-1' into complex-tree
|\ \
| |/
| * 0a920f2f652aea5220a66f9bdb518c43ba941fc7 2019-11-18 12:07:39 +0000  commit in branch complex-tree-3-1
|/
*   755146dc14306481b66cb97fcf1ebdf0b3fb11cc 2019-11-18 12:07:38 +0000  Merge branch 'complex-tree-2-5' into complex-tree
|\
| * 02d2e922e0054e71e5473f8b89d1983c66704e2d 2019-11-18 12:07:33 +0000  commit in branch complex-tree-2-5
* |   637144425b7f4209d6c1a0b11d8d35abcda5365b 2019-11-18 12:07:37 +0000  Merge branch 'complex-tree-2-4' into complex-tree
|\ \
| |/
| * a414500f047190b1d6b41b73762c4bf9935c69a9 2019-11-18 12:07:32 +0000  commit in branch complex-tree-2-4
* |   bb450ad78444017e3261524b8c317f1fab0df475 2019-11-18 12:07:36 +0000  Merge branch 'complex-tree-2-3' into complex-tree
|\ \
| |/
| * 78ced2e2b9d446ee5f4411ebf5fffc314a5939f0 2019-11-18 12:07:31 +0000  commit in branch complex-tree-2-3
* |   20a6a0f07091f8f0885ee784c71e0e6e789b40e8 2019-11-18 12:07:35 +0000  Merge branch 'complex-tree-2-2' into complex-tree
|\ \
| |/
| * 77140c4515e4b2b56055741ccae734f2b04ae0dd 2019-11-18 12:07:30 +0000  commit in branch complex-tree-2-2
* |   2a4e4cadbaf3dfb1a9a53f6f2d6d98fc0d03bcc4 2019-11-18 12:07:34 +0000  Merge branch 'complex-tree-2-1' into complex-tree
|\ \
| |/
| * 01dc09b941a21f503176d5c3ccd2c4785a8d4628 2019-11-18 12:07:29 +0000  commit in branch complex-tree-2-1
|/
*   ef1ed018b72981af9bb149980f39b4e3c8b9780b 2019-11-18 12:07:28 +0000  Merge branch 'complex-tree-1-5' into complex-tree
|\
| * d722a628a81fb627a40a03d28c7d9a3d48338dd8 2019-11-18 12:07:22 +0000  commit in branch complex-tree-1-5
* |   28d4d65262df6bc76eaf211b42002d9a7567f30f 2019-11-18 12:07:27 +0000  Merge branch 'complex-tree-1-4' into complex-tree
|\ \
| |/
| * 7da3aa330e3b6b0c4be14113c1479370809df4e2 2019-11-18 12:07:21 +0000  commit in branch complex-tree-1-4
* |   43359f7ed971e3f6a0fe3f51f929c658a46c5b0e 2019-11-18 12:07:26 +0000  Merge branch 'complex-tree-1-3' into complex-tree
|\ \
| |/
| * 48e83fea25a068ca87caa9d1ac0dc249f347d1bf 2019-11-18 12:07:20 +0000  commit in branch complex-tree-1-3
* |   ac72a6408573c8235810fa6178396894682b03c0 2019-11-18 12:07:25 +0000  Merge branch 'complex-tree-1-2' into complex-tree
|\ \
| |/
| * 4b744e77f36152315ad4b67c14c56f9075095f46 2019-11-18 12:07:19 +0000  commit in branch complex-tree-1-2
* |   3a854d9e512970929c4093a047e06d2b6691178f 2019-11-18 12:07:24 +0000  Merge branch 'complex-tree-1-1' into complex-tree
|\ \
| |/
| * f329323ea04d5c80e87f894915eb1173c3dce435 2019-11-18 12:07:18 +0000  commit in branch complex-tree-1-1
|/
* 9113ad23314b3d7acf6b0b3e275ae6281bb0d4e4 2019-11-18 12:04:12 +0000  commit number 300
* 3bdce76eb754cbc12bac441be0b6da5095a52dd2 2019-11-18 12:04:12 +0000  commit number 299
* 4b36a9b84b7e3d0213500fc71e6dded6d9bc87e5 2019-11-18 12:04:12 +0000  commit number 298
* ...
* ...
* ...
* 4d3d4930a01d238fe3cd3e9a6d5a2f4da7c3a84b 2019-11-18 12:04:06 +0000  commit number 3
* d9f94f430b480bd37e5ca89b855b4e9581b6c53b 2019-11-18 12:04:06 +0000  commit number 2
* 5ee6d1dcdaf9c475ea6c64a8fffa01b6a5ef9cf9 2019-11-18 12:04:06 +0000  commit number 1
* 2091354c7b8659f1846a876fbe2032fd1390d569 2019-11-08 11:42:00 +0000  Initial commit
```

[gitsemver]: https://github.com/giantswarm/gitsemver

# weblog

my weblog.

## init

```shell
git clone git@github.com:CaiJimmy/hugo-theme-stack-starter.git
cd huto-theme-stack-starter
git remote set-url origin git@github.com:bob1118/bob1118.github.io.git
...

git add --all
git commit -m "init"
git push -u origin master
```

## checkout

```shell
git checkout git@github.com:bob1118/bob1118.github.io.git

```

## new

```shell
hugo new post/1st-to-do.md
hugo new post/2nd-with-image/index.md
```

## test

```shell
hugo server -D
```

## build

```shell
hugo -D -d ./docs -b https://tsinling.org
```

## deploy

```shell
git add --all
git commit -m "new post comit"
git push -u origin main
```

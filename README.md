# [Dev Knights](https://dev-knights.github.io/)
![logo](assets/images/logo.png)
## Become a member

If you want to **become an author**, leave us an [issue](https://github.com/dev-knights/dev-knights.github.io/issues) requesting contributor access to this repo.

## Install

1. Clone this repository

```
$ git clone git@github.com:dev-knights/dev-knights.github.io.git
```

2. Install dependencies

```
$ bundle install
```

3. Run dev server

```
$ rake serve
```

## Writing a post

You can write a post about anything you want related to software engineering or programming. Your post can be written in spanish or english.

In order to create a new post, you should create a new markdown file in `_posts` folder or run `rake new:post` to create a new valid post file.

After finishing your post, in order to publish it, change YAML value `published: false` to `published: true`

## Drafts

Drafts should be located in `_drafts` folder. You can create a new draft by running `rake new:draft` command.

After finish it, move it to `_posts` using rake command `rake publish_draft _drafts/name-of-your-draft.markdown`. *Don't forget to set `published` to true too*.

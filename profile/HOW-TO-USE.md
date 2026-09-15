# How to publish the profile README

`README.md` in this folder is written for your **GitHub profile README** — the
block that appears at the top of https://github.com/jepark1207.

GitHub shows it only if it lives in a repository whose name is exactly your
username.

## Steps

1. Create a new **public** repo named exactly `jepark1207`
   ([direct link](https://github.com/new?name=jepark1207&visibility=public)).
   GitHub will show a "✨ You found a secret!" note confirming it becomes your
   profile README.
2. Copy `profile/README.md` from this repo into the root of that new repo as
   `README.md`.
3. Commit and push. It appears on your profile immediately.

```bash
cd ~ && git clone https://github.com/jepark1207/jepark1207.git
cp ~/Portfolio/profile/README.md ~/jepark1207/README.md
cd ~/jepark1207 && git add README.md && git commit -m "Add profile README" && git push
```

## Before you publish

The links in it point to `github.com/jepark1207/portfolio`. Push **this** repo
under the name `portfolio` first, or edit the links to match whatever name you
choose.

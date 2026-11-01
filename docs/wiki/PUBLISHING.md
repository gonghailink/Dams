# Publishing these pages to the GitHub wiki

These files are the source for the GitHub wiki. This `PUBLISHING.md` is not a
wiki page, so don't copy it over.

A GitHub wiki is a separate git repo that doesn't exist until you create the
first page in the UI.

1. On GitHub, open the repo → **Wiki** → **Create the first page**, and save it
   with any content. (Enable Wikis in Settings → Features if the tab is
   missing.)
2. Then run:

```bash
git clone git@github.com:mahmoud-bebars/distributed-ai-memory-system.wiki.git
cd distributed-ai-memory-system.wiki
cp ../docs/wiki/*.md .
rm PUBLISHING.md
git add -A && git commit -m "Add project wiki" && git push
```

The wiki's default branch is `master`. Edits made in the wiki UI live in that
repo, so re-copy deliberately if you keep these files as the source.

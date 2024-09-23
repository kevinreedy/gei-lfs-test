# gei-lfs-test
[GitHub Enterprise Importer](https://github.com/github/gh-gei) is a great tool to migration one or more repositories across various GitHub Enterprises and Organizations. See [its documentation](https://docs.github.com/en/migrations/using-github-enterprise-importer/understanding-github-enterprise-importer/about-github-enterprise-importer) for more details on how to use it. Currently, LFS data is not migrated, so this repo provides some simple instructions on how to migrate that data.

First, start by ensuring Git LFS is installed. This will vary depending on your platform.

```
brew install git-lfs
git lfs install
```

Now let's add a binary file to this repository, but track it via LFS.

```
git lfs track github-mark.png
cp ~/Downloads/github-mark.png ./
git add .gitattributes github-mark.png
git commit -m 'Add github-mark.png via LFS'
git push origin main
```

Now let's migrate this repository from one organization to another. Consult [the GEI documentation](https://github.com/github/gh-gei?tab=readme-ov-file#github-to-github-usage-githubcom---githubcom) for full details.

```
export GH_SOURCE_PAT=[redacted]
export GH_PAT=[redacted]
gh gei migrate-repo \
  --github-source-org src-org \
  --source-repo gei-lfs-test \
  --github-target-org dest-org \
  --target-repo gei-lfs-test

gh gei wait-for-migration --migration-id [redacted]
```

After migration, you'll notice that LFS data was not migrated - only text pointers exist. So let's go back into the original repo and migrate LFS data.

```
git lfs fetch --all
git lfs push https://github.com/dest-org/gei-lfs-test.git --all
```

Now all of our LFS data has been migrated!

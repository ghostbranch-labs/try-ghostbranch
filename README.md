# Try Ghostbranch

A throwaway repo for trying [Ghostbranch](https://ghostbranch.site) in about five minutes, without pointing it at a repo you care about.

Ghostbranch gives a public GitHub repo private feature branches. They live in a private "shadow" copy of the repo, which runs the same CI, and nothing reaches the public repo until you publish it.

This repo has one file, `hello.txt`, and a CI check that it isn't empty. The check runs on pull requests only, which is what Ghostbranch's gate needs.

## 1. Make your copy

Click **Use this template** → **Create a new repository**, at the top of this page. GitHub makes a full copy of this repo in your own account, so there's nothing to download. Keep it **public**, and name it `try-ghostbranch` (any name works; the steps below use that one).

With the [GitHub CLI](https://cli.github.com), one command makes the copy and clones it, so you can skip the `git clone` in step 3:

```sh
gh repo create try-ghostbranch --template ghostbranch-labs/try-ghostbranch --public --clone
```

## 2. Install Ghostbranch

Pick one route from the [install page](https://ghostbranch.site/docs/getting-started/install), for example:

```sh
brew install ghostbranch-labs/tap/ghostbranch     # macOS, Linux
scoop bucket add ghostbranch https://github.com/ghostbranch-labs/scoop-bucket
scoop install ghostbranch                         # Windows
npm install -g ghostbranch                        # anywhere with Node
```

## 3. Set it up

Clone your copy (replace `<you>` with your GitHub username), then run `init` in it:

```sh
git clone https://github.com/<you>/try-ghostbranch
cd try-ghostbranch
ghostbranch init
```

Press Enter at each question. `init` will:

- log you in to GitHub, if you aren't already, with a code you approve in the browser;
- ask you to install the Ghostbranch app. Choose **Only select repositories** and pick only your `try-ghostbranch` copy. The app gets the shadow repo automatically when `init` creates it. [What each permission is for](https://ghostbranch.site/docs/security/tokens-and-keychain#the-github-app-login);
- create the private repo `<you>/try-ghostbranch-shadow`, and install a push guard in this checkout.

`init` never writes to your public copy.

## 4. Make a private change

```sh
ghostbranch feature start feat/try-ghostbranch
echo "And hello from a private branch." >> hello.txt
git commit -am "Say hello privately"
git push shadow feat/try-ghostbranch
```

The branch is in the private shadow repo only. Look at your public copy on GitHub: it has no `feat/try-ghostbranch` branch.

## 5. Gate it

```sh
ghostbranch gate feat/try-ghostbranch
```

The gate rebases the change on public `main`, scans it for leaks, and runs the CI on a draft pull request in the shadow repo. It takes about a minute here.

## 6. Publish it (optional)

```sh
ghostbranch promote feat/try-ghostbranch
```

It shows what it would publish, then asks you to type a one-time code it shows at your terminal, so only a person can publish. It then opens a normal pull request on your public copy. Press Enter when it asks what to do with the shadow branch. Merge the pull request on GitHub, then run `ghostbranch sync`.

## Cost

The shadow repo is private, so its CI uses your GitHub Actions minutes. GitHub Free includes 2,000 a month, and each gate here uses about one.

## Clean up

```sh
ghostbranch remove
ghostbranch guard uninstall
```

Then, on GitHub, delete `<you>/try-ghostbranch-shadow` and your `try-ghostbranch` copy in each repo's **Settings** → **Danger Zone**. Ghostbranch never deletes a repo. To take the app off too: **Settings** → **Applications** → **Installed GitHub Apps** → **ghostbranch-cli** → **Configure**.

## Next

Ready for your own repo? Start at [Set up a repo](https://ghostbranch.site/docs/getting-started/set-up-a-repo).

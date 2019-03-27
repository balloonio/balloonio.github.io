---
title: Manage Multiple GitHub Accounts on the Same Laptop
date: 2019-03-23 22:56:24
tags:
  - github
  - ssh
categories:
  - tutorial
---

Have you ever had the pain to switch between laptops because one has the work GitHub account set up while the other has your personal GitHub account set up? Have you ever dreamed of how to work on repos from both accounts on one single laptop? This blog shows you how to achieve that with SSH.

<!-- more -->

# Generate SSH Key Pairs

Create a SSH key public/private key pair for each GitHub account that you would like to manage on the same pc. Let's start the example with personal account.

```bash
cd ~/.ssh
ssh-keygen -t rsa -C "bolun.zhang@personal_github_account_email.com"
```

This creates a new ssh key, using the provided email as a label:

```bash
> Generating public/private rsa key pair.
```

When you're prompted to "Enter a file in which to save the key", store your key as `~/.ssh/id_rsa_personal`:

```bash
Enter a file in which to save the key (/Users/you/.ssh/id_rsa):
```

(Optional) At the prompt, type a secure passphrase.

```bash
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

Now you should have two files under `~/.ssh` dir - `~/.ssh/id_rsa_personal` and `~/.ssh/id_rsa_personal.pub`. They are the private key and the public key for your personal GitHub account. Now, let's do the same for your company GitHub account. Follow the exactly same procedure as above, but use `bolun.zhang@company_github_account_email.com` and `~/.ssh/id_rsa_company` instead. If you did all those correctly, you should see the following files:

- `~/.ssh/id_rsa_personal`
- `~/.ssh/id_rsa_personal.pub`
- `~/.ssh/id_rsa_company`
- `~/.ssh/id_rsa_company.pub`

# Add SSH Key to GitHub

Add `id_rsa_personal.pub` key to your personal GitHub account; add `id_rsa_company.pub` to your company GitHub account. See the instructions from [here](https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account).

# Add SSH Config

Create a new file `config` under `~/.ssh` to manage the keys. We need this because we have multiple SSH keys and the system would not know which one to use unless we define the config.

```bash
cd ~/.ssh
touch config
```

Define the `config` file as following

```config
Host workgithub
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_company

Host mygithub
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_personal
```

# Update Git Config

SSH keys determine the permissions whether you are allowed to clone or commit to a repository. However, rather surprisingly, the authorship of the commits themselves are determined by the user configured in `.gitconfig`. What this means is you can end up showing as the author of a commit to a repository that you are not permissioned.

For example, you have a private repository *WORK* under your company GitHub account and you have a private repository *HOME* under your personal GitHub account. Obviously, you company account does not have access to *HOME* and your personal account does not have access to *WORK*. As scary as it sounds, if you have your `.gitconfig` configured to be company account and make commits to *HOME*, you will end up making the commit without permission issue but with your company account username shown as the author of the commit. For detailed information, please see [here](https://help.github.com/en/articles/why-are-my-commits-linked-to-the-wrong-user).

To avoid this issue, make sure your `.gitconfig` is configured to use personal username/email within personal repositories and company username/email within company repositories. There are multiple ways to achive this. You can create local `.gitconfig` for each repository. Or alternatively, you can manage multiple `.gitconfig` files in a similar fashion as what we did with SSH keys. Here is how:

Create `~/.gitconfig-company` as following:

```config
[user]
	name = Bolun Zhang
	email = bolun.zhang@company_github_account_email.com
```

Create `~/.gitconfig-personal` as following:

```config
[user]
	name = Bolun Zhang
	email = bolun.zhang@personal_github_account_email.com
```

Now, change the original `~/.gitconfig` to the following:

```
[includeIf "gitdir:~/CompanyRepos/"]
    path = ~/.gitconfig-company
[includeIf "gitdir:~/PersonalRepos/"]
    path = ~/.gitconfig-personal
```

You can probably already guess what this config file does - it applies different `.gitconfig` file based on the current directory. Now you just need to organize your workspace so that company repositories are only cloned under `~/CompanyRepos/` while personal repositories under `~/PersonalRepos/`.

# Usage

Now we are completely done with all the set up.

To clone a work repository:

```
git clone workgithub:<organization-name>/<repo-name>
```

To clone a personal repository:

```
git clone mygithub:<your-username>/<repo-name>
```

# Issues and Solutions

- If you are working on a repository that is already cloned and now have some permission issues after the setup, modify the `<repo-dir>/.git/config` file to make sure the `[remote "origin"]` section matches the SSH host.

    For example, you may have cloned the repository before using HTTP. In that case, the `<repo-dir>/.git/config` file will show the following as `[remote "origin"]`:

    ```config
    [remote "origin"]
            url = https://github.com/<organization-name>/<repo-name.git
            fetch = +refs/heads/*:refs/remotes/origin/*
    ```

    Change it to the following so that Git knows to use SSH key for permission instead:

    ```config
    [remote "origin"]
            url = workgithub:<organization-name>/<repo-name>
            fetch = +refs/heads/*:refs/remotes/origin/*
    ```

- If you are using SSH instead of HTTP. If you have any osxkeychain or credential helper enabled, you might have a cached username for HTTP access. The cached credential sometimes enforces an HTTP connection instead of SSH. A simple solution is to remove any cached GitHub credential.

- In the SSH config, don't give the hostname a meaningful resolvable hostname. For example, at the place where we put `workgithub` as the host name, you can put any name you want but not something like `github.com`. The reason is sometimes `github.com` may appear in your personal repository's `<repo-dir>/.git/config`; it may resolve into your company SSH key since you are using `github.com` as your company SSH key host name. This can create permission issues that are hard to detect. One handy tool to debug SSH permission is run `ssh -vT git@<host-name>`. e.g. `ssh -vT git@github.com`, `ssh -vT git@workgithub` or `ssh -vT git@mygithub`

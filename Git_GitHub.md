---
layout: default
title: "Git / GitHub"
---

{% include Computing_nav.html %}

- [Git commands](./Git_GitHub#git-commands)
- [Setting up GitHub and securing it](./Git_GitHub#setting-up-github-and-securing-it)
- [Setting up a GitHub page](./Git_GitHub#setting-up-a-github-page)
- [Running the GitHub page locally](./Git_GitHub#running-the-github-page-locally)
- [Markdown tips to GitHub Pages](./Git_GitHub#markdown-tips-to-github-pages)

# Git / GitHub / GitHub Pages Tips

GitHub provides a great free service. At the time of writing (early 2021), some of the documentation is outdated or wrong. I have collected tips here, that helped me setting it up. All the local commands are valid for OpenSUSE Tumbleweed. Use the following command to install git on your desktop:
```
sudo zypper in git
```

## Git commands

I am currently switching from SVN to GIT. So I will collect a few tips here primarily for my own use.

### Git clone
```
git clone https://github.com/wedesoft/anymeal.git
```

### Git pull
-u: [The importance of using -u](https://www.interglobalmedianetwork.com/blog/2020-02-15-the-importance-and-advantage-of-git-push-u/)

### Git remove file
```
git rm file
```

###Git rename file
```
git mv old new
```

### Git diff
```
git diff file
```

### Undo local changes (one way)
```
git checkout -- file
```

### Commit changes
```
git add --all
git commit -S -m "Comment"
git push -u origin main
```

## Setting up GitHub and securing it

After creating a GitHub account, a couple of steps should be taken to secure the use.

### Secure Email

**Login**
**Top right of the page click the icon and select Settings**
**On the left select Emails** and choose the following settings
- Below "Primary email address" it will show your @users.noreply.github.com email address
- Keep my email addresses private 
- Block command line pushes that expose my email 

On your desktop issue the following command to use that address for commits. This sets the email address globally to be safe
```
git config --global user.email "...@users.noreply.github.com"
```
To choose a different address for a specific repository issue the following command inside the repository
```
git config user.email "email@example.com"
```
### Create a specific GPG key to use with GitHub

[GitHub docs](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/generating-a-new-gpg-key)

To generate a new GPG key issue the following command:

```
gpg --full-generate-key
```
You will we asked a couple of questions. Choose the following
- Kind of key: (1) RSA and RSA (default)
- Keysize: 4096 bits
- Key is for: 0 = key does not expire
- Real name: 
- Email address: your @users.noreply.github.com email address
- Provide a good passphrase


Now list your secret keys:
```
gpg --list-secret-keys --keyid-format LONG
```
This will now list the new key. The keyid is the bit after "sec   rsa4096/" for the key in question.

Print the public needed to register the key with GitHub:
```
gpg --armor --export keyid
```
### Register the GPG key with GitHub and locally
[GitHub docs](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/adding-a-new-gpg-key-to-your-github-account)

**Login**
**Top right of the page click the icon and select Settings**
**On the left select SSH and GPG keys**
**New GPG key** and choose the following values
- The output of **gpg --armor --export keyid** (see above)
- Insert the bit starting with "-----BEGIN PGP PUBLIC KEY BLOCK-----" to "-----END PGP PUBLIC KEY BLOCK-----"

To register the key locally issue:
```
git config --global user.signingkey keyid
```
where keyid is again the output of **gpg --armor --export keyid** (see above)

### Sign a commit or tag

[GitHub docs](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/signing-commits), 
[GitHub docs](https://git-scm.com/book/en/v2/Git-Tools-Signing-Your-Work)

```
git commit -S -m your commit message
```

To push to GitHub:
```
git push
```

```
git tag -s v1.5 -m 'my signed 1.5 tag'
```

### Add SSH key to GitHub to avoid having to type login and password

Check in **~/.ssh** for the public key
**Login**
**Top right of the page click the icon and select Settings**
**On the left select SSH and GPG keys**
**New SSH key** and choose the following values
- Title: host name SSH public key for user name
- Add contents of **~/.ssh/id_rsa.pub**

Test with:
```
ssh -T git@github.com
```

## Setting up a GitHub page

[GitHub docs](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/creating-a-github-pages-site)

Choose a theme from these [available ones](https://pages.github.com/themes/)

**Login**
**Top right of the page click the + icon and select New repository** Choose the following settings
- Owner: username
- Repository name: username.github.io
- Public
- Add README file

After a couple of seconds your webpage is available at: **https://username.github.io/**

**Navigate to repository username/username.github.io **
**Settings / scroll down to GitHub Pages** Choose the following settings
- Source: main branch and root
- Select a theme: e.g. dinky

Checkout the webpage repository locally to start making first changes:
```
mkdir GitHubPage
cd GitHubPage
git clone ssh://git@github.com/username/username.github.io
cd username.github.io
echo "Hello World" > index.md
git add --all
git commit -S -m "Initial commit"
git push -u origin main
```
**Important** GitHub now uses main instead of master. I believe that Git will use that in the future, too.

Set variables in your _config.yml file.

To change the default layout, copy the version from the theme _layouts/default.html to your repository and adapt.


## Running the GitHub page locally

[Installation Linux](https://jekyllrb.com/docs/installation/other-linux/), 
[Installation Ubuntu](https://jekyllrb.com/docs/installation/ubuntu/)

To install Jekyll locally on OpenSUSE issue the following commands:
```
sudo zypper in -t pattern devel_ruby devel_C_C++
sudo zypper install ruby-devel
```

Add the following lines to your **~/.bashrc**:
```
# Install Ruby Gems to ~/gems
export GEM_HOME="$HOME/gems"
export PATH="$HOME/gems/bin:$PATH"
```

Now issue the following commands:
```
source ~/.bashrc
gem install jekyll bundler
cd ~/gems/bin/
ls
```
For each file listed:
```
ln -s bundler.ruby2.7 bundler
```
Now install missing bundles and create an new Jekyll site. We need to copy some files from there
```
cd .../username.github.io
bundle install
cd ..
mkdir jekyll
cd jekyll
jekyll new .
cd ../username.github.io/
cp ../jekyll/Gemfile .
echo "Gemfile*" > .gitignore

```
In Gemfile
- Disable **#gem "jekyll", "~> 4.1.1"**
- Enable  **gem "github-pages", group: :jekyll_plugins**

Now we can get the site running:
```
bundle update
bundle exec jekyll serve --trace
echo "_site/" >> .gitignore
```
Point your browser to **http://127.0.0.1:4000/** to see your site. Jekyll tracks changes in the files making up the site and will update automatically when you change the content or add new files. Just refresh your browser to see the changes.


## Markdown tips to GitHub Pages

These two webpages ware very helpful for Markdown [mastering markdown](https://guides.github.com/features/mastering-markdown/) and [basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/basic-writing-and-formatting-syntax). Be careful, not all markdown works everywhere on GitHub. Automatic links do not work GitHub Pages.

### Add a page to your site

Add the *.md file to your GitHub Pages sources directory
```
cd .../username.github.io
touch About.md
```
Add frontmatter to file between --- and --- lines
```
---
layout: default
title: "PAGE TITLE"
---
```
The layout must be default or Post. The [Dinky](https://github.com/pages-themes/dinky) style only provides a **default** [layout](https://github.com/pages-themes/dinky/tree/master/_layouts).

### Change the style of the pages

Here are the commands for a simple change to the style provided by the theme:
```
cd .../username.github.io
mkdir assets && cd assets
mkdir css && cd css
```
create **style.scss** and enter
```
---
---

@import "{{ site.theme }}";

h2.header {
        font-size: 20px;
        color: #FDFDFB;
}
```

### Links

Use the []() syntax to create links on GitHub Pages. Headings can be linked to using # and the heading text in the () part. To link to a heading on a different page use 

`[text for baz heading](./Git_GitHub#baz)` 

with Git_GitHub being the filename of the *.md file (without the .md) and baz the text of the heading (all lower case and replace spaces with -"). These ids are auto generated by Jekyll, the web site generator.


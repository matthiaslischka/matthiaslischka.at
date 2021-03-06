---
title: GitHub Jekyll Theme best practice
layout: post
tags: [GitHub, Jekyll]
---
My blog is based on [Jekyll](https://jekyllrb.com/), markdown files transformed to static html, hosted on [GitHub Pages](https://pages.github.com/). I just now changed the theme I use. Thereupon I optimized the way I arrange my repositories around that and want to share my learnings.

![Jekyll + GitHub]({{ "/assets/posts/2018-12-03-github-jekyll-best-practice.png"}})
Up until now I used to fork the [Jekyll](https://jekyllrb.com/) theme that I wanted to use. I added all my content to my fork and still could pull-request changes back to the source repo. However, when I now wanted to change the theme to another theme I realized that I can not change the fork. My blog repository will always be a fork of the first theme I had chosen. GitHub itself states in its helpfiles that you have to contact the support team if you want to change a fork to a self-standing repo. I did not want to harass them with my little problem. So I changed the setup of my blog from scratch.

## #1 Gem-based themes

The easiest way of using a theme is when it is gem-based and deployed to [rubygems.org](https://rubygems.org/search?utf8=%E2%9C%93&query=jekyll-theme).

You can just link the theme as gem with e.g. `gem jekyll-theme-minimal` in the `Gemfile` and then use it in `_config.yml` with `theme: jekyll-theme-minimal`. You can [override theme defaults](https://jekyllrb.com/docs/themes/#overriding-theme-defaults) for some customizations. I would suggest to only customize content related data this way (e.g. add a button to a CV that does not exist in the base theme).  Check out the [official documentation](https://jekyllrb.com/docs/themes/).

For all further customizations to the theme and when it is hosted on GitHub I would suggest solution #2, as gem-based themes are also `jekyll-remote-theme` compatible. When the theme is not hosted on GitHub you could publish an own customized version of the gem and use that. Don't mix your content too much with theme stuff.

## #2 '[jekyll-remote-theme](https://github.com/benbalter/jekyll-remote-theme)' compatible themes

[jekyll-remote-theme](https://github.com/benbalter/jekyll-remote-theme) is a plugin for [Jekyll](https://jekyllrb.com/) that allows you to use a theme that is not located in your repository but in any public repository on GitHub. Effectively separating your content data from the theme data.

So when the theme you want do use does support `jekyll-remote-theme` you should use it.
 - Create an empty repository that will be your content-repository. Add your posts there (_/_posts_/).
 - Fork the theme you want to use and add this fork as remote theme. Check out the [doku](https://github.com/benbalter/jekyll-remote-theme/blob/master/README.md) and this [working demo](https://github.com/matthiaslischka/jekyll-uno-remote-theme-demo).

The fork freezes the version of the theme. Otherwise, when you use the theme directly and not a fork, your blog would always apply the newest version of it when regenerated. While that might be ok, I personally prefer to be in charge of updates. Breaking changes might occur.
In addition the fork repository is for all your customizations to the theme and allows you to pull-request changes back to the origin.

When you want to upgrade the theme version you can `fetch` and merge the new commits from the base into the fork and then trigger a rebuild of your blog.

When you want to change the theme only minor configuration changes in your content repository are necessary.

## #3 Not '[jekyll-remote-theme](https://github.com/benbalter/jekyll-remote-theme)' compatible
 
 _* Consider [patching it](https://github.com/joshgerdes/jekyll-uno/pull/97) to support `jekyll-remote-theme` *_
 
 - Create a new empty repo on GitHub that will be your blog
 - Clone the GitHub repo you want your blog to be based on locally
 - Rename "origin" to "upstream" `$ git remote rename origin upstream`
 - Add your own empty repo as origin `$ git remote add origin http://github.com/YOU/YOUR_REPO.git`
 - Push master/gh-pages branch to your empty GitHub repo `$ git push origin master`
 - Fork the GitHub repo your blog is based on
 - Add the fork as _personalUpstream_ `$ git remote add personalUpstream https://github.com/YOU/FORKES_REPO.git`

You will end up with a personal repository that has all the commits from the base repository and two upstreams, the base repository and your fork. In this repository only content data should be added. (_config.yml, _posts/, images/, assets/ ...)

_upstream_ is the link to this base repository. This allows you to `fetch` all changes to the base and merge them in your repo. Use this to get bugfixes, new features, ...

The fork of the base repository is for all _your changes_ that are not content related but rather changes to the base. Commit those in this repository and use the _personalUpstream_ to `fetch` and merge them into your "content repository". This allows you to still create pull request back to the base without having your content hard-linked (fork) to it.

If you want to change your theme later you just need to change _upstream_ and _personalUpstream_ and overwrite the base-data with your new base. You keep the commit history of all your content data.

When everything is setup you can test your "content-repo" with `$ git remote -v`. It should look like:
```cmd
origin  https://github.com/YOU/YOUR_REPO.git (fetch)
origin  https://github.com/YOU/YOUR_REPO.git (push)
upstream        https://github.com/SOMEBODY/BASE_REPO.git (fetch)
upstream        https://github.com/SOMEBODY/BASE_REPO.git (push)
personalUpstream        https://github.com/YOU/BASE_REPO.git (fetch)
personalUpstream        https://github.com/YOU/BASE_REPO.git (push)
```

## Final thoughts
People tend to just fork a theme or copy it and add their content. While that is the easiest and fastest way it is also the uncleanest way imho. GitHub forks are hard-linked to its base repository and should be used with caution. Don't be afraid to work with multiple repositories. Separation of concerns and loose coupling are key, as always. You want to separate your content from the rest as much as possible. Gem-based themes and `jekyll-remote-theme` are just the very thing for that.

Open for further ideas or other solutions...
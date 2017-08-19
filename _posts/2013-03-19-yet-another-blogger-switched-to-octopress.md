---
ID: 62247
post_title: >
  Yet another blogger switched to
  Octopress
author: admin
post_excerpt: ""
layout: post
permalink: >
  http://3mdeb.kleder.co/blog/yet-another-blogger-switched-to-octopress/
published: true
post_date: 2013-03-19 03:01:00
tags:
  - blog
  - octopress
  - blogger
  - configuration
  - seo
  - tricks
categories:
  - Blog
---
### 1\. Introduction

It's been over 3 months when I published my last post. In chaos of daily jobs I realize that there is so much things that I don't like in Google Blogger that I have to find different solution for my blogging aspirations. So first of all requirements, what do I hate most in Blogger:

*   **editor** - I used to vim and cannot stand that there is no bracket matching, vertical/block modification, and other cool stuff that I use
*   **the way that page was stored** - I want to have control on my blog and if I can use for example git to track its content that would be great
*   **lamish** - with Blogger I learned nothing, I look for something related with new trends in web development, it looks that ruby on rails is the most interesting framework

And I found [Octopress][1] it has all I need. The page states "A blogging framework for hackers". I don't know if I can or should call myself hacker but the idea is great for me.

So I start to move my blog to Octopress. Of course there is a plenty of things to do but also many to learn.

Lets start with first part of hints that I found useful:

### 2\. Blogger post conversion to markdown and import to Octopress

**Update**: *after I wrote this post I found interesting article on [this page][2]*

*   Export Blogger posts. To do this go to your blog `Design->Settings->Other->Export blog->Donwload blog`
*   Convert xml to html files. I used latest modification of [import.rb][3] but you probably can find better solution.
*   Convert html files to markdown for example by using [reverse_markdown][4]: `bash
sudo gem install reverse_markdown
d path/with/your/html/files
find -name "*.html" | sed -e 's/.html//' | xargs -n 1 -I @ sh -c 'reverse_markdown @.html > @.markdown'
mv *.markdown path/to/your/octopress/source/_posts/`
*   At the end of this process you should take a look at generated code, so setup your octopress and run `rake preview`
*   Fix syntax and enjoy your posts in markdown. 
    *   useful thing here for me was markdown syntax highlighting in vim by tpope [vim-markdown][5]
    *   liquid syntax [vim-liquid][6]
    *   also setting spellchecker could be useful, add below line to your `$HOME/.vimrc` and you will get spell cheking with en_us dictionary and word wraping on 80 character: `" blogging with markdown
au BufRead,BufEnter *.markdown setlocal textwidth=80 formatoptions+=w spell spelllang=en_us`

### 3\. Octopress configuration - tricks that I used for this blog

*   Configure your social networks as it was explained in [Octopress documentation][7]
*   It is good to register on [Disqus][8] the idea of keeping comments separated from blog is very good. I realized it when was trying migrate my comments from Blogger and didn't have installed Disqus.
*   [Google Analytics][9] and [AddThis][10] also are very helpful in tracking how your content was shared and what of this stuff has greatest interest. I will write article how to integrate [AddThis][10] because there is lack of information about it in google.
*   Header background image can be changed by dealing with sass files. I did this in `sass/custom/_layout.scss` using below lines: `css
body > header[role="banner"] {
background-image: url(/images/header_bg2_1200x168.png);
background-repeat: no-repeat;
}`
*   If you add some background you probably want to set position of your title and subtitle. You can do this in the same file as background image using something like this: \``\`css body > header h2 { position:relative; padding-left:6.6em; }

body > header h1 { font-weight:bold; position:relative; padding-left:3em; } \``\` * Do not forget about updating your Octopress frequently. Instructions how to do that can be found in [documentation][11]

### 4\. SEO

Most interesting article about SEO is surprisingly on top of google search ['octopress seo'][12]. Main thing that I take from the article is that Octopress already have built in support for SEO by additional elements in post header. This elements are `keywords:, description:` for example:

## \``\`

layout: post post_title: "Another blogger switched to Octopress" post_date: 2013-03-19 03:01 comments: true categories: "about this blog" published: false keywords: blog,octopress,blogger,configuration,seo,tricks

## description: Octopress configuration

\``\` Octopress will use keywords and description to build meta section of every website on your blog.

To not forget keyword and description section in my posts I modified Rakefile: `diff
diff --git a/Rakefile b/Rakefile
index d3a1cb0..3e2ced7 100644
--- a/Rakefile
+++ b/Rakefile
@@ -111,6 +111,9 @@ task :new_post, :title do |t, args|
     post.puts "date: #{Time.now.strftime('%Y-%m-%d %H:%M')}"
     post.puts "comments: true"
     post.puts "categories: "
+    post.puts "published: false"
+    post.puts "keywords:"
+    post.puts "description:"
     post.puts "---"
   end
 end`

### 5\. Others

Few things that were not so obvious for me:

*   to create category name that contain few words like `about this blog` syntax like this: \``\` categories: 
    *   about this blog `not like this:` categories: about this blog # or "about this blog" \``\`
*   ordered and unordered lists are not indented by default, this code in `sass/custom/_layout.scss` fix this behavior: `css
article {
ol, ul {
padding-left: 3em;
}
}`

Hope this tips are useful for you.

 [1]: http://octopress.org
 [2]: https://github.com/mojombo/jekyll/wiki/blog-migrations
 [3]: https://gist.github.com/dnagir/1765496/forks
 [4]: https://github.com/xijo/reverse_markdown
 [5]: https://github.com/tpope/vim-markdown
 [6]: https://github.com/tpope/vim-liquid
 [7]: http://octopress.org/docs/configuring
 [8]: http://disqus.com/
 [9]: http://www.google.com/analytics/
 [10]: http://www.addthis.com/
 [11]: http://octopress.org/docs/updating/
 [12]: http://www.yatishmehta.in/seo-for-octopress
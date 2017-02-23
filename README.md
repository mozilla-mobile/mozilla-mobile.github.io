# mozilla-mobile.github.io
##Mozilla Mobile Blog

The [Mozilla Mobile Blog][blog-link] runs on [Github Pages][github-pages] with [Jekyll][jekyll].

## Submit a new blog post

* Clone the repo: `git clone https://github.com/mozilla-mobile/mozilla-mobile.github.io`
* `cd mozilla-mobile-github.io`
* Create a new file in `./_posts` with a name that matches the format `YYYY-MM-DD-post-name.markdown`
* Use markdown to write your blog post
* Save images in `./images` and link to them in your post using `[image description]({{site_url}}/images/img.jpg)`
* Commit your new file(s) 
* Push to master
* Your blog post is published!

N.B - only members of the mozilla-mobile team can publish posts

You can read more about how to write posts for Jekyll sites at their [website][jekyll-posts].

## Run the site locally

* Setup RubyGem environment if you do not already have one
* `gem install jekyll bundler`
* Clone the repo: `git clone https://github.com/mozilla-mobile/mozilla-mobile.github.io`
* `cd mozilla-mobile-github.io`
* Install the correct version of Jekyll, the theme and github pages: `bundle update`
* Run the site server: `bundle exec jekyll serve`
* Now browse to `http://localhost:4000`


The site is setup to autoload changes to all files except configuration changes found in `_config.yml`, so there should be no need to do anything but refresh the site to see your changes.




[blog-link]: https://mozilla-mobile.github.io
[github-pages]: https://pages.github.com/
[jekyll]: https://jekyllrb.com/docs/home/
[jekyll-posts]: https://jekyllrb.com/docs/posts/

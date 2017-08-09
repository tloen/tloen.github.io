# Kikofri

Kikofri is a theme for Jekyll that's a fork of the [Kiko theme](https://github.com/gfjaru/Kiko) by [@gfjaru](https://twitter.com/gfjaru).

It came about because I like my themes like I like my music: ugly, old and barren. It's an assortment of hacks filtered through web-design choices from the turn of the millennium.

The theme doesn't use any Google Analytics spyware by default. If you necessarily need stuff like that consider using [piwik](https://github.com/piwik/piwik) instead. Kikofri doesn't rely on Google Fonts API for font rendering and the like. Some metadata for search engines is provided by [jekyll-seo-tag](https://github.com/jekyll/jekyll-seo-tag).

You'll see it undead here [http://kikofri.johantkatiska.se/](http://kikofri.johantkatiska.se/)

## To do:

* Clean up redundant markup
* Reevaluate the need for four layouts
* Listen to Good Friendly Violent Fun by Exodus

## How to:

1. Fork this repository and/or...
2. Clone the repository to your computer.<br />`git clone https://github.com/YOURUSERNAME/Kikofri`  
3. Run it.<br />`jekyll serve`
4. If that doesn't work you might want to take a look at the Gemfile in the cloned repository and satisfy dependencies where it is needed.
5. Go to http://127.0.0.1:4000 in your browser.

### General pointers:

* If you don't use Github remove the line `gh-pages` in the Gemfile in addition to consulting the [Jekyll documentation](https://jekyllrb.com/docs/home/) for [deployment options](https://jekyllrb.com/docs/deployment-methods/)
* The same goes for `jekyll-seo-tag` and `jekyll-paginate`. If you don't want to use pagination you should also make some changes to the index.html file
* For indented paragraphs type: `µµ`
* Tags can be sorted first under `/tags` by making the first letter an Upper-case one in the post's front matter
    - To sort something last make the first letter a lower-case one 
    - To just sort tags alphabetically use only upper- or lower-case letter consistently throughout posts
* Kikofri uses the [Ubuntu font](http://font.ubuntu.com/). If you wish to use another font change or remove these files/lines:
    - fonts/
    - fonts.css
    - \_layouts/default.html (look for: `<link href="/fonts.css"...`)  
    - \_layouts/default_post.html (look for: `<link href="/fonts.css"...`)
    - style.scss (look for: `font-family: 'Ubuntu'...`)

## Make it a little less ugly:

1. Change the name and modify nav at `_config.yml`.
2. Make some appropriate changes — or inappropriate if you're into those kind of things — in `about.md`.
3. Navigate to the `_posts` folder and remove the example content by writing `rm *` in your terminal (make sure that you're in the right folder)
 - while you're at it create a post of your own like so: `touch YYYY-MM-DD.md`
 - then do something like this: `echo -e "---\ntitle: 'Friends Don't Lie.'\ndescription: 'No more.'\nauthor: 'Eleven'\n---" > YYYY-MM-DD.md`
4. Type away! Reject my reality and substitute your own!

## License

The Kikofri theme by [kxxvii](https://github.com/kxxvii) is released under the [Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0). The Kiko theme by [@gfjaru](https://twitter.com/gfjaru) is released under the [MIT License](https://opensource.org/licenses/MIT). 

The Ubuntu Font Family is distributed under the [Ubuntu Font License](http://font.ubuntu.com/licence/).


```
   Copyright 2017 kxxvii

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
```

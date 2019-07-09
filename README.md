# K I K O F R I

Kikofri is a theme for Jekyll that's a fork of the [Kiko theme][1] It came
about because I like my themes like I like my music: ugly, old and barren. It's
an assortment of hacks filtered through web-design choices from the turn of the
millennium.

More information about the theme and it's quirks can be found [here][2.1]. A
lot of stuff has changed since the last update, '[Kikofri Classic][2.3]' if you
will. Even more importantly, a lot of junk got dumped and other junk got
simplified.

You'll see it undead [here][2.2].

[1]: https://github.com/gfjaru/Kiko

## To do:

* What amounts to "bug-fixes", not much else
* Listen to Good Friendly Violent Fun by Exodus

## How to:

1. Fork or clone this repository,
2. Run it: `jekyll s`
3. If that doesn't work see `General pointers`.

### General pointers:

* If you use GitHub you might want to add `gh-pages` in the Gemfile in addition
  to consulting the [Jekyll documentation](https://jekyllrb.com/docs/home/).
* Various stuff, like indentation with `µµ`, can be used.
  `µ#[somelink][someref]#µ` creates Wikipedia-Style references. I call these
  liquid tweaks [micros][3].
* Tags can be sorted first under `/tags` by making the first letter an
  Upper-case one in the post's front matter
    - To sort something last make the first letter a lower-case one 
    - To just sort tags alphabetically use only upper- or lower-case letter
      consistently throughout posts

## Make it a little less ugly:

1. Change the name and modify nav at `_config.yml`.
2. Make some appropriate changes — or inappropriate if you're into those kind
of things — in `about.md`.
3. Navigate to the `_posts` directory and remove the example content by writing
   `rm *` in your terminal window. (Make sure that you're in the right folder)
   Might want to do the same under `assets/images`.
   - While you're at it create a post under `_posts/READ-DA-TE.md` with some
     front matter similar to this:
   ```
   title:       "Friends Don't Lie."
   description: "No more."
   author:      "Eleven"
   layout:      post
   ```
4. Type away! Reject my reality and substitute your own!

## Want to contribute?

There's no "protocol" for contributing. Noticed that some people actually
forked this repository/theme, I always found that to be unlikely. Anyway,
_there is **some**_ room for improvement. So here it goes:

* Contributions are appreciated. 
* _Keep it ugly_.
* When possible keep line length to 79 characters per line, like the
  recommendations for [Python Code][4].

[2.1]: https://kxxvii.github.io/Kikofri/ofri
[2.2]: https://kxxvii.github.io/Kikofri
[2.3]: https://github.com/kxxvii/Kikofri/releases/tag/classic
[3]: https://kxxvii.github.io/Kikofri/ofri#micros
[4]: http://pep8.org/#maximum-line-length

## License

The Kikofri theme by [kxxvii](https://github.com/kxxvii) is released under the
[Apache License 2.0](http://www.apache.org/licenses/LICENSE-2.0). 


```
   Copyright 2019 kxxvii (https://gitlab.com/kxxvii)

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

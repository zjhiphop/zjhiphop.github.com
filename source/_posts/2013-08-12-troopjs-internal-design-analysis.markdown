---
layout: post
title: "Troopjs internal design analysis"
date: 2013-08-12 13:52
comments: true
categories: [Decouple TroopJS]
---

### Which problems does this framework try to solve ?
* Front-end developers should know as little as possible
about back-end as possible
* UI is client specific, keep it there
* Code that accomplishes a single task should live all
live together in one place
* Modifying a component should not affect the whole
application
* Avoid boilerplate code
* Stay DRY

### Which solutions does this framework can give ?
* Back-end should be a service provider shared
among all clients
* Generate your UI on the client, but separate
from logic (MVC)
* Write components, not snippets (AMD)
* Connect components (PubSub)
* Convention over configuration
* Pick framework(s) (but KISS)

### Whcih Libs does this framework use?
* [RequireJS][1]
* [ComposeJS][2]
* [jQuery][3]
* [R.js][5]
* [Grunt][6]
* [When.js][7]
* [poly.js][8]


[1]: https://github.com/jrburke/requirejs "RequireJS"
[2]: https://github.com/kriszyp/compose "ComposeJS"
[3]: https://github.com/jquery/jquery "jQuery"
[4]: https://github.com/troopjs "TroopJS"
[5]: https://github.com/jburke/r.js "R.js"
[6]: https://github.com/cowboy/grunt "Grunt"
[7]: https://github.com/cujojs/when "When.js"
[8]: https://github.com/cujojs/poly "poly.js"

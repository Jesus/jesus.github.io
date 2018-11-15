---
layout: post
title:  Summary of Dropbox gems
date:   2018-11-15 16:28:00 +0000
categories: programming
---

Some time ago I wrote the [dropbox_api][gems_dropbox_api] gem, this is a client
for [Dropobox's API][dropbox_api].

I wasn't the first one to write a gem for Dropbox, and currently there're
a few ones available. If you need to use the API, you probably have to spend
a bit of time researching which of the Ruby libraries suits you better. I'm
writing this post to summarize the current status of all these gems and help
you out making a choice.



Here's the list of all libraries available in
[Rubygems][rubygems]:

| Gem | API | Supported endpoints | Latest release | Downloads | Documentation
| --- |:---:|:---:|:---:|:---:|:---:|
| [dropbox_api][gems_dropbox_api] <br/><small>By Jesus Burgos Macia (me!)</small> | v2 | 41 | November 2018 | 210K+ | [YARD][gems_dropbox_api_doc]
| [dropbox-sdk-v2][gems_dropbox_sdk_v2] <br/><small>By [Dylan Waits][gh_waits]</small> | v2 | 28 | July 2016 | 60K+ | [YARD][gems_dropbox_sdk_v2_doc]
| [dropbox_api_v2][gems_dropbox_api_v2] <br/><small>By [Tammy Pop][gh_smurfik]</small> | v2 | 7 | October 2017 | 7K+ | [Readme file][gems_dropbox_api_v2]
| [dropbox][gems_dropbox] <br/><small>By [Tim Morgan][gh_riscfuture]</small> | v1 | *deprecated* | July 2011 | 100K+ | [Readme file][gems_dropbox]
| [dropbox-sdk][gems_dropbox_sdk] <br/><small>By Dropbox Inc.</small> | v1 | *deprecated* | July 2015 | 750K+ | -
| [dropbox-api][gems_dropbox_dash_api] <br/><small>By [Base Lab][gh_baselab]</small> | v1 | *deprecated* | May 2015 | 115K+ | [Readme file][gems_dropbox_dash_api]
| [dropbox-utility][gems_dropbox_utility] <br/><small>By [Casey Scarborough][gh_caseyscarborough]</small> | v1 | *deprecated* | June 2013 | 5K+ | [Readme file][gems_dropbox_utility]

So, it's clear that you can discard the bottom 4 libraries.

Now it should be easier for anyone to make a choice looking at the table data.
But if that's not enough, a sneak peek at the documentation link will hopefully
clear out your doubts.

[dropbox_api]: https://www.dropbox.com/developers/documentation/http/documentation
[rubygems]: https://rubygems.org/search?utf8=%E2%9C%93&query=dropbox
[api_v1_deprecation]: https://blogs.dropbox.com/developers/2017/06/updated-api-v1-deprecation-timeline/

[gems_dropbox]: https://github.com/RISCfuture/dropbox
[gems_dropbox_sdk]: https://github.com/dropbox/dropbox-sdk-ruby
[gems_dropbox_api]: https://github.com/Jesus/dropbox_api
[gems_dropbox_api_doc]: https://www.xuuso.com/dropbox_api/DropboxApi/Client.html
[gems_dropbox_dash_api]: https://github.com/futuresimple/dropbox-api
[gems_dropbox_sdk_v2]: https://github.com/waits/dropbox-sdk-ruby
[gems_dropbox_sdk_v2_doc]: https://www.rubydoc.info/github/waits/dropbox-sdk-ruby/Dropbox/Client
[gems_dropbox_utility]: https://github.com/caseyscarborough/dropbox-utility
[gems_dropbox_api_v2]: https://github.com/smurfik/dropbox_api_v2

[gh_waits]: https://github.com/waits
[gh_smurfik]: https://github.com/smurfik
[gh_riscfuture]: https://github.com/RISCfuture
[gh_baselab]: https://github.com/futuresimple
[gh_caseyscarborough]: https://github.com/caseyscarborough

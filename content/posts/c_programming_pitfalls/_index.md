+++
title = "C Programming Pitfalls"
template = "series.html"
sort_by = "slug"
transparent = true

[extra]
series = true

[extra.series_intro_templates]
default = "Welcome to Part $SERIES_PAGE_INDEX of the $SERIES_HTML_LINK series!"

[extra.series_outro_templates]
default = "That's all for this blog post. Thanks for visiting! 😊"
+++

This series of short blog posts highlights some common C programming pitfalls I've encountered in my work as a firmware developer. I call them pitfalls because they are easy to run into. C compiles them just fine, so only runtime testing shows the wrong behavior, and sometimes these errors aren't noticed right away. Often, there isn't even a compiler warning, which makes them hard to find, which makes debugging the code necessary.
I also want to use this series to show how Rust avoids such problems, which is a major reason why I like programming Rust so much! 👌
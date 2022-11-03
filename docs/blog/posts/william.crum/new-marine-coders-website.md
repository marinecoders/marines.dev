---
date: 2022-11-02
categories:
 - Announcements
 - Technical
---

# New Marine Coders Website
Marine Coders is happy to announce the second version of the Marine Coders website! We moved away from the Ruby and Jekyll site to Python and MkDocs for a better development experience. We hope that this new website with its integration, extensibility and style brings our community and developers a better experience. 

<!-- more -->

## Old Marine Coders website
The old Ruby Based Static Site Generated website was originally forked from the Airmen Coders repository, with a Marines twist Capt Collin Chew and Maj Drew Hutcheon developed the Marine Coders Community of Practice Website.

  - [Jekyll](https://jekyllrb.com/)
  - [Minimal Mistakes (Jekyll Theme)](https://mmistakes.github.io/minimal-mistakes/)
  - [Github Pages](https://pages.github.com/)

Some of the issues we were encountering were related to Github Actions during the build process. Often times when new Gem versions would come, recommended by [Dependabot](https://github.com/dependabot) or [Snyk](https://snyk.io/), we would update them and conflicts within versioning of certain dependencies would break the build process. 

As well, since we work within the Department of Defense we found it might be useful to extend and include components of the Marine Coders website with styling and features seen in the [U.S. Web Design System (USWDS)](https://designsystem.digital.gov/). Originally we built out this hacky ["Banner Injector"](https://github.com/elisoncrum/banner-injector), we added it to our Github Actions pipeline but with dependencies across Python and Jekyll now we thought it might be best for the Pipeline, Security, and future extendability to move into something we can take more control of.

A special thanks to everyone who contributed to the old site!

<a href="https://github.com/marinecoders/marinecoders.github.io/graphs/contributors">
  <img width="70%" src="https://contrib.rocks/image?repo=marinecoders/marinecoders.github.io" />
</a>

## Moving Marine Coders website from Jekyll to MkDocs
Our new Marine Coders website tries to fix all of the issues we noticed with the old site. Our new site seeks to have better page quality, less dependencies, consistent styles, be more extensible, more embeddable with Open Graph and more!

  - [MkDocs](https://www.mkdocs.org/)
  - [Mkdocs Material Theme](https://squidfunk.github.io/mkdocs-material/)
  - [USWDS Styling and Components](https://designsystem.digital.gov/)
  - [Giscus - Github Discussions](https://giscus.app/)
  - [Github Pages](https://pages.github.com/)

Extending MkDocs with overridden custom pages and layouts become easier with [Jinja2](https://jinja.palletsprojects.com/en/3.1.x/), we easily introduced a standard banner that will eventually be deployed once we have our official `.mil` site.

We as well introduced the use of Giscus which allows us to collaborate better with our community extending features of Github Discussions.

Overall we think this greatly improves the development and management experience while bringing a better overall site to our users.

Let us know what you think! Happy Coding Marines!
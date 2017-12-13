# Descriptive Git projects
*Last edited on 7 August 2017.*

The Code itself is not enough. It certainly does the job, but what about project
description, standards, keeping track of changes and legal rights? They are
important points to take into account, especially if we want the project to be
understandable in the long run.

## The EditorConfig

Most editors and IDE's are compatible with this format by default or offer a
third-party plug-in to apply syntax and format standards described in a
configuration file. The `.editorconfig` holds information about indentation
style, line ending, final newlines, whitespace trimming and several other
attributes that appear to be very useful in case we want the edition to be
cross-platform and editor independent.

```bash
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true
indent_style = space
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

The [EditorConfig site][editorconfig] offers complete and comprehensive
information about its syntax supported editors and plug-ins and has an
extensive library of examples that can be used to kick-start your project.

## The CHANGELOG

In order to track changes, the project needs a document that compiles all major
modifications done each time we change its version. The `CHANGELOG.md` file is
the right place to do this, along with Semantic Versioning. This file holds a
section for each version change accompanied by its release date. Changes still
in development are added beneath the `Unreleased` section. Changes can be
`Added`, `Changed`, `Deprecated`, `Removed`, `Fixed` or `Security` related.
[Keep a Changelog][keep-a-changelog] is a great summary of everything this file
should do.

```markdown
# Change Log

All notable changes to this project will be documented in this file. This
project adheres to [Semantic Versioning](http://semver.org).

## Unreleased
### Added
  - Social sharing capabilities for the current page.

## 0.8.1 - 2017-05-12
### Fixed
  - Build script with background serve task.
  - Build information and errata in README usage.
  - Google Analytics tracking code in the main template.
```

The versioning system used contains three sections, `MAJOR.MINOR.PATCH`. A major
version should be incremented when incompatible API changes take place. The
minor version should change when backwards-compatible functionality is added.
Patch increments every time backwards-compatible bug fixes are made. Labels for
pre-releases are also available, for a complete explanation take a look at the
official [Semantic Versioning][semver] site.

## The README

This file contains information about the project's description and usage. It
also covers obtaining, installing and testing the software and, sometimes, its
contribution guidelines. Other information such as a section for troubleshooting
issues and bugs, credits and a short description and link to the license should
also be included in this file. Most projects contain this information in
Markdown format, inside a `README.md` file, although ReStructuredText versions
are also popular, especially in the Python ecosystem. Both templates can be
obtained from Github Gists for the Markdown and ReStructuredText versions. To
learn more, [Wikipedia][wikipedia-readme] has an excellent page about README
documents.

## The LICENSE

Last, but not least, we need to specify the project ownership, usage rights and
conditions. This is tricky and may vary a lot. Certain private licenses may not
be suitable for all situations and public licenses are certainly not an option
for the latter case. Open source licenses, on the other hand, are available to
everyone and can be downloaded online. One example, the
[Apache License, Version 2.0][apache-license-v2], guarantees free usage and
distribution.

> Subject to the terms and conditions of this License, each Contributor hereby
> grants to You a perpetual, worldwide, non-exclusive, no-charge, royalty-free,
> irrevocable copyright license.

Despite this, the Apache License, Version 2.0 does acknowledge and protect
project creators and contributors.

> This License does not grant permission to use the trade names, trademarks,
> service marks, or product names of the Licensor, except as required for
> reasonable and customary use in describing the origin of the Work.

Other well-known free licenses are the GNU General Public License (GPL), the MIT
License or the Creative Commons License. The Open Source Initiative has an
[extended information page][open-source-licenses] about licenses and standards
that can be found on their official page.

## References

  - Apache. Apache License V2 [online]. Available at
    [https://www.apache.org/licenses/LICENSE-2.0][apache-license-v2]
    (Accessed 7 August 2017).
  - EditorConfig. EditorConfig [online]. Available at
    [http://editorconfig.org][editorconfig]
    (Accessed 7 August 2017).
  - Keep a CHANGELOG. Keep a CHANGELOG [online]. Available at
    [http://keepachangelog.com][keep-a-changelog]
    (Accessed 7 August 2017).
  - OpenSource. Licenses [online]. Available at
    [https://opensource.org/licenses][open-source-licenses]
    (Accessed 7 August 2017).
  - Semver. Semver [online]. Available at
    [http://semver.org][semver]
    (Accessed 7 August 2017).
  - Wikipedia. README [online]. Available at
    [https://en.wikipedia.org/wiki/README][wikipedia-readme]
    (Accessed 7 August 2017).

[apache-license-v2]: https://www.apache.org/licenses/LICENSE-2.0
[editorconfig]: http://editorconfig.org
[keep-a-changelog]: http://keepachangelog.com
[open-source-licenses]: https://opensource.org/licenses
[semver]: http://semver.org
[wikipedia-readme]: https://en.wikipedia.org/wiki/README

# Dan Guns's personal site

Hugo and PaperMod site for https://danguns.ca/, deployed to S3 and CloudFront.

## Local development

Install Hugo Extended **0.170.0**, then:

```sh
git submodule update --init --recursive
hugo server --disableFastRender
```

Build the production site with `hugo --minify`. Output is written to `public/`.
Use `hugo server -D` to include articles marked `draft: true` while editing.

## Dependencies

- Hugo Extended: 0.170.0, pinned in `.github/workflows/build.yml`.
- PaperMod: submodule pinned to `d3768854d00ad003b0a8dbdba254ce9224377a01`.
- Mermaid: 12.3.0, pinned in `layouts/partials/extend_head.html`; loaded only on pages using the shortcode.
- GitHub Actions: checkout 7.0.1, actions-hugo 3.2.1, configure-aws-credentials 6.3.0.

Dependabot checks actions and the theme weekly. Review Hugo and Mermaid releases
when updating the site; their version strings are maintained manually. To update
the theme, run `git submodule update --remote themes/PaperMod`, build, and commit
the changed submodule reference.

Pull requests build without AWS credentials. Pushes to `main` build and deploy
using the existing repository secrets, then invalidate CloudFront.

## Content and verification

The November 2026 refresh uses public profile information, the Flux GA article,
and local Git history for the contribution summaries. Links in Open Source point
to the corresponding changes. The Rust article is an editorial draft ready for
review in this checkout (`draft: false` so it appears in a normal preview).

During the refresh, shell DNS/network access was unavailable, so `gh`, theme
initialization, and downloading Hugo 0.170.0 could not complete. The theme pin was
updated from the upstream commit page, but a full build and browser verification
remain required once the submodule can be downloaded. No deployment was performed.

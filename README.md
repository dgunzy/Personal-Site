# Dan Guns's personal site

Hugo and PaperMod site for https://danguns.ca/. The workflow builds on pull
requests and deploys `main` to GitHub Pages. Route 53 remains the DNS provider.

## Local development

Install Hugo Extended **0.170.0**, then:

```sh
git submodule update --init --recursive
hugo server --disableFastRender
```

Build with `hugo --minify --panicOnWarning`. Output is written to `public/`.
Use `hugo server -D` to include articles marked `draft: true` while editing.

## Dependencies and theme compatibility

- Hugo Extended: 0.170.0, pinned in `.github/workflows/build.yml`.
- PaperMod: submodule pinned to `d3768854d00ad003b0a8dbdba254ce9224377a01`.
- Mermaid: 12.3.0, pinned in `layouts/partials/extend_head.html`.
- GitHub Actions versions are pinned in the workflow; Dependabot checks actions
  and the theme weekly. Hugo and Mermaid versions are maintained manually.

PaperMod's base template, RSS template, and Open Graph partial use deprecated
Hugo language properties. Local overrides in `layouts/baseof.html`,
`layouts/rss.xml`, and `layouts/_partials/templates/opengraph.html` replace
`LanguageDirection` with `Direction` and `LanguageCode` with `Locale`. They
otherwise match the pinned upstream templates, licensed under the theme's MIT
license (`themes/PaperMod/LICENSE`). Review these overrides when updating the
theme; remove them once upstream includes the fixes. Do not disable strict
build checks to hide template warnings.

## GitHub Pages setup and DNS cutover

1. In [repository Pages settings](https://github.com/dgunzy/Personal-Site/settings/pages),
   choose **GitHub Actions** as the build source. Set the custom domain to
   **danguns.ca** before changing DNS. Verify domain ownership in your GitHub
   account's Pages settings if it is not already verified; add the exact TXT
   record GitHub supplies to Route 53.
2. Push the workflow changes to `main`. Confirm the build and Pages deployment
   succeed in [Actions](https://github.com/dgunzy/Personal-Site/actions). Pull
   requests only build; they receive no Pages write permission and never deploy.
3. Back up the current Route 53 record sets, then replace only the web records
   for `danguns.ca` and `www.danguns.ca` using the values below. Replace any old
   CloudFront A/AAAA aliases for those names; do not leave IPv6 pointing at the
   old site. Leave mail, NS, SOA, TXT, and unrelated subdomain records unchanged.
4. Wait for DNS and GitHub's certificate provisioning, then enable **Enforce
   HTTPS** in Pages settings. Confirm both domains, images, article links, and
   RSS work. GitHub should redirect `www.danguns.ca` to `danguns.ca`.
5. Keep S3 and CloudFront available until the new site and HTTPS are verified.
   Their removal is a separate cleanup. The new workflow does not need AWS
   credentials; remove the old repository AWS secrets after the migration is
   confirmed and they are no longer used elsewhere.

Use ordinary records (Route 53 **Alias: No**), with TTL 300 during the cutover:

| Name | Type | Values |
| --- | --- | --- |
| `danguns.ca` | A | `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153` |
| `danguns.ca` | AAAA | `2606:50c0:8000::153`, `2606:50c0:8001::153`, `2606:50c0:8002::153`, `2606:50c0:8003::153` |
| `www.danguns.ca` | CNAME | `dgunzy.github.io` |

The apex uses A/AAAA records, not a CNAME. Do not put `/Personal-Site/` in the
CNAME target. With a custom Actions workflow, GitHub ignores a repository
`CNAME` file: the custom domain must be set in Pages settings. Hugo's `baseURL`
remains `https://danguns.ca/`.

Useful checks after authentication:

```sh
gh run list --repo dgunzy/Personal-Site --limit 5
gh api repos/dgunzy/Personal-Site/pages
aws route53 list-hosted-zones-by-name --dns-name danguns.ca
# Copy the exact public zone ID from that output, then back up its records:
aws route53 list-resource-record-sets --hosted-zone-id YOUR_ZONE_ID > /tmp/danguns-dns-before.json
dig +short danguns.ca A
dig +short danguns.ca AAAA
dig +short www.danguns.ca CNAME
curl -I https://danguns.ca/
curl -I https://www.danguns.ca/
```

DNS values and setup order follow the [GitHub custom domain documentation](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site).

## AWS CLI on this Mac

The old `/usr/local/bin/aws` points to an Intel binary at `/usr/local/aws-cli/aws`.
This Mac is ARM64 and has ARM Homebrew under `/opt/homebrew`. Install its native
AWS CLI and use that binary:

```sh
/opt/homebrew/bin/brew install awscli
eval "$(/opt/homebrew/bin/brew shellenv)"
rehash
which aws
aws --version
aws login
```

`which aws` should report `/opt/homebrew/bin/aws`. If the old binary still wins,
use `/opt/homebrew/bin/aws` explicitly. Ensure Homebrew's `shellenv` is loaded
from your shell startup file for future terminals. Existing AWS profiles can be
kept; there is no need to delete `~/.aws` or remove the old installation first.
If Homebrew reports awscli is already installed but its binary is broken, use
`brew reinstall awscli` instead.

Console-based authentication is `aws login`, not `aws auth login`.
For an IAM Identity Center profile, use `aws sso login --profile YOUR_PROFILE`.
See [Homebrew's AWS CLI formula](https://formulae.brew.sh/formula/awscli) and
[AWS sign-in documentation](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sign-in.html).

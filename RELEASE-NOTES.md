# Windows x64 NTS build for PHP 7.4 — October 2026 security backport

Community build of the PHP team's official PHP 7.4 source backport for:

- **CVE-2026-109618** — RCE via OOM corruption.
- **CVE-2026-109619** — RCE via stale global state.

Both issues were disclosed on **21 October 2026**. The PHP team provided a minimal backport for PHP 7.4; this package compiles that source for Windows x64 NTS / IIS FastCGI.

## Download

Choose **`php74-patched-windows-x64-nts-712e60f9edfd.zip`** under Assets. The automatically generated “Source code” ZIP/tar.gz files are not the Windows runtime package.

```text
SHA-256:
cd3627ce0e42cbfb76ba14030c4ea3e02d279fb3173bec99222196ee8edbe176
```

Additional assets: `SHA256SUMS.txt`, `BUILD-PROVENANCE.json` and `upstream-tests.xml`.

## Source and verification

- Official source commit: [`712e60f9edfd03f0960fc4fc3c2c375f42b90aa2`](https://github.com/php/php-src/commit/712e60f9edfd03f0960fc4fc3c2c375f42b90aa2).
- Successful build: [GitHub Actions run 39783222676](https://github.com/serkanburat/php74-patched-windows/actions/runs/39783222676).
- Runtime version string: **`7.4.31-dev`**, as retained in the official backport's Windows version header.
- CLI/CGI startup and bundled extension smoke checks passed.
- All **seven upstream upload/session regression tests** passed; report attached.
- The runtime ZIP is unchanged from the successful build output.

The security fixes were developed by the PHP team. This is a community-compiled Windows package, not an official PHP Windows release. Its scope is these two fixes; it does not provide ongoing PHP 7.4 maintenance. Functional tests are not an exhaustive exploit validation.

## Installation

Use a separate runtime directory, configure IIS FastCGI and `PHPRC`, then change one site's PHP handler and recycle its application pool. Validate application behavior before moving additional sites.

[English installation guide](https://github.com/serkanburat/php74-patched-windows/blob/main/README.md) · [Türkçe kurulum](https://github.com/serkanburat/php74-patched-windows/blob/main/README.tr.md)

[Official PHP announcement](https://www.php.net/archive/2026.php) · [CVE-2026-109618 advisory](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w) · [CVE-2026-109619 advisory](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp)

The ZIP retains the PHP License 3.01 and bundled library notices. This product includes PHP software, freely available from <http://www.php.net/software/>.

# Windows x64 NTS community build for PHP 7.4

[Türkçe kurulum ve açıklama](README.tr.md)

A downloadable Windows FastCGI build of the PHP team's official PHP 7.4 source backport for **CVE-2026-109618** and **CVE-2026-109619**, disclosed on **21 October 2026**.

**[Download the Windows x64 NTS ZIP](https://github.com/serkanburat/php74-patched-windows/releases/download/v2026.10.22-1/php74-patched-windows-x64-nts-712e60f9edfd.zip)** · [Release and verification files](https://github.com/serkanburat/php74-patched-windows/releases/tag/v2026.10.22-1)

This is a community-compiled binary, not an official PHP Windows release. Its scope is the two fixes above; it does not provide ongoing PHP 7.4 maintenance. The supplied binaries target **Windows x64, Non Thread Safe (NTS), IIS/FastCGI**.

## What is included

| Item | Value |
| --- | --- |
| Upstream source | [`php/php-src`](https://github.com/php/php-src) |
| Official backport | [`PHP-7.4-CVE-2026-109618-109619`](https://github.com/php/php-src/tree/PHP-7.4-CVE-2026-109618-109619) |
| Exact source commit | [`712e60f9edfd03f0960fc4fc3c2c375f42b90aa2`](https://github.com/php/php-src/commit/712e60f9edfd03f0960fc4fc3c2c375f42b90aa2) |
| Runtime version string | `7.4.31-dev` |
| Architecture / thread model | Windows x64 / NTS |
| Toolchain reported by PHP | Visual C++ 2017 (VC15) |
| Build | [GitHub Actions run 39783222676](https://github.com/serkanburat/php74-patched-windows/actions/runs/39783222676) |

The upstream commit explicitly addresses both CVEs and is described by the PHP team as a minimal backport for an end-of-life version. Its Windows version header still says `7.4.31-dev`; the exact source commit identifies the patched build.

The ZIP contains `php.exe`, `php-cgi.exe`, `php7.dll`, bundled extensions, dependency DLLs, INI templates and upstream license notices. An application-specific `php.ini` and additional third-party extensions are not supplied.

## Download and verify

Download the runtime ZIP from **Releases → Assets**, rather than the automatically generated “Source code” archives. Release assets are available without a GitHub account.

The SHA-256 of `php74-patched-windows-x64-nts-712e60f9edfd.zip` is:

```text
cd3627ce0e42cbfb76ba14030c4ea3e02d279fb3173bec99222196ee8edbe176
```

In PowerShell, from the directory containing the downloaded ZIP:

```powershell
$zip = Join-Path (Get-Location) 'php74-patched-windows-x64-nts-712e60f9edfd.zip'
$expected = 'cd3627ce0e42cbfb76ba14030c4ea3e02d279fb3173bec99222196ee8edbe176'
if ((Get-FileHash -LiteralPath $zip -Algorithm SHA256).Hash -ine $expected) {
    throw 'SHA-256 mismatch. Do not use this file.'
}
```

The release also includes `SHA256SUMS.txt`, `BUILD-PROVENANCE.json` and `upstream-tests.xml`.

## Install alongside an existing IIS runtime

1. Back up the current PHP configuration and the site's `web.config`. Record the existing PHP handler's executable path so it can be restored.
2. Ensure the Microsoft Visual C++ Redistributable for x64 is installed. See [Microsoft's supported downloads](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist?view=msvc-170).
3. Extract into a new directory, for example `C:\PHP74-Patched`. Keep the complete package together, including its DLLs.
4. Check that both executables start:

   ```powershell
   & 'C:\PHP74-Patched\php.exe' -n -v
   & 'C:\PHP74-Patched\php-cgi.exe' -n -v
   ```

5. Create `C:\PHP74-Patched\php.ini` from `php.ini-production`, and merge the settings needed by your application. Set the following existing entries for IIS:

   ```ini
   extension_dir = "C:\PHP74-Patched\ext"
   cgi.force_redirect = 0
   fastcgi.impersonate = 1
   ```

   Enable the required extensions. Additional extension DLLs must match **PHP 7.4, x64 and NTS**. Review application-specific paths, session storage, uploads and file permissions.

6. In **IIS Manager → server → FastCGI Settings**, add an application with full path `C:\PHP74-Patched\php-cgi.exe`, empty arguments and environment variable `PHPRC=C:\PHP74-Patched`.
7. Select **one site → Handler Mappings**. Change the PHP handler that currently handles `*.php` to the new executable. Check its ordering if several PHP mappings exist.
8. Recycle that site's application pool and test login, data reads/writes, uploads and downloads. Repeat for the remaining sites after validating each one.

To roll back a site, restore the handler's previous executable path and recycle its application pool. Existing PHP processes must be replaced for a runtime change to take effect.

## Verification and build transparency

The published ZIP is the unchanged output of the successful Actions build. It passed CLI and CGI startup checks, bundled extension smoke checks, and **seven upstream upload/session regression tests**. The test report is attached to the release.

The maintainer also exercised IIS/FastCGI startup and application login on Windows Server 2019. These are functional checks, not an exhaustive exploit validation or a guarantee of application compatibility.

The [build workflow](.github/workflows/build-php74.yml) pins both source and Windows build tools to exact commits, verifies the checked-out commits, and builds the checked-out source. Run it from **Actions → PHP 7.4 patched - Windows x64 NTS → Run workflow** to build independently. A new compilation can have different timestamps and hashes.

For another platform or architecture, start from the linked official source backport; the download in this repository is Windows x64 NTS only.

## Upstream references and credits

- [PHP security announcement, 21 October 2026](https://www.php.net/archive/2026.php)
- [CVE-2026-109618: RCE via OOM corruption](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w)
- [CVE-2026-109619: RCE via stale global state](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp)
- [Official commit fixing both issues](https://github.com/php/php-src/commit/712e60f9edfd03f0960fc4fc3c2c375f42b90aa2)
- [Official PHP Windows build tools](https://github.com/php/php-windows-builder/tree/5f63433eb2bda2ca8d0d970fe487be84f7ab195c)

The vulnerability fixes were developed by the PHP team. This repository provides a community Windows build and installation notes. Report packaging or build problems in [Issues](https://github.com/serkanburat/php74-patched-windows/issues), with configuration secrets and personal data removed.

## Licenses

The runtime retains the [PHP License 3.01](LICENSE). Bundled libraries retain their respective licenses and notices, included in the ZIP and in [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt).

This product includes PHP software, freely available from <http://www.php.net/software/>.

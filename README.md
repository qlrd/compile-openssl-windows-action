# compile-openssl-windows-action

This action compile openssl under windows environment, at specific tagged version.

## Usage

You must define 4 variables:

### `version`

Version is a tag from [openssl repository](https://github.com/openssl/openssl/tags)

### `prefix`

The top of the installation directory tree.

### `openssldir`

Directory for OpenSSL configuration files, and also the default certificate and key store.

### `build-type`

Can be:

- `plain`:
    - Uses `no-makedepend no-shared no-fips VC-WIN64A-masm` compilation flags;
    - Uses `VERBOSE_FAILURE=yes HARNESS_JOBS=4` for tests.
- `minimal`:
    - Uses `no-makedepend no-bulk no-deprecated no-fips no-asm -DOPENSSL_SMALL_FOOTPRINT` compilation flags;
    - Uses `VERBOSE_FAILURE=yes TESTS=-test_fuzz* HARNESS_JOBS=4` for tests

### Example

```
on: [push]

jobs:

    ...

      - name: Set a custom folder to install
        id: custom
        shell: pwsh
        run: |
          $loc = Get-Location
          echo "prefix=$loc\mySSL" | Out-File -FilePath $Env:GITHUB_OUTPUT -Encoding utf8 -Append
          echo "openssldir=$loc\mySSL\files" | Out-File -FilePath $Env:GITHUB_OUTPUT -Encoding utf8 -Append

      - name: Build openssl
        id: build
        uses: compile-openssl-windows-action@v0.0.1
        with:
          version: openssl-3.1.0-beta1
          prefix: ${{ steps.custom.outputs.prefix }}
          openssldir: ${{ steps.custom.outputs.openssldir }}
          build-type: 'minimal'
```

# vardoger-trust

Public trust artifacts for the [Vardoger](https://vardoger.app) project. This repo exists so that users can cross-check critical verification material against a second, independent origin — separate from `vardoger.app` (Netlify-served) and `get.vardoger.app` (R2-served).

**This repo intentionally contains no source code and no proprietary material.** Only public keys, checksums, and human-readable documentation.

## Files

| File | Purpose |
|---|---|
| [`vardoger-release.pub.asc`](./vardoger-release.pub.asc) | The Vardoger Signing GPG public key. Used to verify install.sh, .deb, .rpm, .exe signatures. |
| [`SHA256SUMS-install.sh`](./SHA256SUMS-install.sh) | SHA-256 hash of the current `install.sh` published at `https://get.vardoger.app/install.sh`. |

## Signing key fingerprint

```
3D09 BF9F 4B72 E6FB 8146  38F0 4C3C F6E9 AEF1 6F48
```

The same fingerprint must appear:

- In this repo's `vardoger-release.pub.asc` after `gpg --import-options show-only --import`
- On <https://vardoger.app/verify/>
- At <https://get.vardoger.app/vardoger-release.pub.asc>

If they don't all match, **stop** — do not run any artifact signed by that key. Contact `security@vardoger.app`.

## Verifying with GPG (recommended)

```sh
# 1. Import the public key from this repo
curl -fsSL https://raw.githubusercontent.com/ctfish/vardoger-trust/main/vardoger-release.pub.asc | gpg --import

# 2. Verify the fingerprint against the displayed value above
gpg --fingerprint signing@vardoger.app

# 3. Fetch install.sh + signature, verify
curl -O https://get.vardoger.app/install.sh
curl -O https://get.vardoger.app/install.sh.asc
gpg --verify install.sh.asc install.sh

# 4. Only run after "Good signature" appears
sudo sh install.sh
```

## Verifying with SHA-256 (no GPG required)

```sh
curl -O https://get.vardoger.app/install.sh

# Fetch the hash from this repo
curl -fsSL https://raw.githubusercontent.com/ctfish/vardoger-trust/main/SHA256SUMS-install.sh -o SHA256SUMS-install.sh

# Cross-check against the Netlify-served copy at vardoger.app
curl -fsSL https://vardoger.app/SHA256SUMS-install.sh -o SHA256SUMS-install.sh.netlify
diff SHA256SUMS-install.sh SHA256SUMS-install.sh.netlify
# (no output = both origins agree; any difference means STOP and investigate)

# Verify the install.sh you downloaded matches
sha256sum -c SHA256SUMS-install.sh
# Expected: install.sh: OK
```

## Full verification workflow

For maximum assurance, do both: cross-check the SHA-256 across this repo and `vardoger.app`, AND use GPG to verify the detached signature. The GPG signature transitively trusts the YubiKey-resident signing subkey, which is hardware-bound and offline; the cross-origin SHA-256 ensures both publishing locations agree on what the canonical file is. They defend against complementary attack scenarios.

See <https://vardoger.app/verify/> for the long-form documentation, key rotation policy, and contact information.

## Updating this repo

The hashes here are byte-identical to the copies in the `vardoger-web` repo's `static/` directory. They are updated together whenever `install.sh` (or other release artifacts) are re-signed.

## License

The public key and checksums in this repo are not subject to copyright in any meaningful sense — they're cryptographic facts. The text of this README is © Vardoger, all rights reserved, but you are welcome to quote or fork the repo to publish copies as additional verification origins for the Vardoger project.

# Source audit and corrective changes

Date: 2026-11-13

## Confirmed issue corrected

The source declared Spotify `9.2.0.26` in `Compatibility.kt` and `patches-list.json`, while the patch implementations and their ABI/fingerprint snapshots still target `9.1.80.2221` (including ads, player ad cards, extensions, Home pins, local files, navigation, settings and theme resources). This mismatch could make Morphe advertise/apply patches to an APK for which their fingerprints were not validated, resulting in patch refusal or unsafe patch assumptions.

Changes made:
- Pinned the declared target in `Compatibility.kt` to `9.1.80.2221`.
- Updated all eight entries in `patches-list.json` to the same target.
- Corrected the generated supported-version row in `README.md`.
- Kept the target marked Experimental because runtime installation and behavior have not been tested in this environment.
- Kept the `9.2.0.26` settings snapshot file as an inactive research artifact; it is no longer advertised as a supported target.

## Validation performed

- ZIP integrity: passed (no CRC errors).
- Source archive inventory: 464 entries.
- `patches-list.json`: parses as JSON; 8 patch entries; all declared targets now consistently say `9.1.80.2221`.
- Referenced ABI snapshot files for ads, extensions, Home, local files and navigation exist in the resources tree.
- README no longer advertises `9.2.0.26` as the active target.

## Not verified

- Gradle compilation and unit tests.
- Morphe patch execution against a real Spotify APK.
- APK install, launch, runtime stability, extension behavior, ad handling or theme rendering.
- Building a new `.mpp` bundle.

The Gradle test command was attempted with Java 21. The wrapper requires Gradle `9.7.1`, but downloading the distribution failed because this environment cannot resolve `services.gradle.org` (network/DNS unavailable). No claim of build or runtime success is made.

## Next required verification

Run `gradlew.bat :patches:test` and `gradlew.bat :patches:generatePatchesList` on Windows with the configured Android/Java toolchain and network access. Then build the MPP through the Morphe patcher task and test it against the exact ARM64 Spotify `9.1.80.2221` APK (build code `145767611` as stated in the repository README).

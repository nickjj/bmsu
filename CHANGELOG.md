# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a
Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

- Nothing yet

## [0.5.1] - 2026-04-07

### Added

- `BMSU_DEFAULT_RSYNC_OPTS_EXTRAS` and `BMSU_DEFAULT_RSYNC_OPTS_SKIP` config options

### Fixed

- Restore logic when source paths had `/./`, now all path variants are correct

## [0.5.0] - 2026-04-06

### Added

- Better support for remote `BMSU_SOURCES` and / or `BMSU_DESTINATION`

### Fixed

- Allow `/` as a `BMSU_DESTINATION` (handy for remote backups)

## [0.4.1] - 2026-04-04

### Added

- `bmsu --version-latest` to view the latest version and self-update if you choose

## [0.4.0] - 2026-04-03

### Added

- `bmsu config list` to list all of your profile configs
- `bmsu config show` to show your profile's config
- `bmsu config show example` to show the default config that comes with bsmu
- `bmsu config diff` to show a diff of your profile config vs the example config
- `bmsu config edit` to edit your profile's config
- `bmsu config new PROFILE_NAME` to create a new a new profile config
- `bmsu config delete` to delete your profile's config

### Removed

- `bmsu config example` which has been replaced with `bmsu config show example`

## [0.3.2] - 2026-04-03

### Added

- `bmsu config edit` to edit your profile's config in your configured `EDITOR`

## [0.3.1] - 2026-04-02

### Fixed

- Report back the correct help message for failing config validations

## [0.3.0] - 2026-04-02

### Added

- `bmsu config example` to show the default profile config for your version of bmsu
- `bmsu config diff` to diff your config vs the default profile config

## [0.2.0] - 2026-04-02

### Added

- `bmsu restore` command to support easy restoring

### Changed

- Enforce `--relative` (or `-R`) for more predictable backups and restores
- `bmsu config` now supports passing in rsync flags to preview what will get set

## [0.1.0] - 2026-04-01

### Added

- Everything!

[Unreleased]: https://github.com/nickjj/bmsu/compare/0.5.1...HEAD
[0.5.1]: https://github.com/nickjj/bmsu/compare/0.5.0...0.5.1
[0.5.0]: https://github.com/nickjj/bmsu/compare/0.4.1...0.5.0
[0.4.1]: https://github.com/nickjj/bmsu/compare/0.4.0...0.4.1
[0.4.0]: https://github.com/nickjj/bmsu/compare/0.3.2...0.4.0
[0.3.2]: https://github.com/nickjj/bmsu/compare/0.3.1...0.3.2
[0.3.1]: https://github.com/nickjj/bmsu/compare/0.3.0...0.3.1
[0.3.0]: https://github.com/nickjj/bmsu/compare/0.2.0...0.3.0
[0.2.0]: https://github.com/nickjj/bmsu/compare/0.1.0...0.2.0
[0.1.0]: https://github.com/nickjj/bmsu/releases/tag/0.1.0

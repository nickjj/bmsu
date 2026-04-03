# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a
Changelog](https://keepachangelog.com/en/1.0.0/).

## [Unreleased]

- Nothing yet

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

[Unreleased]: https://github.com/nickjj/bmsu/compare/0.3.2...HEAD
[0.3.2]: https://github.com/nickjj/bmsu/compare/0.3.1...0.3.2
[0.3.1]: https://github.com/nickjj/bmsu/compare/0.3.0...0.3.1
[0.3.0]: https://github.com/nickjj/bmsu/compare/0.2.0...0.3.0
[0.2.0]: https://github.com/nickjj/bmsu/compare/0.1.0...0.2.0
[0.1.0]: https://github.com/nickjj/bmsu/releases/tag/0.1.0

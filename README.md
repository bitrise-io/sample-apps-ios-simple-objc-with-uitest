# Bitrise sample iOS app (Objective-C, with UI tests)

A small Objective-C iOS app used as a fixture for testing Bitrise steps.

## Project layout

- Objective-C app in an **`.xcodeproj`** (`ios-simple-objc/ios-simple-objc.xcodeproj`).
- One **shared scheme**: `ios-simple-objc`.
- Three targets:
  - `ios-simple-objc` — the app (a single screen showing a "Bitrise ios-simple-objc" label).
  - `ios-simple-objcTests` — unit-test bundle.
  - `ios-simple-objcUITests` — UI-test bundle.
- iOS deployment target 15.6.

## Features relevant for step testing

This sample is deliberately shaped to exercise specific step behaviors:

- **Objective-C `.xcodeproj` coverage.** A plain project (not a workspace or Swift package) built
  with an Objective-C toolchain.
- **Unit + UI test bundles in one scheme.** The scheme tests both `ios-simple-objcTests` and
  `ios-simple-objcUITests`, so a step parses results from multiple test bundles.
- **UI-test screenshot attachment.** `ios_simple_objcUITests/testExample` captures an
  `XCUIScreenshot` as an `XCTAttachment` with `XCTAttachmentLifetimeKeepAlways`, so a step can
  exercise test-attachment export and HTML report generation.
- **Shared scheme.** The scheme is shared and checked in, so a step can resolve it by name.
- **Automatic code signing.** The app target and both test targets use Xcode's automatic
  (managed) signing, so a step can exercise automatic-signing behavior.
- **Wide Xcode compatibility.** The 15.6 deployment target is low enough to build on older CI
  Xcode versions and still valid on the newest, so the same fixture compiles across the whole CI
  Xcode matrix.

## CI

`bitrise.yml` defines a `pr_check` pipeline that runs on every pull request. It fans out into
four parallel workflows: tests and archive, each on two stacks. One stack is the lowest Xcode
version this fixture supports, the other tracks the latest stable Xcode. That pair is what keeps
the wide Xcode compatibility above honest.

The steps live in two step bundles, `run_tests` and `build_archive`, so each workflow is a stack
plus one bundle reference and a step change only has to be made once.

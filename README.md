<!--
This README describes thA development-time accessibility auditor for Flutter apps, inspired by axe-core.
It runs inside your app while you build it, walks Flutter's own semantics tree, and reports accessibility violations as you navigate — no external tooling, no separate audit pass, no waiting for a screen reader test at the end of the sprint.
Status: early development. The rule engine and public API are still taking shape and will change. Not yet published on pub.dev. Issues and ideas are welcome.
Why this exists
Since 28 June 2025, the European Accessibility Act — implemented in Germany as the Barrierefreiheitsstärkungsgesetz (BFSG) — has required consumer-facing digital products, mobile apps included, to meet WCAG 2.1 Level AA. Non-compliance carries fines of up to €100,000, and the first warning letters have already gone out.
Web developers have good tooling for this. axe-core runs in the page, evaluates a rule set, and plugs into CI. Mobile has far less, and what exists mostly runs outside the app:
Tool
Where it runs
Limitation
Xcode Accessibility Inspector
External, macOS only
Manual, one screen at a time
Google Accessibility Scanner
On-device, Android only
Manual, after the build
Device farms
Cloud
Slow, expensive, late in the cycle
All of them catch problems after the code is written. None of them can run in your test suite.
There is a second problem: almost all accessibility guidance is written for the web. The principles carry over, but the implementation does not — a Flutter Semantics tree is not a DOM, and advice about alt attributes and tab order does not tell a Flutter developer what to do with a MergeSemantics boundary or an ExcludeSemantics wrapper.
accessibility_auditor takes the axe-core approach and points it at the framework's own accessibility layer.
What it checks
The initial rule set targets the three violation classes that account for most real-world failures in mobile apps:
Missing semantic labels
Interactive elements that expose no meaningful label to VoiceOver or TalkBack — icon-only buttons, tappable GestureDetectors, images that carry meaning but no description. To a screen reader user these announce as nothing, or as "button".
WCAG 1.1.1 (Non-text Content), 4.1.2 (Name, Role, Value)
Undersized tap targets
Interactive elements smaller than the platform minimum — 44×44 pt on iOS, 48×48 dp on Android. Common in dense list rows, close buttons, and custom-drawn controls where the visual size and the hit area drift apart.
WCAG 2.5.5 (Target Size)
Insufficient colour contrast
Text that falls below 4.5:1 against its background (3:1 for large text). Especially common in disabled states, placeholder text, and light-grey secondary labels that look fine on a designer's monitor and disappear in sunlight.
WCAG 1.4.3 (Contrast Minimum)
How it works
Flutter does not build a semantics tree unless something is listening for one — a screen reader, or an explicit request. The auditor calls:
Dart
to force the tree into existence during development, then traverses it recursively through visitChildren, evaluating every node against the registered rules.
This matters, and it is the core design decision of the package: the auditor checks the tree that assistive technology actually receives, not the widget tree you wrote. Those two are not the same. A Semantics widget can be merged away by a parent, excluded by an ancestor, or silently overridden — and a check that reads the widget tree would report a label that no screen reader will ever announce.
Traversal is development-time only. It is not intended to ship in release builds.
Planned API
The public surface is deliberately small: a widget you wrap your app in, and a rule interface you can extend.
Dart
Custom rules implement the Rule interface, so project-specific conventions — a minimum tap target larger than the platform default, a house contrast ratio stricter than AA — can be enforced alongside the built-in checks.
This section describes the intended design. See the roadmap below for what is actually implemented.
Roadmap
[x] Package scaffolding and semantics tree integration
[ ] Rule interface and rule registry
[ ] Missing semantic label rule
[ ] Tap target size rule
[ ] Colour contrast rule
[ ] In-app overlay for surfacing violations during development
[ ] Test-suite integration, so violations can fail a build
[ ] Configurable severity levels and rule suppression
[ ] Publication on pub.dev
Requirements
Dart SDK ^3.5.0
Flutter >=3.24.0
Contributing
The project is early enough that design feedback is worth more than code. If you have run into an accessibility problem in a Flutter app that a rule here could have caught, open an issue and describe it — that is the most useful contribution right now.
License
See LICENSE.
Built by Ali Sodan.e package. If you publish this package to pub.dev,
this README's contents appear on the landing page for your package.

For information about how to write a good package README, see the guide for
[writing package pages](https://dart.dev/tools/pub/writing-package-pages).

For general information about developing packages, see the Dart guide for
[creating packages](https://dart.dev/guides/libraries/create-packages)
and the Flutter guide for
[developing packages and plugins](https://flutter.dev/to/develop-packages).
-->

TODO: Put a short description of the package here that helps potential users
know whether this package might be useful for them.

## Features

TODO: List what your package can do. Maybe include images, gifs, or videos.

## Getting started

TODO: List prerequisites and provide or point to information on how to
start using the package.

## Usage

TODO: Include short and useful examples for package users. Add longer examples
to `/example` folder.

```dart
const like = 'sample';
```

## Additional information

TODO: Tell users more about the package: where to find more information, how to
contribute to the package, how to file issues, what response they can expect
from the package authors, and more.

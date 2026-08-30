# Contributing

Thank you for contributing to Sam DarkBall Mods.

## Before opening a pull request

1. Search existing issues and pull requests.
2. Keep one logical change per pull request.
3. Test with the smallest possible mod set.
4. Run `hemtt check` and `hemtt build --no-bin` locally.
5. Include documentation and stringtable changes when behavior or user-facing
   text changes.

## Arma requirements

- Write SQF for Arma 3.
- Use HPP/CPP only for Arma 3 configuration.
- Do not use `breakOut` or `scopeName`.
- Preserve existing `CfgPatches`, public function names, PBO prefixes, and
  serialized data unless the pull request explicitly documents a breaking
  change.

## Testing a bug fix

Describe the failing behavior, add a regression check where practical, and
state how the fix was tested in Singleplayer, hosted Multiplayer, or on a
Dedicated Server.

## Licensing contributions

By contributing, you agree that source code and configuration are licensed
under GPL-2.0-or-later. Original Arma-specific art, models, textures,
materials, animation, and audio contributions are licensed under APL-SA unless
a closer file states another compatible license. You must have the right to
submit every contributed file and must preserve existing attribution notices.

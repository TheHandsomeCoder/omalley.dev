---
title: Fixing a broken dependancy with npm overrides
date: 2022-05-06T22:30:00Z
description: TIL I can replace nested npm dependancies
tags: [nodejs, npm, thingy52]
---

Hey! Recently I've been putting together a small program to track the temperature inside of a hand-me-down fridge. I'd planned to use my [Nordic Thingy52](https://www.nordicsemi.com/Products/Development-hardware/Nordic-Thingy-52) as it has all the sensors I need plus its own power source, but unfortunately I found that it's NodeJS reference implementation was built with an [abandoned Bluetooth Low Energy library](https://github.com/noble/noble) which had stopped working with OSX after High Sierra.
 
Thankfully a few fine folks came together to create [@abandonware](https://abandonware.github.io/) and have forked and updated noble, and a quick hack of replacing the existing noble reference with the @abandonware one on the surface seems to resolve my problem, but I don't think this is a particularly elegant solution.
 
 
## Replacing a dependency of a dependency
Digging into the Thingys Node library we can see that it's not actually relying directly on Noble. Instead it uses a small wrapper made by the same author called `noble-device`. Meaning I can't just replace the noble dependency the way I'd done in my quick fix previously.


```
$ npm list --depth 1
thingy52@1.0.4
└─┬ noble-device@1.4.1
  └── noble@1.9.1
```

## npm overrides
npm version 8.3 was released in December 2021 and while it's a minor release it did include a very useful feature "overrides".
 
[npm overrides](https://docs.npmjs.com/cli/v8/configuring-npm/package-json#overrides) as the name descriebes lets you replace a dependency in your tree. Previously the only way to do nested dependency control was to fork and fix it yourself, so this is a welcome change that brings `npm` inline with similar features in [yarn](https://classic.yarnpkg.com/en/docs/selective-version-resolutions/) and [pnpm](https://pnpm.io/package_json#pnpmoverrides)
 
 
Looking at our dependency tree above, if we were to go the forking route we'd have to fork `noble-device` to update its version of noble and either fork `thingy52` or get a PR opened to the base repo to use our forked version. But with overrides we can add the following to our `package.json`


```json
"overrides": {
    "noble-device": {
        "noble": "npm:@abandonware/noble@1.9.2-15"
    }
}
```

which will instruct npm to install our override in place of the original dependency.


```
$ npm list --depth 1
thingy52@1.0.4
└─┬ noble-device@1.4.1
  └── noble@npm:@abandonware/noble@1.9.2-15
```

There are a number of ways you can customise which dependency is replaced and in what situation. You can also be more targeted by specifying only to replace in certain situations. Below we only replace `foo` if `bar` is version `2.0.0`

```
 "overrides": {
    "bar@2.0.0": {
      "foo": "1.0.0"
    }
  }
```

You can also use it to standardise across your project. The below override will make foo always be v1.0.0 regardless of what any dependancy requests.

```
{
  "overrides": {
    "foo": "1.0.0"
  }
}
```

Additionally it's important to note that the overrides only take effect in the top level project. This means if I was to PR the change to the Thingy library, it would only work if I was running the Thingy library itself. To have it take effect in my own project I need to specify it there.

The new overrides feature will come in handy when:

- patching some security issues
- replacing an existing dependency with a fork (like we did here)
- make sure that the same package version is used everywhere.

It's a great addition and I hope you found this as interesting as I did!



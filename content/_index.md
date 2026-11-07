+++
title = "Build operating systems. Keep control."
description = "Build the operating system you need without building the whole toolchain around it. Barney brings packages, build steps and system definitions together for engineers and the tools that work alongside them."

[extra.devlab.home]
layout = "wide"

[[extra.devlab.home.blocks]]
type = "hero"
eyebrow = "Building for people, tools and agents."
title = "Bring your own OS."
description = "Build the operating system you need. Own the engineering behind it — and give people, tools and AI agents a common way to work with it."
image = "/images/hero_big.png"
actions = [
    { label = "Explore Barney", path = "/about/" },
    { label = "See how .bny works", path = "/docs/bny/" }
]

[[extra.devlab.home.blocks]]
type = "features"
#title = "Knowledge and process are not mutually exclusive"
#description = "Sure, you could use 30 different projects to manage the OS. Or you could automate the shite out of it, drop years of hacks and actually work on what matters: Features. Stability. Actual requirements."
items = [
    {
        title = "Start with packages",
        kicker = "Adjust",
        description = "Reuse packages, recipes and build steps instead of rebuilding the same foundations from scratch.",
        path = "#",
    },
    {
        title = "Build the system you need",
        kicker = "Bake",
        description = "Choose your packages, configuration and build behaviour. Your operating system is yours to shape",
        path = "#",
    },
    {
        title = "Make the work transferable",
        kicker = "Keep",
        description = "Keep configuration, dependencies and build steps explicit. Your engineering knowledge shouldn't live in one person's head.",
        path = "#",
    },
    {
        title = "Build with people and agents",
        kicker = "Extend",
        description = "Give engineers, tools and AI agents a shared way to understand and work with your system.",
        path = "#",
    }
]

[[extra.devlab.home.blocks]]

type = "learn"
title = "One platform, different systems"
description = "Barney provides the tools to build operating systems. The recipes, foundations and systems built with it can be entirely your own."
items = [
    {
        title = "Barney",
        kicker = "Explore",
        description = "The platform itself: tools and infrastructure for describing, building and maintaining operating systems.",
        path = "#",
    },
    {
        title = "bny-base",
        kicker = "Browse",
        description = "A growing collection of packages, recipes and reusable build modules to build upon..",
        path = "#",
    },
    {
        title = "Pavee OS",
        kicker = "Use",
        description = "A real-world system built with Barney. A place to exercise the platform and explore what's possible.",
        path = "#",
    },
]

[[extra.devlab.home.blocks]]
type = "features"
items = []

[[extra.devlab.home.blocks]]
type = "cta"
title = "Built in the open. Better with you."
description = "Barney is independent, open-source engineering. Explore the code, follow development, report issues or help support the work."
actions = [
    { label = "Explore GitHub", path = "https://github.com/barney-build" },
    { label = "Support development", path = "https://ko-fi.com/TravellingEngineer" }
]

[[extra.devlab.home.blocks]]
type = "content"

+++

## More than recipes

`.bny` describes packages, properties, dependencies, variants and build behaviour in a structured format.

That means the same engineering model can be understood by the people building a system, the tools inspecting it, and AI agents working with it.

Instead of asking an agent to guess what a pile of shell scripts does, Barney gives it something structured to understand and work with.

{% <devlab.tabs label="Explore bny examples"> %}

{% <devlab.tab name="Properties" value="properties" selected={true}> %}

A package can define settings that affect how it is built.

```kdl,name=util-linux.bny
properties {
    (bool) with_systemd #true
    (list) enabled {
        "systemd"
    }
    (list) disabled {}
}
```

{% </devlab.tab> %}

{% <devlab.tab name="Variants" value="variants"> %}

A variant lets a package be built with a different set of settings.

```kdl,name=util-linux.bny
variant "bootstrap" {
    with_systemd #false
    enabled { }
    disabled {
        "systemd"
    }
}
```

{% </devlab.tab> %}

{% <devlab.tab name="Dependencies" value="dependencies"> %}

Dependencies can depend on a setting or use a particular variant. Bootstrap variants can help break dependency cycles.

```kdl,name=util-linux.bny
depends {
    build {
        (binary)make
        util-linux variant="bootstrap" conflicts=(prop)with_systemd
        systemd if=(prop)with_systemd
    }
}
```

{% </devlab.tab> %}

{% <devlab.tab name="Build steps" value="phases"> %}

Build and installation are separate steps. Recipes can call actions provided by a module.

```kdl,name=util-linux.bny
build {
    autotools::configure enable=(prop)enabled disable=(prop)disabled
    autotools::make
}

install {
    autotools::make_install
}
```

{% </devlab.tab> %}

{% <devlab.tab name="Modules" value="modules"> %}

Modules let recipes share common build actions instead of repeating them.

```kdl,name=autotools.bny
module autotools {
    action configure {
        properties {
            (string) command "./configure"
            (string) prefix (prop)env::prefix
            (list) enable "shared"
            (list) disable "static"
        }
        execute {
            emit (prop)command "--prefix=" (prop)prefix
            // Further options are emitted from the declared properties.
        }
    }
}
```

{% </devlab.tab> %}

{% </devlab.tabs> %}

Have a look at the full [`util-linux` recipe](https://github.com/barney-build/bny-base/blob/main/recipes/core/util-linux.bny), [`systemd` recipe](https://github.com/barney-build/bny-base/blob/main/recipes/core/systemd.bny), [`bash` recipe](https://github.com/barney-build/bny-base/blob/main/recipes/shell/bash.bny) and [Autotools module](https://github.com/barney-build/bny-base/blob/main/modules/autotools.bny).

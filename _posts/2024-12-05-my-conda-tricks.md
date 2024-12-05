---
layout: post
title: My conda tips/tricks
author: Brandon Victor
readtime: true
date: 2024-12-05
tags: [conda, tipsandtricks]
---

# My conda tips/tricks

I use conda a lot these days. Well, technically, I use `micromamba`, but it's (mostly) the same. There's a few tips/tricks I've picked up that I wanted to share.

1. Install environments in the same folder.
2. Automatically activate environments.
3. Set PYTHONPATH in conda environment.

## Install environments in the same folder

The default behaviour of conda-likes is to install every environment in a global folder. Same as docker. I don't like this behaviour. As much as possible, I want each project to exist in a self-contained folder. When I'm done, I want "cleaning up" to be as simple as deleting the folder.

In all flavours I've tried, you can use `-p` when creating the environment to set a local directory. However, if you use something like `-p ./env`, and activate it, the environment tag at the start of the line will be the cwd path, potentially leading to a very long prompt line. e.g.

```
brandon@mymachine:~/git/multihuntr.github.io/test_area$ micromamba create -p ./env
brandon@mymachine:~/git/multihuntr.github.io/test_area$ micromamba activate ./env
(/home/brandon/git/multihuntr.github.io/test_area/env) brandon@mymachine:~/git/multihuntr.github.io/test_area$ echo "hello"
```

So here's the trick. If you prefix your environment with `./envs`, it will use the shortname when activated instead of the full working path. e.g.

```
brandon@mymachine:~/git/multihuntr.github.io/test_area$ micromamba create -p ./envs/env
brandon@mymachine:~/git/multihuntr.github.io/test_area$ micromamba activate envs/env/
(env) brandon@mymachine:~/git/multihuntr.github.io/test_area$ echo "hello"
```

## Automatically activate environment

I'm sure there's all sorts of fancy tools to do this, but, I like customisable solutions. I happened to find a tiny code snippet that goes in your `~/.bashrc` [here](https://github.com/vallops99/Conda-autoactivate-env).

I've barely used `micromamba activate` in weeks, even while working on multiple projects! Highly recommended.

## Set PYTHONPATH in environment

I mainly work on machine learning projects, so often I'll have a few data processing scripts, a train script, an evaluation script, maybe a script that aggregates results across trained models. I choose to gather these together into a `scripts/` folder in my project root instead of polluting my project root. But this causes a problem for imports. This problem also appears when writing test scripts. Let's say we have a folder structure like this:

```
myprojectname/
    # source code
scripts/
    train.py
    evaluate.py
    aggregate_scores.py
tests/
    test_module_a.py
    test_module_b.py
    integration_test.py
# ... other dev tools, README.md and such
```

With this folder structure, by default, I could not run `import myprojectname` directly in `scripts/train.py`. I believe that the most correct solution is to use `PYTHONPATH='.' python scripts/train.py` [(see here)](https://stackoverflow.com/a/79209936/4082104). But explaining exactly why to other people is a hassle. Most people are uncomfortable with changes to `sys.path`, and it's just an extra hurdle to communicate that that is how the scripts are intended to be run.

But, if I control the conda environment, I can avoid this without forcing the user to mess with their environment variables. I can make conda set that environment variable *only while the conda environment is active*. All you have to do is add the following to the bottom of your `environment.yaml`.

```yaml
variables:
    PYTHONPATH: '.'
```

And, if you already have an environment, you can update it with:

```bash
# for conda/mamba - while environment is active
conda envs config vars set PYTHONPATH='.'
# for micromamba - assuming you don't already have a state file
echo '{
    "env_vars": {
        "PYTHONPATH": "."
    }
}' > ./envs/myprojectname/conda-meta/state
```

## Desired outcome

I want to create a documentation creation framework.

This framework would sit somewhere on my machine (location irrelevant) but contain the styles, html and filters desired for creating documentation. I would then alias a command to the 11ty generation.

Each repository/package that I wish to document would have markdown files (generally in a `docs` folder). I want to try and reduce the amount of 11ty/site generation specific code required in the markdown files, so they they can be read by Github/Gitlab (and any other doc framework). The output will also be in the repository under `docs-dist`.

The ultimate end-goal is to be able to generate the docs using a `.gitlab-ci.yml` file to have the docs on Gitlab Pages.

## Expected Process

The dev would make a `docs` folder in their repository with subfolders etc. The less frontmatter required the better.

They would then run something like `generate docs`, which is a custom command. This uses an 11ty framework (much like `doc-framework`) to generate the docs.

I want to avoid having 11ty specific code in the repo itself.

## Proposed Solutions

### Clone/Symlink

The first solution is to clone the framework into the target repo (or symlink it) and have the `.eleventy.js` paths set up like:

```
dir: {
	input: './../docs/',
	output: '../docs-dist/',

	data: '/../doc-framework/app/data',
	includes: '/../doc-framework/app/includes',
	layouts: '/../doc-framework/app/layouts',
}
```

Making 11ty "go out" of the `doc-framework' for the input and then, becuase everything else is relative to that, we have to navigate back round the file system.

#### Advantages

- No guessing as to where and how the files system is laid out

#### Disadvantages

- Have to "clean up" the doc-framework
- Risk of being committed
- "Warm-up" time (cloning/symlinkinging)


### Sibling folder

Another solution is to ensure the `doc-framework` is parallel to your packages. In a similar vein to the symlink/cloning solution, the 11ty config would traverse the file system.

This would require using the `--input` and `--output` flags when generating on the command line.

```
dir: {
	data: '/../doc-framework/app/data',
	includes: '/../doc-framework/app/includes',
	layouts: '/../doc-framework/app/layouts',
}
```

#### Advantages

- No chance of having files committed
- No need to keep cloning the repo - all the packages can use the same framework


#### Disadvantages

- Need to pass in input and output flags via cli
- Requires a a strict file structure
- Easily broken/not compatible with deploying packages "in situ"
- Can't guarantee where the CI clones to repo to ensure `doc-framework` is a sibling.

### Absolute Paths

The final solution I can think of is allowing absolute paths in the `eleventy.js`  (or allow paths to be relative to the config file).

This means the `dir` structure would look like the following (input and output would be passed in via CLI)

```
dir: {
	data: '@/app/data',
	includes: '@/app/includes',
	layouts: '@/app/layouts',
}
```

#### Advantages

- Can be added with no breaking changes
- Allows the doc framework to live wherever

#### Disadvantages

- Need to pass input and output via CLI

#### Bonus

What would be awesome is if there was a `--base` CLI param you could pass in which equates to `^` or similar...

```
dir: {
	input: '^/docs/',
	output: '^/docs-dist/',

	data: '@/app/data',
	includes: '@/app/includes',
	layouts: '@/app/layouts',
}
```


## Feedback

I am open to ideas/structure/additional advantanges and disadvantages. Please raise an issue if you have anything to add.

Thanks for coming to my TED talk.

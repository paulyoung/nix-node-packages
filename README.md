# Nix node package repository

This is a collection of node packages expressed in the nix language. They can be installed via the nix package manager. They have been generated by [nixfromnpm](https://github.com/adnelson/nixfromnpm). The packages defined in this repo can be used as-is, for example as `buildInputs` or `propagatedBuildInputs` to other nix derivations, or can be used with `nixfromnpm` to auto-generate more nix expressions for other node packages. At present, the top 36 npm packages (as listed [here](https://www.npmjs.com/browse/star)) and all of their dependencies have been defined, though few of them tested.

## Using this library

Of course, make sure you have nix installed.

Clone the repo:

```bash
$ git clone https://github.com/adnelson/nix-node-packages.git
```

The expressions here refer to a `nixpkgs` path variable, so make sure you have it your `NIX_PATH`. For example, to point it at your `nixpkgs` channel:

```bash
$ export NIX_PATH="$HOME/.nix-defexpr/channels:$NIX_PATH"
```

After that you're free to do whatever you please with the library. Packages are locate under the top-level attribute `nodePackages`. An unqualified name will build the latest version (as determined by comparing package version names), while a particular version can be referred to as shown below. For example, the following commands build the latest version of `grunt`, and version `0.4.5`, respectively:

```bash
$ nix-build nix-node-packages/nodePackages -A nodePackages.grunt
$ nix-build nix-node-packages/nodePackages -A nodePackages.grunt_0-4-5
```

## Extending the libraries

It's possible that you'll want to add additional packages that haven't been defined here. Alternatively, you might have your own packages, perhaps private, that you want to generate expressions for, but not have alongside all of the other packages in this repo. While you can always write these packages by hand, it's easier to do this with `nixfromnpm`. It can be obtained [here](https://github.com/adnelson/nixfromnpm).

### Adding new packages to the central package set

```bash
$ nixfromnpm -p package1 -p package2 -o nix-node-packages
```

This will calculate expressions for `package1` and `package2`, and place the generated expressions alongside all of the existing packages.

### Create a new package set which refers to the central set

```bash
$ nixfromnpm -p package1 -p package2 -o path/to/new/set --extend nix-node-packages
```

This will calculate expressions for `package1` and `package2`, and place them and their dependencies in `path/to/new/set`. However, any dependencies which exist in `nix-node-packages/nodePackages` will not need to be fetched, and will not appear in `path/to/new/set`.

### Generate a `default.nix` file from a project

You might have a project which has a `package.json` that specifies a bunch of dependencies. You can use `nixfromnpm` to generate expressions for the package's dependencies, and output a `default.nix` file for the package.

```bash
$ nixfromnpm -f path/to/project -o nix-node-packags
```
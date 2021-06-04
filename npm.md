# npm: The Node Package Manager

## Conceptual Aside: Packages & Package Managers

**Package**: Code...

...managed and maintained with a package management system.

**Package Management System**: Software that automates installing and updating packages.

Deals with what version you have or need, and manages **dependencies**.

**Dependency**: Code that another set of code depends on to function.

If you use that code in your app, it is a dependency. Your app depends on it.

## Conceptual Aside: Semantic Versioning (semver)

**Versioning**: Specifiying what version of a set of code this is...

...so others can track if a new version has come out. This allows to watch for new features, or to watch for 'breaking changes'.

The word 'semantic' implies that something conveys meaning.

The basic core of seamtic versioning is:

Major.Minor.Patch === 1.7.3

Patch: Some bug were fixed. Your code will work fine.

Minor: Some new features were added. Your code will work fine.

Major: Big changes. Your code might break.

## npm & The npm Registry: Other People's Code

Inside the terminal before downloading npm packages we initialize npm and fill in the relevant info.

```
npm init
```

The ^ inside the dependencies in package.json tells node it is okay to update inside the major release.

Using ~ means that only patches are allowed to automatically update.
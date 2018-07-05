---
title:  "Optional Dependencies"
---

# Working Example

All code snippets are taken from a "real boy" [build-able solution](https://github.com/dmorrison42/net-core-optional-dependencies).

If something isn't working for you, make sure the solution builds.

[MSDN](https://msdn.microsoft.com/en-us/library/ms972962.aspx?f=255&MSPPError=-2147217396) and [StackOverflow](https://stackoverflow.com/a/1422271/8999186) already had a pretty write-ups, but I found their explanations more confusing than satisfactory during my initial research.

# What, Why?

The basis for this project is the [reflection factory pattern](reflection-factory) so all the benefits apply.

Additionally, this allows for solutions to be better broken up into smaller projects for re-use.

With a little creativity, this can even be used along with [Impromtu Interface](https://github.com/ekonbenefits/impromptu-interface) for linking against a LGPL library in non-GPL code optionally.

# Getting the Types

## Warning

> Reflection can be slow and/or dangerous.
>
> Avoid running untrusted code (such as directly from user input) or making heavy calls such as to `GetTypes` repeatedly.

Because assemblies aren't used until their first reference in c#, consider using something like [LoadAssembliesOnStartup.Fody](https://github.com/Fody/LoadAssembliesOnStartup). Alternatively the internet seems to suggest loading all of the assemblies in the executing folder.
As someone who is mildly paranoid, I would recommend either specifying a path manually (like a plugins directory), or just not doing that, because if somehow an untrusted dll gets dropped into your executing directory, the system will be compromised, and you should feel bad.
However, arguably if anyone gets write access to the file system it is already compromised, so you do you.

```c#
foreach (var reference in Assembly.GetEntryAssembly().GetReferencedAssemblies())
{
    try
    {
        AllTypes.AddRange(Assembly.Load(reference).GetTypes());
    }
    catch (ReflectionTypeLoadException e)
    {
        AllTypes.AddRange(e.Types);
    }
}
```

# Why so many projects?

If you look at the example solution, there are three projects. The executing assembly, a library containing the interface, and a library containing the optional dependency (plugin).

This is because in order for the optional dependency to be type safe, it needs to reference the interface, and in order for the optional dependency type to show up, it must be referenced by the executing assembly.

The simplest solution to this circular dependency is simply to split out the interface into its own project, along with all of its related code, because why not, you've already got a project for it.

If you don't care about type checking, you might hate yourself. But if you really don't care, just trust yourself to implement the interface correctly in the optional dependency, then use [Impromtu Interface](https://github.com/ekonbenefits/impromptu-interface) to make your optional dependency to act like it is really the correct interface.

This

```c#
return (IPlugin) Activator.CreateInstance(type);
```

Becomes

```c#
return Activator.CreateInstance(type).ActLike<IPlugin>();
```

Again, I find `ActLike` useful for linking against LGPL projects (and maybe a few other things), because I'm a bad open source developer, but it sacrifices all of the tooling that makes type safe languages bearable.

---
title:  "Reflection Factory"
---

# What, Why?

Like any [factory pattern](https://en.wikipedia.org/wiki/Factory_method_pattern), this allows for a interface to be instantiated without specifying a class.

Use Cases, Switch Based On:

- Class Name
- Priority
- Method (bool IsRightClass)

This allows implementations to cleanly and easily be split into individual files. These files don't even need to all be in the same assembly!

# Getting the Types

## Warning

Reflection can be slow and/or dangerous.

Avoid running untrusted code (such as directly from user input) or making heavy calls such as to `GetTypes` repeatedly.

As such: We're going to put this into the static constructor, caching the results as much as possible.

```c#
// Requires assemblies to be loaded. LoadAssembliesOnStartup.Fody does a clever job.
// Do as much caching as possible, Reflection can be heavy!
static readonly List<Type> AllTypes = new List<Type>();
static readonly Type[] PluginTypes;
static Plugin()
{
    foreach (var reference in Assembly.GetEntryAssembly().GetReferencedAssemblies())
    {
        AllTypes.AddRange(Assembly.Load(reference).GetTypes());
    }

    PluginTypes = AllTypes
        .Where(t => typeof(IPlugin).IsAssignableFrom(t))
        .Where(t => t.IsClass)
        .ToArray();
}
```

If all the required types are in the current assembly, AllTypes can be simply `Assembly.GetAssembly.GetTypes()`.

# Returning the Interface

```c#
public static IPlugin New()
{
    var defaultType = typeof(DefaultPlugin);
    var targetType = PluginTypes
        .FirstOrDefault(t => t != defaultType);
    if (targetType != null)
    {
        return (IPlugin) Activator.CreateInstance(targetType);
    }
    return new DefaultPlugin();
}
```

Now you can instantiate the interface without calling the class directly, therefore allowing the correct implementation to be chosen from the arguments passed to `New()` (left as an exercise for the reader, obviously, there are no arguments in this example).
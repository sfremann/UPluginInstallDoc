# UPluginInstallDoc
Small doc to explain how to install and add dependencies to Unreal Engine plugin containing C++ code

## Installation

Many Unreal Engine plugins can be found directly on the marketplace [Fab](https://www.fab.com/), in which case installation instructions are given there.

More plugins can be found online, on GitHub repositories for example, and require a manual installation:
- For __global__ installation (all projects) download release and extract in Unreal installation folder
    - `"/Epic Games/UE_[X].[Y]/Engine/Plugins/Marketplace/"`
- For __local__ installation (specific project MyProject) download release or clone repository (as submodule eventually) in your project folder
  	- `"/[MyProject]/Plugins/"`

Then it should show up in your project when you go to `Edit > Plugins`.

## Dependencies

You can use code from the plugins containing C++ in your project or in other plugins. For this, you need to set up dependencies for the plugin module(s). Let's say imagine you're interested in a plugin ThisPlugin (specifically the ThisPluginModule module) and you want to add dependencies to your Unreal project MyProject and/or to your other plugin MyPlugin (specifically in the module MyPluginModule). You will need to add dependencies in the `Build.cs` files for MyProject and MyPlugin.

- __MyProject__ `Build.cs` -> "`/[MyProject]/Source/[MyProject]/[MyProject].Build.cs"`
- __MyPlugin__ `Build.cs` -> `"/[MyProject]/Plugins/[MyPlugin]/Source/[MyPluginModule]/[MyPluginModule].Build.cs"`

If you use the code in another plugin, you might want to add a dependency for the ThisPlugin plugin in the `.uplugin` file: open `"/[MyProject]/Plugins/[MyPlugin]/[MyPlugin].uplugin"` and add `"ThisPlugin"` to `"Plugins"`.
```
{
    ..., 
    "Plugins": [
        ..., 
        {
            "Name": "ThisPlugin", 
            "Enabled": true
        }
    ]
}
```

In the `Build.cs` file(s): add `"ThisPluginModule"` to `PrivateDependencyModuleNames`. This will allow you to include files from ThisPluginModule module in your project/plugin's __private__ `.cpp` and `.h` files.
```
...
PrivateDependencyModuleNames.AddRange(new string[]
    {
        ...,
        "ThisPluginModule"
    }
);
```

If you need to include files from ThisPluginModule module in your project/plugin's __public__ `.h` files (e.g. if you want a class to inherit from one of the plugin's classes), in the `Build.cs` file: add `"ThisPluginModule"` to `PublicDependencyModuleNames`.
```
...
PublicDependencyModuleNames.AddRange(new string[]
    {
        ...,
        "ThisPluginModule"
    }
);
```

> [!WARNING]
> Some modules are __Runtime__ modules, some others are __Editor__ modules. __Editor__ modules are not supposed to be used at runtime. Do not try to include them in a runtime plugin module or in the main project's `Build.cs`.

> [!CAUTION]
> If you run into trouble, you might need to do the following in the `Build.cs` file (but that should __NOT__ be necessary):
> - Add `"ThisPluginModule/Public"` and `"ThisPluginModule/Classes"` to `PublicIncludePaths`
> - Add `"ThisPluginModule/Public"` and `"ThisPluginModule/Classes"` to `PrivateIncludePaths`
```
...
PublicIncludePaths.AddRange(new string[]
    {
        ...,
        "ThisPluginModule/Public",
        "ThisPluginModule/Classes",
    }
);
...
PrivateIncludePaths.AddRange(new string[]
    {
        ...,
        "ThisPluginModule/Public",
        "ThisPluginModule/Classes",
    }
);
```

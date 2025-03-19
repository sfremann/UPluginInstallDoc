# UPluginInstallDoc
Small doc to explain how to install and add dependencies to Unreal Engine plugin containing C++ code

## Installation

Many Unreal Engine plugins can be found directly on the marketplace [Fab](https://www.fab.com/), in which case installation instructions are given there.

More plugins can be found online, on GitHub repositories for example, and require a manual installation:
- For __global__ installation (all projects) download release and extract in Unreal installation folder
    - `"/Epic Games/UE_[X].[Y]/Engine/Plugins/Marketplace/"`
- For __local__ installation (specific project) download release or clone repository (as submodule eventually) in your project folder
    - `"/[Project]/Plugins/"`

Then it should show up in your project when you go to `Edit > Plugins`.

## Dependencies

You can use code from the plugins containing C++ in your project or in other plugins. For this, you need to set up dependencies for the plugin module(s). Let's say imagine you're interested in a plugin `[Plugin]` (specifically in the `[PluginModule]` module) and you want to add dependencies to your Unreal project `[Project]` and/or to your other plugin `[OtherPlugin]` (specifically to the `[OtherPluginModule]` module). You will need to add dependencies in the `Build.cs` files for `[Project]` and `[OtherPlugin]`.

- __Project__ `Build.cs`:
    - "`/[Project]/Source/[Project]/[Project].Build.cs"`
- __Other plugin__ `Build.cs`:
    - `"/[Project]/Plugins/[OtherPlugin]/Source/[OtherPluginModule]/[OtherPluginModule].Build.cs"`

If you use the code in another plugin, you might want to add a dependency for the `[Plugin]` plugin in the `.uplugin` file: open `"/[Project]/Plugins/[OtherPlugin]/[OtherPlugin].uplugin"` and add `"[Plugin]"` to `"Plugins"`.
```
{
    ..., 
    "Plugins": [
        ..., 
        {
            "Name": "[Plugin]", 
            "Enabled": true
        }
    ]
}
```

In the `Build.cs` file(s): add `"[PluginModule]"` to `PrivateDependencyModuleNames`. This will allow you to include files from [PluginModule] module in your project/plugin's __private__ `.cpp` and `.h` files.
```
...
PrivateDependencyModuleNames.AddRange(new string[]
    {
        ...,
        "[PluginModule]"
    }
);
```

If you need to include files from `[PluginModule]` module in your project/plugin's __public__ `.h` files (e.g. if you want a class to inherit from one of the plugin's classes), in the `Build.cs` file: add `"[PluginModule]"` to `PublicDependencyModuleNames`.
```
...
PublicDependencyModuleNames.AddRange(new string[]
    {
        ...,
        "[PluginModule]"
    }
);
```

> [!WARNING]
> Some modules are __Runtime__ modules, some others are __Editor__ modules. __Editor__ modules are not supposed to be used at runtime. Do not try to include them in a plugin runtime module or in the main project's `Build.cs`.

> [!CAUTION]
> If you run into trouble, you might need to do the following in the `Build.cs` file (but that should __NOT__ be necessary):
> - Add `"[PluginModule]/Public"` and `"[PluginModule]/Classes"` to `PublicIncludePaths`
> - Add `"[PluginModule]/Public"` and `"[PluginModule]/Classes"` to `PrivateIncludePaths`
```
...
PublicIncludePaths.AddRange(new string[]
    {
        ...,
        "[PluginModule]/Public",
        "[PluginModule]/Classes",
    }
);
...
PrivateIncludePaths.AddRange(new string[]
    {
        ...,
        "[PluginModule]/Public",
        "[PluginModule]/Classes",
    }
);
```

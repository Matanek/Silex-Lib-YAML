# YAML for Silex

The `YAML` package reads, creates, modifies, and writes YAML documents intended
for human editing. Its API makes data provenance explicit: `parse` receives a
`str`, `read` receives a local path, `emit` produces a `str`, and `write`
replaces a local file.

[Lire cette documentation en français.](../FR/README.md)

## Install the package

```text
silex install YAML
```

Then import its public module:

```sx
use YAML
```

YAML requires Silex 0.39.0 or newer.

## Read a first configuration

`YAML.parse` is appropriate for text already loaded in memory:

```sx
use YAML

func main() {
    let source = "window:\n  width: 1280\n  fullscreen: false\n"
    var configuration = YAML.parse(source)
    var window = configuration.root().mapping("window")

    let width = window.integer("width")
    let fullscreen = window.boolean("fullscreen")
    print("Width: $(width), fullscreen: $(fullscreen)")
}
```

The program prints:

```text
Width: 1280, fullscreen: false
```

Use `YAML.read("application.yml")` when the source is a local file. A file
embedded in the executable remains ordinary text and uses the same parser:

```sx
let defaults = YAML.parse(embed_text("Config/defaults.yml"))
```

Asset paths are relative to the `.sx` file that references them. The
[embedded configuration recipe](Recipes/Embed-configuration.md) shows the
complete file layout.

## Modify and write

A mapping directly accepts strings, integers, and booleans, as well as
arbitrary `Node` values. Starting with the `window` node from the previous
example:

```sx
window.set("width", 1920)
window.set("fullscreen", true)
YAML.write("application.yml", configuration)
```

Mapping order and parsed presentation — comments, collection and scalar style,
tags, anchors, and aliases — remain represented by the document model. New
mappings and sequences use readable block style.

Follow the [configuration editing recipe](Recipes/Edit-configuration.md) for a
complete path that does not access the file system.

## Build a structured document

The `mapping`, `sequence`, `string`, `integer`, `boolean`, `null_value`,
`number_text`, and `alias` functions construct nodes. `Node.set` accepts any
YAML node as a key; its string overloads cover the common configuration case.

The [scene construction recipe](Recipes/Build-scene.md) composes and emits a
small scene graph. The engine remains responsible for the meaning of entity
identifiers, components, assets, and cross-references. YAML anchors and aliases
remain available when a format intentionally uses graph identity.

## Handle advanced documents

`parse_stream` and `read_stream` read multiple YAML documents. `Stream`
preserves their order and explicit markers.

The parser covers block and flow mappings and sequences, plain and quoted
scalars, literal and folded block scalars, comments, tags, anchors, aliases,
and document streams. Every parsed node retains its original line and column.

`ParseOptions` can limit bytes, depth, document count, and aliases:

```sx
let options = YAML.ParseOptions(
    maximum_bytes:64 * 1024 * 1024,
    maximum_depth:512,
    maximum_documents:32,
    maximum_aliases:10000,
)

let world = YAML.read("Scenes/world.yml", options)
```

Ordinary calls do not need to construct these options: `read(path)` and
`parse(text)` apply safe limits. `EmitOptions` only exposes an override for the
default two-space indentation.

## Recover from errors

Direct operations stop with a precise diagnostic when a key is missing, a node
or scalar has the wrong kind, a document is invalid, or a file is inaccessible.
They never silently coerce an unexpected value into an application setting.

An application that needs to continue can use `try_parse`, `try_parse_stream`,
`try_read`, `try_read_stream`, `try_emit`, `try_write`, and the node operations
prefixed with `try_`. `ReadError` distinguishes file errors from parse errors;
`WriteError` distinguishes emission errors from file errors.

The [recovery recipe](Recipes/Recover-invalid-configuration.md) reports an
invalid user configuration, then loads a known default value.

## Choose a recipe

- [Edit a configuration](Recipes/Edit-configuration.md)
- [Embed a configuration](Recipes/Embed-configuration.md)
- [Recover from an invalid configuration](Recipes/Recover-invalid-configuration.md)
- [Build a scene document](Recipes/Build-scene.md)

## Develop the package

From the repository root, link the checkout and run the public API consumer
tests:

```text
silex link .
silex test "$PWD/Tests"
```

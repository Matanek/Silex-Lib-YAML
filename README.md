# YAML for Silex

`YAML` reads, creates, modifies and writes human-authored YAML documents.
The common API keeps input provenance explicit: `parse` consumes a `str`,
`read` consumes a local path, `emit` produces a `str`, and `write` replaces a
local file.

```text
silex install YAML
```

```sx
use YAML
```

## Read configuration

```sx
var config = YAML.read("application.yml")
var window = config.root().mapping("window")

let width = window.integer("width")
let fullscreen = window.boolean("fullscreen")
```

The ordinary API returns the requested value directly and stops with a precise
diagnostic for a missing key, unexpected node kind, invalid scalar, malformed
document or inaccessible file. Every parsed node retains its source line and
column.

An embedded YAML file is ordinary text and uses the same parser:

```sx
let defaults = YAML.parse(embed_text("Config/defaults.yml"))
```

See [ReadEmbeddedConfiguration.sx](Examples/ReadEmbeddedConfiguration.sx)
and [EditConfiguration.sx](Examples/EditConfiguration.sx).

## Modify and write

Mappings accept direct string, integer and boolean values as well as arbitrary
`Node` values:

```sx
window.set("width", 1920)
window.set("fullscreen", true)
YAML.write("application.yml", config)
```

Comments, mapping order, collection style, scalar style, tags, anchors and
aliases are represented by the document model. Existing parsed styles are
retained when emitting; newly created mappings and sequences use readable
block style.

## Build structured documents

Use `mapping`, `sequence`, `string`, `integer`, `boolean`, `null_value`,
`number_text` and `alias` to construct a document. A mapping accepts arbitrary
YAML nodes as keys through `Node.set(key, value)`; the string-key overloads are
the ergonomic configuration path.

[StoreScene.sx](Examples/StoreScene.sx) constructs and emits a minimal
scene graph. Interpretation of entity identifiers, components, assets and
cross-references remains the engine's responsibility. YAML anchors and aliases
remain available when the scene format intentionally uses YAML graph identity.

## Advanced documents and limits

`parse_stream` and `read_stream` handle multiple YAML documents. `Stream`
preserves their order and explicit document markers. `ParseOptions` exposes
byte, depth, document and alias limits when an application needs policy beyond
the safe defaults:

```sx
let options = YAML.ParseOptions(
    maximum_bytes:64 * 1024 * 1024,
    maximum_depth:512,
    maximum_documents:32,
    maximum_aliases:10000,
)

let world = YAML.read("Scenes/world.yml", options)
```

Ordinary callers should use `read(path)` and `parse(text)` without constructing
options. `EmitOptions` similarly exposes indentation only when the caller wants
to override the readable two-space default.

The current parser covers block and flow mappings and sequences, plain and
quoted scalars, literal and folded block scalars, comments, tags, anchors,
aliases and document streams. Parse and access failures never silently coerce
an unexpected value into an application setting.

## Handle errors deliberately

Applications that recover from invalid input can opt into `try_parse`,
`try_parse_stream`, `try_read`, `try_read_stream`, `try_emit`, `try_write` and
the node operations `try_require`, `try_mapping`, `try_sequence`, `try_string`,
`try_boolean`, `try_integer`, `try_at`, `try_append` and `try_set`:

```sx
match YAML.try_read("application.yml") {
    success(config) => { print(config.root().string("application")) }
    failure(error) => { /* recover, report or use defaults */ }
}
```

`ReadError` distinguishes file failures from parse failures; `WriteError`
distinguishes emission from file failures. Fallible construction remains
available through `try_number_text` and `try_alias`.

[RecoverInvalidConfiguration.sx](Examples/RecoverInvalidConfiguration.sx)
shows a complete recovery path: report invalid user YAML, then continue with a
known-good default document.

## Development

Register the checkout, then exercise the public API through its consumer tests
and examples:

```text
silex link .
silex test "$PWD/Tests"
silex compile "$PWD/Examples/ReadEmbeddedConfiguration.sx" -o read-yaml-config
```

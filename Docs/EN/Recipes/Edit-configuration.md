# Edit a configuration

This recipe changes a value without losing the parsed comment or key order. It
uses an in-memory string to focus on the YAML model; replace `parse` with
`read` and `emit` with `write` to work on a local file.

```sx
use YAML

func main() {
    let source = "# User settings\naudio:\n  volume: 80\n  muted: false\n"
    var document = YAML.parse(source)
    var audio = document.root().mapping("audio")

    audio.set("volume", 65)
    print(YAML.emit(document))
}
```

The program prints:

```yaml
# User settings
audio:
  volume: 65
  muted: false
```

`mapping("audio")` requires an existing mapping and reports a diagnostic if it
is missing or contains another node kind. Here, `set` replaces `volume` at its
current position. It would append a new key to the mapping if none existed.

[Return to the YAML documentation](../README.md)

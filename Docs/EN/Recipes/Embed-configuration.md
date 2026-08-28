# Embed a configuration

This recipe embeds default values in the executable. It is useful for settings
that must remain available without a file deployed next to the application.

Place both files in the same directory; the path passed to `embed_text` is
relative to `Main.sx`:

```text
MyApp/
├── Defaults.yml
└── Main.sx
```

Write `Defaults.yml`:

```yaml
# Defaults embedded in the application binary.
window:
  width: 1280
  height: 720
  fullscreen: false
```

Then write `Main.sx`:

```sx
use YAML

func main() {
    let source = embed_text("Defaults.yml")
    var document = YAML.parse(source)
    var window = document.root().mapping("window")
    let width = window.integer("width")

    print("Default window width: $(width)")
}
```

The program prints:

```text
Default window width: 1280
```

`embed_text` includes the text at compile time; `YAML.parse` then applies the
same rules as it does to a string loaded at runtime.

[Return to the YAML documentation](../README.md)

# Recettes YAML

Ces recettes documentent des parcours complets sans maintenir un catalogue
d’exécutables dans le package.

## Defaults

```yaml
# Defaults embedded in the application binary.
window:
  width: 1280
  height: 720
  fullscreen: false
```

## EditConfiguration

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

## ReadEmbeddedConfiguration

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

## RecoverInvalidConfiguration

```sx
use YAML

func defaults() YAML.Document {
    return YAML.parse("window:\n  width: 1280\n  fullscreen: false\n")
}

func main() {
    let user_source = "window:\n\twidth: 1920\n"

    match YAML.try_parse(user_source) {
        success(configuration) => { print(YAML.emit(configuration)) }
        failure(error) => {
            print("Ignoring invalid configuration at $(error.line):$(error.column): $(error.detail)")
            print(YAML.emit(defaults()))
        }
    }
}
```

## StoreScene

```sx
use YAML

func main() {
    var camera = YAML.mapping()
    camera.set("id", "camera")
    camera.set("type", "Camera")

    var player = YAML.mapping()
    player.set("id", "player")
    player.set("parent", "world")

    var entities = YAML.sequence()
    entities.append(camera)
    entities.append(player)

    var root = YAML.mapping()
    root.set("scene", "Demo")
    root.set("entities", entities)
    var scene = YAML.document(root)

    print(YAML.emit(scene))
}
```

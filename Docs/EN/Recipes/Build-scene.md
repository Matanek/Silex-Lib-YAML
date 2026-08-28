# Build a scene document

This recipe constructs a document without initial YAML text. It shows how to
compose mappings and a sequence before joining them under one root.

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

The program emits a document equivalent to this one:

```yaml
scene: Demo
entities:
  -
    id: camera
    type: Camera
  -
    id: player
    parent: world
```

`mapping` and `sequence` create block-style collections. `append` adds the
entities in order, then `set("entities", entities)` attaches the whole sequence
to the root. The model retains generic `Node` values: the meaning of `id`,
`type`, and `parent` belongs to the scene format, not to YAML.

[Return to the YAML documentation](../README.md)

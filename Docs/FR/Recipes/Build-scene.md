# Construire un document de scène

Cette recette construit un document sans texte YAML initial. Elle montre
comment composer des mappings et une séquence avant de les réunir sous une
racine.

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

Le programme émet un document équivalent à celui-ci :

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

`mapping` et `sequence` créent des collections en style bloc. `append` ajoute
les entités dans leur ordre, puis `set("entities", entities)` attache la
séquence entière à la racine. Le modèle conserve des `Node` génériques : le
sens de `id`, `type` et `parent` appartient au format de scène, pas à YAML.

[Revenir à la documentation YAML](../README.md)

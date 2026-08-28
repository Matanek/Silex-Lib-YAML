# Modifier une configuration

Cette recette modifie une valeur sans perdre le commentaire ni l’ordre des
clés analysées. Elle utilise une chaîne en mémoire afin de se concentrer sur le
modèle YAML ; remplacez `parse` par `read` et `emit` par `write` pour travailler
sur un fichier local.

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

Le programme affiche :

```yaml
# User settings
audio:
  volume: 65
  muted: false
```

`mapping("audio")` exige un mapping existant et fournit un diagnostic s’il
manque ou contient un autre type de nœud. `set` remplace ici la valeur de
`volume` à sa position actuelle. Il ajouterait une nouvelle clé à la fin du
mapping si elle n’existait pas.

[Revenir à la documentation YAML](../README.md)

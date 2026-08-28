# Incorporer une configuration

Cette recette embarque des valeurs par défaut dans l’exécutable. Elle convient
aux paramètres qui doivent rester disponibles sans fichier déployé à côté de
l’application.

Placez les deux fichiers dans le même dossier ; le chemin donné à `embed_text`
est relatif à `Main.sx` :

```text
MyApp/
├── Defaults.yml
└── Main.sx
```

Écrivez `Defaults.yml` :

```yaml
# Defaults embedded in the application binary.
window:
  width: 1280
  height: 720
  fullscreen: false
```

Puis écrivez `Main.sx` :

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

Le programme affiche :

```text
Default window width: 1280
```

`embed_text` incorpore le texte pendant la compilation ; `YAML.parse` applique
ensuite exactement les mêmes règles que pour une chaîne chargée à l’exécution.

[Revenir à la documentation YAML](../README.md)

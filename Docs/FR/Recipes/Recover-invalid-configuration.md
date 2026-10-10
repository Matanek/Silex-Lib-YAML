# Récupérer une configuration invalide

Cette recette accepte qu’une configuration modifiable par l’utilisateur soit
incorrecte. Elle signale l’emplacement du problème, puis continue avec un
document par défaut connu.

```sx
use YAML

func defaults() YAML.Document {
    return YAML.parse("window:\n  width: 1280\n  fullscreen: false\n")
}

func main() {
    let user_source = "window:\n\twidth: 1920\n"

    match (try YAML.parse(user_source)) {
        success(configuration) => { print(YAML.emit(configuration)) }
        failure(error) => {
            print("Ignoring invalid configuration at $(error.line):$(error.column): $(error.detail)")
            print(YAML.emit(defaults()))
        }
    }
}
```

Le programme affiche :

```text
Ignoring invalid configuration at 2:1: YAML indentation cannot use tabs
window:
  width: 1280
  fullscreen: false
```

`try YAML.parse(...)` conserve un `Result<Document, ParseError>` au lieu d’arrêter le
programme. `ParseError` fournit une catégorie, un décalage en octets, une ligne,
une colonne et un détail lisible. Le même modèle existe pour les fichiers avec
`read`, pour l’émission avec `emit` et pour l’écriture avec `write`.

[Revenir à la documentation YAML](../README.md)

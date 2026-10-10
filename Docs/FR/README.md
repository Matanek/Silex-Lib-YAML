# YAML pour Silex

Le package `YAML` lit, crée, modifie et écrit des documents YAML destinés à
être édités par des humains. Son API distingue explicitement la provenance des
données : `parse` reçoit un `str`, `read` reçoit un chemin local, `emit`
produit un `str` et `write` remplace un fichier local.

[Read this documentation in English.](../EN/README.md)

## Installer le package

```text
silex install YAML
```

Importez ensuite son module public :

```sx
use YAML
```

Cette révision en développement requiert la toolchain qui distingue l’appel
ordinaire et la capture `try` des opérations `Result`.

## Lire une première configuration

`YAML.parse` convient au texte déjà chargé en mémoire :

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

Le programme affiche :

```text
Width: 1280, fullscreen: false
```

Utilisez `YAML.read("application.yml")` lorsque la source est un fichier
local. Un fichier incorporé à l’exécutable reste du texte ordinaire et passe
par le même parseur :

```sx
var defaults = YAML.parse(embed_text("Config/defaults.yml"))
```

Les chemins d’assets sont relatifs au fichier `.sx` qui les référence. La
[recette de configuration incorporée](Recipes/Embed-configuration.md) montre
l’arborescence complète.

## Modifier et écrire

Un mapping accepte directement les chaînes, entiers et booléens, ainsi que des
valeurs `Node` arbitraires. À partir du nœud `window` de l’exemple précédent :

```sx
window.set("width", 1920)
window.set("fullscreen", true)
YAML.write("application.yml", configuration)
```

L’ordre des mappings et la présentation déjà analysée — commentaires, style
des collections et scalaires, tags, ancres et alias — restent représentés par
le modèle du document. Les nouveaux mappings et séquences emploient un style
bloc lisible.

Suivez la [recette de modification d’une configuration](Recipes/Edit-configuration.md)
pour un parcours complet sans accès au système de fichiers.

## Construire un document structuré

Les fonctions `mapping`, `sequence`, `string`, `integer`, `boolean`,
`null_value`, `number_text` et `alias` construisent des nœuds. `Node.set`
accepte n’importe quel nœud YAML comme clé ; ses surcharges par chaîne couvrent
le cas courant des configurations.

La [recette de construction d’une scène](Recipes/Build-scene.md) compose et
émet un petit graphe de scène. Le moteur reste responsable du sens des
identifiants, composants, assets et références croisées. Les ancres et alias
YAML sont disponibles lorsqu’un format utilise volontairement l’identité d’un
graphe.

## Traiter les documents avancés

`parse_stream` et `read_stream` lisent plusieurs documents YAML. `Stream`
conserve leur ordre et leurs marqueurs explicites.

Le parseur couvre les mappings et séquences en style bloc ou flow, les
scalaires simples ou entre guillemets, les blocs littéraux ou repliés, les
commentaires, tags, ancres, alias et flux de documents. Chaque nœud analysé
conserve sa ligne et sa colonne d’origine.

`ParseOptions` permet de limiter le nombre d’octets, la profondeur, le nombre
de documents et les alias :

```sx
let options = YAML.ParseOptions(
    maximum_bytes:64 * 1024 * 1024,
    maximum_depth:512,
    maximum_documents:32,
    maximum_aliases:10000,
)

var world = YAML.read("Scenes/world.yml", options)
```

L’appel ordinaire n’a pas besoin de construire ces options : `read(path)` et
`parse(text)` appliquent des limites sûres. `EmitOptions` permet de remplacer
uniquement l’indentation de deux espaces utilisée par défaut.

## Récupérer les erreurs

Les opérations directes s’arrêtent avec un diagnostic précis si une clé manque,
si un nœud ou scalaire n’a pas le type attendu, si le document est invalide ou
si un fichier est inaccessible. Elles ne convertissent jamais silencieusement
une valeur inattendue en paramètre applicatif.

Chaque opération possède un seul nom et une seule déclaration `Result`.
L’appel ordinaire rend la valeur attendue ou arrête le programme sur erreur.
Ajoutez `try` pour conserver le résultat et traiter l’erreur :
`var résultat = try YAML.parse(texte)`, puis `match résultat`. Les opérations
sur les nœuds suivent le même modèle.

`ReadError` distingue les erreurs
de fichier des erreurs d’analyse ; `WriteError` distingue les erreurs d’émission
de celles du fichier.

La [recette de récupération](Recipes/Recover-invalid-configuration.md) signale
une configuration utilisateur invalide, puis charge une valeur par défaut
connue.

## Choisir une recette

- [Modifier une configuration](Recipes/Edit-configuration.md)
- [Incorporer une configuration](Recipes/Embed-configuration.md)
- [Récupérer une configuration invalide](Recipes/Recover-invalid-configuration.md)
- [Construire un document de scène](Recipes/Build-scene.md)

## Développer le package

Depuis la racine du dépôt, liez le checkout puis exécutez les tests
consommateurs de l’API publique :

```text
silex link .
silex test "$PWD/Tests"
```

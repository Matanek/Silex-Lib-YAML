# Recover from an invalid configuration

This recipe allows a user-editable configuration to be invalid. It reports the
location of the problem, then continues with a known default document.

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

The program prints:

```text
Ignoring invalid configuration at 2:1: YAML indentation cannot use tabs
window:
  width: 1280
  fullscreen: false
```

`try_parse` returns a `Result<Document, ParseError>` instead of stopping the
program. `ParseError` provides a category, byte offset, line, column, and
readable detail. The same model is available for files with `try_read`, for
emission with `try_emit`, and for writing with `try_write`.

[Return to the YAML documentation](../README.md)

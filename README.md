# Fag-Python

This is a test-skill developed for demo purposes. For use in APM.

## Usage

In your python based project:

- Add an apm.yml file by running `apm init`
- Add one of these to the file:

```yaml
dependencies:
  apm:
    - ways/fag-python-demo.git#v1.0.0
```

```yaml
dependencies:
  apm:
    - git@gitlab.met.no:larsfp/fag-python-demo.git#v1.0.0
```

- Run `apm install --refresh --ssh`

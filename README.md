# Fag-Python

This is a test-skill developed for demo purposes. For use in APM.

## Usage

In your python based project:

- Add an apm.yml file by running `apm init`
- Add one of these to the file:

```yaml
dependencies:
  apm:
    - ways/fag-python.git
```

```yaml
dependencies:
  apm:
    - git@gitlab.met.no:larsfp/fag-python.git
```

- Run `apm install --refresh --ssh`

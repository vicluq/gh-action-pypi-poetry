# Publish Python Package to PyPi with Poetry

## Configuration

You must add a repository secret called `PYPI_API_TOKEN` on your repo settings, this will be your pypi API Token.
Beware not to add it as an environment variable, but as a secret.

## Action Setup

The action will be triggered on the publication of a new release

```yaml
on:
  release:
    types: [published]
```

### Jobs

Setup python, using any version within the range 3.0 <= x < 4.0 (you can customize to your needs).

```yaml
- name: Set up Python
      uses: actions/setup-python@v3
      with:
        python-version: '3.x'
```

Installation of poetry, which will install it and lock the packages versions as specified in the `pyproject.toml` file under `[tool.poetry.dependencies]`.

```yaml
- name: Install Poetry
      run: |
        curl -sSL https://install.python-poetry.org | python -
        poetry lock
```

Dependency installation (not really needed, but it helped me verify if the dependencies were ok).

```yaml
- name: Install dependencies
      run: |
        poetry install
```

Package building which will create a `dist` folder to be sent to pypi.

```yaml
- name: Build package
      run: poetry build
```

Finally, the package publication, which will run the PyPi token config on poetry and the publication command.

```yaml
- name: Publish package
      env:
        PYPI_API_TOKEN: ${{ secrets.PYPI_API_TOKEN }}
      run: |
        poetry config pypi-token.pypi ${PYPI_API_TOKEN}
        poetry publish
```

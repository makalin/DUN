# Dependency Update Notifier

A Python tool that automatically checks your project's dependencies for updates and creates either GitHub issues or pull requests for available updates. The tool supports both `requirements.txt` and `pyproject.toml` files.

## Features

- Monitors multiple Python dependency files (`requirements.txt` and `pyproject.toml`)
- Checks PyPI for the latest versions of your dependencies
- Creates GitHub issues or pull requests for outdated dependencies
- Supports both Poetry and standard project dependencies in `pyproject.toml`
- Comprehensive logging for monitoring and debugging
- Configurable through environment variables

## Installation

1. Clone this repository:
```bash
git clone https://github.com/yourusername/dependency-update-notifier.git
cd dependency-update-notifier
```

2. Install the required dependencies:
```bash
pip install PyGithub packaging toml requests
```

## Configuration

The tool requires the following environment variables:

- `GITHUB_TOKEN`: A GitHub personal access token with repo permissions
- `GITHUB_REPOSITORY`: The repository name in the format `owner/repo`

### Creating a GitHub Token

1. Go to GitHub Settings > Developer settings > Personal access tokens
2. Generate a new token with the `repo` scope
3. Save the token securely and set it as an environment variable

## Usage

### Basic Usage

```bash
export GITHUB_TOKEN='your-github-token'
export GITHUB_REPOSITORY='owner/repo'
python dependency-updater.py
```

### As a GitHub Action

Create a new GitHub Actions workflow file (`.github/workflows/check-dependencies.yml`):

```yaml
name: Check Dependencies

on:
  schedule:
    - cron: '0 0 * * *'  # Run daily at midnight
  workflow_dispatch:  # Allow manual triggers

jobs:
  check-dependencies:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: '3.x'
      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install PyGithub packaging toml requests
      - name: Check dependencies
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          GITHUB_REPOSITORY: ${{ github.repository }}
        run: python dependency-updater.py
```

## Supported File Formats

### requirements.txt
```txt
package1==1.2.3
package2==2.3.4
```

### pyproject.toml
```toml
[tool.poetry.dependencies]
python = "^3.8"
package1 = "^1.2.3"
package2 = "^2.3.4"

[project.dependencies]
package3 = "^1.0.0"
```

## Features in Detail

### Version Detection
- Supports semantic versioning
- Handles version constraints (^, ~) in pyproject.toml
- Compares versions using the `packaging` library for accurate version parsing

### GitHub Integration
- Creates detailed issues or pull requests with:
  - Current and latest versions
  - File location
  - Automatic labels ('dependencies', 'update')
- For pull requests:
  - Creates a new branch
  - Updates dependency files
  - Provides detailed description of changes

### Logging
- Comprehensive logging with timestamps
- Error handling and reporting
- Warning messages for missing files or failed updates

## Error Handling

The tool includes robust error handling for:
- Missing configuration
- Invalid dependency files
- Failed API requests
- GitHub API errors
- Version parsing issues

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

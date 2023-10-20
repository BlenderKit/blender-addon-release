# Blender Addon Release Action

This GitHub Action is designed to automatically create a release of the Blender add-on and upload .zip file to it.
Action requires the add-on to be previously build and uploaded as artifact using [Blender Addon Build](https://github.com/BlenderKit/blender-addon-build) action or [Upload Artifact](https://github.com/actions/upload-artifact) action.

## 🚀 Features

- Automated zipping of Blender Addon artifacts.
- Dynamic naming convention based on the stage of release and other inputs.
- Supports multiple release stages including: alpha, beta, rc (release candidate), and gold (final public release).

## 🛠 Usage

Use the Blender Addon Release action in your GitHub workflow after the [Blender Addon Build](https://github.com/BlenderKit/blender-addon-build) or [Upload Artifact](https://github.com/actions/upload-artifact) action.
Here's a basic example:

```yaml
name: Release

on:
  pull_request:
    branches: [ '**' ]
  workflow_dispatch:
    inputs:
      version:
        description: 'version'
        required: true
        default: '1.0.0'

permissions:
  contents: write

jobs:
  Build:
    runs-on: ubuntu-latest
    steps:        
      - name: Build addon
        uses: blenderkit/blender-addon-build@main
        with:
          name: sample-addon
          exclude-files: ".git;.github;README.md"

  Release:
    runs-on: ubuntu-latest
    needs: Build
    steps:
      - name: Release addon
        uses: blenderkit/blender-addon-release@main
        with:
          artifact_name: sample-addon
          release_name: SampleAddon
          version: ${{github.event.inputs.version}}
```

### Inputs

| Name           | Description                                                                                                                                                       | Default  |
|----------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|
| `version`      | Version of the release in the format: X.Y.Z (major.minor.patch).                                                                                                 |          |
| `release_stage`| Stage of the release. Available options: `alpha`, `beta`, `rc`, and `gold`.                                                                                       | `gold`   |
| `artifact_name`| Name of the artifact to be downloaded. If left empty, the action will use the repository name.                                                                     | `''`     |
| `release_name` | Name of the release. If left empty, the action will use the repository name.                                                                                      | `''`     |

## 📝 Notes

It's important to note that this action is designed to be used in conjunction with the [Blender Addon Build](https://github.com/BlenderKit/blender-addon-build) action.
Make sure to run the Build job before the Release job.
Or run custom build job and finish it by uploading the zip file as artifact using the [Upload Artifact](https://github.com/actions/upload-artifact) action.

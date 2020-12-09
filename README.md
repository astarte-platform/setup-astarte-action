# Setup Astarte Action

This Github Action allows to setup an Astarte cluster in your CI workflow. This can be used to
perform end-to-end tests for an Astarte application.

## Usage

### Prerequisites

Create a workflow `.yml` file in your repositories `.github/workflows` directory. An [example
workflow](#example-workflow) is available below. For more information, reference the GitHub Help
Documentation for [Creating a workflow
file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file).

### Inputs

Various inputs are defined in [`action.yml`](action.yml) to let you configure the action:

| Name | Description | Default |
| - | - | - |
| `realm`      | Name of the realm that will be created by the action                       | `test` |
| `device-id` | The device ID that will be registered by the action | `xeT0nbhuTyCYzCoqD3w7Lg` |
| `interface-dir` | A relative path (starting from the root of the repo) containing interfaces to be installed in the created realm | N/A. If no `interface-dir` is provided, Astarte [standard interfaces](https://github.com/astarte-platform/astarte/tree/master/standard-interfaces) will be installed.
| `register-device` | If `true`, the action will register the device and provide a valid `credentials-secret` as output. If `false`, the action will not register the device, and the user will be responsible of doing so using the provided `pairing-jwt` | `true`                   |

### Outputs

The outputs defined in [`action.yml`](action.yml) are provided by the action after its execution and
can be used in the following steps:

| Name | Description |
| - | - |
| `realm`      | Name of the realm that was created by the action                   |
| `device-id` | The device ID that was registered by the action |
| `pairing-base-url` | The base URL to reach Pairing API in the Astarte instance created by the action |
| `appengine-base-url` | The base URL to reach AppEngine API in the Astarte instance created by the action |
| `pairing-jwt` | A JWT allowing the access to Pairing API for the created realm. This can be used to manually register the device |
| `appengine-jwt` | A JWT allowing the access to AppEngine API for the created realm. This can be used to query device data |
| `credentials-secret` | If register-device is `true`, this variable will contain the `credentials-secret` of the registered device |

## Example workflow

Create a workflow (e.g.: `.github/workflows/test-astarte.yml` see [Creating a Workflow
file](https://help.github.com/en/articles/configuring-a-workflow#creating-a-workflow-file)) to
utilize the setup-astarte action with this content:

```yaml
name: Setup Astarte example
on:
  pull_request:
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - id: setup-astarte
      uses: astarte-platform/setup-astarte-action@v1
      with:
        realm: test
        device-id: B552uoZ5SmGQq8d6NhtzPQ
        register-device: true
    - name: Use setup-astarte outputs
      run: |
        # Here you can use setup-astarte-action outputs
        echo Realm ${{ steps.setup-astarte.outputs.realm }}
        echo Device ID ${{ steps.setup-astarte.outputs.device-id }}
        echo Credentials Secret ${{ steps.setup-astarte.outputs.credentials-secret }}
        echo Pairing URL ${{ steps.setup-astarte.outputs.pairing-base-url }}
        echo Pairing JWT ${{ steps.setup-astarte.outputs.pairing-jwt }}
        echo AppEngine URL ${{ steps.setup-astarte.outputs.appengine-base-url }}
        echo AppEngine JWT ${{ steps.setup-astarte.outputs.appengine-jwt }}
      shell: bash
```

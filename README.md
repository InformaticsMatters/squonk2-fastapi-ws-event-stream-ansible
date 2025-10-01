# Ansible playbooks for the Squonk2 FastAPI WebSocket Event Stream

[![build](https://github.com/InformaticsMatters/squonk2-fastapi-ws-event-stream-ansible/actions/workflows/lint.yaml/badge.svg)](https://github.com/InformaticsMatters/squonk2-fastapi-ws-event-stream-ansible/actions/workflows/lint.yaml)

![Architecture](https://img.shields.io/badge/architecture-amd64%20%7C%20arm64-lightgrey)

![GitHub Release](https://img.shields.io/github/v/release/InformaticsMatters/squonk2-fastapi-ws-event-stream-ansible)

[![Conventional Commits](https://img.shields.io/badge/Conventional%20Commits-1.0.0-yellow.svg)](https://conventionalcommits.org)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit&logoColor=white)](https://github.com/pre-commit/pre-commit)

Ansible playbooks for our FastAPI (Python) implementation of the Squonk2 (AS)
Event Streaming service.

## Contributing
The project uses: -

- [pre-commit] to enforce linting of files prior to committing them to the
  upstream repository
- [Commitizen] to enforce a [Conventional Commit] commit message format

You **MUST** comply with these choices in order to  contribute to the project.

To get started review the pre-commit utility and the conventional commit style
and then set-up your local clone by following the **Installation** and
**Quick Start** sections: -

    python -m venv venv
    source venv/bin/activate
    pip install --upgrade pip
    pip install pre-commit
    pre-commit install -t commit-msg -t pre-commit

Now the project's rules will run on every commit, and you can check the
current health of your clone with: -

    pre-commit run --all-files

## Installation
The repository contains an ansible playbook that can be used to simplify the
deployment of the application into a pre-existing Kubernetes **Namespace** but you will
need to customise the playbook by first defining your own variables.

To install the application follow the steps below.

>   You will need access to your Kubernetes cluster and a **KUBECONFIG** file.

All the _required_ variables can be found at the top of the standard
`default/main.yaml` file, but you are advised to inspect all the variables to
ensure they are suitable for your installation (variables are defined in
`default/main.yaml` and `vars/main.yaml`).

You can create a `parameters.yaml` file and set your variables there.
A `parameters-template.yaml` file is provided as an example. This is protected from
accidental commits as it's in the project's `.gitignore` file: -

    cp parameters-template.yaml parameters.yaml

Then, when you have set your variables, identify your **KUBECONFIG** file,
and run the playbook: -

    export KUBECONFIG=~/k8s-config/nw-xch-dev.yaml
    ansible-playbook site.yaml -e @parameters.yaml

Once deployed the application's internal API will be behind the service
`ess-ws-api` on port `8081`, and available to any application running in the
cluster. The Account Server will be able to manage event streams via the URL
`http://ess-ws-api:8081/event-stream/`.

The external web-socket service will be available on the ingress host you've specified,
as either a `ws://` or `wss://` service, depending on whether you have set
the Ansible variable `ess_cert_issuer`. If the host is `example.com` you should be able
to connect to an unsecured web socket using the URL `ws://example.com/event-stream/{uuid}`
or `wss://example.com/event-stream/{uuid}` for a secured connection.

To update the running image (to deploy a new tagged version) just re-run the
playbook with a suitable value for `ess_image_tag`.

To remove the application run the playbook again, but set the `ess_state` variable
to `absent`: -

    ansible-playbook site.yaml -e @parameters.yaml -e ess_state=absent

---

[commitizen]: https://commitizen-tools.github.io/commitizen/
[conventional commit]: https://www.conventionalcommits.org/en/v1.0.0/
[pre-commit]: https://pre-commit.com

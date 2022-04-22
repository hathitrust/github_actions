# Testing GitHub Actions Locally with `act`

* Clone this repository
* Install act from https://github.com/nektos/act

* Copy `test/event.json.sample` to `test/event.json`
* Configure parameters as inputs to actions -- see notes in `.github/workflows/test.yml`

* In the repository root, run `act workflow-dispatch -l`. This lists the
  available jobs to run.

```
Stage  Job ID        Job name      Workflow name  Workflow file  Events           
0      tag-release   tag-release   Test           test.yaml      workflow_dispatch
0      validate-tag  validate-tag  Test           test.yaml      workflow_dispatch
0      build         build         Test           test.yaml      workflow_dispatch
```

Run a specific job with `act workflow-dispatch -j JOB -e test/event.json`. 

where `JOB` is `tag-release`, `validate-tag`, or `build`.

A sample `Dockerfile` is provided for the purposes of testing building and
pushing a small / trivial image.

Note that `act` has special handling for `actions/checkout` and ignores it by
default. For the `build` action. if you are trying to build something other
than the currently checked-out version, you may need to use the
`--no-skip-checkout` flag.

Testing actions that push to the GitHub Container Registry (`build` and
`tag-release`) as well as actions that deploy to Kubernetes (none in this
repository yet) requires additional configuration:

## Testing with GHCR

* Copy `test/secrets.sample` to `.secrets`
* Create a PAT in GitHub with repository and package scope and put it in `.secrets`

## Testing with minikube

* Install minikube: https://minikube.sigs.k8s.io/docs/start/
* Create a namespace, service account, role binding, and deployment:

```bash
kubectl create namespace action-test
kubectl -n action-test create serviceaccount github
kubectl -n action-test create rolebinding github-edit --clusterrole=edit --serviceaccount=action-test:github
kubectl -n action-test create deployment web --image some-image-to-run
```

* Add the minikube API endpoint, signing certificate, and access token to `.secrets`

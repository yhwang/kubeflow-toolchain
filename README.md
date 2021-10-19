# kubeflow-toolchain
Use toolchain service on IBM Cloud to verify Kubeflow deployment

This repository provides several toolchain tasks (Tekton Tasks) for Kubeflow, including
- `schematics-workspace-create`: it creates a schematics workspace to point to
  [IBM/auto-kubeflow](https://github.com/IBM/auto-kubeflow) repository which contains
  terraform scripts for IKS creation and Kubeflow multi-user deployment.
- `schematics-workspace-apply`: it applies the schematics workspace and waits for
  it finishes IKS and Kubeflow set up
- `schematics-workspace-destroy`: it runs the destroy action against the schematics
  workspace which delete the IKS and related resources
- `profile-creation`: create the Kubeflow Profile based on the user's email. It also
  adds a `PodDefault` and creates a Notebook using the PodDefault. Therefore, the
  Notebook server can access the ml-pipeline
- `profile-deletion`: delete the Kubeflow Profile based on the user's email.
- `venv-creation`: create the virtualenv on the persistent volume claim and install
  `kfp` and related pip packages on it. This is needed by other testing tasks which
  need `kfp` CLI.
- `flip-coin`: this dependes on the `venv-creation` Task. It runs `flip coin` test case
- `e2e-mnist`: this depends on the `venv-creation` Task. It runs end-to-end mnist
  test case which contains katib and inferenceservice in its kubeflow pipeline. It verifies
  if the external predictor can properly classify a `9` image.

There are 4 Tekton Pipelines that you can use for toolchain service:
- `deploy-kubeflow`: it creates a schematics workspace to provision IKS cluster,
  deploy Kubeflow and setup a Kubeflow profile for the specified user.
- `setup-kubeflow`: it creates a Kubeflow profile on the target IKS cluster which
  contains Kubeflow deployment. After profile is ready, it adds a `PodDefault` and
  create a Notebook server using the PodDefault. In this case, the Notebook server
  can access ml-pipeline API with proper authentication token.
- `test-kubeflow`: this pipeline depends on `setup-kubeflow`, since you always need
  a profile in order to run a pipeline experiments and katib. It runs 2 test cases:
  flip coin and end-2-end mnist
- `remove-kubeflow`: it removes the IKS cluster which contains Kubeflow deployment
  along with the schematics workspace.

  You can find all Tekton Tasks/Pipelines under [.tekton](/.tekton)

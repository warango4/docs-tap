# Installing Tanzu Application Platform on clusters onboarded to Tanzu Service Mesh

This topic describes the workaround for using Tanzu Service Mesh with Tanzu Application Platfor. You cannot install Tanzu Application Platfor on a cluster that has Tanzu Service Mesh attached. To install Tanzu Application Platfor on a cluster where Tanzu Service Mesh is attached, follow the procedure below.

This workaround describes how Tanzu Service Mesh is to be configured in order to ignore the Tanzu Application Platfor namespaces. This allows you to install Tanzu Application Platfor, while Tanzu Service Mesh continues to satisfy other connectivity concerns.

`Note:` Tanzu Application Platfor workloads are unable to use Tanzu Service Mesh features like Global Namespace, Mutual Transport Layer Security authentication (mTLS), retries, and timeouts.

For information about Tanzu Service Mesh, see the [Tanzu Service Mesh Documentation](https://docs.vmware.com/en/VMware-Tanzu-Service-Mesh/index.html).

## Install on a Cluster Attached to Tanzu Service Mesh
This procedure assumes you have a cluster attached to Tanzu Service Mesh, and that you have not yet installed Tanzu Application Platfor.

`Note:` If you installed Cloud Native Runtimes on a cluster that has Tanzu Service Mesh attached before doing the procedure below, Pods fail to start. To fix this problem, follow the procedure below and then delete all Pods in the excluded namespaces.

Configure Tanzu Service Mesh to ignore namespaces related to Tanzu Application Platfor:

1. Navigate to the **Cluster Overview** tab in the Tanzu Service Mesh UI.
2. On the cluster where you want to install Tanzu Application Platfor, click **...**, then select **Edit Cluster**.
3. Create an Is Exactly rule for each of the following namespaces:

    + `app-live-view`
    + `build-service`
    + `cartographer-system`
    + `cert-manager`
    + `contour-external`
    + `contour-internal`
    + `conventions-system`
    + `developer-conventions`
    + `educates`
    + `educates-tutorials-ui`
    + `flux-system`
    + `image-policy-system`
    + `kapp-controller`
    + `kapp-controller-packaging-global`
    + `knative-discovery`
    + `knative-eventing`
    + `knative-serving`
    + `knative-sources`
    + `kpack`                                                   
    + `kube-node-lease`
    + `kube-public`
    + `kube-system`
    + `scan-link-system`
    + `scp-toolkit`
    + `secretgen-controller`
    + `service-bindings`
    + `source-system`
    + `spring-boot-convention`
    + `stacks-operator-system`
    + `tanzu-global`
    + `tap-gui`
    + `tap-install`
    + `tekton-pipelines`
    + `tkg-system`
    + `tkg-system-public`
    + `triggermesh`
    + `vmware-sources`

The namespace or namespaces where you plan to run Tanzu Application Platfor> workloads.
`Next Steps`
After configuring Tanzu Service Mesh, install Tanzu Application Platform and verify your installation:

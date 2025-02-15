# Tanzu Apps Workload List

This topic will help you list workloads in a namespace or across all namespaces.

```
tanzu apps workload list [flags]
```

## Examples

```
tanzu apps workload list
tanzu apps workload list --all-namespaces
```

## Options

```
      --all-namespaces   use all kubernetes namespaces
      --app name         application name the workload is a part of
  -h, --help             help for list
  -n, --namespace name   kubernetes namespace (defaulted from kube config)
```

## Options Inherited from Parent Commands

```
      --context name      name of the kubeconfig context to use (default is current-context defined by kubeconfig)
      --kubeconfig file   kubeconfig file (default is $HOME/.kube/config)
      --no-color          disable color output in terminals
  -v, --verbose int32     number for the log level verbosity (default 1)
```

## See Also

* [Tanzu Apps Workload](tanzu_apps_workload.md) - Workload lifecycle management


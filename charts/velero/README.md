# Velero

Velero is an open source tool to safely backup and restore, perform disaster recovery, and migrate Kubernetes cluster resources and persistent volumes.

Velero has two main components: a CLI, and a server-side Kubernetes deployment.

## Installing the Velero CLI

See the different options for installing the [Velero CLI](https://velero.io/docs/v1.5/basic-install/#install-the-cli).

## Installing the Velero server

### Velero version

This helm chart installs Velero version v1.5 https://velero.io/docs/v1.5/. See the [#Upgrading](#upgrading) section for information on how to upgrade from other versions.

### Provider credentials

When installing using the Helm chart, the provider's credential information will need to be appended into your values. The easiest way to do this is with the `--set-file` argument, available in Helm 2.10 and higher. See your cloud provider's documentation for the contents and creation of the `credentials-velero` file.

### Installing

The default configuration values for this chart are listed in values.yaml.

See Velero's full [official documentation](https://velero.io/docs/v1.5/basic-install/). More specifically, find your provider in the Velero list of [supported providers](https://velero.io/docs/v1.5/supported-providers/) for specific configuration information and examples.

#### Set up Helm

See the main [README.md](https://github.com/vmware-tanzu/helm-charts#kubernetes-helm-charts-for-vmware-tanzu).

#### Using Helm 3

##### Option 1) CLI commands

Note: You may add the flag `--set cleanUpCRDs=true` if you want to delete the Velero CRDs after deleting a release.
Please note that cleaning up CRDs will also delete any CRD instance, such as BackupStorageLocation and VolumeSnapshotLocation, which would have to be reconfigured when reinstalling Velero. The backup data in object storage will not be deleted, even though the backup instances in the cluster will.

Specify the necessary values using the --set key=value[,key=value] argument to helm install. For example,

```bash
helm install velero vmware-tanzu/velero \
--namespace <YOUR NAMESPACE> \
--create-namespace \
--set-file credentials.secretContents.cloud=<FULL PATH TO FILE> \
--set configuration.provider=<PROVIDER NAME> \
--set configuration.backupStorageLocation.name=<BACKUP STORAGE LOCATION NAME> \
--set configuration.backupStorageLocation.bucket=<BUCKET NAME> \
--set configuration.backupStorageLocation.config.region=<REGION> \
--set configuration.volumeSnapshotLocation.name=<VOLUME SNAPSHOT LOCATION NAME> \
--set configuration.volumeSnapshotLocation.config.region=<REGION> \
--set initContainers[0].name=velero-plugin-for-<PROVIDER NAME> \
--set initContainers[0].image=velero/velero-plugin-for-<PROVIDER NAME>:<PROVIDER PLUGIN TAG> \
--set initContainers[0].volumeMounts[0].mountPath=/target \
--set initContainers[0].volumeMounts[0].name=plugins \
```

Users of zsh might need to put quotes around key/value pairs.

##### Option 2) YAML file

Add/update the necessary values by changing the values.yaml from this repository, then run:

```bash
helm install vmware-tanzu/velero --namespace <YOUR NAMESPACE> -f values.yaml --generate-name
```
##### Upgrade the configuration

If a value needs to be added or changed, you may do so with the `upgrade` command. An example:

```bash
helm upgrade vmware-tanzu/velero <RELEASE NAME> --namespace <YOUR NAMESPACE> --reuse-values --set configuration.provider=<NEW PROVIDER>
```

#### Using Helm 2

We're no longer support Helm v2 since it's deprecated in November 2020.

##### Upgrade the configuration

If a value needs to be added or changed, you may do so with the `upgrade` command. An example:

```bash
helm upgrade vmware-tanzu/velero <RELEASE NAME> --reuse-values --set configuration.provider=<NEW PROVIDER> 
```

## Upgrading

### Upgrading to v1.6

The [instructions found here](https://velero.io/docs/v1.6/upgrade-to-1.6/) will assist you in upgrading from version v1.5.x to v1.6.

### Upgrading to v1.5

The [instructions found here](https://velero.io/docs/v1.5/upgrade-to-1.5/) will assist you in upgrading from version v1.4.x to v1.5.

### Upgrading to v1.4

The [instructions found here](https://velero.io/docs/v1.4/upgrade-to-1.4/) will assist you in upgrading from version v1.3.x to v1.4.

### Upgrading to v1.3.1

The [instructions found here](https://velero.io/docs/v1.3.1/upgrade-to-1.3/) will assist you in upgrading from version v1.2.0 or v1.3.0 to v1.3.1.

### Upgrading to v1.2.0

The [instructions found here](https://velero.io/docs/v1.2.0/upgrade-to-1.2/) will assist you in upgrading from version v1.0.0 or v1.1.0 to v1.2.0.

### Upgrading to v1.1.0

The [instructions found here](https://velero.io/docs/v1.1.0/upgrade-to-1.1/) will assist you in upgrading from version v1.0.0 to v1.1.0.

## Uninstall Velero

Note: when you uninstall the Velero server, all backups remain untouched.

### Using Helm 3

```bash
helm delete <RELEASE NAME> -n <YOUR NAMESPACE>
```

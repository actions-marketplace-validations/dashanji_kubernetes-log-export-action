# kubernetes-log-export-action

This GitHub action interacts with `kubectl` to download logs for all containers in the specified namespaces.
It also generates a mapping of the files within the logging directory so [Stoat](https://stoat.dev/) can provide
a link to browse the files generated by this action.

## Inputs
- `show_timestamps`: the string 'true' or 'false'. 'true' will prefix each log line with the k8s log timestamp
- `output_dir`: directory that you want to specify

## Example Configuration

```yaml
      # k8s cluster and kubectl initialization 

      - name: Dump Kubernetes Logs
        uses: dashanji/kubernetes-log-export-action@v1
        with:
          show_timestamps: 'true'
          output_dir: ./output

      - name: Run Stoat Action
        uses: stoat-dev/stoat-action@v0
        if: always()
```

## Example Output

For the above example, here's a possible output file structure:
```
❯ find output
output
output/default
output/default/whalesay-77db544f4d-rwmx9
output/default/whalesay-77db544f4d-rwmx9/whalesay.log
output/filetree.json
output/kube-system
output/kube-system/coredns-95db45d46-4dcml
output/kube-system/coredns-95db45d46-4dcml/coredns.log
output/kube-system/kube-proxy-rtm57
output/kube-system/kube-proxy-rtm57/kube-proxy.log
output/kube-system/kube-controller-manager-docker-desktop
output/kube-system/kube-controller-manager-docker-desktop/kube-controller-manager.log
output/kube-system/coredns-95db45d46-sl4vs
output/kube-system/coredns-95db45d46-sl4vs/coredns.log
output/kube-system/kube-apiserver-docker-desktop
output/kube-system/kube-apiserver-docker-desktop/kube-apiserver.log
output/kube-system/vpnkit-controller
output/kube-system/vpnkit-controller/vpnkit-controller.log
output/kube-system/etcd-docker-desktop
output/kube-system/etcd-docker-desktop/etcd.log
output/kube-system/kube-scheduler-docker-desktop
output/kube-system/kube-scheduler-docker-desktop/kube-scheduler.log
output/kube-system/storage-provisioner
output/kube-system/storage-provisioner/storage-provisioner.log
```

The file tree in `output/filetree.json` would have the following contents:
```json
{
  "name": "/",
  "type": "directory",
  "children": [
    {
      "name": "default",
      "type": "directory",
      "children": [
        {
          "name": "whalesay-77db544f4d-rwmx9",
          "type": "directory",
          "children": [{ "name": "whalesay.log", "type": "file" }]
        }
      ]
    },
    {
      "name": "kube-system",
      "type": "directory",
      "children": [
        {
          "name": "coredns-95db45d46-4dcml",
          "type": "directory",
          "children": [{ "name": "coredns.log", "type": "file" }]
        },
        {
          "name": "coredns-95db45d46-sl4vs",
          "type": "directory",
          "children": [{ "name": "coredns.log", "type": "file" }]
        },
        { "name": "etcd-docker-desktop", "type": "directory", "children": [{ "name": "etcd.log", "type": "file" }] },
        {
          "name": "kube-apiserver-docker-desktop",
          "type": "directory",
          "children": [{ "name": "kube-apiserver.log", "type": "file" }]
        },
        {
          "name": "kube-controller-manager-docker-desktop",
          "type": "directory",
          "children": [{ "name": "kube-controller-manager.log", "type": "file" }]
        },
        { "name": "kube-proxy-rtm57", "type": "directory", "children": [{ "name": "kube-proxy.log", "type": "file" }] },
        {
          "name": "kube-scheduler-docker-desktop",
          "type": "directory",
          "children": [{ "name": "kube-scheduler.log", "type": "file" }]
        },
        {
          "name": "storage-provisioner",
          "type": "directory",
          "children": [{ "name": "storage-provisioner.log", "type": "file" }]
        },
        {
          "name": "vpnkit-controller",
          "type": "directory",
          "children": [{ "name": "vpnkit-controller.log", "type": "file" }]
        }
      ]
    }
  ]
}
```

When provided as part of the configuration input to Stoat:
```yaml
plugins:
  static_hosting:
    kube-logs:
      metadata:
        name: "Kubernetes Logs"
      path: output
      file_viewer: true
```

You get a nifty link in each of your pull requests that links to an interactive log viewer for all of your Kubernetes logs!

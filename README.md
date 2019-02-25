# Install a self-hosted global load balancer

These are the instrctions to install a self-hosted global load balancer based on external-DNS and coredns.
We assume you have already installed federation. You can do it following these [instructions](https://github.com/raffaelespazzoli/openshift-federation).

To install the slef-hosted global load balancer you need an [inventory](./ansible/inventory) file as follows:

```yaml
clusters:
- name: <cluster_1_name>
  url: <cluster_1_master_api_url>
  username: <cluster_1_username>
  password: <cluster_1_password>
  federation_control_plane: true  
- name: <cluster_2_name>
  url: <cluster2_master_api_url>
  username: <cluster_2_username>
  password: <cluster_2_password>
  federation_control_plane: false
```

Once you inventory is ready, you can run the playbook as follows:

```shell
ansible-playbook -i <inventory> ./ansible/playbooks/deploy-sh-glb/deploy-sh-glb.yaml
```

This will deploy a dns server in each of you cluster. The DNS server is implemnetd by coredns using etcd as the storage.
external-DNS is the also deployed to feed coredns pointing to the cluster with the federation control plane:

![architecture](./media/shglb-architecture.png)

Once the deployemnt complete, coredns will explose a load balancer type sservice for each cluster in the inventory.
You should get the IPs of these services and configure the registrar of your domain to send requests to those IPs.

![holistic](./media/shglb-holistic.png)

## Test

In the cluster with the federation controle plane create a federated service and corresponfing federated service record

```shell
oc new project shglb-test
oc apply -f service.yaml
oc apply -f dnsrecord.yaml
```

now you should be able to the following

```shell
dig myservice.<domain> <IP of one of the exposed coredns>
```

and see a result.
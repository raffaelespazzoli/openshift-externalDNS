# openshift-externalDNS

oc new-project external-dns
helm template ./charts/etcd-operator --namespace external-dns -n etcd-operator | oc apply -f -
oc apply -f ./etcd.yaml -n external-dns
helm template --name external-coredns --namespace external-dns ./charts/coredns | oc apply -f -
oc apply -f ./external-dns.yaml -n external-dns
oc apply -f ./dnsrecord.yaml


oc new-project sh-glb 
helm template --name sh-glb --namespace sh-glb ./charts/sh-glb | pc apply -f -

oc apply -f ./dnsrecord.yaml
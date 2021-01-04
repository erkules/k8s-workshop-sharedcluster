# PSP-(Cluster)RoleBindings

Bei EKS interessieren uns 2 Sachen:

1. Netzwerk
2. PodSecurityPolicy (PSP)

# Netzwerk/CNI

Das Netzwerk von EKS beherrscht keine NetworkPolicies.
Daher haben wir Calico installiert:

~~~
kubectl apply -f https://raw.githubusercontent.com/aws/amazon-vpc-cni-k8s/v1.7.5/config/v1.7/calico.yaml
~~~

Nachzulesen in: https://docs.aws.amazon.com/eks/latest/userguide/calico.html

# PSP

Die PSPs in EKS sind zu lasch :/


# Die laschen Defaults ändern!

Das Binding Löschen, dass allen Authentifizierten erlaubt auch privilegiertn Workload zu fahren

~~~
kubectl delete -f eks:default-psp-binding.yaml
~~~

Den Workload nur für Serviceaccounts aus dem kube-system Namespace erlauben:

~~~
kubectl apply -f erkan-psp-kube-system.yaml
~~~

Für alle anderen nur das lasche PSP erlauben:

~~~
kubectl apply -f podsecurity-policy.yaml
~~~

Na jetzt ratet mal was ihr machen müsst um alles rückgänging zu machen? ...

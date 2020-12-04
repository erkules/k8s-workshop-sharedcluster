# PSP-(Cluster)RoleBindings


# Die laschen Defaults ändern

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

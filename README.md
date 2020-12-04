# K8s/OCP Multitenant

* Wir haben ein Cluster für viele Projekte
* Wie kann der Cluster gemeinsam genutzt/geshared werden?
* Wie bekommen wir den Workload so isoliert, dass nur wir auf die eigenen Applikationen zugreifen?
* Wie schaffen wir es, dass der Cluster und andere Projekte nicht in "Gefahr" kommen?


# Topics

* Wir mocken einen User projekt11 (Authentication)
* Dieser User wird Admin auf einem Namespace (Authorization/RBAC)
* Wir begrenzen den Resourcenverbrauch etc. in diesem Namespace (LimitRange, ResourceQuota)
* Wir beschränken das Netzwerk mit dynamischen Iptables-Regeln (NetworkPolicies)
* Wir gehen sicher, dass Container/Pods nur mit reduzierten Rechnten ausgerollt werden dürfen (PodSecurityPolicies/SCC)


# Ablauf


## Wir mocken einen User projekt11 (Authentication)

~~~
kubectl --as projekt11 get nodes 
~~~

## Dieser User wird Admin auf einem Namespace (Authorization/RBAC)

Namespace ist ne Abstraktion, aber keine Isolation. 

~~~
kubectl create ns projekt11                           <-- Namespace anlegen
kubectl apply -f rolebinding-namespaceadmin.yaml      <-- User projekt11 wird Admin im projekt11-ns
kubectl -n projekt11 --as projekt11 get pods          <-- Kein Fehler
kubectl -n default --as projekt11 get pods            <-- Fehler (gut so!)
~~~

Wir erstellen ein Alias (faule Leute).
Sprich kpro ist immer der User projekt11 im Namespace projekt11.

~~~
alias kpro="kubectl -n projekt11 --as projekt11"
kpro apply -f deployment.yaml                <-- Können wir ewig skalieren/aber funzt
kpro delete -f deployment.yaml               <-- Aufräumen
~~~


## Wir begrenzen den Resourcenverbrauch etc. in diesem Namespace (LimitRange, ResourceQuota)

~~~
kubectl apply -f resourcequota.yaml
...
kubectl apply -f limitrange-like-in-prod.yaml
...
~~~

und aufräumen

## Wir beschränken das Netzwerk mit dynamischen Iptables-Regeln (NetworkPolicies)

~~~
kpro apply -f deployment.yaml
kpro apply -f pod.yaml
kpro exec  www -- wget www.projekt11 -O -
kubectl -n projekt5  apply -f pod.yaml
kubectl -n projekt5  exec  www -- wget www.projekt11 -O -
kubectl apply -f networkpolicy.yaml
kpro exec  www -- wget www.projekt11 -O -
kubectl -n projekt5  exec  www -- wget www.projekt11 -O -
~~~

aufräumen

## Wir gehen sicher, dass Container/Pods nur mit reduzierten Rechnten ausgerollt werden dürfen (PodSecurityPolicies/SCC)

Siehe AWS/README.md

~~~
kpro apply -f  deployment.yml
kpro apply -f  deployment-user.yaml
~~~


# Einige Reste

* API Priority/Fairness
* Multus-CNI
* OPA (Vortrag geplant)
* PodPriority
* Hierarchical Namespaces
* Tenant
* Virtual Cluster!!!
* OLM


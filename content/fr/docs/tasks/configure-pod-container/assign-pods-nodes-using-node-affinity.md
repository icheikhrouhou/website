---
title: Affecter des pods aux nœuds en utilisant l'affinité des nœuds
min-kubernetes-server-version: v1.10
content_template: templates/task
weight: 120
---

{{% capture overview %}}
Cette page montre comment attribuer un Kubernetes pod à un nœud particulier en utilisant l'Affinité de nœud dans un cluster Kubernetes.
{{% /capture %}}

{{% capture prerequisites %}}

{{< include "task-tutorial-prereqs.md" >}} {{< version-check >}}

{{% /capture %}}

{{% capture steps %}}

## Ajouter un label à un nœud

1. Listez les nœuds de votre cluster, ainsi que leurs labels :

    ```shell
    kubectl get nodes --show-labels
    ```
    Le résultat est similaire à celui-ci :

    ```shell
    NAME      STATUS    ROLES    AGE     VERSION        LABELS
    worker0   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker0
    worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
    worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
    ```
1. Choisissez un de vos nœuds, et ajoutez-y un label :

    ```shell
    kubectl label nodes <le-nom-de-votre-nœud> disktype=ssd
    ```
    où `<le-nom-de-votre-nœud>` est le nom du nœud que vous avez choisi.

1. Vérifiez que le nœud que vous avez choisi possède un label qui correspond à `disktype=ssd` :

    ```shell
    kubectl get nodes --show-labels
    ```

    Le résultat est similaire à celui-ci :

    ```
    NAME      STATUS    ROLES    AGE     VERSION        LABELS
    worker0   Ready     <none>   1d      v1.13.0        ...,disktype=ssd,kubernetes.io/hostname=worker0
    worker1   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker1
    worker2   Ready     <none>   1d      v1.13.0        ...,kubernetes.io/hostname=worker2
    ```

    Dans le résultat précédent, vous pouvez voir que le nœud "worker0" a un label `disktype=ssd`.

## Planifier un pod en utilisant une affinité de nœud requise

Ce manifeste décrit un pod qui a une affinité de nœud de type `requiredDuringSchedulingIgnoredDuringExecution`,`disktype: ssd`. 
Cela signifie que le pod sera planifié uniquement sur un nœud qui a un label qui correspond à `disktype=ssd`. 

{{< codenew file="pods/pod-nginx-required-affinity.yaml" >}}

1. Appliquez le manifeste pour créer un Pod qui sera planifié sur votre nœud choisi :
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/pod-nginx-required-affinity.yaml
    ```

1. Vérifiez que le pod tourne sur le nœud que vous avez choisi :

    ```shell
    kubectl get pods --output=wide
    ```

    Le résultat est similaire à celui-ci :
    
    ```
    NAME     READY     STATUS    RESTARTS   AGE    IP           NODE
    nginx    1/1       Running   0          13s    10.200.0.4   worker0
    ```
    
## Planifier un pod en utilisant l'affinité de nœud préféré

Ce manifeste décrit un pod qui a une affinité de nœud de type `preferredDuringSchedulingIgnoredDuringExecution`,`disktype: ssd`. 
Cela signifie que le pod préfèrera un nœud qui a un label qui correspond à `disktype=ssd`. 

{{< codenew file="pods/pod-nginx-preferred-affinity.yaml" >}}

1. Appliquez le manifeste pour créer un Pod qui sera planifié sur votre nœud choisi :
    
    ```shell
    kubectl apply -f https://k8s.io/examples/pods/pod-nginx-preferred-affinity.yaml
    ```

1. Vérifiez que le pod tourne sur le nœud que vous avez choisi :

    ```shell
    kubectl get pods --output=wide
    ```

    Le résultat est similaire à celui-ci :
    
    ```
    NAME     READY     STATUS    RESTARTS   AGE    IP           NODE
    nginx    1/1       Running   0          13s    10.200.0.4   worker0
    ```

{{% /capture %}}

{{% capture whatsnext %}}
Pour en savoir plus sur
[Affinité des nœuds](/docs/concepts/configuration/assign-pod-node/#node-affinity).
{{% /capture %}}

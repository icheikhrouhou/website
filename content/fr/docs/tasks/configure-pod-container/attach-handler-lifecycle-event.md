---
title: Attacher des handlers aux événements du cycle de vie des conteneurs
content_template: templates/task
weight: 140
---

{{% capture overview %}}

Cette page montre comment attacher des handlers aux événements du cycle de vie des conteneurs. Kubernetes supporte les événements postStart et preStop. Kubernetes envoie l'événement postStart immédiatement après le démarrage d'un conteneur, et l'événement preStop immédiatement avant qu'il ne soit mis fin au conteneur.

{{% /capture %}}


{{% capture prerequisites %}}

{{< include "task-tutorial-prereqs.md" >}} {{< version-check >}}

{{% /capture %}}


{{% capture steps %}}

## Définir les handlers postStart et preStop 

Dans cet exercice, vous créez un pod qui a un seul conteneur. Le conteneur a des handlers pour les événements postStart et preStop.

Voici le fichier de configuration pour le pod :

{{< codenew file="pods/lifecycle-events.yaml" >}}

Dans le fichier de configuration, vous pouvez voir que la commande postStart écrit un `message`
dans le répertoire `/usr/share` du conteneur.  La commande preStop provoque l'arrêt de l'nginx de façon gracieuse. Cela est utile si le conteneur est arrêté en raison d'une défaillance.

Créez le pod :

    kubectl apply -f https://k8s.io/examples/pods/lifecycle-events.yaml

Vérifiez que le conteneur dans le pod tourne :

    kubectl get pod lifecycle-demo

Ouvrez un shell dans le conteneur qui tourne dans votre pod :

    kubectl exec -it lifecycle-demo -- /bin/bash

Dans votre shell, vérifiez que le handler `postStart` a créé le fichier `message` :

    root@lifecycle-demo:/# cat /usr/share/message

Le résultat montre le texte écrit par le handler postStart :

    Hello from the postStart handler

{{% /capture %}}



{{% capture discussion %}}

## Discussion

Kubernetes envoie l'événement postStart immédiatement après la création du conteneur.
Il n'est toutefois pas garanti que le handler postStart soit appelé avant
le entrypoint du conteneur est executé. Le handler postStart fonctionne de manière asynchrone
relatif au code du conteneur, mais la gestion du conteneur par Kubernetes est bloqué
jusqu'à ce que le handler de postStart termine. Le statut du conteneur n'est donc pas
mis à RUNNING jusqu'à ce que le handler de postStart termine.

Kubernetes envoie l'événement preStop immédiatement avant que le conteneur ne soit terminé.
La gestion du conteneur par Kubernetes est bloquée jusqu'à ce que le handler preStop ait terminé, à moins que le délai de grâce du pod expire. Pour plus de détails, voir [Terminaison des pods](/fr/docs/concepts/workloads/pods/pod/#termination-of-pods).

{{< note >}}
Kubernetes n'envoie l'événement preStop que lorsqu'un pod est *terminé*.
Cela signifie que le hook preStop n'est pas invoqué lorsque le pod est *complété*. 
Cette limitation est suivie dans [issue #55087](https://github.com/kubernetes/kubernetes/issues/55807).
{{< /note >}}

{{% /capture %}}


{{% capture whatsnext %}}

* Pour en savoir plus sur les [hooks de cycle de vie du conteneur](/fr/docs/concepts/containers/container-lifecycle-hooks/).
* Pour en savoir plus sur le [cycle de vie du pod](/fr/docs/concepts/workloads/pods/pod-lifecycle/).


### Référence

* [Lifecycle](/docs/reference/generated/kubernetes-api/{{< param "version" >}}/#lifecycle-v1-core)
* [Conteneur](/docs/reference/generated/kubernetes-api/{{< param "version" >}}/#container-v1-core)
* Voir `terminationGracePeriodSeconds` dans [PodSpec](/docs/reference/generated/kubernetes-api/{{< param "version" >}}/#podspec-v1-core)

{{% /capture %}}



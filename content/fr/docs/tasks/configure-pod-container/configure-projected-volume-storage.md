---
title: Configurer un pod pour utiliser un volume projeté pour le stockage
content_template: templates/task
weight: 70
---

{{% capture overview %}}
Cette page montre comment utiliser un volume [`projeté`](/fr/docs/concepts/storage/volumes/#projected) pour monter plusieurs sources de volume existantes dans le même répertoire. Actuellement, `secret`, `configMap`, `downwardAPI`, et `serviceAccountToken` peuvent être projetés.

{{< note >}}
`serviceAccountToken` n'est pas un type de volume.
{{< /note >}}
{{% /capture %}}

{{% capture prerequisites %}}
{{< include "task-tutorial-prereqs.md" >}} {{< version-check >}}
{{% /capture %}}

{{% capture steps %}}
## Configurer un volume projeté pour un pod

Dans cet exercice, vous créez un nom d'utilisateur et un mot de passe dans des {{< glossary_tooltip text="Secrets" term_id="secret" >}} à partir des fichiers locaux. Vous créez ensuite un Pod qui fait tourner un conteneur, en utilisant un volume [`projeté`](/fr/docs/concepts/storage/volumes/#projected) pour monter les secrets dans le même répertoire partagé.

Voici le fichier de configuration du Pod :

{{< codenew file="pods/storage/projected.yaml" >}}

1. Créez les secrets:

    ```shell
    # Créez des fichiers contenant le nom d'utilisateur et le mot de passe :
    echo -n "admin" > ./username.txt
    echo -n "1f2d1e2e67df" > ./password.txt

    # Emballez ces fichiers dans des secrets :
    kubectl create secret generic user --from-file=./username.txt
    kubectl create secret generic pass --from-file=./password.txt
    ```
1. Créez le pod:

    ```shell
    kubectl apply -f https://k8s.io/examples/pods/storage/projected.yaml
    ```
1. Vérifiez que le conteneur du pod tourne, puis surveillez les changements apportés au pod :

    ```shell
    kubectl get --watch pod test-projected-volume
    ```
    Le résultat obtenu ressemble à ceci :
    ```
    NAME                    READY     STATUS    RESTARTS   AGE
    test-projected-volume   1/1       Running   0          14s
    ```
1. Dans un autre terminal, ouvrez un shell dans le conteneur en cours d'utilisation :

    ```shell
    kubectl exec -it test-projected-volume -- /bin/sh
    ```
1. Dans votre shell, vérifiez que le répertoire `projected-volume` contient vos sources projetées :

    ```shell
    ls /projected-volume/
    ```

## Clean up

Supprimez le Pod et les Secrets :

```shell
kubectl delete pod test-projected-volume
kubectl delete secret user pass
```

{{% /capture %}}

{{% capture whatsnext %}}
* Pour en savoir plus sur les volumes [projetés](/fr/docs/concepts/storage/volumes/#projected).
* Lire le document de conception de [all-in-one volume](https://github.com/kubernetes/community/blob/{{< param "githubbranch" >}}/contributors/design-proposals/node/all-in-one-volume.md).
{{% /capture %}}

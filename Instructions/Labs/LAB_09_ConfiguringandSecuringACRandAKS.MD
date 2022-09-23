---
lab:
  title: 09 - Configuration et sécurisation d’ACR et d’AKS
  module: Module 02 - Implement Platform Protection
ms.openlocfilehash: 167dca080d65d97997b3a884015bfa2228c17a23
ms.sourcegitcommit: 967cb50981ef07d731dd7548845a38385b3fb7fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2022
ms.locfileid: "145955416"
---
# <a name="lab-09-configuring-and-securing-acr-and-aks"></a>Labo 09 : Configuration et sécurisation d’ACR et d’AKS
# <a name="student-lab-manual"></a>Manuel de labo pour l’étudiant

## <a name="lab-scenario"></a>Scénario du labo

Vous avez été invité à déployer une preuve de concept avec Azure Container Registry et Azure Kubernetes Service. Plus précisément, la preuve de concept devrait montrer ce qui suit :

- Utilisation de Dockerfile pour générer une image.
- Utilisation d’Azure Container Registry pour stocker des images.
- Configuration d’un environnement Azure Kubernetes Service.
- Sécurisation des applications conteneurs et accès à celles-ci tant en interne qu’en externe. 

> Pour toutes les ressources utilisées dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser pour la classe. 

## <a name="lab-objectives"></a>Objectifs du labo

Dans ce labo, vous allez effectuer les exercices suivants :

- Exercice 1 : Configuration et sécurisation d’ACR et d’AKS

## <a name="configuring-and-securing-acr-and-aks-diagram"></a>Diagramme de configuration et sécurisation d’ACR et d’AKS

![image](https://user-images.githubusercontent.com/91347931/157532250-1104a829-792a-4b6d-beff-fe976e2d5d4b.png)

## <a name="intructions"></a>Intructions

## <a name="lab-files"></a>Fichiers du labo :

- **\\Allfiles\\Labs\\09\\nginxexternal.yaml**
- **\\Allfiles\\Labs\\09\\nginxinternal.yaml**

### <a name="exercise-1-configuring-and-securing-acr-and-aks"></a>Exercice 1 : Configuration et sécurisation d’ACR et d’AKS

### <a name="estimated-timing-45-minutes"></a>Durée estimée : 45 minutes

> Pour toutes les ressources utilisées dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser pour la classe. 

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créer un registre de conteneurs Azure
- Tâche 2 : Créer un Dockerfile, générer un conteneur et l’envoyer (push) à Azure Container Registry
- Tâche 3 : Créer un cluster Azure Kubernetes Service
- Tâche 4 : Accorder au cluster AKS les autorisations d’accès à l’ACR
- Tâche 5 : Déployer un service externe sur AKS
- Tâche 6 : Vérifier que vous pouvez accéder à un service externe hébergé par AKS
- Tâche 7 : Déployer un service interne sur AKS
- Tâche 8 : Vérifier que vous pouvez accéder à un service interne hébergé par AKS

#### <a name="task-1-create-an-azure-container-registry"></a>Tâche 1 : Créer un registre de conteneurs Azure

Dans cette tâche, vous allez créer un groupe de ressources pour le labo et un Azure Container Registry.

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte titulaire du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo, et du rôle Administrateur général dans le locataire Azure AD associé à cet abonnement.

2. Dans le portail Azure, ouvrez le Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, cliquez sur **Bash**, puis **Créer un stockage**.

3. Vérifiez que **Bash** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

4. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de créer un groupe de ressources pour ce labo :

    ```sh
    az group create --name AZ500LAB09 --location eastus
    ```

5. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de vérifier que le groupe de ressources a été créé :

    ```
    az group list --query "[?name=='AZ500LAB09']" -o table
    ```

6. Dans la session Bash du volet Cloud Shell, exécutez la commande suivante pour créer une instance Azure Container Registry (ACR) (le nom de l’ACR doit être globalement unique) : 

    ```sh
    az acr create --resource-group AZ500LAB09 --name az500$RANDOM$RANDOM --sku Basic
    ```

7. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de vérifier que le nouvel ACR a été créé :

    ```sh
    az acr list --resource-group AZ500LAB09
    ```

    >**Remarque** : notez le nom de l’ACR. Vous en aurez besoin dans la prochaine tâche.

#### <a name="task-2-create-a-dockerfile-build-a-container-and-push-it-to-azure-container-registry"></a>Tâche 2 : Créer un Dockerfile, générer un conteneur et l’envoyer (push) à Azure Container Registry

Dans cette tâche, vous allez créer un Dockerfile, générer une image à partir de celui-ci, et déployer l’image sur l’ACR. 

1. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de créer un Dockerfile pour créer une image basée sur Nginx : 

    ```sh
    echo FROM nginx > Dockerfile
    ```

2. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de générer une image à partir du Dockerfile et d’envoyer (push) celle-ci au nouvel ACR. 

    >**Remarque** : le point à la fin de la commande est requis. Il désigne le répertoire actuel comme emplacement du Dockerfile. 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az acr build --image sample/nginx:v1 --registry $ACRNAME --file Dockerfile .
    ```

    >**Remarque** : Attendez que l’exécution de la commande se termine. Cela peut prendre environ 2 minutes.

3. Fermez le volet Cloud Shell.

4. Dans le portail Azure, accédez au groupe de ressources **AZ500Lab09**, puis, dans la liste des ressources, cliquez sur l’entrée représentant l’instance Azure Container Registry que vous avez approvisionnée dans la tâche précédente.

5. Dans le volet Registre de conteneurs, dans la section **Services**, cliquez sur **Dépôts**. 

6. Vérifiez que la liste des dépôts inclut la nouvelle image conteneur nommée **sample/nginx**.

7. Cliquez sur l’entrée **sample/nginx** et vérifiez la présence de l’étiquette **v1** qui identifie la version de l’image.

8. Cliquez sur l’entrée **v1** pour afficher le manifeste de l’image.

    >**Remarque** : le manifeste inclut la synthèse sha256, la date de création du manifeste et des entrées de plateforme. 

#### <a name="task-3-create-an-azure-kubernetes-service-cluster"></a>Tâche 3 : Créer un cluster Azure Kubernetes Service

Dans cette tâche, vous allez créer un service Azure Kubernetes et examiner les ressources déployées. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Services Kubernetes**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Services Kubernetes**, cliquez sur **+ Créer**, puis, dans le menu déroulant, cliquez sur **+ Créer un cluster Kubernetes**.

3. Sous l’onglet **Options de base** du volet **Créer un cluster Kubernetes**, sélectionnez **Configuration prédéfinie du cluster**,puis **Dev/Test ($)** . Spécifiez maintenant les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Value|
    |----|----|
    |Abonnement|Nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Groupe de ressources|**AZ500LAB09**|
    |Nom du cluster Kubernetes|**MyKubernetesCluster**|
    |Région|**(États-Unis) USA Est**|
    |Zones de disponibilité |**Aucun**|
    |Méthode de mise à l’échelle|**Manuel**|
    |Nombre de nœuds|**1**|

4. Cliquez sur **Suivant : Pools de noeuds >** , puis sous l’onglet **Pools de noeuds** du volet **Créer un cluster Kubernetes**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Value|
    |----|----|
    |Activer les nœuds virtuels|Case à cocher désactivée|
    
5. Cliquez sur **Suivant : Accès >** , puis, sous l’onglet **Accès** du volet **Créer un cluster Kubernetes**, acceptez les valeurs par défaut et cliquez sur **Suivant : Mise en réseau >** . 

6. Sous l’onglet **Mise en réseau** du volet **Créer un cluster Kubernetes**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Value|
    |----|----|
    |Configuration réseau|**Azure CNI**|
    |Préfixe du nom DNS|**Conservez la valeur par défaut**|

    >**Remarque** : AKS peut être configuré comme un cluster privé. Cela a pour effet d’attribuer une adresse IP privée au serveur d’API pour garantir que le trafic réseau entre votre serveur d’API et vos pools de nœuds reste uniquement sur le réseau privé. Pour plus d’informations, consultez [Créer un cluster Azure Kubernetes Service privé](https://docs.microsoft.com/en-us/azure/aks/private-clusters).

7. Cliquez sur **Suivant : Intégrations >** , puis, sous l’onglet **Intégrations** du volet **Créer un cluster Kubernetes**, définissez la **Surveillance du conteneur** sur **Désactivée**. 

    >**Remarque** : dans les scénarios de production, il est préférable d’activer la surveillance. En l’occurrence, la surveillance est désactivée parce qu’elle n’est pas couverte dans le labo. 

8. Cliquez sur **Vérifier + créer**, puis cliquez sur **Créer**.

    >**Remarque** : Attendez la fin du déploiement. Ceci peut prendre environ 10 minutes.

9. Une fois le déploiement terminé, dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Groupes de ressources**, puis appuyez sur la touche **Entrée**.

10. Dans le volet **Groupes de ressources**, dans la liste des groupes de ressources, notez la présence d’un nouveau groupe de ressources nommé **MC_AZ500LAB09_MyKubernetesCluster_eastus** qui contient des composants des nœuds AKS. Examinez les ressources dans ce groupe de ressources. 
    
11. Revenez au volet **Groupes de ressources**, puis cliquez sur l’entrée **AZ500LAB09**. 

    >**Remarque** : dans la liste des ressources, notez le cluster AKS et le réseau virtuel correspondant.

12. Dans le portail Azure, ouvrez une session Bash dans le Cloud Shell. 

    >**Remarque** : Vérifiez que **Bash** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

13. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de vous connecter au cluster Kubernetes :

    ```sh
    az aks get-credentials --resource-group AZ500LAB09 --name MyKubernetesCluster
    ```

14. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de répertorier les nœuds du cluster Kubernetes : 

    ```sh
    kubectl get nodes
    ```

    >**Remarque** : vérifiez que l’**État** du nœud de cluster est **Prêt**.

#### <a name="task-4-grant-the-aks-cluster-permissions-to-access-the-acr-and-manage-its-virtual-network"></a>Tâche 4 : Accorder des autorisations de cluster AKS pour accéder à l’ACR et gérer son réseau virtuel

Dans cette tâche, vous allez accorder des autorisations de cluster AKS pour accéder à l’ACR et gérer son réseau virtuel 

1. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de configurer le cluster AKS pour utiliser l’instance Azure Container Registry que vous avez créée précédemment dans ce labo. 

    ```sh
    ACRNAME=$(az acr list --resource-group AZ500LAB09 --query '[].{Name:name}' --output tsv)

    az aks update -n MyKubernetesCluster -g AZ500LAB09 --attach-acr $ACRNAME
    ```

    >**Remarque** : cette commande accorde l’attribution de rôle « acrpull » à l’ACR. 

    >**Remarque** : L’exécution de cette commande peut prendre plusieurs minutes. 

2. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin d’octroyer au cluster AKS le rôle Contributeur dans son réseau virtuel. 

    ```sh
    RG_AKS=AZ500LAB09
    
    AKS_VNET_NAME=AZ500LAB09-vnet
    
    AKS_CLUSTER_NAME=MyKubernetesCluster
    
    AKS_VNET_ID=$(az network vnet show --name $AKS_VNET_NAME --resource-group $RG_AKS --query id -o tsv)
    
    AKS_MANAGED_ID=$(az aks show --name $AKS_CLUSTER_NAME --resource-group $RG_AKS --query identity.principalId -o tsv)
    
    az role assignment create --assignee $AKS_MANAGED_ID --role "Contributor" --scope $AKS_VNET_ID
    ```

#### <a name="task-5-deploy-an-external-service-to-aks"></a>Tâche 5 : Déployer un service externe sur AKS

Dans cette tâche, vous allez télécharger les fichiers manifestes, modifier le fichier YAML et appliquer vos modifications au cluster. 

1. Dans la session Bash dans le volet Cloud Shell, cliquez sur l’icône **Charger/Télécharger des fichiers**. Dans le menu déroulant, cliquez sur **Charger**. Dans la boîte de dialogue **Ouvrir**, accédez à l’emplacement où vous avez téléchargé les fichiers du labo, sélectionnez **\\Allfiles\\Labs\\09\\nginxexternal.yaml**, puis cliquez sur **Ouvrir**. Ensuite, sélectionnez **\\Allfiles\\Labs\\09\\nginxinternal.yaml**, puis cliquez sur **Ouvrir**.

2. Dans la session Bash du volet Cloud Shell, exécutez la commande suivante afin d’identifier le nom de votre instance Azure Container Registry :

    ```sh
    echo $ACRNAME
    ```

    >**Remarque** : enregistrez le nom de l’instance Azure Container Registry. Vous en aurez besoin plus tard dans cette tâche.

3. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin d’ouvrir le fichier nginxexternal.yaml pour pouvoir modifier son contenu. 

    ```sh
    code ./nginxexternal.yaml
    ```

    >**Remarque** : il s’agit du fichier yaml *externe*.

4. Dans le volet de l’éditeur, faites défiler jusqu’à la **ligne 24** et remplacez l’espace réservé **`<ACRUniquename>`** par le nom de l’ACR.

5. Dans le volet de l’éditeur, dans l’angle supérieur droit, cliquez sur l’icône **Points de suspension**, sur **Enregistrer**, puis sur **Fermer l’éditeur**. 

6. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin d’appliquer la modification au cluster :

    ```sh
    kubectl apply -f nginxexternal.yaml
    ```

7. Dans la session Bash dans le volet Cloud Shell, examinez la sortie de la commande que vous avez exécutée dans la tâche précédente afin de vérifier que le déploiement et le service correspondant ont été créés. 

    ```
    deployment.apps/nginxexternal created
    service/nginxexternal created
    ```

#### <a name="task-6-verify-the-you-can-access-an-external-aks-hosted-service"></a>Tâche 6 : Vérifier que vous pouvez accéder à un service externe hébergé par AKS

Dans cette tâche, vérifiez que le conteneur est accessible en externe à l’aide de l’adresse IP publique.

1. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de récupérer des informations sur le service nginxexternal, dont le nom, le type, les adresses IP et les ports. 

    ```sh
    kubectl get service nginxexternal
    ```

2. Dans la session Bash dans le volet Cloud Shell, examinez la sortie et enregistrez la valeur dans la colonne External-IP. Vous en aurez besoin à l’étape suivante. 

3. Ouvrez un nouvel onglet de navigateur et accédez à l’adresse IP que vous avez identifiée à l’étape précédente.

4. Vérifiez que la page **Bienvenue dans nginx** s’affiche. 

#### <a name="task-7-deploy-an-internal-service-to-aks"></a>Tâche 7 : Déployer un service interne sur AKS

Dans cette tâche, vous allez déployer le service interne sur l’AKS. 

1. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin d’ouvrir le fichier nginxinternal.yaml pour pouvoir modifier son contenu. 

    ```sh
    code ./nginxinternal.yaml
    ```

    >**Remarque** : il s’agit du fichier yaml *interne*.

2. Dans le volet de l’éditeur, faites défiler jusqu’à la ligne contenant la référence à l’image conteneur, puis remplacez l’espace réservé **`<ACRUniquename>`** par le nom de l’ACR.

3. Dans le volet de l’éditeur, dans l’angle supérieur droit, cliquez sur l’icône **Points de suspension**, sur **Enregistrer**, puis sur **Fermer l’éditeur**. 

4. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin d’appliquer la modification au cluster :

    ```sh
    kubectl apply -f nginxinternal.yaml
    ```

5.  Dans la session Bash dans le volet Cloud Shell, examinez la sortie afin de vérifier que votre déploiement et le service ont été créés :

    ```
    deployment.apps/nginxinternal created
    service/nginxinternal created
    ```

6. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de récupérer des informations sur le service nginxinternal, dont le nom, le type, les adresses IP et les ports. 

    ```sh
    kubectl get service nginxinternal
    ```

7. Dans la session Bash dans le volet Cloud Shell, examinez la sortie. Dans ce cas, l’External-IP, est une adresse IP privée. Si la commande précédente est dans l’état **En attente**, réexécutez-la.

    >**Remarque** : Notez-la. Vous en aurez besoin dans la prochaine tâche. 

    >**Remarque** : pour accéder au point de terminaison de service interne, vous allez vous connecter de manière interactive à l’un des pods s’exécutant dans le cluster. 

    >**Remarque** : vous pouvez également utiliser l’adresse CLUSTER-IP.

#### <a name="task-8-verify-the-you-can-access-an-internal-aks-hosted-service"></a>Tâche 8 : Vérifier que vous pouvez accéder à un service interne hébergé par AKS

Dans cette tâche, vous allez utiliser l’un des pods s’exécutant sur le cluster AKS pour accéder au service interne. 

1. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de répertorier les pods dans l’espace de noms par défaut sur le cluster AKS :

    ```sh
    kubectl get pods
    ```

2. Dans la liste des pods, copiez la première entrée dans la colonne **NAME**.

    >**Remarque** : il s’agit du pod que vous allez utiliser dans les étapes suivantes.

3. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante afin de vous connecter de manière interactive au premier pod (remplacez l’espace réservé `<pod_name>` par le nom que vous avez copié à l’étape précédente) :

    ```sh
    kubectl exec -it <pod_name> -- /bin/bash
    ```

4. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour vérifier que le site web nginx est disponible via l’adresse IP privée du service (remplacez l’espace réservé `<internal_IP>` par l’adresse IP que vous avez enregistrée dans la tâche précédente) :

    ```sh
    curl http://<internal_IP>
    ```

5. Fermez le volet Cloud Shell.

> Résultat : Vous avez configuré et sécurisé un ACR et un AKS.


**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus.

1. Dans le portail Azure, ouvrez le Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. 

2. Dans le menu déroulant en haut à gauche du volet Cloud Shell, sélectionnez **PowerShell**, puis, lorsque vous y êtes invité, cliquez sur **Confirmer**.

3. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour supprimer les groupes de ressources que vous avez créés dans ce labo :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB09" -Force -AsJob
    ```

4.  Fermez le volet **Cloud Shell**. 

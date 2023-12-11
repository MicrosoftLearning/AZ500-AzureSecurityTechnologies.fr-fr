---
lab:
  title: 04 – Verrous Resource Manager
  module: Module 01 - Manage Identity and Access
---

# Labo 4 : Verrous Resource Manager
# Manuel de labo de l’étudiant

## Scénario du labo 

Vous avez été invité à créer une preuve de concept montrant comment les verrous de ressources peuvent être utilisés pour empêcher la suppression accidentelle ou les modifications. Plus précisément, vous devez :

- Créer un verrou ReadOnly

- Créer un verrou Supprimer

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 
 
## Objectifs du labo

Dans ce labo, vous allez effectuer l’exercice suivant :

- Exercice 1 : Verrous Resource Manager

## Schéma des verrous Resource Manager

![image](https://user-images.githubusercontent.com/91347931/157514986-1bf6a9ea-4c7f-4487-bcd7-542648f8dc95.png)

## Instructions

### Exercice 1 : Verrous Resource Manager

#### Durée estimée : 20 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créer un groupe de ressources avec un compte de stockage.
- Tâche 2 : Ajouter un verrou en lecture seule sur le compte de stockage. 
- Tâche 3 : Tester le verrou ReadOnly. 
- Tâche 4 : Supprimer le verrou ReadOnly et créer un verrou Supprimer.
- Tâche 5 : Tester le verrou Supprimer.

#### Tâche 1 : Créer un groupe de ressources avec un compte de stockage.

Dans cette tâche, vous allez créer un groupe de ressources et un compte de stockage pour le labo. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au Portail Azure à l’aide d’un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce laboratoire.

1. Ouvrez Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, sélectionnez **PowerShell**, puis **Créer un stockage**.

1. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

1. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour créer un groupe de ressources (vérifiez avec votre instructeur la valeur du paramètre d’emplacement) :

    ```powershell
    New-AzResourceGroup -Name AZ500LAB03 -Location 'EastUS'
    
    Confirm
    Provided resource group already exists. Are you sure you want to update it?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"): Y
    ```
1. Dans la session PowerShell du volet Cloud Shell, tapez **Y**, puis appuyez sur la touche Entrée.

1. Dans le volet Cloud Shell de la session PowerShell, exécutez la commande suivante pour créer un compte de stockage dans le nouveau groupe de ressources :
    
    ```powershell
    New-AzStorageAccount -ResourceGroupName AZ500LAB03 -Name (Get-Random -Maximum 999999999999999) -Location  EastUS -SkuName Standard_LRS -Kind StorageV2 
    ```

   >**Remarque** :  attendez que le compte de stockage soit créé. Cela peut prendre quelques minutes. 

1. Fermez le volet Cloud Shell.

#### Tâche 2 : Ajouter un verrou en lecture seule sur le compte de stockage. 

Dans cette tâche, vous allez ajouter un verrou en lecture seule au compte de stockage. Cela protégera la ressource contre toute suppression ou modification accidentelle. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Groupes de ressources**, puis appuyez sur la touche **Entrée**.

1. Dans le volet **Groupes de ressources**, sélectionnez l’entrée du groupe de ressources **AZ500LAB03**.

1. Dans le volet du groupe de ressources **AZ500LAB03**, dans la liste des ressources, sélectionnez le nouveau compte de stockage. 

1. Dans la section **Paramètres**, cliquez sur l’icône « Verrous ».

1. Cliquez sur **+Ajouter** et spécifiez les paramètres suivants :

   |Paramètre|Value|
   |---|---|
   |Nom du verrou|**Verrou ReadOnly**|
   |Type de verrou|**Lecture seule**|

1. Cliquez sur **OK**. 

   >**Remarque** :  le compte de stockage est désormais protégé contre la suppression et la modification accidentelles.

#### Tâche 3 : Tester le verrou ReadOnly 

1. Dans la section **Paramètres du panneau** du compte de stockage, cliquez sur **Configuration**.

1. Définissez l’option **Transfert sécurisé obligatoire** sur **Désactivé**, puis cliquez sur **Enregistrer**.

1. Vous devriez être en mesure de voir une notification indiquant que **Le compte de compte de stockage n’a pas pu être mis à jour**.

1. Cliquez sur l’icône **Notifications** dans la barre d’outils en haut du portail Azure et examinez la notification, qui doit ressembler à ce qui suit : 

    > **« Échec de la mise à jour du compte de stockage 'xxxxxxxx'. Erreur : L’étendue 'xxxxxxxx' ne peut pas effectuer l’opération d'écriture car les étendues suivantes sont verrouillées : '/subscriptions/xxxxx-xxx-xxxx-xxxx-xxxxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'. Retirez le verrou et réessayez »**

1. Revenez dans le volet **Configuration** du compte de stockage, puis cliquez sur **Ignorer**. 

1. Dans le volet du compte de stockage, sélectionnez **Vue d’ensemble** et, dans le volet **Vue d’ensemble**, cliquez sur **Supprimer**.

1. Dans le volet **Supprimer le compte de stockage**, tapez le nom du compte de stockage pour confirmer que vous avez l’intention de continuer, puis cliquez sur **Supprimer**.

1. Passez en revue la notification nouvellement générée, qui ressemblera au texte suivant : 

    > **« Échec de la suppression du compte de stockage 'xxxxxxxx'. Erreur : L’étendue 'xxxxxxxx' ne peut pas effectuer l’opération de suppression, car les étendues suivantes sont verrouillées : '/subscriptions/xxxx-xxxx-xxxx-xxxx-xxxxxx/resourceGroups/AZ500LAB03/providers/Microsoft.Storage/storageAccounts/xxxxxxx'. Retirez le verrou et réessayez »**

   >**Remarque** :  vous avez maintenant vérifié qu’un verrou ReadOnly arrête la suppression accidentelle et la modification d’une ressource.

#### Tâche 4 : Supprimer le verrou ReadOnly et créer un verrou Supprimer.

Dans cette tâche, vous supprimez le verrou ReadOnly du compte de stockage et créez un verrou Supprimer. 

1. Dans le Portail Azure, revenez au volet affichant les propriétés du compte de stockage nouvellement créé.

1. Dans la section **Paramètres**, sélectionnez **Verrous**.  

1. Dans le volet **Verrous**, cliquez sur l’icône **Supprimer** à droite de l’entrée **ReadOnly Lock**.

1. Cliquez sur **+Ajouter** et spécifiez les paramètres suivants :

   |Paramètre|Value|
   |---|---|
   |Nom du verrou|**Verrou Supprimer**|
   |Type de verrou|**Supprimer**|

1. Cliquez sur **OK**. 

#### Tâche 5 : Tester le verrou Supprimer.

Dans cette tâche, vous allez tester le verrou Supprimer. Vous devez pouvoir modifier le compte de stockage, mais pas le supprimer. 

1. Dans la section **Paramètres du panneau** du compte de stockage, cliquez sur **Configuration**.

1. Définissez l’option **Transfert sécurisé obligatoire** sur **Désactivé**, puis cliquez sur **Enregistrer**.

   >**Remarque** :  cette fois, la modification devrait réussir.

1. Dans le volet du compte de stockage, sélectionnez **Vue d’ensemble** et, dans le volet **Vue d’ensemble**, cliquez sur **Supprimer**.

1. Passez en revue la notification qui ressemble au texte suivant : 

    > **« Impossible de supprimer 'xxxxxx', car cette ressource ou son parent comporte un verrou Supprimer. Les verrous doivent être supprimés avant de pouvoir supprimer cette ressource »**

   >**Remarque** :  vous avez maintenant vérifié qu’un verrou **Supprimer** autorise les modifications de configuration, mais arrête la suppression accidentelle.

   >**Remarque** :  en utilisant les verrous de ressources, vous pouvez implémenter une ligne de défense supplémentaire contre les modifications accidentelles ou malveillantes et/ou la suppression des ressources les plus importantes. Les verrous de ressources peuvent être supprimés par n’importe quel utilisateur avec le rôle **Propriétaire**, mais cela nécessite un effort conscient. Les verrous viennent en complément du contrôle d’accès basé sur les rôles. 

> Résultats : dans cet exercice, vous avez appris à utiliser des verrous Resource Manager pour protéger les ressources contre la modification et la suppression accidentelle.

**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus.

1. Dans le portail Azure, ouvrez Cloud Shell en cliquant sur la première icône située en haut à droite du portail Azure. Si vous y êtes invité, cliquez sur **Se connecter**.

1. Dans le volet Cloud Shell, dans la session PowerShell, exécutez la commande suivante pour supprimer le verrou Supprimer :

    ```powershell
    $storageaccountname = (Get-AzStorageAccount -ResourceGroupName AZ500LAB03).StorageAccountName

    $lockName = (Get-AzResourceLock -ResourceGroupName AZ500LAB03 -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).Name

    Remove-AzResourceLock -LockName $lockName -ResourceName $storageAccountName  -ResourceGroupName AZ500LAB03 -ResourceType Microsoft.Storage/storageAccounts -Force
    ```

1.  Dans le volet Cloud Shell, dans la session PowerShell, exécutez la commande suivante pour supprimer le groupe de ressources :

    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB03" -Force -AsJob
    ```

1.  Fermez le volet **Cloud Shell**. 

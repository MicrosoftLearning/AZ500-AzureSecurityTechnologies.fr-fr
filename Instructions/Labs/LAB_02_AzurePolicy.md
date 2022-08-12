---
lab:
  title: 02 - Azure Policy
  module: Module 01 - Manage Identity and Access
ms.openlocfilehash: d49ce05e4620310d45317fe582bddb3aa511430b
ms.sourcegitcommit: 967cb50981ef07d731dd7548845a38385b3fb7fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/31/2022
ms.locfileid: "145955388"
---
# <a name="lab-02-azure-policy"></a>Lab 02 : Azure Policy
# <a name="student-lab-manual"></a>Manuel de labo de l’étudiant

## <a name="lab-scenario"></a>Scénario du labo

Vous avez été invité à créer une preuve de concept montrant comment Azure Policy peut être utilisé. Plus précisément, vous devez :

- Créer une stratégie Emplacements autorisés qui garantit que des ressources ne sont créées que dans une région spécifique.
- Tester pour vérifier que des ressources ne sont créées que dans l’emplacement autorisé

> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit de la région à utiliser pour la classe. 

## <a name="lab-objectives"></a>Objectifs du labo

Dans ce labo, vous allez effectuer les opérations suivantes :

- Exercice 1 : Implémenter Azure Policy. 

## <a name="azure-policy-diagram"></a>Diagramme Azure Policy

![image](https://user-images.githubusercontent.com/91347931/157511920-19c1f06c-86bd-440d-80ac-d96aa27aefff.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-implement-azure-policy"></a>Exercice 1 : Implémenter Azure Policy

#### <a name="estimated-timing-20-minutes"></a>Durée estimée : 20 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créer un groupe de ressources Azure. 
- Tâche 2 : Créer une attribution de stratégie Emplacements autorisés.
- Tâche 3 : Vérifier le bon fonctionnement de l’attribution de stratégie Emplacements autorisés. 

#### <a name="task-1-create-a-resource-group-for-the-lab"></a>Tâche 1 : Créer un groupe de ressources pour le labo. 

Dans cette tâche, vous allez créer un groupe de ressources pour le labo. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo.

1. Ouvrez Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, sélectionnez **PowerShell**, puis **Créer un stockage**.

1. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

1. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour créer un groupe de ressources (vérifiez avec votre instructeur la valeur du paramètre d’emplacement) :

    ```powershell
    New-AzResourceGroup -Name AZ500LAB02 -Location 'East US'
    ```

1. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour répertorier les groupes de ressources afin de vérifier que le nouveau groupe de ressources a été créé :

    ```powershell
    Get-AzResourceGroup | format-table
    ```

1. Fermez le **Cloud Shell**.

#### <a name="task-2-create-an-allowed-locations-policy-assignment"></a>Tâche 2 : Créer une attribution de stratégie Emplacements autorisés.

Dans cette tâche, vous allez créer une attribution de stratégie Emplacements autorisés et spécifier les régions Azure que la stratégie peut utiliser. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Stratégie**, puis appuyez sur la touche **Entrée**.

1. Dans le volet **Stratégie**, dans la section **Création**, sélectionnez **Définitions**.

1. Prenez une minute pour parcourir les définitions intégrées. Utilisez la liste déroulante **Catégorie** pour filtrer la liste des stratégies.

1. Dans la zone de texte **Rechercher**, tapez **Emplacements autorisés**. 

   >**Remarque** : la stratégie **Emplacements autorisés** vous permet de restreindre l’emplacement de ressources, non de groupes de ressources. Pour restreindre les emplacements de groupes de ressources, vous pouvez utiliser la stratégie **Emplacements autorisés pour les groupes de ressources**.

1. Cliquez sur la définition de stratégie **Emplacements autorisés** pour afficher ses détails. 

   >**Remarque** : cette définition de stratégie prend un tableau d’emplacements en guise de paramètres. Une règle de stratégie est une instruction « if-then ». La clause « if » vérifie si l’emplacement de la ressource figure dans la liste des paramètres et, si ce n’est pas le cas, la clause « then » refuse la création de la ressource ou, pour des ressources existantes, marque celles-ci comme non conformes.

1. Dans le volet **Emplacements autorisés**, cliquez sur **Attribuer**.

1. Sous l’onglet **Options de base** du volet **Emplacements autorisés**, cliquez sur le bouton de sélection (...) affiché en regard de la zone de texte **Étendue**, puis, dans le volet **Étendue**, spécifiez les paramètres suivants :

   |Paramètre|Value|
   |---|---|
   |Abonnement|nom de votre abonnement Azure|
   |Groupe de ressources|**AZ500LAB02**|

1. Cliquez sur **Sélectionner**.

1. Dans le volet **Emplacements autorisés**, sous l’onglet **Options de base**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

   |Paramètre|Value|
   |---|---|
   |Nom de l’attribution|**Autoriser Royaume-Uni Sud pour AZ500LAB02**|
   |Description|**Autoriser la création de ressources dans la région Royaume-Uni Sud uniquement pour AZ500LAB02**|
   |Application de stratégies|**Enabled**|

1. Cliquez sur **Suivant**.

1. Sous l’onglet **Paramètres** du volet **Emplacements autorisés**, dans la liste déroulante **Emplacements autorisés**, sélectionnez **Royaume-Uni Sud** comme seul emplacement autorisé. 

   >**Remarque** : Vous pouvez sélectionner plusieurs emplacements. Si la stratégie nécessitait un autre ensemble de paramètres, cet onglet fournirait ces sélections. 

1. Cliquez sur **Vérifier + créer**, puis sur **Créer** pour créer l’attribution de stratégie. 

   >**Remarque** : vous verrez s’afficher une notification indiquant que l’attribution a réussi et qu’elle pourrait prendre environ 30 minutes.

   >**Remarque** : la raison pour laquelle l’attribution de stratégie Azure pourrait prendre jusqu’à 30 minutes est qu’elle doit être répliquée globalement. En règle générale, cela ne prend que quelques minutes.  Si la tâche suivante échoue, patientez simplement quelques minutes, puis réessayez d’effectuer ses étapes.

#### <a name="task-3-test-the-allowed-locations-policy-assignment"></a>Tâche 3 : Tester l’attribution de stratégie Emplacements autorisés

Dans cette tâche, vous allez tester l’attribution de stratégie Emplacements autorisés. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Réseaux virtuels**, puis appuyez sur la touche **Entrée**.

1. Dans le volet **Réseaux virtuels**, cliquez sur **+ Créer**.

   >**Remarque** : tout d’abord, vous allez essayer de créer un réseau virtuel dans la région USA Est. Étant donné qu’il ne s’agit pas d’un emplacement autorisé, la demande devrait être bloquée. 

1. Dans le volet **Créer un réseau virtuel**, sous l’onglet **Options de base**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Valeur|
    |---|---|
    |Groupe de ressources|**AZ500LAB02**|
    |Nom|**myVnet**|
    |Région|**(États-Unis) USA Est**|

1. Cliquez sur **Vérifier + créer**. 

1. Dans le volet **Créer un réseau virtuel**, sous l’onglet **Vérifier + créer**, notez le message **Échec de validation**. 

    > **Remarque** : si l’avertissement **Échec de validation** n’apparaît pas, cliquez sur **Précédent**, puis patientez quelques minutes supplémentaires.

1. Sous l’onglet **Options de base**, cliquez sur le lien du message d’erreur pour ouvrir le volet **Attribution de stratégie**. Vous verrez l’attribution de stratégie qui restreint l’emplacement.

1. Fermez le volet **Attribution de stratégie**. Dans le volet **Créer un réseau virtuel**, cliquez sur l’onglet **Options de base**, puis, dans la liste déroulante **Région**, sélectionnez **(Europe) Royaume-Uni Sud**.

1. Cliquez sur **Vérifier + créer**, vérifiez que la validation a réussi, cliquez sur **Créer**, puis vérifiez que la création du réseau virtuel a réussi. 

> Résultats de l’exercice : Dans cet exercice, vous avez appris à appliquer une stratégie Azure en sélectionnant une définition de stratégie intégrée et en attribuant celle-ci à un groupe de ressources.

**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’exposerez pas de coûts imprévus.

1. Dans le portail Azure, ouvrez le Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, cliquez sur **Se connecter**.

1. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour supprimer le groupe de ressources que vous avez créé dans ce labo :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB02" -Force -AsJob
    ```
1.  Fermez le volet **Cloud Shell**. 
  
1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Stratégie**, puis appuyez sur la touche **Entrée**.

1. Dans la section Création, sélectionnez **Attributions**.

1. Dans la liste des attributions, sélectionnez le nom de la stratégie **Emplacements autorisés** que vous avez créée dans ce labo.

1. Dans l’attribution de stratégie, sélectionnez **Supprimer l’attribution**, puis sélectionnez **Oui**.

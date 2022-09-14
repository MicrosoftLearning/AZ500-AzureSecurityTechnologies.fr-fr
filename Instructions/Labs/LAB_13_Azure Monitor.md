---
lab:
  title: 13 - Azure Monitor
  module: Module 04 - Manage security operations
ms.openlocfilehash: d7418287b895ccb5af66f01b499181b321e2bc36
ms.sourcegitcommit: 3c178de473f4f986a3a7ea1d03c9f5ce699a05a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/09/2022
ms.locfileid: "147871971"
---
# <a name="lab-13-azure-monitor"></a>Labo 13 : Azure Monitor
# <a name="student-lab-manual"></a>Manuel de labos pour étudiant

## <a name="lab-scenario"></a>Scénario du labo

Vous avez été invité à créer une preuve de concept de surveillance des performances des machines virtuelles. Plus précisément, vous souhaitez :

- Configurer une machine virtuelle de sorte que les données de télémétrie et les journaux puissent être collectés.
- Montrer quelles données de télémétrie et quels journaux peuvent être collectés.
- Montrer comment les données peuvent être utilisées et interrogées. 

> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit de la région à utiliser pour la classe. 

## <a name="lab-objectives"></a>Objectifs du labo

Dans ce labo, vous effectuerez les exercices suivants :

- Exercice 1 : Collecter des données à partir d’une machine virtuelle Azure avec Azure Monitor

## <a name="azure-monitor"></a>Azure Monitor

![image](https://user-images.githubusercontent.com/91347931/157536648-0a286514-a7e2-4058-9dea-e42da21eef76.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-collect-data-from-an-azure-virtual-machine-with-azure-monitor"></a>Exercice 1 : Collecter des données à partir d’une machine virtuelle Azure avec Azure Monitor

### <a name="exercise-timing-20-minutes"></a>Durée de l’exercice : 20 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes : 

- Tâche 1 : Déployer une machine virtuelle Azure 
- Tâche 2 : Créer un espace de travail Log Analytics
- Tâche 3 : Activer l’extension de machine virtuelle Log Analytics
- Tâche 4 : Collecter des données de performances et d’événement de machine virtuelle
- Tâche 5 : Afficher et interroger des données collectées 

#### <a name="task-1-deploy-an-azure-virtual-machine"></a>Tâche 1 : Déployer une machine virtuelle Azure

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo.

2. Ouvrez Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, sélectionnez **PowerShell**, puis **Créer un stockage**.

3. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

4. Dans la session PowerShell du volet Cloud Shell, exécutez la commande suivante pour supprimer un groupe de ressources que vous avez utilisé dans ce labo :
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Remarque** : ce groupe de ressources sera utilisé pour les labos 13, 14 et 15. 

5. Dans la session PowerShell du volet Cloud Shell, exécutez la commande suivante pour créer une nouvelle machine virtuelle Azure. 

    >**Attention** : la commande New-AzVm ne fonctionne pas dans la version 4.24 d’Azure CLI et Microsoft étudie actuellement comment résoudre ce problème.  Le travail autour de ce labo consiste à installer et à revenir à la version 4.23.0 d’Az.Compute, qui n’est pas affectée par ce problème.
   
    >**Instructions** : Retour à la version 4.23.0 d’Az.Compute 
  
   #### <a name="step-1-download-the-working-version-of-the-module-4230-into-your-cloud-shell-session"></a>Étape 1 : Télécharger la version de travail du module (4.23.0) dans votre session Cloud Shell 
   **Type** : Install-Module -Name Az.Compute -Force -RequiredVersion 4.23.0

   #### <a name="step-2-start-a-new-powershell-session-that-will-allow-the-azcompute-assembly-version-to-be-loaded"></a>Étape 2 : Démarrer une nouvelle session PowerShell qui permettra le chargement de la version de l’assembly Az.Compute 
   **Type** : pwsh

   #### <a name="step-3-verify-that-version-4230-is-loaded"></a>Étape 3 : Vérifier que la version 4.23.0 est chargée
   **Type** : Get-Module -Name Az.Compute
   
    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -OpenPorts 80,3389
    ```

6.  Lorsque vous êtes invité des informations d'identification:

    |Paramètre|Valeur|
    |---|---|
    |Utilisateur |**localadmin**|
    |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|

    >**Remarque** : Attendez la fin du déploiement. 

7. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour vérifier que la machine virtuelle nommée **myVM** a été créée et que son **ProvisioningState** est **Réussi**.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. Fermez le volet Cloud Shell. 

#### <a name="task-2-create-a-log-analytics-workspace"></a>Tâche 2 : Créer un espace de travail Log Analytics

Dans cette tâche, vous allez créer un espace de travail Log Analytics. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Espaces de travail Log Analytics**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Espaces de travail Log Analytics** , cliquez sur **+ Créer**.

3. Sous l’onglet **Informations de base** du volet **Créer un espace de travail Log Analytics**, spécifiez les paramètres suivants (laissez les valeurs par défaut des autres paramètres) :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Resource group|**AZ500LAB131415**|
    |Nom|n’importe quel nom unique valide et global|
    |Région|**(États-Unis) USA Est**|

4. Sélectionnez **Revoir + créer**.

5. Dans l’onglet **Vérifier + créer** du volet **Créer un espace de travail Log Analytics**, sélectionnez **Créer**.

#### <a name="task-3-enable-the-log-analytics-virtual-machine-extension"></a>Tâche 3 : Activer l’extension de machine virtuelle Log Analytics

Dans cette tâche, vous allez activer l’extension de machine virtuelle Log Analytics. Cette extension installe l’agent Log Analytics sur les machines virtuelles Windows et Linux. Cet agent collecte les données de la machine virtuelle et les transfère à l’espace de travail Log Analytics que vous désignez. Une fois l’agent installé, il est automatiquement mis à niveau pour vous assurer que vous disposez toujours des dernières fonctionnalités et correctifs. 

1. Dans le portail Azure, revenez au volet **Espaces de travail Log Analytics** et, dans la liste des espaces de travail, cliquez sur l’entrée représentant l’espace de travail que vous avez créé dans la tâche précédente.

2. Dans le panneau de l’espace de travail Log Analytics de la page  **Vue d’ensemble**, cliquez sur l’entrée **Machines virtuelles Azure** dans la section **Connecter une source de données**.

    >**Remarque** : pour que l’agent soit correctement installé, la machine virtuelle doit être en cours d’exécution.

3. Dans la liste des machines virtuelles, recherchez l’entrée représentant la machine virtuelle Azure **myVM** que vous avez déployée dans la première tâche de cet exercice et notez qu’elle est répertoriée comme **Non connectée**.

4. Cliquez sur l’entrée **myVM**, puis, dans le volet **myVM**, cliquez sur **Connecter**. 

5. Attendez que la machine virtuelle se connecte à l’espace de travail Log Analytics.

    >**Remarque** : Elle peut prendre quelques minutes. **L’état** affiché dans le volet **myVM** passera de **Connexion en cours** à **Cet espace de travail**. 

#### <a name="task-4-collect-virtual-machine-event-and-performance-data"></a>Tâche 4 : Collecter des données de performances et d’événement de machine virtuelle

Dans cette tâche, vous allez configurer la collection du journal système Windows et plusieurs compteurs de performances courants. Vous examinerez également d’autres sources disponibles.

1. Dans le portail Azure, revenez à l’espace de travail Log Analytics que vous avez créé précédemment dans cet exercice.

2. Dans le volet de l’espace de travail Log Analytics, dans la section **Paramètres**, cliquez sur **Gestion des agents hérités**.

3. Dans le volet **Configuration des agents**, passez en revue les paramètres configurables, notamment les journaux d’événements Windows, les compteurs de performances Windows, les compteurs de performances Linux, les journaux IIS et Syslog. 

4. Vérifiez que **Journal des événements Windows** est sélectionné, cliquez sur **+ Ajouter un journal des événements Windows**. Dans la liste des types de journaux d’événements, sélectionnez **Système**.

    >**Remarque** : il s’agit de la façon dont vous ajoutez des journaux d’événements à l’espace de travail. D’autres choix incluent, par exemple, des **événements matériels** ou le **Service de gestion de clés**.  

5. Décochez la case **Informations** , en laissant les cases **Erreur** et **Avertissement** cochées.

6. Cliquez sur **Compteurs de performances Windows**, cliquez sur **+ Ajouter un compteur de performances**, passez en revue la liste des compteurs de performances disponibles et ajoutez les éléments suivants :

    - Mémoire(\*)\Mo de mémoire disponible
    - Process(\*)\\% Processor Time
    - Suivi d’événements pour Windows\Utilisation totale de la mémoire --- Pool non paginé
    - Suivi d’événements pour Windows\Utilisation totale de la mémoire --- Pool paginé

    >**Remarque** : les compteurs sont ajoutés et configurés avec un intervalle d’échantillonnage de collecte de 60 secondes.
  
7. Dans le volet **Configuration des agents**, cliquez sur **Appliquer**.

#### <a name="task-5-view-and-query-collected-data"></a>Tâche 5 : Afficher et interroger des données collectées

Dans cette tâche, vous allez exécuter une recherche dans les journaux sur votre collecte de données. 

1. Dans le portail Azure, revenez à l’espace de travail Log Analytics que vous avez créé précédemment dans cet exercice.

2. Dans le volet de l’espace de travail Log Analytics, dans la section **Général**, cliquez sur **Journaux**.

3. Si nécessaire, fermez la fenêtre **Bienvenue dans l’analyse des journaux**. 

4. Dans le volet **Requêtes**, dans la colonne **Toutes les requêtes**, faites défiler vers le bas de la liste des types de ressources, puis cliquez sur **Machines virtuelles**
    
5. Passez en revue la liste des requêtes prédéfinies, sélectionnez **Utilisation de la mémoire et du processeur**, puis cliquez sur le bouton **Exécuter** correspondant.

    >**Remarque** : vous pouvez commencer par la **mémoire disponible de la machine virtuelle** de la requête. Si vous n’obtenez aucun résultat, vérifiez que l’étendue est définie sur la machine virtuelle

6. La requête s’ouvre automatiquement dans un nouvel onglet de requête. 

    >**Remarque** : Log Analytics utilise le langage de requête Kusto. Vous pouvez personnaliser les requêtes existantes ou créer les vôtres. 

    >**Remarque** : les résultats de la requête que vous avez sélectionnée s’affichent automatiquement sous le volet de requête. Pour réexécuter la requête, cliquez sur **Exécuter**.

    >**Remarque** : étant donné que cette machine virtuelle vient d’être créée, il se peut qu’il n’y ait pas encore de données. 

    >**Remarque** : vous avez la possibilité d’afficher des données dans différents formats. Vous avez également la possibilité de créer une règle d’alerte en fonction des résultats de la requête.

    >**Remarque** : vous pouvez générer une charge supplémentaire sur la machine virtuelle Azure que vous avez déployée précédemment dans ce laboratoire en suivant les étapes suivantes :

    1. Accédez au volet de la machine virtuelle Azure.
    2. Dans le volet de la machine virtuelle Azure, dans la section **Opérations**, sélectionnez **Exécuter la commande**, dans le volet **RunPowerShellScript**, tapez le script suivant, puis cliquez sur **Exécuter** :
    3. 
       ```cmd
       cmd
       :loop
       dir c:\ /s > SWAP
       goto loop
       ```
       
    4. Revenez au volet Log Analytics et réexécutez la requête. Vous devrez peut-être attendre quelques minutes pour que les données soient collectées et réexécuter la requête.

> Résultats : vous avez utilisé un espace de travail Log Analytics pour configurer les sources de données et les journaux de requête. 

**Nettoyer les ressources**

>**Remarque** : ne supprimez pas les ressources de ce labo, car elles sont nécessaires pour le labo Azure Security Center et le labo Azure Sentinel.
 

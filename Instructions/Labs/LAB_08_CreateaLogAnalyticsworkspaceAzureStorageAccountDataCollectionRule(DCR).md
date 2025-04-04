---
lab:
  title: '08 : créer un espace de travail Log Analytics, un compte de stockage Azure et une règle de collecte de données (DCR)'
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# Labo 08 : créer un espace de travail Log Analytics, un compte de stockage Azure et une règle de collecte de données (DCR)

# Manuel de labo de l’étudiant

## Scénario du labo

En tant qu’ingénieur sécurité Azure pour une entreprise de technologie financière, vous êtes chargé d’améliorer la surveillance et la visibilité de la sécurité sur toutes les machines virtuelles Azure utilisées pour traiter les transactions financières et gérer les données client sensibles. L’équipe de sécurité nécessite des journaux détaillés et des indicateurs de performances de ces machines virtuelles pour détecter les menaces potentielles et optimiser les performances du système. Le directeur de la sécurité de l’information (CISO) vous a demandé d’implémenter une solution qui collecte des événements de sécurité, des journaux système et des compteurs de performances. Vous avez été chargé de configurer l’agent Azure Monitor (AMA) ainsi que les règles de collecte de données (DCR) pour centraliser la collecte des journaux et le monitoring des performances.



> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous allez effectuer les exercices suivants :

- Exercice 1 : Déployer une machine virtuelle Azure
- Exercice 2 : Créer un espace de travail Log Analytics
- Exercice 3 : Créer un compte de stockage Azure
- Exercice 4 : créer une règle de collecte de données
  
## Instructions

### Exercice 1 : Déployer une machine virtuelle Azure

### Durée de l’exercice : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes : 

- Tâche 1 : Déployez une machine virtuelle Azure. 

#### Tâche 1 : Déployer une machine virtuelle Azure

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au Portail Azure à l’aide d’un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce laboratoire.

2. Ouvrez Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, sélectionnez **PowerShell**, puis **Créer un stockage**.

3. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

4. Dans la session PowerShell du volet Cloud Shell, exécutez la commande suivante pour supprimer un groupe de ressources que vous avez utilisé dans ce labo :
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Remarque** : ce groupe de ressources sera utilisé pour les labos 8, 9 et 10.

5. Dans le volet Cloud Shell, dans la session PowerShell, exécutez la commande suivante pour activer le chiffrement sur l’hôte (EAH).
   
   ```powershell
    Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace Microsoft.Compute 
    ```

5. Dans la session PowerShell du volet Cloud Shell, exécutez la commande suivante pour créer une nouvelle machine virtuelle Azure. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
    ```
    
6.  Lorsque vous êtes invité des informations d'identification:

    |Paramètre|Valeur|
    |---|---|
    |Utilisateur |**localadmin**|
    |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 02 > Exercice 2 > Tâche 1 > Étape 3.**|

    >**Remarque** : Attendez la fin du déploiement. 

7. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour vérifier que la machine virtuelle nommée **myVM** a été créée et que son **ProvisioningState** est **Réussi**.

    ```powershell
    Get-AzVM -Name 'myVM' -ResourceGroupName 'AZ500LAB131415' | Format-Table
    ```

8. Fermez le volet Cloud Shell. 

### Exercice 2 : Créer un espace de travail Log Analytics

### Durée de l’exercice : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes : 

- Tâche 1 : Créer un espace de travail Log Analytics.

#### Tâche 1 : Créer un espace de travail Log Analytics

Dans cette tâche, vous allez créer un espace de travail Log Analytics. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Espaces de travail Log Analytics**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Espaces de travail Log Analytics**, cliquez sur  **+ Créer**.

3. Sous l’onglet **Informations de base** du volet **Créer un espace de travail Log Analytics**, spécifiez les paramètres suivants (laissez les valeurs par défaut des autres paramètres) :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Resource group|**AZ500LAB131415**|
    |Nom|n’importe quel nom unique valide et global|
    |Région|**USA Est**|

4. Sélectionnez **Revoir + créer**.

5. Dans l’onglet **Vérifier + créer** du volet **Créer un espace de travail Log Analytics**, sélectionnez **Créer**.

### Exercice 3 : Créer un compte de stockage Azure

### Durée estimée : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créez un compte de stockage Azure.

#### Tâche 1 : Créer un compte de stockage Azure

Dans cette tâche, vous allez créer un compte de stockage.

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** située en haut de la page, tapez **Comptes de stockage**, puis appuyez sur la touche **Entrée**.

2. Dans le panneau **Comptes de stockage** du portail Azure, cliquez sur le bouton **+ Créer** pour créer un compte de stockage.

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/73eb9241-d642-455a-a1ff-b504670395c0)

3. Sous l’onglet **Options de base** du volet **Créer un compte de stockage**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Resource group|**AZ500LAB131415**|
    |Nom du compte de stockage|Nom global unique comprenant entre 3 et 24 caractères alphanumériques.|
    |Emplacement|**(États-Unis) EastUS**|
    |Performances|**Standard (compte v2 à usage général)**|
    |Redondance|**Stockage localement redondant (LRS)**|

4. Sous l’onglet **Informations de base** du panneau **Créer un compte de stockage**, cliquez sur **Vérifier**, attendez la fin du processus de validation, puis cliquez sur **Créer**.

     ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d443821c-2ddf-4794-87fa-bfc092980eba)

    >**Remarque** : attendez que le compte de stockage soit créé. Ce processus prend environ 2 minutes.

### Exercice 4 : créer une règle de collecte de données

### Durée estimée : 15 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créez une règle de collecte de données.

#### Tâche 1 : Créez une règle de collecte de données.

Dans cette tâche, vous allez créer une règle de collecte de données.

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Surveillance**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Paramètres**, cliquez sur **Règles de collecte de données**.

  ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d43e8f94-efb2-4255-9320-210c976fd45e)


3. Cliquez sur **+Créer** pour créer une règle de collecte de données.

4. Sous l’onglet **Informations de base** du volet **Créer une règle de collecte de données**, spécifiez les paramètres suivants :
  
    |Paramètre|Valeur|
    |---|---|
    |**Détails de la règle**|
    |Nom de la règle|**DCR1**|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Groupe de ressources|**AZ500LAB131415**|
    |Région|**USA Est**|
    |Type de plate-forme|**Windows**|
    |Point de terminaison de collecte de données|*Laisser vide*|

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/9b58c4ce-b7a8-4acf-8289-d95b270a6083)


4. Cliquez sur le bouton étiqueté **Suivant : Ressources >** pour continuer.
   
6. Sous l’onglet Ressources, sélectionnez **+ Ajouter des ressources** et cochez la case **Activer les points de terminaison de collecte de données**. Dans le modèle Sélectionner une étendue, cochez la case **AZ500LAB131415**, puis cliquez sur **Appliquer.**

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d4191115-11bc-43ec-9bee-e84b9b95a821)

10. Cliquez sur le bouton étiqueté **Suivant : Collecter et livrer >** pour continuer.

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8294d300-f910-4757-ad52-43c7594ac822)

11. Cliquez sur **+ Ajouter une source de données**, puis dans la page **Ajouter une source de données**, modifiez le menu déroulant **Type de source de données** pour afficher **Compteurs de performances**. Laissez les paramètres par défaut suivants :

    |Paramètre|Valeur|
    |---|---|
    |**Compteur de performances**|**Taux d’échantillonnage (secondes)**|
    |UC|60|
    |Mémoire|60|
    |Disque|60|
    |Réseau|60|

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/a24e44ad-1d10-4533-80e2-bae1b3f6564d)

11. Cliquez sur le bouton étiqueté **Suivant : Destination >** pour continuer.
  
12. Cliquez sur **+Ajouter une destination**, modifiez le menu déroulant **Type de destination** pour afficher **Journaux d’activité Azure Monitor**. Dans la fenêtre **Abonnement**, vérifiez que votre *abonnement* est affiché, puis modifiez le menu déroulant **Compte ou espace de noms** pour refléter votre espace de travail Log Analytics créé précédemment.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/481843f5-94c4-4a8f-bf51-a10d49130bf8)

11. Cliquez sur **Ajouter une source de données** en bas de la page.
    
    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/964091e7-bbbc-4ca8-8383-bb2871a1e7f0)

13. Cliquez sur **Vérifier + créer**.

    ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/50dd8407-a106-4540-9e14-ae40a3c04830)

14. Cliquez sur **Créer**.

> Résultats : Vous avez déployé une machine virtuelle Azure, un espace de travail Log Analytics, un compte de stockage Azure et une règle de collecte de données pour collecter les événements et les compteurs de performances à partir de machines virtuelles avec l’agent Azure Monitor.

>**Note** : ne supprimez pas les ressources de ce labo, car elles sont nécessaires pour le labo Microsoft Defender pour le cloud et le labo Microsoft Sentinel.
 

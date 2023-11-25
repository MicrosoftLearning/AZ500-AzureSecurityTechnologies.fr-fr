---
lab:
  title: 13 – Déployer une machine virtuelle Azure et un espace de travail Log Analytics
  module: Module 04 - Manage security operations
---

# Labo 13 : Déployer une machine virtuelle Azure et un espace de travail Log Analytics

# Manuel de labo de l’étudiant

## Scénario de l’exercice

Il vous a été demandé de créer une machine virtuelle Azure et un espace de travail Log Analytics.

- Déployer une machine virtuelle Azure.
- Créez un espace de travail Log Analytics.

> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous effectuerez les exercices suivants :

- Exercice 1 : Créer une machine virtuelle Azure
  
## Instructions

### Exercice 1 : Déployer une machine virtuelle Azure

### Durée de l’exercice : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes : 

- Tâche 1 : Déployer une machine virtuelle Azure 

#### Tâche 1 : Déployer une machine virtuelle Azure

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au Portail Azure à l’aide d’un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce laboratoire.

2. Ouvrez Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, sélectionnez **PowerShell**, puis **Créer un stockage**.

3. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

4. Dans la session PowerShell du volet Cloud Shell, exécutez la commande suivante pour supprimer un groupe de ressources que vous avez utilisé dans ce labo :
  
    ```powershell
    New-AzResourceGroup -Name AZ500LAB131415 -Location 'EastUS'
    ```

    >**Remarque** : ce groupe de ressources sera utilisé pour les labos 13, 14 et 15. 

5. Dans la session PowerShell du volet Cloud Shell, exécutez la commande suivante pour créer une nouvelle machine virtuelle Azure. 

    ```powershell
    New-AzVm -ResourceGroupName "AZ500LAB131415" -Name "myVM" -Location 'EastUS' -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName   "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress" -PublicIpSku Standard -OpenPorts 80,3389 -Size Standard_DS1_v2 
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

#### Tâche 2 : Créer un espace de travail Log Analytics

Dans cette tâche, vous allez créer un espace de travail Log Analytics. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Espaces de travail Log Analytics**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Espaces de travail Log Analytics**, cliquez sur  **+ Créer**.

3. Sous l’onglet **Informations de base** du volet **Créer un espace de travail Log Analytics**, spécifiez les paramètres suivants (laissez les valeurs par défaut des autres paramètres) :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Resource group|**AZ500LAB131415**|
    |Nom|n’importe quel nom unique valide et global|
    |Région|**USA Est**|

4. Sélectionnez **Revoir + créer**.

5. Dans l’onglet **Vérifier + créer** du volet **Créer un espace de travail Log Analytics**, sélectionnez **Créer**.

> Résultats : Vous avez déployé une machine virtuelle Azure et créé un espace de travail Log Analytics.

>**Remarque** : Ne supprimez pas les ressources de ce labo, car elles sont nécessaires pour le labo Microsoft Defender pour le cloud et le labo Microsoft Sentinel.
 

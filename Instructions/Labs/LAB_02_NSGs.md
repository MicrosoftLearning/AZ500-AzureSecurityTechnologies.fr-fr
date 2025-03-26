---
lab:
  title: 02 - Groupes de sécurité réseau et groupes de sécurité des applications
  module: Module 01 - Plan and implement security for virtual networks
---

# Lab 02 : Groupes de sécurité réseau et groupes de sécurité des applications
# Manuel de labo de l’étudiant

## Scénario du labo

Vous avez été invité à implémenter l’infrastructure de mise en réseau virtuelle de votre organisation et à la tester pour s’assurer qu’elle fonctionne correctement. En particulier :

- L’organisation dispose de deux groupes de serveurs : les serveurs web et les serveurs d’administration.
- Chaque groupe de serveurs doit se trouver dans son propre groupe de sécurité d’application. 
- Vous devez être en mesure de vous connecter en RDP aux serveurs d’administration, mais pas aux serveurs web.
- Les serveurs web doivent afficher la page web IIS lorsque vous y accédez à partir d’Internet. 
- Les règles de groupe de sécurité réseau doivent être utilisées pour contrôler l’accès réseau. 

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous allez effectuer les exercices suivants :

- Exercice 1 : Créer l’infrastructure réseau virtuelle
- Exercice 2 : Déployer les machines virtuelles et tester les filtres réseau

## Schéma des groupes de sécurité réseau et d’application

![image](https://user-images.githubusercontent.com/91347931/157526438-6da4f68b-db88-4931-a041-8474e66d3fe5.png)

## Instructions

### Exercice 1 : Créer l’infrastructure réseau virtuelle

### Durée estimée : 20 minutes

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser pour la classe. 

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créer un réseau virtuel avec un sous-réseau
- Tâche 2 : Créer deux groupes de sécurité d’application.
- Tâche 3 : Créer un groupe de sécurité réseau et associez-le au sous-réseau virtuel.
- Tâche 4 : Créez des règles de sécurité NSG entrantes pour tous les trafics vers des serveurs web et RDP vers les serveurs d’administration.

#### Tâche 1 :  Créer un réseau virtuel

Dans cette tâche, vous allez créer un réseau virtuel à utiliser avec les groupes de sécurité réseau et d’application. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Réseaux virtuels**, puis appuyez sur la touche **Entrée**.

3. Dans le volet **Réseaux virtuels**, cliquez sur **+Créer**.

4. Sous l’onglet **Options de base**, dans le volet **Créer un réseau virtuel**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres), puis cliquez sur **Suivant : Adresses IP** :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Resource group|cliquez sur **Créer** et tapez le nom **AZ500LAB07**|
    |Nom|**myVirtualNetwork**|
    |Région|**USA Est**|

5. Sous l’onglet **Adresses IP** du volet **Créer un réseau virtuel**, définissez l’**espace d’adressage IPv4** sur **10.0.0.0/16**. Si nécessaire, dans la colonne **Nom du sous-réseau**, cliquez sur **Par défaut**, puis, dans le volet **Modifier le sous-réseau**, spécifiez les paramètres suivants et cliquez sur **Enregistrer** :

    |Paramètre|Valeur|
    |---|---|
    |Nom du sous-réseau|**default**|
    |Plage d’adresses de sous-réseau|**10.0.0.0/24**|

6. De retour sous l’onglet **Adresses IP** du volet **Créer un réseau virtuel**, cliquez sur **Vérifier + créer**.

7. Sous l’onglet **Vérifier + créer** du volet **Créer un réseau virtuel**, cliquez sur **Créer**.

#### Tâche 2 :  Créer des groupes de sécurité d’application

Dans cette tâche, vous allez créer un groupe de sécurité d’application.

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents**, située en haut de la page, tapez **Groupes de sécurité d’application**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Groupes de sécurité d’application** , cliquez sur **+Créer**.

3. Sous l’onglet **Informations de base** du panneau **Créer un groupe de sécurité réseau**, spécifiez les paramètres suivants : 

    |Paramètre|Valeur|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |Nom|**myAsgWebServers**|
    |Région|**USA Est**|

    >**Remarque** : Ce groupe sera destiné aux serveurs web.

4. Cliquez sur **Vérifier + créer**, puis sur **Créer**.

5. Revenez au volet **Groupes de sécurité d’application**, puis cliquez sur **+Créer**.

6. Sous l’onglet **Informations de base** du panneau **Créer un groupe de sécurité réseau**, spécifiez les paramètres suivants : 

    |Paramètre|Valeur|
    |---|---|
    |Resource group|**AZ500LAB07**|
    |Nom|**myAsgMgmtServers**|
    |Région|**USA Est**|

    >**Remarque** : Ce groupe sera destiné aux serveurs d’administration.

7. Cliquez sur **Vérifier + créer**, puis sur **Créer**.

#### Tâche 3 :  Créez un groupe de sécurité réseau et associez-le au sous-réseau

Dans cette tâche, vous allez créer un groupe de sécurité de réseau. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Réseau virtuel**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Groupes de sécurité réseau**, cliquez sur **+Créer**.

3. Sous l’onglet **Options de base** du volet **Créer un groupe de sécurité réseau**, spécifiez les paramètres suivants : 

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Resource group|**AZ500LAB07**|
    |Nom|**myNsg**|
    |Région|**USA Est**|

4. Cliquez sur **Vérifier + créer**, puis sur **Créer**.

5. Dans le Portail Azure, revenez au volet **Groupes de sécurité réseau**, puis cliquez sur l’entrée **myNsg**.

6. Dans le volet **myNsg**, dans la section **Paramètres**, cliquez sur **Sous-réseaux**, puis sur **+Associer**. 

7. Dans le volet **Associer un sous-réseau** , spécifiez les paramètres suivants, puis cliquez sur **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel|**myVirtualNetwork**|
    |Subnet|**default**|

#### Tâche 4 : créez des règles de sécurité NSG entrantes pour tous les trafics vers des serveurs web et RDP vers les serveurs. 

1. Dans le volet **myNsg**, dans la section **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant**.

2. Examinez les règles de sécurité de trafic entrant, puis cliquez sur **+Ajouter**.

3. Dans le volet **Ajouter une règle de sécurité de trafic entrant**, spécifiez les paramètres suivants pour autoriser les ports TCP 80 et 443 dans le groupe de sécurité d’application **myAsgWebServers** (laissez toutes les autres valeurs avec leurs valeurs par défaut) : 

    |Paramètre|Valeur|
    |---|---|
    |Destination|Dans la liste déroulante, sélectionnez **Groupe de sécurité d’application**, puis cliquez sur **myAsgWebServers**|
    |Plages de ports de destination|**80,443**|
    |Protocol|**TCP**|
    |Priority|**100**|                                                    
    |Nom|**Allow-Web-All**|

4. Dans le volet **Ajouter une règle de sécurité de trafic entrant**, cliquez sur **Ajouter** pour créer une règle de trafic entrant. 

5. Dans le volet **myNsg**, dans la section **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant**, puis sur **+ Ajouter**.

6. Dans le volet **Ajouter une règle de sécurité de trafic entrant**, spécifiez les paramètres suivants pour autoriser le port RDP (TCP 3389) dans le groupe de sécurité d’application **myAsgWebServers** (laissez toutes les autres valeurs avec leurs valeurs par défaut) : 

    |Paramètre|Valeur|
    |---|---|
    |Destination|Dans la liste déroulante, sélectionnez **Groupe de sécurité d’application**, puis cliquez sur **myAsgWebServers**|
    |Plages de ports de destination|**3389**|
    |Protocol|**TCP**|
    |Priority|**110**|                                                    
    |Nom|**Allow-RDP-All**|

7. Dans le volet **Ajouter une règle de sécurité de trafic entrant**, cliquez sur **Ajouter** pour créer une règle de trafic entrant. 

> Résultat : vous avez déployé un réseau virtuel, une sécurité réseau avec des règles de sécurité entrantes et deux groupes de sécurité d’application. 

### Exercice 2 : Déployer les machines virtuelles et tester les filtres réseau

### Durée estimée : 25 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créez une machine virtuelle à utiliser comme serveur web.
- Tâche 2 : Créez une machine virtuelle à utiliser comme serveur d’administration. 
- Tâche 3 : Associez chaque interface réseau de machines virtuelles au groupe de sécurité d’application.
- Tâche 4 : Testez les règles de filtrage du trafic réseau.

#### Tâche 1 : Créez une machine virtuelle à utiliser comme serveur web.

Dans cette tâche, vous allez créer une machine virtuelle à utiliser comme serveur web.

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Machines virtuelles** et appuyez sur la touche **Entrée**.

2. Dans le volet **Machines virtuelles**, cliquez sur **+ Créer** et, dans la liste déroulante, cliquez sur **+ Machine virtuelle Azure**.

3. Sous l’onglet **Informations de base** du volet **Créer une machine virtuelle**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

   |Paramètre|Valeur|
   |---|---|
   |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
   |Resource group|**AZ500LAB07**|
   |Nom de la machine virtuelle|**myVmWeb**|
   |Région|**(États-Unis) USA Est**|
   |Options de disponibilité|**Aucune redondance de l’infrastructure requise**
   |Type de sécurité|**Standard**
   |Image|**Centre de données Windows Server 2022 : Édition Azure - x64 Gen2**|
   |Taille|**Standard D2s v3**|
   |Nom d’utilisateur|**Étudiant**|
   |Mot de passe|**Créez votre propre mot de passe et enregistrez-le pour l’utiliser lors de labos ultérieurs.**|
   |Confirmer le mot de passe|**Saisissez à nouveau votre mot de passe**|
   |Aucun port d’entrée public|**Aucun**|
   |Si vous voulez utiliser une licence Windows Server existante |**Non**|

    >**Remarque** : pour les ports d’entrée publics, nous allons nous appuyer sur le groupe de sécurité réseau (NSG) pré-créé. 

5. Cliquez sur **Suivant : Disques >** , puis, sous l’onglet **Disques** du volet **Créer une machine virtuelle**, définissez le **Type de disque du système d’exploitation** sur **HDD Standard** et cliquez sur **Suivant : Mise en réseau >** .

6. Sous l’onglet **Mise en réseau** du volet **Créer une machine virtuelle**, sélectionnez le réseau **myVirtualNetwork** créé précédemment.

7. Sous **Groupe de sécurité réseau de la carte réseau**, sélectionnez **Aucun**.

8. Cliquez sur **Suivant : Gestion >**, puis sur **Suivant : Surveillance >**. Sur l’onglet **Surveillance** du volet **Créer une machine virtuelle**, vérifiez le paramètre suivant :

   |Paramètre|Valeur|
   |---|---|
   |Diagnostics de démarrage|**Activer avec un compte de stockage managé (recommandé)**|

9. Cliquez sur **Vérifier + créer** dans le volet **Vérifier + créer** et vérifiez que la validation a réussi, puis cliquez sur **Créer**.

#### Tâche 2 : Créez une machine virtuelle à utiliser comme serveur d’administration. 

Dans cette tâche, vous allez créer une machine virtuelle à utiliser comme serveur d’administration.

1. Sur le portail Azure, revenez au volet **Machines virtuelles**, cliquez sur **+ Créer** et, dans la liste déroulante, cliquez sur **+ Machine virtuelle Azure**.

2. Sous l’onglet **Informations de base** du volet **Créer une machine virtuelle**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

   |Paramètre|Valeur|
   |---|---|
   |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
   |Resource group|**AZ500LAB07**|
   |Nom de la machine virtuelle|**myVMMgmt**|
   |Région|(États-Unis) USA Est|
   |Image|**Centre de données Windows Server 2022 : Édition Azure - x64 Gen2**|
   |Taille|**Standard D2s v3**|
   |Nom d’utilisateur|**Étudiant**|
   |Mot de passe|**Utilisez votre mot de passe personnel créé dans le Labo 02 > Exercice 2 > Tâche 1 > Étape 3.**|
   |Aucun port d’entrée public|**Aucun**|
   |Vous disposez déjà d’une licence Windows Server|**Non**|

    >**Remarque** : pour les ports d’entrée publics, nous allons nous appuyer sur le groupe de sécurité réseau (NSG) pré-créé. 

3. Cliquez sur **Suivant : Disques >** , puis, sous l’onglet **Disques** du volet **Créer une machine virtuelle**, définissez le **Type de disque du système d’exploitation** sur **HDD Standard** et cliquez sur **Suivant : Mise en réseau >** .

4. Sous l’onglet **Mise en réseau** du volet **Créer une machine virtuelle**, sélectionnez le réseau **myVirtualNetwork** créé précédemment.

5. Sous **Groupe de sécurité réseau de la carte réseau**, sélectionnez **Aucun**.

6. Cliquez sur **Suivant : Gestion >**, puis sur **Suivant : Surveillance >**. Sur l’onglet **Surveillance** du volet **Créer une machine virtuelle**, vérifiez le paramètre suivant :

   |Paramètre|Valeur|
   |---|---|
   |Diagnostics de démarrage|**Activer avec un compte de stockage managé (recommandé)**|

7. Cliquez sur **Vérifier + créer** dans le volet **Vérifier + créer** et vérifiez que la validation a réussi, puis cliquez sur **Créer**.

    >**Remarque** : Attendez que les deux machines virtuelles soient approvisionnées avant de continuer. 

#### Tâche 3 : Associez l’interface réseau de chaque machine virtuelle à son groupe de sécurité d’application.

Dans cette tâche, vous allez associer chaque interface réseau des machines virtuelles au groupe de sécurité d’application correspondant. L’interface de machine virtuelle myVMWeb est associée à l’ASG myAsgWebServers. L’interface de machine virtuelle myVMMgmt est associée à l’ASG myAsgMgmtServers. 

1. Dans le portail Azure, revenez au volet **Machines virtuelles** et vérifiez que les deux machines virtuelles sont répertoriées avec l’état **En cours d’exécution**.

2. Dans la liste des machines virtuelles, cliquez sur l’entrée **myVMWeb**.

3. Dans le volet **myVMWeb**, dans la section **Mise en réseau**, cliquez sur **Paramètres réseau**, puis, dans le volet **myVMWeb \| Paramètres de mise en réseau**, cliquez sur l’onglet **Groupes de sécurité d’application**.

4. Cliquez sur **+ Ajouter des groupes de sécurité d’application**. Dans la liste **Groupe de sécurité d’application**, sélectionnez **myAsgWebServers**, puis cliquez sur **Enregistrer**.

5. Revenez au volet **Machines virtuelles** et dans la liste des machines virtuelles, cliquez sur l’entrée **myVMMgmt**.

6. Dans le volet **myVMMgmt**, dans la section **Mise en réseau**, cliquez sur **Paramètres réseau**, puis, dans le volet **myVMMgmt \| Paramètres de mise en réseau**, cliquez sur l’onglet **Groupes de sécurité d’application**.

7. Cliquez sur **+ Ajouter des groupes de sécurité d’application**. Dans la liste **Groupe de sécurité d’application**, sélectionnez **myAsgMgmtServers**, puis cliquez sur **Enregistrer**.

#### Tâche 4 : Testez les règles de filtrage du trafic réseau

Dans cette tâche, vous allez tester les filtres de trafic réseau. Vous devez être en mesure vous connecter en RDP à la machine virtuelle myVMMgmnt. Vous devez pouvoir vous connecter à partir d’Internet à la machine virtuelle myVMWeb et voir la page web IIS par défaut.  

1. Revenez au volet la machine virtuelle **myVMMgmt**.

2. Dans le volet **myVMMgmt**, cliquez sur **Se connecter**, puis, dans le menu déroulant, cliquez sur **RDP**. 

3. Cliquez sur **Télécharger le fichier RDP**, puis utilisez-le pour vous connecter à la machine virtuelle Azure **myVMMgmt** via le Bureau à distance. Lorsque vous êtes invité à vous authentifier, fournissez les informations d’identification suivantes :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’utilisateur|**Étudiant**|
   |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 2 > Exercice 1 > Tâche 1 > Étape 9.**|

    >**Remarque** : vérifiez que la connexion Bureau à distance a réussi. À ce stade, vous avez confirmé que vous pouvez vous connecter via le Bureau à distance à myVMMgmt.

4. Dans le portail Azure, accédez au volet de la machine virtuelle **myVMWeb**.

5. Dans le volet **myVMWeb**, dans la section **Opérations**, cliquez sur **Exécuter la commande**, puis sur **RunPowerShellScript**.

6. Dans le volet **Exécuter le script de commande**, exécutez ce qui suit pour installer le rôle serveur web sur **myVmWeb** :

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

    >**Remarque** : Attendez que l’installation se termine. Cela peut prendre quelques minutes. À ce stade, vous pouvez vérifier que myVMWeb est accessible via HTTP/HTTPS.

7. Dans le Portail Azure, revenez au volet **myVMWeb**.

8. Dans le volet **myVMWeb**, identifiez l’**adresse IP publique** de la machine virtuelle Azure myVmWeb.

9. Démarrez une autre fenêtre de navigateur et naviguez vers l'adresse IP que vous avez identifiée à l'étape précédente.

    >**Remarque** : la page du navigateur doit afficher la page d’accueil IIS par défaut, car le port 80 est autorisé à être entrant à partir d’Internet en fonction du paramètre du groupe de sécurité des applications **myAsgWebServers** . L’interface réseau de la machine virtuelle Azure myVMWeb est associée à ce groupe de sécurité d’application. 

> Résultat : vous avez validé que la configuration NSG et ASG fonctionne et que le trafic est correctement géré. 

**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus. 

1. Ouvrez Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, sélectionnez **PowerShell**, puis **Créer un stockage**.

2. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

3. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour supprimer le groupe de ressources que vous avez créé dans ce labo :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB07" -Force -AsJob
    ```

4.  Fermez le volet **Cloud Shell**. 

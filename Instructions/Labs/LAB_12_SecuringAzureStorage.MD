---
lab:
  title: ' 12 - Points de terminaison de service et sécurisation du stockage'
  module: Module 03 - Secure data and applications
---

# <a name="lab-12-service-endpoints-and-securing-storage"></a>Labo 12 : Points de terminaison de service et sécurisation du stockage
# <a name="student-lab-manual"></a>Manuel de labo pour l’étudiant

## <a name="lab-scenario"></a>Scénario du labo

Vous avez été invité à créer une preuve de concept pour montrer la sécurisation des partages de fichiers Azure. Plus précisément, vous souhaitez :
    
- Créer un point de terminaison de stockage afin que le trafic destiné à Stockage Azure reste toujours au sein du réseau principal Azure.
- Configurez le point de terminaison de stockage afin que seules des ressources d’un sous-réseau spécifique puissent accéder au stockage.
- Vérifiez que des ressources extérieures au sous-réseau spécifique ne peuvent pas accéder au stockage. 

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## <a name="lab-objectives"></a>Objectifs du labo

Dans ce labo, vous allez effectuer les exercices suivants :

- Exercice 1 : Points de terminaison de service et stockage de sécurité

## <a name="service-endpoints-and-securing-storage-diagram"></a>Diagramme de points de terminaison de services et de sécurisation du stockage

![image](https://user-images.githubusercontent.com/91347931/157534883-29664a05-85d1-4c70-99a7-f16d2360755d.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-service-endpoints-and-security-storage"></a>Exercice 1 : Points de terminaison de service et stockage de sécurité

### <a name="estimated-timing-45-minutes"></a>Durée estimée : 45 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créer un réseau virtuel
- Tâche 2 : Ajouter un sous-réseau au réseau virtuel et configurer un point de terminaison de stockage
- Tâche 3 : Configurer un groupe de sécurité réseau pour restreindre l’accès au sous-réseau
- Tâche 4 : Configurer un groupe de sécurité réseau pour autoriser RDP sur le sous-réseau public
- Tâche 5 : Créer un compte de stockage avec un partage de fichiers
- Tâche 6 : Déployer des machines virtuelles dans les sous-réseaux désignés
- Tâche 7 : Tester la connexion de stockage à partir du sous-réseau privé pour confirmer que l’accès est autorisé
- Tâche 8 : Tester la connexion de stockage à partir du sous-réseau public pour confirmer que l’accès est refusé 

#### <a name="task-1-create-a-virtual-network"></a>Tâche 1 : Créer un réseau virtuel

Dans cette tâche, vous allez créer un réseau virtuel.

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Réseaux virtuels**, puis appuyez sur la touche **Entrée**.

3. Dans le volet **Réseaux virtuels**, cliquez sur  **+ Créer**.

4. Dans le volet **Créer un réseau virtuel**, sous l’onglet **Options de base**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres), puis cliquez sur **Suivant : Adresses IP** :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Groupe de ressources|Cliquez sur **Créer** et tapez le nom **AZ500LAB12**|
    |Nom|**myVirtualNetwork**|
    |Région|**(États-Unis) USA Est**|

5. Sous l’onglet **Adresses IP** du volet **Créer un réseau virtuel**, définissez l’**espace d’adressage IPv4** sur **10.0.0.0/16**. Dans la colonne **Nom du sous-réseau**, cliquez sur **Par défaut**, puis, dans le volet **Modifier le sous-réseau**, spécifiez les paramètres suivants et cliquez sur **Enregistrer** :

    |Paramètre|Valeur|
    |---|---|
    |Nom du sous-réseau|**Public**|
    |Plage d’adresses de sous-réseau|**10.0.0.0/24**|

6. De retour sous l’onglet **Adresses IP** du volet **Créer un réseau virtuel**, cliquez sur **Vérifier + créer**.

7. Sous l’onglet **Vérifier + créer** du volet **Créer un réseau virtuel**, cliquez sur **Créer**.

#### <a name="task-2-add-a-subnet-to-the-virtual-network-and-configure-a-storage-endpoint"></a>Tâche 2 : Ajouter un sous-réseau au réseau virtuel et configurer un point de terminaison de stockage

Dans cette tâche, vous allez créer un autre sous-réseau et activer un point de terminaison de service sur celui-ci. Les points de terminaison de service sont activés par service, par sous-réseau. 

1. Dans le Portail Azure, revenez au volet **Réseaux virtuels**.

2. Dans le volet **Réseaux virtuels**, cliquez sur l’entrée **myVirtualNetwork**.

3. Dans le volet **myVirtualNetwork**, dans la section **Paramètres**, cliquez sur **Sous-réseaux**.

4. Dans le volet **myVirtualNetwork \| Sous-réseaux**, cliquez sur **+ Sous-réseau**. 

5. Dans le volet **Ajouter un sous-réseau**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Valeur|
    |---|---|
    |Nom du sous-réseau|**Privé**|
    |Plage d’adresses de sous-réseau|**10.0.1.0/24**|
    |Points de terminaison de service|**Microsoft.Storage**|

6. Dans le volet **Ajouter un sous-réseau**, cliquez sur **Enregistrer** pour ajouter le nouveau sous-réseau.

    >**Remarque** : le réseau virtuel a maintenant deux sous-réseaux : public et privé. 
    
#### <a name="task-3-configure-a-network-security-group-to-restrict-access-to-the-subnet"></a>Tâche 3 : Configurer un groupe de sécurité réseau pour restreindre l’accès au sous-réseau

Dans cette tâche, vous allez créer un groupe de sécurité réseau avec deux règles de sécurité du trafic sortant (Stockage et Internet) et une règle de sécurité du trafic entrant (RDP). Vous allez également associer le groupe de sécurité réseau au sous-réseau privé. Cela aura pour effet de limiter le trafic sortant à partir de machines virtuelles Azure connectées à ce sous-réseau.

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Réseau virtuel**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Groupes de sécurité réseau**, cliquez sur **+Créer**.

3. Sous l’onglet **Options de base** du volet **Créer un groupe de sécurité réseau**, spécifiez les paramètres suivants : 

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Groupe de ressources|**AZ500LAB12**|
    |Nom|**myNsgPrivate**|
    |Région|**USA Est**|

4. Cliquez sur **Vérifier + créer**, puis sur **Créer**.

    >**Remarque** : dans les étapes suivantes, vous allez créer une règle de sécurité de trafic sortant qui permet de communiquer avec le service Stockage Azure. 

5. Dans le Portail Azure, revenez au volet **Groupes de sécurité réseau**, puis cliquez sur l’entrée **myNsgPrivate**.

6. Dans le volet **myNsgPrivate**, dans la section **Paramètres**, cliquez sur **Règles de sécurité de trafic sortant**.

7. Dans le volet **myNsgPrivate \| Règles de sécurité de trafic sortant**, cliquez sur **+ Ajouter**.

8. Dans le volet **Ajouter une règle de sécurité de trafic sortant**, spécifiez les paramètres suivants pour autoriser explicitement le trafic sortant vers Stockage Azure (conservez les valeurs par défaut pour les autres) : 

    |Paramètre|Valeur|
    |---|---|
    |Source|**Étiquette du service**|
    |Balise du service source|**VirtualNetwork**|
    |Source port ranges|**\***|
    |Destination|**Étiquette du service**|
    |Identification de destination|**Stockage**|
    |Plages de ports de destination|**\***|
    |Protocol|**Any**|
    |Action|**Autoriser**|
    |Priority|**1 000**|
    |Nom|**Allow-Storage-All**|

9. Dans le volet **Ajouter une règle de sécurité de trafic sortant**, cliquez sur **Ajouter** pour créer une règle de trafic sortant. 

10. Dans le volet **myNsgPrivate**, dans la section **Paramètres**, cliquez sur **Règles de sécurité de trafic sortant**, puis sur **+ Ajouter**.

11. Dans le volet **Ajouter une règle de sécurité de trafic sortant**, spécifiez les paramètres suivants pour refuser explicitement le trafic sortant vers Internet (conservez les valeurs par défaut pour les autres) : 

    |Paramètre|Valeur|
    |---|---|
    |Source|**Étiquette du service**|
    |Balise du service source|**VirtualNetwork**|
    |Source port ranges|**\***|
    |Destination|**Étiquette du service**|
    |Identification de destination|**Internet**|
    |Plages de ports de destination|**\***|
    |Protocol|**Any**|
    |Action|**Deny**|
    |Priority|**1100**|
    |Nom|**Deny-Internet-All**|

    >**Remarque** : Cette règle qui permet la communication Internet sortante se substitue à une règle par défaut dans tous les groupes de sécurité réseau. 

    >**Remarque** : Dans les étapes suivantes, vous allez créer une règle de sécurité de trafic entrant qui autorise le trafic via le protocole RDP (Remote Desktop Protocol) vers le sous-réseau. La règle remplace une règle de sécurité par défaut qui refuse tout le trafic entrant provenant d’Internet. Les connexions Bureau à distance sont autorisées vers le sous-réseau afin que la connectivité puisse être testée dans une étape ultérieure.

12. Dans le volet **myNsgPrivate**, dans la section **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant**, puis sur **+ Ajouter**.

13. Dans le volet **Ajouter une règle de sécurité de trafic entrant**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) : 

    |Paramètre|Valeur|
    |---|---|
    |Source|**Aucune**|
    |Source port ranges|**\***|
    |Destination|**Étiquette du service**|
    |Identification de destination|**VirtualNetwork**|
    |Plages de ports de destination|**3389**|
    |Protocol|**TCP**|
    |Action|**Autoriser**|
    |Priority|**1 200**|                                                    
    |Nom|**Allow-RDP-All**|

14. Dans le volet **Ajouter une règle de sécurité de trafic entrant**, cliquez sur **Ajouter** pour créer une règle de trafic entrant. 

    >**Remarque** : Vous allez maintenant associer le groupe de sécurité réseau avec le sous-réseau privé.

15. Dans le volet **Sous-réseaux**, sélectionnez **+ Associer** et spécifiez les paramètres suivants dans la section **Associer un sous-réseau**, puis cliquez sur **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel|**myVirtualNetwork**|
    |Subnet|**Privé**|

#### <a name="task-4-configure-a-network-security-group-to-allow-rdp-on-the-public-subnet"></a>Tâche 4 : Configurer un groupe de sécurité réseau pour autoriser RDP sur le sous-réseau public

Dans cette tâche, vous allez créer un groupe de sécurité réseau avec une règle de sécurité de trafic entrant (RDP). Vous allez également associer le groupe de sécurité réseau au sous-réseau public. Cela autorisera l’accès RDP à la machine virtuelle publique.

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Réseau virtuel**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Groupes de sécurité réseau**, cliquez sur **+Créer**.

3. Sous l’onglet **Options de base** du volet **Créer un groupe de sécurité réseau**, spécifiez les paramètres suivants : 

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Groupe de ressources|**AZ500LAB12**|
    |Nom|**myNsgPublic**|
    |Région|**USA Est**|

4. Cliquez sur **Vérifier + créer**, puis sur **Créer**.

    >**Remarque** : dans les étapes suivantes, vous allez créer une règle de sécurité de trafic sortant qui permet de communiquer avec le service Stockage Azure. 

5. Dans le portail Azure, revenez au volet **Groupes de sécurité réseau**, puis cliquez sur l’entrée **myNsgPublic**.

6. Dans le volet **myNsgPublic**, dans la section **Paramètres**, cliquez sur **Règles de sécurité de trafic entrant**, puis sur **+ Ajouter**.

7. Dans le volet **Ajouter une règle de sécurité de trafic entrant**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) : 

    |Paramètre|Valeur|
    |---|---|
    |Source|**Aucune**|
    |Source port ranges|**\***|
    |Destination|**Étiquette du service**|
    |Identification de destination|**VirtualNetwork**|
    |Plages de ports de destination|**3389**|
    |Protocol|**TCP**|
    |Action|**Autoriser**|
    |Priority|**1 200**|                                                    
    |Nom|**Allow-RDP-All**|

8. Dans le volet **Ajouter une règle de sécurité de trafic entrant**, cliquez sur **Ajouter** pour créer une règle de trafic entrant. 

    >**Remarque** : Vous allez maintenant associer le groupe de sécurité réseau avec le sous-réseau public.

9. Dans le volet **Sous-réseaux**, sélectionnez **+ Associer** et spécifiez les paramètres suivants dans la section **Associer un sous-réseau**, puis cliquez sur **OK** :

    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel|**myVirtualNetwork**|
    |Subnet|**Public**|
    
#### <a name="task-5-create-a-storage-account-with-a-file-share"></a>Tâche 5 : Créer un compte de stockage avec un partage de fichiers

Dans cette tâche, vous allez créer un compte de stockage avec un partage de fichiers et obtenir la clé du compte de stockage.  

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** située en haut de la page, tapez **Comptes de stockage**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Comptes de stockage**, cliquez sur  **+ Créer**.

3. Sous l’onglet **Options de base** du volet **Créer un compte de stockage**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Groupe de ressources|**AZ500LAB12**|
    |Nom du compte de stockage|Nom global unique comprenant entre 3 et 24 caractères alphanumériques.|
    |Emplacement|**(États-Unis) EastUS**|
    |Performances|**Standard (compte v2 à usage général)**|
    |Redondance|**Stockage localement redondant (LRS)**|

4. Sous l’onglet **Informations de base** du volet **Créer un compte de stockage**, cliquez sur **Vérifier + créer**, attendez la fin du processus de validation, puis cliquez sur **Créer**.

    >**Remarque** : attendez que le compte de stockage soit créé. Ce processus prend environ 2 minutes.

5. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Groupes de ressources**, puis appuyez sur la touche **Entrée**.

6. Dans le volet **Groupes de ressources**, dans la liste des groupes de ressources, cliquez sur l’entrée **AZ500LAB12**.

7. Dans le volet du groupe de ressources **AZ500LAB12**, dans la liste des ressources, cliquez sur l’entrée représentant le compte de stockage nouvellement créé.

8. Dans le volet **Vue d’ensemble** du compte de stockage, cliquez sur **Partages de fichiers** sous l’onglet **Stockage de données**, puis cliquez sur **+ Partage de fichiers**.

9. Dans le volet **Nouveau partage de fichiers**, spécifiez les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |Nom|**my-file-share**|

10. Dans le volet **Nouveau partage de fichiers**, cliquez sur **Créer**.

    >**Remarque** : à présent, récupérez et enregistrez le script PowerShell qui crée un mappage de lecteur au partage de fichiers Azure. 

11. Dans le volet du compte de stockage, dans la liste des partages de fichiers, cliquez sur **my-file-share**.

12. Dans le volet **my-file-share**, cliquez sur **Se connecter**.

13. Dans le volet **Se connecter**, sous l’onglet **Windows**, copiez le script PowerShell qui crée un mappage de lecteur Z au partage de fichiers. 

    >**Remarque** : enregistrez ce script. Vous en aurez besoin plus tard dans ce labo pour mapper le partage de fichiers à partir de la machine virtuelle Azure sur le sous-réseau **Privé**.

14. Revenez au volet du compte de stockage, puis, dans la section **Sécurité + mise en réseau**, cliquez sur **Mise en réseau**.

15. Dans le volet **Pare-feu et réseaux virtuels**, sélectionnez l’option **Activé à partir des réseaux virtuels et adresses IP sélectionnés**, puis cliquez sur le lien **+ Ajouter un réseau virtuel existant**. 

16. Dans le volet **Ajouter des réseaux**, spécifiez les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Réseaux virtuels|**myVirtualNetwork**|
    |Sous-réseaux|**Privé**|

17. Dans le volet **Ajouter des réseaux**, cliquez sur **Ajouter**. 

18. De retour dans le volet du compte de stockage, cliquez sur **Enregistrer**.

    >**Remarque** : à ce stade du labo, vous avez configuré un réseau virtuel, un groupe de sécurité réseau et un compte de stockage avec un partage de fichiers. 

#### <a name="task-6-deploy-virtual-machines-into-the-designated-subnets"></a>Tâche 6 : Déployer des machines virtuelles dans les sous-réseaux désignés

Dans cette tâche, vous allez créer deux machines virtuelles : l’une dans le sous-réseau privé et l’autre dans le sous-réseau public. 

>**Remarque** : la première machine virtuelle sera connectée au sous-réseau privé.

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Machines virtuelles** et appuyez sur la touche **Entrée**.

2. Dans le volet **Machines virtuelles**, cliquez sur **+ Créer** et, dans la liste déroulante, cliquez sur **+ Machine virtuelle Azure**.

3. Sous l’onglet **Informations de base** du volet **Créer une machine virtuelle**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Groupe de ressources|**AZ500LAB12**|
    |Nom de la machine virtuelle|**myVmPrivate**|
    |Région|**(États-Unis) USA Est**|
    |Image|**Windows Server 2022 Datacenter : Édition Azure - Gen 2**|
    |Nom d’utilisateur|**localadmin**|
    |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|
    |Aucun port d’entrée public|**Aucun**|
    |Vous disposez déjà d’une licence Windows Server|**Non sélectionnée**|

    >**Remarque** : pour les ports d’entrée publics, nous allons nous appuyer sur le groupe de sécurité réseau (NSG) pré-créé. 

4. Cliquez sur **Suivant : Disques >** , puis, sous l’onglet **Disques** du volet **Créer une machine virtuelle**, définissez le **Type de disque du système d’exploitation** sur **HDD Standard** et cliquez sur **Suivant : Mise en réseau >** .

5. Cliquez sur **Suivant : Mise en réseau >** , sous l’onglet **Mise en réseau** du volet **Créer une machine virtuelle**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel|**myVirtualNetwork**|
    |Subnet|**Privé (10.0.1.0/24)**|
    |Adresse IP publique|**(new)myVmPrivate-ip**|
    |Groupe de sécurité réseau de la carte réseau|**Aucun**|

6. Cliquez sur **Suivant : Gestion >** , puis, sous l’onglet **Gestion** du volet **Créer une machine virtuelle**, acceptez les paramètres par défaut et cliquez sur **Vérifier + créer**.

7. Dans le volet **Vérifier + créer**, vérifiez que la validation a réussi, puis cliquez sur **Créer**.

    >**Remarque** : la deuxième machine virtuelle sera connectée au sous-réseau public.

8. Dans le volet **Machines virtuelles**, cliquez sur **+ Ajouter** et, dans la liste déroulante, cliquez sur **+ Machine virtuelle Azure**.

9. Sous l’onglet **Informations de base** du volet **Créer une machine virtuelle**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|Nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Groupe de ressources|**AZ500LAB12**|
    |Nom de la machine virtuelle|**myVmPublic**|
    |Région|**(États-Unis) USA Est**|
    |Image|**Windows Server 2022 Datacenter : Édition Azure - Gen 2**|
    |Nom d’utilisateur|**localadmin**|
    |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|
    |Aucun port d’entrée public|**Aucun**|
    |Vous disposez déjà d’une licence Windows Server|**Non sélectionnée**|

    >**Remarque** : pour les ports d’entrée publics, nous allons nous appuyer sur le groupe de sécurité réseau (NSG) pré-créé. 

10. Cliquez sur **Suivant : Disques >** , puis, sous l’onglet **Disques** du volet **Créer une machine virtuelle**, définissez le **Type de disque du système d’exploitation** sur **HDD Standard** et cliquez sur **Suivant : Mise en réseau >** .

11. Cliquez sur **Suivant : Mise en réseau >** , sous l’onglet **Mise en réseau** du volet **Créer une machine virtuelle**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

    |Paramètre|Valeur|
    |---|---|
    |Réseau virtuel|**myVirtualNetwork**|
    |Subnet|**Public (10.0.0.0/24)**|
    |Adresse IP publique|**(new)myVmPublic-ip**|
    |Groupe de sécurité réseau de la carte réseau|**Aucun**|

12. Cliquez sur **Suivant : Gestion >** , puis, sous l’onglet **Gestion** du volet **Créer une machine virtuelle**, acceptez les paramètres par défaut et cliquez sur **Vérifier + créer**.

13. Dans le volet **Vérifier + créer**, vérifiez que la validation a réussi, puis cliquez sur **Créer**.

    >**Remarque** : vous pouvez passer à la tâche suivante une fois le déploiement de la machine virtuelle Azure **myVMPrivate** terminé.

#### <a name="task-7-test-the-storage-connection-from-the-private-subnet-to-confirm-that-access-is-allowed"></a>Tâche 7 : Tester la connexion de stockage à partir du sous-réseau privé pour confirmer que l’accès est autorisé

Dans cette tâche, vous allez vous connecter à la machine virtuelle myVMPrivate via le Bureau à distance et mapper un lecteur au partage de fichiers. 

1. Revenez au panneau **Machines virtuelles**. 

2. Dans le volet **Machines virtuelles**, cliquez sur l’entrée **myVMPrivate**.

3. Dans le volet **myVMPrivate**, cliquez sur **Se connecter**, puis, dans le menu déroulant, cliquez sur **RDP**. 

4. Cliquez sur **Télécharger le fichier RDP**, puis utilisez-le pour vous connecter à la machine virtuelle Azure **myVMPrivate** via le Bureau à distance. Lorsque vous êtes invité à vous authentifier, fournissez les informations d’identification suivantes :

    |Paramètre|Valeur|
    |---|---|
    |Nom d’utilisateur|**localadmin**|
    |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|

    >**Remarque** : attendez que la session Bureau à distance s’ouvre et que le Gestionnaire de serveur soit chargé

    >**Remarque** : vous allez maintenant mapper le lecteur Z à un partage de fichiers Azure au sein de la session Bureau à distance sur un ordinateur Windows Server 2022

5. Dans la session Bureau à distance sur **myVMPrivate**, cliquez sur **Démarrer**, puis sur **Windows PowerShell ISE**.

6. Dans la fenêtre **Windows PowerShell ISE**, ouvrez le volet **Script**, puis collez et exécutez le script PowerShell que vous avez enregistré précédemment dans ce labo. Le format du script est le suivant :

    ```powershell
    $connectTestResult = Test-NetConnection -ComputerName <storage_account_name>.file.core.windows.net -Port 445
    if ($connectTestResult.TcpTestSucceeded) {
       # Save the password so the drive will persist on reboot
       cmd.exe /C "cmdkey /add:`"<storage_account_name>.file.core.windows.net`" /user:`"localhost\<storage_account_name>`"  /pass:`"<storage_account_key>`""
       # Mount the drive
       New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage_account_name>.file.core.windows.net\my-file-share" -Persist
    } else {
       Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
    }
    ```
    >**Remarque** : l’espace réservé `<storage_account_name>` représente le nom du compte de stockage hébergeant le partage de fichiers, et `<storage_account_key>` sa clé primaire

7. Démarrez l’Explorateur de fichiers et vérifiez que le mappage du lecteur Z: a été correctement créé.

8. Ensuite, dans le volet de la console **Windows PowerShell ISE**, exécutez la commande suivante pour vérifier que la machine virtuelle n’a pas de connectivité sortante à Internet :

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**Remarque** : le test échouera parce que le groupe de sécurité réseau associé au sous-réseau privé n’autorise pas l’accès sortant à Internet.

9. Mettez fin à la session Bureau à distance sur la machine virtuelle **myVmPrivate**.

    >**Remarque** : à ce stade, vous avez confirmé que la machine virtuelle dans le sous-réseau privé peut accéder au compte de stockage. 

####  <a name="task-8-test-the-storage-connection-from-the-public-subnet-to-confirm-that-access-is-denied"></a>Tâche 8 : Tester la connexion de stockage à partir du sous-réseau public pour confirmer que l’accès est refusé 

1. Revenez au panneau **Machines virtuelles**. 

2. Dans le volet **Machines virtuelles**, cliquez sur l’entrée **myVMPrivate**.

3. Dans le volet **myVMPublic**, cliquez sur **Se connecter**, puis, dans le menu déroulant, cliquez sur **RDP**. 

4. Cliquez sur **Télécharger le fichier RDP**, puis utilisez-le pour vous connecter à la machine virtuelle Azure **myVMPublic** via le Bureau à distance. Lorsque vous êtes invité à vous authentifier, fournissez les informations d’identification suivantes :

    |Paramètre|Valeur|
    |---|---|
    |Nom d’utilisateur|**localadmin**|
    |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|

    >**Remarque** : attendez que la session Bureau à distance s’ouvre et que le Gestionnaire de serveur soit chargé

    >**Remarque** : vous allez maintenant mapper le lecteur Z à un partage de fichiers Azure au sein de la session Bureau à distance sur un ordinateur Windows Server 2022

5. Dans la session Bureau à distance sur **myVMPublic**, cliquez sur **Démarrer**, puis sur **Windows PowerShell ISE**.

6. Dans la fenêtre **Windows PowerShell ISE**, ouvrez le volet **Script**, puis collez et exécutez le script PowerShell que vous avez exécuté au sein de la session Bureau à distance sur la machine virtuelle Azure **myVMPrivate**.

    >**Remarque** : Cette fois, vous allez voir s’afficher l’erreur **New-PSDrive : Accès refusé**. 

    >**Remarque** : L’accès est refusé parce que la machine virtuelle *myVmPublic* est déployée dans le sous-réseau Public. Le sous-réseau public ne dispose d’aucun point de terminaison de service activé pour le Stockage Azure. Le compte de stockage ne permet d’accès réseau qu’à partir du sous-réseau privé.

7. Ensuite, dans le volet de la console **Windows PowerShell ISE**, exécutez la commande suivante pour vérifier que la machine virtuelle a une connectivité sortante à Internet : 

    ```powershell
    Test-NetConnection -ComputerName www.bing.com -Port 80
    ```

    >**Remarque** : Le test réussit parce qu’il n’existe aucune règle de sécurité de trafic sortant pour refuser Internet sur le sous-réseau public.

8. Mettez fin à la session Bureau à distance sur la machine virtuelle **myVmPublic**.

    >**Remarque** : à ce stade, vous avez confirmé que la machine virtuelle dans le sous-réseau public ne peut pas accéder au compte de stockage, mais a accès à Internet.


**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus. 

1. Ouvrez Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, sélectionnez **PowerShell**, puis **Créer un stockage**.

2. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

3. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour supprimer le groupe de ressources que vous avez créé dans ce labo :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB12" -Force -AsJob
    ```

4.  Fermez le volet **Cloud Shell**. 


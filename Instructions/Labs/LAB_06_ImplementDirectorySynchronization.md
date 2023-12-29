---
lab:
  title: "06\_: Implémenter la synchronisation des annuaires"
  module: Module 01 - Manage Identity and Access
---

# Labo 06 : Implémenter la synchronisation des annuaires
# Manuel de labos pour étudiant

## Scénario du labo

Vous avez été invité à créer une preuve de concept montrant comment intégrer un environnement Microsoft Entra Domain Services local à un locataire Microsoft Entra. Plus précisément, vous souhaitez :

- Implémenter une forêt Microsoft Entra Domain Services à domaine unique en déployant une machine virtuelle Azure hébergeant un contrôleur de domaine Microsoft Entra Domain Services
- Créer et configurer un locataire Microsoft Entra
- Synchroniser la forêt Microsoft Entra Domain Services avec le locataire Microsoft Entra

> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous allez effectuer les exercices suivants :

- Exercice 1 : Déployer une machine virtuelle Azure hébergeant un contrôleur de domaine Microsoft Entra ID
- Exercice 2 : Créer et configurer un locataire Microsoft Entra
- Exercice 3 : Synchroniser la forêt Microsoft Entra ID avec un locataire Microsoft Entra

## Implémenter la synchronisation des annuaires

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/5d9cc4c7-7dcd-4d88-920d-9c97d5a482a2)

## Instructions

### Exercice 1 : Déployer une machine virtuelle Azure hébergeant un contrôleur de domaine Microsoft Entra ID

### Durée estimée : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Identifier un nom DNS disponible pour un déploiement Azure VM
- Tâche 2 : Utiliser un modèle ARM pour déployer une machine virtuelle Azure hébergeant un contrôleur de domaine Microsoft Entra ID

#### Tâche 1 : Identifier un nom DNS disponible pour un déploiement Azure VM

Dans cette tâche, vous identifierez un nom DNS pour votre déploiement de machine virtuelle Azure. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au Portail Azure à l’aide d’un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce laboratoire.

2. Ouvrez Cloud Shell en cliquant sur la première icône située en haut à droite du portail Azure. Si vous y êtes invité, cliquez sur **PowerShell** et **Créer un stockage**.

3. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

4. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour identifier un nom DNS disponible que vous pouvez utiliser pour un déploiement de machine virtuelle Azure dans la tâche suivante de cet exercice :

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**Remarque** : remplacez l’espace réservé `<custom-label>` par un nom DNS valide susceptible d’être globalement unique. Remplacez l’espace réservé `<location>` par le nom de la région dans laquelle vous souhaitez déployer la machine virtuelle Azure qui hébergera le contrôleur de domaine Active Directory que vous utiliserez dans ce laboratoire.

    >**Remarque** : pour identifier les régions Azure où vous pouvez approvisionner des machines virtuelles Azure, reportez-vous à [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)

5. Vérifiez que la commande a retourné **True**. Si ce n’est pas le cas, réexécutez la même commande avec une valeur différente de celle-ci `<custom-label>` jusqu’à ce que la commande retourne la valeur **True**.

6. Enregistrez la valeur de `<custom-label>` qui a permis d’obtenir le bon résultat. Vous en aurez besoin dans la prochaine tâche.

7. Fermez Cloud Shell.

#### Tâche 2 : Utiliser un modèle ARM pour déployer une machine virtuelle Azure hébergeant un contrôleur de domaine Microsoft Entra ID

Dans cette tâche, vous allez déployer une machine virtuelle Azure qui hébergera un contrôleur de domaine Microsoft Entra ID.

1. Ouvrez un autre onglet de navigateur dans la même fenêtre de navigateur web et accédez à la page **https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain** .

2. Dans la page **Créer une machine virtuelle Azure avec une nouvelle forêt Active Directory**, cliquez sur **Déployer sur Azure**. Cela va rediriger automatiquement le navigateur vers le volet **Créer une machine virtuelle Azure avec une nouvelle forêt AD** dans le portail Azure. 

3. Dans le volet **Créer une machine virtuelle Azure avec une nouvelle forêt Active Directory**, cliquez sur **Modifier les paramètres**.

4. Dans le volet **Modifier les paramètres**, cliquez sur **Charger un fichier**, dans la boîte de dialogue **Ouvrir**, accédez au dossier **\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json**, cliquez sur **Ouvrir**, puis sur **Enregistrer**.

5. Dans le volet **Créer une machine virtuelle Azure avec une nouvelle forêt AD**, spécifiez les paramètres suivants (laissez les autres avec leurs valeurs existantes) :

   |Paramètre|Valeur|
   |---|---|
   |Abonnement|nom de votre abonnement Azure|
   |Resource group|cliquez sur **Créer** et tapez le nom **AZ500LAB06**|
   |Région|la région Azure que vous avez identifiée dans la tâche précédente|
   |Nom d’utilisateur d’administrateur|**Étudiant**|
   |Mot de passe d’administrateur|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|
   |Nom de domaine|**adatum.com**|
   |Préfixe DNS|le nom d’hôte DNS que vous avez identifié dans la tâche précédente|
   |Taille de la machine virtuelle|**Standard_D2s_v3**|

6. Dans le volet **Créer une machine virtuelle Azure avec une nouvelle forêt AD**, cliquez sur **Vérifier + créer**, puis sur **Créer**.

    >**Remarque** : n’attendez pas que le déploiement se termine et passez à l’exercice suivant. Le déploiement peut prendre environ 15 minutes. Vous allez utiliser la machine virtuelle déployée dans cette tâche dans le troisième exercice de ce laboratoire.

> Résultat : une fois cet exercice terminé, vous avez lancé le déploiement d’une machine virtuelle Azure qui hébergera un contrôleur de domaine Microsoft Entra ID à l’aide d’un modèle Azure Resource Manager.


### Exercice 2 : Créer et configurer un locataire Microsoft Entra 

### Durée estimée : 20 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créer un locataire Microsoft Entra
- Tâche 2 : Ajouter un nom DNS personnalisé au nouveau locataire Microsoft Entra
- Tâche 3 : Créer un utilisateur Microsoft Entra ID avec le rôle Administrateur général

#### Tâche 1 : Créer un locataire Microsoft Entra

Dans cette tâche, vous allez créer un locataire Microsoft Entra à utiliser dans ce laboratoire. 

1. Dans le Portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents**, située en haut de la page Portail Azure, tapez **Microsoft Entra ID** et appuyez sur la touche **Entrée**.

2. Dans le volet affichant la **vue d’ensemble** de votre locataire Microsoft Entra actuel, cliquez sur **Gérer les locataires**, puis sur l’écran suivant, cliquez sur **+Créer**.

3. Sous l’onglet **Options de base** du volet **Créer un locataire**, assurez-vous que l’option **Microsoft Entra ID** est sélectionnée, puis sélectionnez **Suivant : Configuration >**.

4. Sous l’onglet **Configuration** du volet **Créer un répertoire**, spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Nom de l’organisation|**AdatumSync**|
   |Nom de domaine initial|un nom unique constitué d’une combinaison de lettres et de chiffres|
   |Pays ou région|**États-Unis**|

    >**Remarque** : notez le nom de domaine initial. Vous en aurez besoin plus tard dans ce laboratoire.

    >**Remarque** : la coche verte dans la zone de texte **Nom de domaine initial** indique que le nom de domaine que vous avez tapé est valide et unique. (Prenez note de votre nom de domaine initial pour une utilisation ultérieure).

5. Cliquez sur **Vérifier + créer**, puis sur **Créer**.

    >**Remarque** : Attendez que le nouveau locataire soit créé. Utilisez l’icône **Notification** pour surveiller l’état du déploiement. 

#### Tâche 2 : Ajouter un nom DNS personnalisé au nouveau locataire Azure AD

Dans cette tâche, vous allez ajouter votre nom DNS personnalisé au nouveau locataire Azure AD. 

1. Dans la barre d’outils du portail Azure, cliquez sur l’icône **Répertoire + abonnement**, située à droite de l’icône Cloud Shell. 

2. Dans le volet **Annuaire + abonnement** , sélectionnez la ligne **AdatumSync** du locataire nouvellement créée, puis cliquez sur le bouton **Basculer**.

    >**Remarque** : vous devrez peut-être actualiser la fenêtre du navigateur si l’entrée **AdatumSync** n’apparaît pas dans la liste de filtres **Annuaire + abonnement**.

3. Dans le volet **AdatumSync \| Microsoft Entra ID**, dans la section **Gérer**, cliquez sur **Noms de domaine personnalisés**.

4. Dans le volet **AdatumSync \|Noms de domaine personnalisés**, cliquez sur **+ Ajouter un domaine personnalisé**.

5. Dans le volet **Nom de domaine personnalisé**, dans la zone de texte **Nom de domaine personnalisé**, tapez **adatum.com**, puis cliquez sur **Ajouter un domaine**.

6. Dans le volet **adatum.com**, passez en revue les informations nécessaires pour effectuer la vérification du nom de domaine Microsoft Entra, puis sélectionnez **Supprimer** deux fois. 

    >**Remarque** : vous ne pourrez pas terminer le processus de validation, car vous ne possédez pas le nom de domaine DNS **adatum.com**. Cela ne vous empêchera pas de synchroniser le domaine Microsoft Entra Domain Services **adatum.com** avec le locataire Microsoft Entra. Vous utiliserez à cet effet le nom DNS initial du locataire Microsoft Entra (le nom se terminant par le suffixe **onmicrosoft.com**), que vous avez identifié dans la tâche précédente. Toutefois, gardez à l’esprit que, par conséquent, le nom de domaine DNS du domaine Microsoft Entra Domain Services et le nom DNS du locataire Microsoft Entra seront différents. Cela signifie que les utilisateurs Adatum devront utiliser différents noms lors de la connexion au domaine Microsoft Entra Domain Services et lors de la connexion au locataire Microsoft Entra.

#### Tâche 3 : Créer un utilisateur Microsoft Entra ID avec le rôle Administrateur général

Dans cette tâche, vous allez ajouter un nouvel utilisateur Microsoft Entra ID et lui affecter le rôle Administrateur général. 

1. Dans le volet du locataire Microsoft Entra **AdatumSync**, dans la section **Gérer**, cliquez sur **Utilisateurs**.

2. Dans le volet **Utilisateurs | Tous les utilisateurs**, cliquez sur **+ Nouvel utilisateur**, puis sur **Créer un utilisateur**.

3. Dans le volet **Nouvel utilisateur**, vérifiez que l’option **Créer un utilisateur** est sélectionnée, spécifiez les paramètres suivants sous l’onglet Informations de base (laissez les autres paramètres avec leurs valeurs par défaut), puis cliquez sur **Suivant : Propriétés > ** :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’utilisateur|**syncadmin**|
   |Nom|**syncadmin**|
   |Mot de passe|vérifiez que l’option **Générer automatiquement un mot de passe** est cochée et cliquez sur **Afficher le mot de passe**|

    >**Remarque** : prenez note du nom complet. Vous pouvez copier sa valeur en cliquant sur le bouton **Copier dans le Presse-papiers** sur le côté droit de la liste déroulante en affichant le nom de domaine et en le collant dans un document du Bloc-notes. Vous en aurez besoin plus tard dans ce labo.

    >**Remarque** : Enregistrez le mot de passe de l’utilisateur en cliquant sur le bouton **Copier dans le Presse-papiers** sur le côté droit de la zone de texte Mot de passe et en le collant dans un document du Bloc-notes. Vous en aurez besoin plus tard dans ce labo.

4. Sous l’onglet **Propriétés**, faites défiler vers le bas de l’écran et spécifiez l’emplacement d’utilisation : **États-Unis** (laissez les autres paramètres avec leurs valeurs par défaut), puis cliquez sur **Suivant : Affectations > **.

5. Sous l’onglet **Attributions**, cliquez sur **+ Ajouter un rôle**, recherchez et sélectionnez **Administrateur général**, puis cliquez sur **Sélectionner**. Cliquez sur **Vérifier + créer**, puis sur **Créer**.
   
    >**Remarque** : un utilisateur Microsoft Entra disposant du rôle Administrateur général est requis pour implémenter Microsoft Entra Connect.

6. Ouvrez une fenêtre de navigation InPrivate.

7. Accédez au portail Azure sur **`https://portal.azure.com/`** et connectez-vous avec le compte d’utilisateur **syncadmin**. Lorsque vous y êtes invité, remplacez le mot de passe que vous avez enregistré précédemment dans cette tâche par votre propre mot de passe qui répond aux critères de complexité, puis enregistrez-le pour vous y référer plus tard. Vous serez invité à entrer ce mot de passe dans des tâches ultérieures.

    >**Remarque** : pour vous connecter, vous devez fournir un nom complet du compte d’utilisateur **syncadmin**, y compris le nom de domaine DNS du locataire Microsoft Entra dont vous avez pris note précédemment dans cette tâche. Ce nom d’utilisateur est au format syncadmin@`<your_tenant_name>`.onmicrosoft.com, où `<your_tenant_name>` est l’espace réservé représentant votre nom de locataire Microsoft Entra unique. 

8. Déconnectez-vous en tant que **syncadmin** et fermez la fenêtre du navigateur InPrivate.

> **Résultat** : une fois cet exercice terminé, vous avez créé un locataire Microsoft Entra, appris à ajouter un nom DNS personnalisé à ce nouveau locataire et créé un utilisateur Microsoft Entra avec le rôle Administrateur général.


### Exercice 3 : Synchroniser la forêt Microsoft Entra ID avec un locataire Microsoft Entra

### Durée estimée : 20 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Préparer Microsoft Entra Domain Services pour la synchronisation d’annuaires
- Tâche 2 : Installer Microsoft Entra Connect
- Tâche 3 : Vérifier la synchronisation d'annuaires

#### Tâche 1 : Préparer Microsoft Entra Domain Services pour la synchronisation d’annuaires

Dans cette tâche, vous allez vous connecter à la machine virtuelle Azure exécutant le contrôleur de domaine Microsoft Entra Domain Services et créer un compte de synchronisation d’annuaires. 

   > Avant de commencer cette tâche, vérifiez que le déploiement de modèle que vous avez démarré dans le premier exercice de ce labo est terminé.

1. Dans le Portail Azure, définissez le filtre **Annuaire + abonnement** sur le locataire Microsoft Entra associé à l’abonnement Azure dans lequel vous avez déployé la machine virtuelle Azure dans le premier exercice de ce labo.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Machines virtuelles** et appuyez sur la touche **Entrée**.

3. Dans le volet **Machines virtuelles**, cliquez sur l’entrée **adVM**. 

4. Dans le volet **adVM**, cliquez sur **Connecter** et, dans le menu déroulant, cliquez sur **RDP**. 

5. Dans la liste déroulante **Adresse IP**, sélectionnez **Adresse IP publique de l’équilibreur de charge**, puis cliquez sur **Télécharger le fichier RDP** et utilisez-le pour vous connecter à la machine virtuelle Azure **adVM** via le Bureau à distance. Lorsque vous êtes invité à vous authentifier, fournissez les informations d’identification suivantes :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’utilisateur|**Étudiant**|
   |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|

    >**Remarque** : attendez que la session Bureau à distance et que le **Gestionnaire de serveur** se chargent.  

    >**Remarque** : les étapes suivantes sont effectuées dans la session Bureau à distance sur la machine virtuelle Azure **adVM**.

    >**Remarque** : Si l’**adresse IP publique de l’équilibreur de charge** n’est pas disponible dans la liste déroulante **Adresse IP** du panneau RDP dans le Portail Azure, recherchez **Adresses IP publiques**, sélectionnez **adPublicIP** et notez son adresse IP. Cliquez sur le bouton Démarrer, tapez **MSTSC**, puis appuyez sur **Entrée** pour lancer le client Bureau à distance. Tapez l’adresse IP publique de l’équilibreur de charge dans la zone de texte **Ordinateur :** et cliquez sur **Se connecter**.

6. Dans le **Gestionnaire de serveur**, cliquez sur **Outils**, puis dans le menu déroulant, cliquez sur **Centre d’administration Microsoft Entra ID**.

7. Dans le **Centre d’administration Microsoft Entra ID**, cliquez sur **adatum (local)** dans le volet **Tâches**, sous le nom de domaine **adatum (local)**, cliquez sur **Nouveau**, puis, dans le menu en cascade, cliquez sur **Unité d’organisation**.

8. Dans la fenêtre **Créer une unité d’organisation**, dans la zone de texte **Nom**, tapez **ToSync**, puis cliquez sur **OK**.

9. Double-cliquez sur l’unité d’organisation **ToSync** nouvellement créée afin que son contenu apparaisse dans le volet d’informations de la console du Centre d’administration Microsoft Entra ID. 

10. Dans le volet **Tâches** de la section **ToSync**, cliquez sur **Nouveau** et, dans le menu en cascade, cliquez sur **Utilisateur**.

11. Dans la fenêtre **Créer un utilisateur**, créez un compte d’utilisateur avec les paramètres suivants (laissez les autres avec leurs valeurs existantes) et cliquez sur **OK** :
    
    |Paramètre|Valeur|
    |---|---|
    |Nom complet|**aduser1**|
    |Ouverture de session UPN de l’utilisateur|**aduser1**|
    |Ouverture de session SamAccountName de l’utilisateur|**aduser1**|
    |Mot de passe et Confirmer le mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|
    |Autres options de mot de passe|**Le mot de passe n'expire jamais**|


#### Tâche 2 : Installer Microsoft Entra Connect

Dans cette tâche, vous allez installer Microsoft Entra Connect sur la machine virtuelle. 

1. Dans la session Bureau à distance vers **adVM**, utilisez Microsoft Edge pour accéder au portail Azure à l’adresse **https://portal.azure.com** , puis connectez-vous à l’aide du compte d’utilisateur **syncadmin** que vous avez créé l’exercice précédent. Lorsque vous y êtes invité, spécifiez le nom d’utilisateur principal en entier et le mot de passe que vous avez enregistrés dans l’exercice précédent.

2. Dans le Portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents**, située en haut de la page Portail Azure, tapez **Microsoft Entra ID** et appuyez sur la touche **Entrée**.

3. Dans le Portail Azure, dans le panneau **AdatumSync \| Vue d’ensemble**, dans le volet de navigation de gauche sous **Gérer**, cliquez sur **Microsoft Entra Connect**.

4. Dans le volet **Microsoft Entra Connect \| Démarrer**, cliquez sur **Connect Sync** dans le volet de navigation de gauche, puis cliquez sur le lien **Télécharger Microsoft Entra Connect**. Vous êtes redirigé vers la page de téléchargement de **Microsoft Entra Connect**.

5. Sur la page de téléchargement de **Microsoft Entra Connect**, cliquez sur **Télécharger**.

6. Lorsque vous y êtes invité, cliquez sur **Exécuter** pour démarrer l’assistant **Microsoft Entra Connect**.

7. Sur la page **Bienvenue dans Microsoft Entra Connect** de l’assistant **Microsoft Entra Connect**, cochez la case **J’accepte les termes du contrat de licence et la notification de confidentialité**, puis cliquez sur **Continuer**.

8. Sur la page **Configuration rapide** de l’assistant **Microsoft Entra Connect**, cliquez sur l’option **Personnaliser**.

9. Dans la page **Installer les composants requis**, laissez toutes les options de configuration facultatives décochées, puis cliquez sur **Installer**.

10. Dans la page **de connexion utilisateur**, vérifiez que seule la **synchronisation de hachage du mot de passe** est activée, puis cliquez sur **Suivant**.

11. Sur la page **Connexion à Microsoft Entra ID**, authentifiez-vous à l’aide des informations d’identification du compte d’utilisateur **syncadmin** que vous avez créé dans l’exercice précédent, puis cliquez sur **Suivant**. 

12. Dans la page **Connexion de vos annuaires**, cliquez sur le bouton **Ajouter un annuaire** à droite de l’entrée de forêt **adatum.com**.

13. Dans la fenêtre **du compte de forêt AD**, vérifiez que l’option **Créer un compte Microsoft Entra ID** est sélectionnée, spécifiez les informations d’identification suivantes, puis cliquez sur **OK** :

    |Paramètre|Valeur|
    |---|---|
    |User Name|**ADATUM\\Student**|
    |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 06 > Exercice 1 > Tâche 2 > Étape 9**|

14. De retour sur la page **Connexion de vos annuaires**, vérifiez que l’entrée **adatum.com** s’affiche sous la forme d’un répertoire configuré, puis cliquez sur **Suivant**

15. Sur la page **Configuration de la connexion à Microsoft Entra ID**, notez l’avertissement indiquant que **les utilisateurs ne pourront pas se connecter à Microsoft Entra ID avec des informations d’identification locales si le suffixe UPN ne correspond pas à un nom de domaine vérifié**. Cochez la case **Continuer sans faire correspondre tous les suffixes UPN au domaine vérifié**, puis cliquez sur **Suivant**.

    >**Remarque** : comme expliqué précédemment, ce comportement est normal, car vous n’avez pas pu vérifier le domaine DNS Microsoft Entra ID personnalisé **adatum.com**.

16. Dans la page **Filtrage par domaine et unité d’organisation**, cliquez sur l’option **Synchroniser les domaines et les unités d’organisation sélectionnés**, puis décochez la case à côté du nom de domaine **adatum.com**. Cliquez pour développer **adatum.com**, cochez uniquement la case à côté de l’unité d’organisation **ToSync**, puis cliquez sur **Suivant**.

17. Sur la page **Identification de manière unique de vos utilisateurs**, acceptez les paramètres par défaut, puis cliquez sur **Suivant**.

18. Sur la page **Filtrer les utilisateurs et appareils**, acceptez les paramètres par défaut, puis cliquez sur **Suivant**.

19. Sur la page **Fonctionnalités facultatives**, acceptez les paramètres par défaut, puis cliquez sur **Suivant**.

20. Sur la page **Prêt à configurer**, vérifiez que la case **Démarrez le processus de synchronisation une fois la configuration terminée** est cochée, puis cliquez sur **Installer**.

    >**Remarque** : cette installation prend environ 2 minutes.

21. Passez en revue les informations de la page **Configuration terminée**, puis cliquez sur **Quitter** pour fermer la fenêtre **Microsoft Entra Connect**.


#### Tâche 3 : Vérifier la synchronisation d'annuaires

Dans cette tâche, vous allez vérifier que la synchronisation d’annuaires fonctionne. 

1. Dans la session Bureau à distance vers **adVM**, dans la fenêtre Microsoft Edge affichant le Portail Azure, accédez au volet **Utilisateurs - Tous les utilisateurs (préversion)** du locataire Microsoft Entra ID Adatum Lab.

2. Dans le volet **Utilisateurs \| Tous les utilisateurs**, notez que la liste des objets utilisateur inclut le compte **aduser1**. 

   >**Remarque** : vous devrez peut-être attendre quelques minutes et sélectionner **Actualiser** pour que le compte d’utilisateur **aduser1** apparaisse.

3. Cliquez sur le compte **aduser1**, puis sélectionnez l’onglet **Propriétés**. Faites défiler jusqu’à la section **Local**, notez que l’attribut **Synchronisation locale activée** est défini sur **Oui**.

4. Dans le volet **aduser1**, dans la section **Informations de travail**, notez que l’attribut **Département** n’est pas défini.

5. Dans la session Bureau à distance vers **adVM**, basculez vers le **Centre d’administration Microsoft Entra ID**, sélectionnez l’entrée **aduser1** dans la liste des objets de l’unité d’organisation **ToSync**. Dans la section **aduser1** du volet **Tâches**, sélectionnez **Propriétés**.

6. Dans la fenêtre **aduser1** de la section **Organisation**, dans la zone de texte **Département**, tapez **Ventes**, puis sélectionnez **OK**.

7. Dans la session Bureau à distance vers **adVM**, démarrez **Windows PowerShell**.

8. Depuis la console **Administrateur : Windows PowerShell**, exécutez l’action suivante pour démarrer la synchronisation delta Microsoft Entra Connect :

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

9. Basculez vers la fenêtre Microsoft Edge affichant le volet **aduser1**, actualisez la page et notez que la propriété Department est définie sur Sales.

    >**Remarque** : Il est possible que vous deviez attendre jusqu’à trois minutes. Réactualisez la page si l’attribut **Service** n’est toujours pas défini.

> **Résultat** : une fois cet exercice terminé, vous avez préparé Microsoft Entra Domain Services pour la synchronisation d’annuaires, installé Microsoft Entra Connect et vérifié la synchronisation d’annuaires.


**Nettoyer les ressources**

>**Remarque** : commencez par désactiver la synchronisation de Microsoft Entra ID.

1. Dans la session Bureau à distance vers **adVM**, démarrez Windows PowerShell en tant qu’administrateur.

2. À partir de la console Windows PowerShell, installez le module MsOnline PowerShell en exécutant ce qui suit (lorsque vous y êtes invité. Dans la boîte de dialogue Le fournisseur NuGet est requis pour continuer, tapez **Oui** et appuyez sur Entrée.) :

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

3. À partir de la console Windows PowerShell, connectez-vous au locataire Microsoft Entra AdatumSync en exécutant ce qui suit (lorsque vous y êtes invité, connectez-vous avec les informations d’identification **syncadmin**) :

    ```powershell
    Connect-MsolService
    ```

4. À partir de la console Windows PowerShell, désactivez la synchronisation Microsoft Entra Connect en exécutant ce qui suit :

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

5. À partir de la console Windows PowerShell, vérifiez que l’opération a réussi en exécutant ce qui suit :

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```
    >**Remarque** : Le résultat devrait être `False`. Si ce n’est pas le cas, patientez une minute et réexécutez la commande.

    >**Remarque** : supprimez ensuite les ressources Azure
6. Fermez la session Bureau à distance.

7. Dans le Portail Azure, définissez le filtre **Annuaire + abonnement** sur le locataire Microsoft Entra associé à l’abonnement Azure dans lequel vous avez déployé la machine virtuelle Azure **adVM**.

8. Dans le portail Azure, ouvrez Cloud Shell en cliquant sur la première icône située en haut à droite du portail Azure. 

9. Dans le menu déroulant dans le coin supérieur gauche du volet Cloud Shell, sélectionnez **PowerShell** et, lorsque vous y êtes invité, cliquez sur **Confirmer**. 

10. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour supprimer le groupe de ressources que vous avez créé dans ce labo :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
11. Fermez le volet **Cloud Shell**.

    >**Remarque** : enfin, supprimez le locataire Microsoft Entra.
    
    >**Remarque 2** : la suppression d’un locataire est censée être un processus très difficile, de sorte qu’il ne peut jamais être effectué accidentellement ou de façon malveillante.  Cela signifie que la suppression du locataire dans le cadre de ce laboratoire ne fonctionne souvent pas.  Bien que nous disposions des étapes ci-dessous pour supprimer le locataire, il n’est pas nécessaire pour terminer ce labo. Si, un jour, vous avez déjà besoin de supprimer un locataire, il existe des articles sur DOCS.Microsoft.com pour vous y aider.

12. De retour dans le Portail Azure, utilisez le filtre **Annuaire + abonnement** pour basculer vers le locataire Microsoft Entra **AdatumSync**.

13. Dans le portail Azure, accédez au volet **Utilisateurs - Tous les utilisateurs**, cliquez sur l’entrée représentant le compte d’utilisateur **syncadmin**, dans le volet **syncadmin - Profil**, cliquez sur **Supprimer**, puis, lorsque vous êtes invité à confirmer, cliquez sur **Oui**.

14. Répétez la même séquence d’étapes pour supprimer le compte d’utilisateur **aduser1** et le **compte de service de synchronisation d’annuaires local**.

15. Accédez au volet **AdatumSync - Vue d’ensemble** du locataire Microsoft Entra, cliquez sur **Gérer les locataires** et cochez la case de l’annuaire **AdatumSync**, puis cliquez sur **Supprimer**. Dans le volet **Supprimer le locataire AdatumSync**, cliquez sur le lien **Obtenir l’autorisation de supprimer les ressources Azure**. Dans le volet **Propriétés** de Microsoft Entra, définissez **Gestion de l’accès pour les ressources Azure** sur **Oui** et cliquez sur **Enregistrer**.

    >**Remarque** : au moment de la suppression, si vous recevez un avertissement comme **Supprimer tous les utilisateurs**, supprimez alors les utilisateurs que vous avez créés ou si l’avertissement indique **Supprimer les applications LinkedIn**, cliquez sur le message d’avertissement et confirmez la suppression de l’application LinkedIn ; tout l’avertissement doit être résolu pour valider la suppression du locataire.

16. Déconnectez-vous du portail Azure et reconnectez-vous. 

17. Revenez au volet **Supprimer le locataire AdatumSync**, puis cliquez sur **Supprimer**.

> Pour plus d’informations sur cette tâche, reportez-vous à [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)

---
lab:
  title: 03 - Pare-feu Azure
  module: Module 02 - Plan and implement security for public access to Azure resources
---

# Lab 03 : Pare-feu Azure
# Manuel de labo de l’étudiant

## Scénario du labo

Vous avez été invité à installer le pare-feu Azure. Cela aidera votre organisation à contrôler l’accès au réseau entrant et sortant, ce qui constitue un élément important d’un plan de sécurité réseau global. Plus précisément, vous souhaitez créer et tester les composants d’infrastructure suivants :

- Un réseau virtuel avec un sous-réseau de charge de travail et un sous-réseau d’hôte de saut.
- Une machine virtuelle est chaque sous-réseau. 
- Un itinéraire personnalisé qui garantit que tout le trafic de charge de travail sortant du sous-réseau de charge de travail doit utiliser le pare-feu.
- Des règles d’application de pare-feu qui autorisent uniquement le trafic sortant vers www.bing.com. 
- Des règles de réseau de pare-feu qui autorisent les recherches de serveur DNS externes.

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous allez effectuer l’exercice suivant :

- Exercice 1 : Déployer et tester un pare-feu Azure

## Schéma de Pare-feu Azure

![image](https://user-images.githubusercontent.com/91347931/157529954-a1bc434b-2eca-41c1-b875-1f0c977d5e20.png)

## Instructions

## Fichiers du labo :

- **\\Allfiles\\Labs\\08\\template.json**

### Exercice 1 : Déployer et tester un pare-feu Azure

### Durée estimée : 40 minutes

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser pour la classe. 

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Utiliser un modèle pour déployer l’environnement lab. 
- Tâche 2 : Déployer un pare-feu Azure.
- Tâche 3 : Créer une route par défaut.
- Tâche 4 : Configurer une règle d’application.
- Tâche 5 : Configurer une règle de réseau. 
- Tâche 6 : Configurer les serveurs DNS.
- Tâche 7 : Tester le pare-feu. 

#### Tâche 1 : Utiliser un modèle pour déployer l’environnement lab. 

Dans cette tâche, vous allez examiner et déployer l’environnement lab. 

Dans cette tâche, vous allez créer une machine virtuelle à l’aide d’un modèle ARM. Cette machine virtuelle sera utilisée dans le dernier exercice de ce labo. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Déployer un modèle personnalisé**, puis appuyez sur la touche **Entrée**.

3. Dans le volet **Déploiement personnalisé**, sélectionnez **Créer votre propre modèle dans l’éditeur**.

4. Dans le volet **Modifier le modèle**, cliquez sur **Charger le fichier**, recherchez le fichier **\\Allfiles\\Labs\\08\\template.json**, puis cliquez sur **Ouvrir**.

    >**Remarque** : passez en revue le contenu du modèle et notez qu’il déploie une machine virtuelle Azure hébergeant Windows Centre de données Server 2016.

5. Dans le volet **Modifier le modèle**, cliquez sur **Enregistrer**.

6. Dans le volet **Déploiement personnalisé**, vérifiez que les paramètres suivants sont configurés (laissez les autres avec leurs valeurs par défaut) :

   |Paramètre|Valeur|
   |---|---|
   |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
   |Resource group|cliquez sur **Créer** et tapez le nom **AZ500LAB08**|
   |Emplacement|**(États-Unis) USA Est**|
   |adminPassword|Mot de passe sécurisé de votre choix pour les machines virtuelles. Mémorisez le mot de passe. Vous en avez besoin plus tard pour vous connecter aux machines virtuelles.|

    >**Remarque** : pour identifier les régions Azure où vous pouvez approvisionner des machines virtuelles Azure, consultez [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)

7. Cliquez sur **Examiner et créer**, puis cliquez sur **Créer**.

    >**Remarque** : Attendez la fin du déploiement. Ce processus prend environ 2 minutes. 

#### Tâche 2 : Déployer le Pare-feu Azure

Au cours de cette tâche, vous allez déployer le pare-feu dans le réseau virtuel. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Pare-feu**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Pare-feu** , cliquez sur **+Créer**.

3. Sous l’onglet **Informations de base** du panneau **Créer un pare-feu**, spécifiez les paramètres suivants : 

   |Paramètre|Valeur|
   |---|---|
   |Resource group|**AZ500LAB08**|
   |Nom|**Test-FW01**|
   |Région|**(États-Unis) USA Est**|
   |Référence SKU de pare-feu|**Standard**|
   |Gestion de pare-feu|**Utiliser des règles de pare-feu (classique) pour gérer ce pare-feu**|
   |Choisir un réseau virtuel|cliquez sur l’option **Utiliser l’option existante** et, dans la liste déroulante, sélectionnez **Test-FW-VN**|
   |Carte réseau de gestion du pare-feu|Pour désactiver cette fonctionnalité, **désélectionnez** l’option **Activer la carte réseau de gestion du pare-feu**.|
   |Adresse IP publique|Cliquez sur **Ajouter nouveau** et tapez le nom **TEST-FW-PIP**, puis cliquez sur **OK**|

5. Cliquez sur **Vérifier + créer**, puis sur **Créer**. 

    >**Remarque** : Attendez la fin du déploiement. Ce processus prend environ 5 minutes. 

6. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Groupes de ressources**, puis appuyez sur la touche **Entrée**.

7. Dans le volet **Groupes de ressources**, dans la liste des groupes de ressources, cliquez sur l’entrée **AZ500LAB08**.

    >**Remarque** : Dans le panneau du groupe de ressources **AZ500LAB08**, passez en revue la liste des ressources. Vous pouvez trier par **type**.

8. Dans la liste des ressources, cliquez sur l’entrée représentant le **pare-feu Test-FW01**.

9. Dans le panneau **Test-FW01**, identifiez l’adresse **IP privée** affectée au pare-feu. 

    >**Remarque** : Vous aurez besoin de ces informations dans la tâche suivante.


#### Tâche 3 : Créer un itinéraire par défaut

Dans cette tâche, vous allez créer un itinéraire par défaut pour le sous-réseau **Workload-SN**. Cet itinéraire configure le trafic sortant via le pare-feu.

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Tables de routage**, puis appuyez sur la touche **Entrée**.

2. Dans le panneau **Tables de routage**, cliquez sur **+Créer**.

3. Dans le panneau **Créer une table de routage**, spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Resource group|**AZ500LAB08**|
   |Région| **USA Est**|
   |Nom|**Firewall-route**|

4. Cliquez sur **Vérifier + créer**, puis sur **Créer**, puis attendez que l’approvisionnement se termine. 

5. Dans le panneau **Tables de routage**, cliquez sur **Actualiser** et, dans la liste des tables de routage, cliquez sur l’entrée **Routage de pare-feu**.

6. Dans le panneau **Pare-feu**, dans la section **Paramètres**, cliquez sur **Sous-réseaux**, puis, dans le panneau **Sous-réseaux de routage \| de pare-feu**, cliquez sur **+Associer**.

7. Dans le volet **Associer un sous-réseau** , spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Réseau virtuel|**Test-FW-VN**|
   |Subnet|**Workload-SN**|

    >**Remarque** : vérifiez que le sous-réseau **Workload-SN** est sélectionné pour cette route, sinon le pare-feu ne fonctionnera pas correctement.

8. Cliquez sur **OK** pour associer le pare-feu au sous-réseau de réseau virtuel. 

9. De retour dans le panneau **Pare-feu**, dans la section **Paramètres**, cliquez sur **Itinéraires**, puis sur **+ Ajouter**. 

10. Dans le volet **Ajouter un itinéraire**, spécifiez les paramètres suivants :  

   |Paramètre|Valeur|
   |---|---|
   |Nom de l’itinéraire|**FW-DG**|
   |Destination du préfixe d’adresse|**Adresse IP**|
   |Plages d’adresses IP/CIDR de destination|**0.0.0.0/0**
   |Type de tronçon suivant|**Appliance virtuelle**|
   |adresse de tronçon suivant|l’adresse IP privée du pare-feu que vous avez identifié à l’étape précédente|

    >**Remarque** : Le Pare-feu Azure est en réalité un service managé, mais l’appliance virtuelle fonctionne dans ce cas.
    
11.  Cliquez sur **Ajouter** pour ajouter l’itinéraire. 


#### Tâche 4 : Configurer une règle d’application

Dans cette tâche, vous allez ajouter une règle d’application qui autorise l’accès sortant à `www.bing.com`.

1. Dans le Portail Azure, revenez au pare-feu **Test-FW01**.

2. Dans le panneau **Test-FW01**, dans la section **Paramètres**, cliquez sur **Règles (classique)** .

3. Dans le panneau **Règles de test FW01 \| (classique)** , cliquez sur l’onglet **Regroupement de règles d’application**, puis cliquez sur **+Ajouter une collection de règles d’application**.

4. Dans le volet **Ajouter une collection de règles d’application**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

   |Paramètre|Valeur|
   |---|---|
   |Nom|**App-Coll01**|
   |Priority|**200**|
   |Action|**Autoriser**|

5. Dans le volet **Ajouter une collection de règles d’application**, créez une entrée dans la section **Nom de domaine complet cible** avec les paramètres suivants (conservez les valeurs par défaut pour les autres) :

   |Paramètre|Valeur|
   |---|---|
   |name|**AllowGH**|
   |Type de source|**Adresse IP**|
   |Source|**10.0.2.0/24**|
   |Protocole:port|**http:80, https:443**|
   |Noms de domaine complets cibles|**www.bing.com**|

6. Cliquez sur **Ajouter** pour ajouter la règle d’application basée sur les noms de domaine complets cibles.

    >**Remarque** : Le Pare-feu Azure comprend un regroupement de règles intégré pour les noms de domaine complets d’infrastructure qui sont autorisés par défaut. Ces noms de domaine complets sont spécifiques à la plateforme et ne peuvent pas être utilisés à d’autres fins. 

#### Tâche 5 : Configurer une règle de réseau

Dans cette tâche, vous allez créer une règle réseau qui autorise l’accès sortant à deux adresses IP sur le port 53 (DNS).

1. Dans le Portail Azure, revenez au volet **Test-FW01 \| Règles (classique)** .

2. Dans le volet **Règles de test FW01 \| (classique)** , cliquez sur l’onglet **Collection de règles d’application**, puis cliquez sur **+Ajouter une collection de règles d’application**.

3. Dans le volet **Ajouter une collection de règles réseau**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

   |Paramètre|Valeur|
   |---|---|
   |Nom|**Net-Coll01**|
   |Priority|**200**|
   |Action|**Autoriser**|

4. Dans le volet **Ajouter une collection de règles réseau**, créez une entrée dans la section **Adresses IP** avec les paramètres suivants (conservez les valeurs par défaut pour les autres) :

   |Paramètre|Valeur|
   |---|---|
   |Nom|**AllowDNS**|
   |Protocol|**UDP**|
   |Type de source|**Adresse IP**|
   |Adresses sources|**10.0.2.0/24**|
   |Type de destination|**Adresse IP**|
   |Destination Address|**209.244.0.3,209.244.0.4**|
   |Ports de destination|**53**|

5. Cliquez sur **Ajouter** pour ajouter la règle réseau.

    >**Remarque** : les adresses de destination utilisées dans ce cas sont des serveurs DNS publics connus. 

#### Tâche 6 : Configurer les serveurs DNS de la machine virtuelle

Dans cette tâche, vous allez configurer les adresses DNS principales et secondaires de la machine virtuelle. Il ne s’agit pas d’une exigence de pare-feu. 

1. Dans le Portail Azure, revenez au groupe de ressources **AZ500LAB08**.

2. Dans le panneau **AZ500LAB08**, dans la liste des ressources, cliquez sur la machine virtuelle **Srv-Work**.

3. Dans le panneau **Srv-Work**, cliquez sur **Mise en réseau**.

4. Dans le panneau **Srv-Work \| Réseau**, cliquez sur le lien en regard de l’entrée de l’**interface réseau**.

5. Dans le volet de l’interface réseau, dans la section **Paramètres**, cliquez sur **Serveurs DNS**, sélectionnez l’option **Personnalisé**, ajoutez les deux serveurs DNS référencés dans la règle réseau : **209.244.0.3** et **209.244.0.4**, et cliquez sur **Enregistrer** pour enregistrer la modification.

6. Revenez sur la page de machine virtuelle **Srv-Work**.

    >**Remarque** : Attendez la fin de la mise à jour.

    >**Remarque** : La mise à jour des serveurs DNS de cette interface réseau va automatiquement redémarrer la machine virtuelle à laquelle elle est attachée et, le cas échéant, les autres machines virtuelles du même groupe à haute disponibilité.

#### Tâche 7 : Tester le pare-feu

Dans cette tâche, vous allez tester le pare-feu pour confirmer qu’il fonctionne comme prévu.

1. Dans le Portail Azure, revenez au groupe de ressources **AZ500LAB08**.

2. Dans le volet **AZ500LAB08**, dans la liste des ressources, cliquez sur la machine virtuelle **Srv-Jump**.

3. Dans le volet **Srv-Jump**, cliquez sur **Connecter** et, dans le menu déroulant, cliquez sur **RDP**. 

4. Cliquez sur **Télécharger le fichier RDP**, puis utilisez-le pour vous connecter à la machine virtuelle Azure **Srv-Jump** via le Bureau à distance. Lorsque vous êtes invité à vous authentifier, fournissez les informations d’identification suivantes :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’utilisateur|**localadmin**|
   |Mot de passe|Mot de passe sécurisé que vous avez choisi pendant le déploiement du modèle personnalisé dans la tâche 1, étape 6.|

    >**Remarque** : les étapes suivantes sont effectuées dans la session Bureau à distance sur la machine virtuelle Azure **Srv-Jump**. 

    >**Remarque** : vous allez vous connecter à la machine virtuelle **Srv-Work**. Pour cela, nous pouvons tester la possibilité d’accéder au site web bing.com.  

5. Dans la session Bureau à distance pour **Srv-Jump**, cliquez avec le bouton droit sur **Démarrer**, dans le menu contextuel, cliquez sur **Exécuter**, puis, dans la boîte de dialogue **Exécuter**, exécutez la commande suivante pour vous connecter à **Srv-Work**. 

    ```
    mstsc /v:Srv-Work
    ```

6. Lorsque vous êtes invité à vous authentifier, fournissez les informations d’identification suivantes :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’utilisateur|**localadmin**|
   |Mot de passe|Mot de passe sécurisé que vous avez choisi pendant le déploiement du modèle personnalisé dans la tâche 1, étape 6.|

    >**Remarque** : attendez que la session Bureau à distance soit établie et que l’interface Gestionnaire de serveur charge.

7. Dans la session Bureau à distance vers **Srv-Work**, dans **Gestionnaire de serveur**, cliquez sur **Serveur local**, puis sur **Configuration de sécurité améliorée d’Internet Explorer**.

8. Dans la boîte de dialogue **Configuration de sécurité renforcée d’Internet Explorer**, définissez les deux options sur **Désactivé** et cliquez sur **OK**.

9. Dans la session Bureau à distance vers **Srv-Work**, démarrez Internet Explorer et accédez à **`https://www.bing.com`** . 

    >**Remarque** : le site web devrait s’afficher correctement. Le pare-feu vous autorise l’accès.

10. Accédez à **`http://www.microsoft.com/`**.

    >**Remarque** : Dans la page du navigateur, vous devriez recevoir un message avec un texte similaire à ce qui suit : `HTTP request from 10.0.2.4:xxxxx to microsoft.com:80. Action: Deny. No rule matched. Proceeding with default action.` Ceci est attendu, car le pare-feu bloque l’accès à ce site web. 

11. Terminez les deux sessions Bureau à distance.

> Résultat : Vous avez configuré et testé le Pare-feu Azure.

**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus. 

1. Dans le portail Azure, ouvrez Cloud Shell en cliquant sur la première icône située en haut à droite du portail Azure. Si vous y êtes invité, cliquez sur **PowerShell** et **Créer un stockage**.

2. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

3. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour supprimer le groupe de ressources que vous avez créé dans ce labo :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB08" -Force -AsJob
    ```
4. Fermez le volet **Cloud Shell**. 

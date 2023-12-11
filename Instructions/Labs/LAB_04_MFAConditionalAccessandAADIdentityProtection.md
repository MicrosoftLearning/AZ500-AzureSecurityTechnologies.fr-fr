---
lab:
  title: 04 - MFA et Accès conditionnel
  module: Module 01 - Manage Identity and Access
---

# Labo 04 : MFA et Accès conditionnel
# Manuel de labo de l’étudiant

## Scénario du labo

Vous avez été invité à créer une preuve de concept de fonctionnalités qui améliorent l’authentification Azure Active Directory (Azure AD). Plus précisément, vous souhaitez évaluer :

- Authentification multifacteur Azure AD
- Accès conditionnel Azure AD
- Stratégies d’accès conditionnel Azure AD basées sur les risques

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous allez effectuer les exercices suivants :

- Exercice 1 : Déployer une machine virtuelle Azure en utilisant un modèle Azure Resource Manager
- Exercice 2 : Implémenter Azure MFA
- Exercice 3 : Implémenter des stratégies d’accès conditionnel Azure AD 
- Exercice 4 : Implémenter Azure AD Identity Protection

## Authentification multifacteur - Accès conditionnel - Schéma de protection de l’identité

![image](https://user-images.githubusercontent.com/91347931/157518628-8b4a9efe-0086-4ec0-825e-3d062748fa63.png)

## Instructions

## Fichiers du labo :

- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json**
- **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json** 

### Exercice 1 : Déployer une machine virtuelle Azure en utilisant un modèle Azure Resource Manager

### Durée estimée : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Déployer une machine virtuelle Azure en utilisant un modèle Azure Resource Manager.

#### Tâche 1 : Déployer une machine virtuelle Azure en utilisant un modèle Azure Resource Manager

Dans cette tâche, vous allez créer une machine virtuelle à l’aide d’un modèle ARM. Cette machine virtuelle sera utilisée dans le dernier exercice de ce labo. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte titulaire du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo, et du rôle Administrateur général dans le locataire Azure AD associé à cet abonnement.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Déployer un modèle personnalisé**.

    >**Remarque** : Vous pouvez aussi sélectionner **Déploiement de modèle (déploiement à l’aide de modèles personnalisés)** , dans la liste **Place de marché**.

3. Dans le volet **Déploiement personnalisé**, sélectionnez **Créer votre propre modèle dans l’éditeur**.

4. Dans le volet **Modifier le modèle**, cliquez sur **Charger le fichier**, recherchez le fichier **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.json**, puis cliquez sur **Ouvrir**.

    >**Remarque** : passez en revue le contenu du modèle et notez qu’il déploie une machine virtuelle Azure hébergeant Windows Centre de données Server 2019.

5. Dans le volet **Modifier le modèle**, cliquez sur **Enregistrer**.

6. Dans le volet **Déploiement personnalisé**, cliquez sur **Modifier les paramètres**.

7. Dans le volet **Modifier les paramètres**, cliquez sur **Charger le fichier**, recherchez le fichier **\\Allfiles\\Labs\\04\\az-500-04_azuredeploy.parameters.json**, puis cliquez sur **Ouvrir**.

    >**Remarque** :  passez en revue le contenu du fichier de paramètres en notant les valeurs adminUsername etadminPassword.

8. Dans le volet **Modifier les paramètres**, cliquez sur **Enregistrer**.

9. Dans le volet **Déploiement personnalisé**, vérifiez que les paramètres suivants sont configurés (laissez les autres avec leurs valeurs par défaut) :

   >**Remarque** : vous devez créer un mot de passe unique qui sera utilisé pour créer des machines virtuelles (machines virtuelles) pour le reste du cours. Le mot de passe doit contenir au moins 12 caractères et satisfaire aux exigences de complexité définies. (Le mot de passe doit présenter 3 des caractéristiques suivantes : 1 majuscule, 1 minuscule, 1 chiffre et 1 caractère spécial). [Exigences relatives au mot de passe de la machine virtuelle](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm-). Notez le mot de passe.

   |Paramètre|Valeur|
   |---|---|
   |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
   |Resource group|Cliquez sur **Créer** et tapez le nom **AZ500LAB04**|
   |Emplacement|**USA Est**|
   |Taille de machine virtuelle|**Standard_D2s_v3**|
   |Nom de la machine virtuelle|**az500-04-vm1**|
   |Nom d’utilisateur d’administrateur|**Étudiant**|
   |Mot de passe d’administrateur|**Créez votre propre mot de passe et prenez-en note. Vous serez invité à fournir ce mot de passe pour l’accès au labo.**|
   |Nom du réseau virtuel|**az500-04-vnet1**|

   >**Remarque** : pour identifier les régions Azure où vous pouvez approvisionner des machines virtuelles Azure, consultez [ **https://azure.microsoft.com/en-us/regions/offers/** ](https://azure.microsoft.com/en-us/regions/offers/)

10. Cliquez sur **Examiner et créer**, puis cliquez sur **Créer**.

    >**Remarque** : n’attendez pas que le déploiement se termine, mais passez à l’exercice suivant. Vous allez utiliser la machine virtuelle incluse dans ce déploiement dans le dernier exercice de ce labo.

> Résultat : vous avez lancé un déploiement de modèle d’une machine virtuelle Azure **az500-04-vm1** que vous utiliserez dans le dernier exercice de ce labo.


### Exercice 2 : Implémenter Azure MFA

### Durée estimée : 30 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créer un nouveau locataire Azure AD.
- Tâche 2 : Activer l’essai Azure AD Premium P2.
- Tâche 3 : Créer des utilisateurs et des groupes Azure AD.
- Tâche 4 : Affecter des licences Azure AD Premium P2 aux utilisateurs Azure AD.
- Tâche 5 : Configurer les paramètres de l’authentification multifacteur Azure.
- Tâche 6 : Valider la configuration de l’authentification multifacteur

#### Tâche 1 : Créer un nouveau locataire Azure AD

Dans cette tâche, vous allez créer un locataire Azure AD. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Azure Active Directory** et appuyez sur la touche **Entrée**.

2. Dans le volet affichant la **vue d’ensemble** de votre locataire Azure AD actuel, cliquez sur **Gérer les locataires**, puis sur l’écran suivant, cliquez sur **+Créer**.

3. Sous l’onglet**Informations de base** du volet **Créer un locataire**, assurez-vous que l’option **Azure Active Directory** est cochée, puis cliquez sur **Suivant : Configuration >** .

4. Sous l’onglet **Configuration** du volet **Créer un locataire**, spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Nom de l’organisation|**AdatumLab500-04**|
   |Nom de domaine initial|un nom unique constitué d’une combinaison de lettres et de chiffres|
   |Pays ou région|**États-Unis**|

    >**Remarque** : notez le nom de domaine initial. Vous en aurez besoin plus tard dans ce labo.

5. Cliquez sur **Vérifier + créer**, puis cliquez sur **Créer**.
6. Ajoutez un code Captcha dans le volet **Aide pour prouver que vous n’êtes pas un robot**, puis cliquez sur le bouton **Envoyer**. 

    >**Remarque** : Attendez que le nouveau locataire soit créé. Utilisez l’icône **Notification** pour surveiller l’état du déploiement. 


#### Tâche 2 : Activer l’essai Azure AD Premium P2

Dans cette tâche, vous allez vous inscrire à l’évaluation gratuite d’Azure AD Premium P2. 

1. Dans la barre d’outils du portail Azure, cliquez sur l’icône **Répertoire + abonnement** située à droite de l’icône Cloud Shell. 

2. Dans le volet **Répertoire + abonnement**, cliquez sur le locataire **Nouvellement créé AdatumLab500-04**, puis cliquez sur le bouton **Basculer** pour le définir comme répertoire actif.

    >**Remarque** : vous devrez peut-être actualiser la fenêtre du navigateur si l’entrée **AdatumLab500-04** n’apparaît pas dans la liste de filtres **Annuaire + abonnement**.

3. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Azure Active Directory** et appuyez sur la touche **Entrée**. Dans le volet **AdatumLab500-04**, dans la section **Gérer**, cliquez sur **Licences**.

4. Dans le volet **Vue d’ensemble des licences\|** , dans la section **Gérer**, cliquez sur **Tous les produits**, puis sur **+Essayer/Acheter**.

5. Dans le volet **Activer**, dans la section Azure AD Premium P2, cliquez sur **Évaluation gratuite**, puis sur **Activer**.


#### Tâche 3 : Créer des utilisateurs et des groupes Azure AD.

Dans cette tâche, vous allez créer trois utilisateurs : aaduser1 (Administrateur global), aaduser2 (utilisateur) et aaduser3 (utilisateur). Vous aurez besoin du nom d’utilisateur principal et du mot de passe de chaque utilisateur pour des tâches ultérieures. 

1. Revenez au volet **AdatumLab500-04 Azure Active Directory** et, dans la section **Gérer**, cliquez sur **Utilisateurs**.

2. Dans le volet **Utilisateurs \| Tous les utilisateurs**, cliquez sur **+ Nouvel utilisateur**, puis sur **Créer un utilisateur**. 

3. Dans le volet **Nouvel utilisateur**, vérifiez que l’option **Créer un utilisateur** est sélectionnée, spécifiez les paramètres suivants sous l’onglet Informations de base (laissez les autres paramètres avec leurs valeurs par défaut) et cliquez sur **Suivant : Propriétés > ** :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’utilisateur principal|**aaduser1**|
   |Nom|**aaduser1**|
   |Mot de passe|vérifiez que l’option **Générer automatiquement un mot de passe** est cochée|
   
      >**Remarque** : Notez le nom d’utilisateur principal en entier. Vous pouvez copier sa valeur en cliquant sur le bouton **Copier dans le Presse-papiers** sur le côté droit de la liste déroulante affichant le nom de domaine. Vous en aurez besoin plus tard dans ce labo.
   
      >**Remarque** : prenez note du mot de passe de l’utilisateur. Vous pouvez copier sa valeur en cliquant sur le bouton **Copier dans le Presse-papiers** sur le côté droit de la zone de texte. Vous en aurez besoin plus tard dans ce labo. 
   
4. Sous l’onglet **Propriétés**, faites défiler vers le bas et spécifiez l’emplacement d’utilisation : **États-Unis** (laissez les autres paramètres avec leurs valeurs par défaut) et cliquez sur **Suivant : Affectations > **.

5. Sous l’onglet **Attributions**, cliquez sur **+ Ajouter un rôle**, puis recherchez et sélectionnez **Administrateur général**. Cliquez sur **Sélectionner**, sur **Vérifier + créer**, puis sur **Créer**.

6. Revenez dans le volet **Utilisateurs \| Tous les utilisateurs**, cliquez sur **+Nouvel utilisateur**. 

7. Dans le volet **Nouvel utilisateur**, vérifiez que l’option **Créer un utilisateur** est sélectionnée, spécifiez les paramètres suivants (laissez les autres paramètres avec leurs valeurs par défaut) et cliquez sur **Suivant : Propriétés > ** :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’utilisateur principal|**aaduser2**|
   |Nom|**aaduser2**|
   |Mot de passe|vérifiez que l’option **Générer automatiquement un mot de passe** est cochée| 

    >**Remarque** : Notez le nom d’utilisateur principal en entier et le mot de passe.

8. Sous l’onglet **Propriétés**, faites défiler vers le bas et spécifiez l’emplacement d’utilisation : **États-Unis** (laissez les autres paramètres avec leurs valeurs par défaut) et cliquez sur **Vérifier + créer**, puis sur **Créer**.

9. Revenez dans le volet **Utilisateurs \| Tous les utilisateurs**, cliquez sur **+Nouvel utilisateur**. 

10. Dans le volet **Nouvel utilisateur**, vérifiez que l’option **Créer un utilisateur** est sélectionnée, spécifiez les paramètres suivants (laissez les autres paramètres avec leurs valeurs par défaut) et cliquez sur **Suivant : Propriétés > ** :

    |Paramètre|Valeur|
    |---|---|
    |Nom d’utilisateur principal|**aaduser3**|
    |Nom|**aaduser3**|
    |Mot de passe|vérifiez que l’option **Générer automatiquement un mot de passe** est cochée|

    >**Remarque** : Notez le nom d’utilisateur principal en entier et le mot de passe.

11. Sous l’onglet **Propriétés**, faites défiler vers le bas et spécifiez l’emplacement d’utilisation : **États-Unis** (laissez les autres paramètres avec leurs valeurs par défaut) et cliquez sur **Vérifier + créer**, puis sur **Créer**.

    >**Remarque** : À ce stade, vous devez disposer de trois nouveaux utilisateurs répertoriés sur la page **Utilisateurs** . 
    
#### Tâche 4 : Affecter des licences Azure AD Premium P2 aux utilisateurs Azure AD

Dans cette tâche, vous allez affecter chaque utilisateur à la licence Azure Active Directory Premium P2.

1. Dans le volet **Utilisateurs \| Tous les utilisateurs**, cliquez sur l’entrée représentant votre compte d’utilisateur. 

2. Dans le panneau affichant les propriétés de votre compte d’utilisateur, cliquez sur **Modifier les propriétés**.  Vérifiez que le Lieu d’utilisation est défini sur **États-Unis**. Si ce n’est pas le cas, définissez le lieu d’utilisation et cliquez sur **Enregistrer**.

3. Revenez au volet **AdatumLab500-04 Azure Active Directory** et, dans la section **Gérer**, cliquez sur **Licences**.

4. Dans le volet **Licences \| Vue d’ensemble**, cliquez sur **Tous les produits**, cochez la case **Azure Active Directory Premium P2** et cliquez sur **+Affecter**.

5. Dans le volet **Affecter une licence**, cliquez sur **+Ajouter des utilisateurs et des groupes**.

6. Dans le volet **Utilisateurs**, sélectionnez **aaduser1**, **aaduser2**, **aaduser3** et votre compte d’utilisateur, puis cliquez sur **Sélectionner**.

7. Revenez dans le panneau **Attribuer des licences**, cliquez sur **Options d’affectation** et vérifiez que toutes les options sont activées, puis cliquez sur **Vérifier + attribuer** et sur **Attribuer**.

8. Déconnectez-vous du Portail Azure et connectez-vous à l’aide du même compte. (Cette étape est nécessaire pour que l’attribution de licence prenne effet.)

    >**Remarque** : À ce stade, vous avez attribué Azure Active Directory Premium P2 licences à tous les comptes d’utilisateur que vous utiliserez dans ce laboratoire. Veillez à vous déconnecter, puis à vous reconnecter. 

#### Tâche 5 : Configurer les paramètres de l’authentification multifacteur Azure.

Dans cette tâche, vous allez configurer l’authentification multifacteur et activer l’authentification multifacteur pour aaduser1. 

1. Dans le portail Azure, revenez au volet du locataire Azure Active Directory **AdatumLab500-04**.

    >**Remarque** : Assurez-vous de bien utiliser le locataire Azure AD AdAtumLab500-04.

2. Sur le volet de locataire **AdatumLab500-04** Azure Active Directory, dans la section **Gérer**, cliquez sur **Sécurité**.

3. Dans le volet **Prise en main de la sécurité\|** , dans la section **Gérer**, cliquez sur **Authentification multifacteur**.

4. Dans le volet **Prise en main de l’authentification multifacteur\|** , cliquez sur le lien des **paramètres d’authentification multifacteur basés sur le cloud supplémentaires**. 

    >**Remarque** : Cela ouvre un nouvel onglet de navigateur, affichant la page **d’authentification multifacteur**.

5. Dans la page d’**authentification multifacteur**, cliquez sur **Paramètres du service**. Examinez les **options de vérification**. Notez que **Message texte vers le téléphone**, **Notification via l’application mobile** et **Code de vérification à partir d’une application mobile ou d’un jeton matériel** sont activés. Cliquez sur **Enregistrer**, puis sur **Fermer**.

6. Revenez à l’onglet **Utilisateurs**, cliquez sur l’entrée **aaduser1**, cliquez sur le lien **Activer**, puis lorsque vous y êtes invité, cliquez sur **Activer l’authentification multifacteur** et sur **Fermer**.

7. Notez que la colonne de l’**état Multi-Factor Auth** pour **aaduser1** est désormais **activée**.

8. Cliquez sur **aaduser1** et notez que, à ce stade, vous avez également l’option **Appliquer**. 

    >**Remarque** : La modification de l’état de l’utilisateur activé vers l’application a un impact uniquement sur les applications intégrées Azure AD héritées qui ne prennent pas en charge l’authentification multifacteur Azure et, une fois que l’état est appliqué, nécessite l’utilisation des mots de passe d’application.

9. Avec l’entrée **aaduser1** sélectionnée, cliquez sur **Gérer les paramètres utilisateur** et passez en revue les options disponibles : 

   - Require selected users to provide contact methods again :

   - Delete all existing app passwords generated by the selected users :

   - Restaurer l’authentification multifacteur pour tous les appareils mémorisés.

10. Cliquez sur **Annuler** et revenez à l’onglet du navigateur affichant le volet **Authentification multifacteur \| Démarrage** dans le portail Azure.

11. Dans la section **Paramètres**, cliquez sur **Alerte à la fraude**.

12. Dans le volet **Alerte de fraude multifacteur d’authentification\|** , configurez les paramètres suivants :

    |Paramètre|Valeur|
    |---|---|
    |Autoriser les utilisateurs à envoyer des alertes de fraude|**Activé**|
    |Bloquer automatiquement les utilisateurs qui signalent une fraude|**Activé**|
    |Code pour signaler une fraude lors du message d’accueil initial|**0**|

13. Cliquez sur **Enregistrer**.

    >**Remarque** : À ce stade, vous avez activé l’authentification multifacteur pour aaduser1 et configuré les paramètres d’alerte de fraude. 

14. Revenez au volet du locataire Azure Active Directory **AdatumLab500-04**. Dans la section **Gérer**, cliquez sur **Propriétés**, puis sur le lien **Gérer les paramètres de sécurité par défaut** en bas du volet. Dans le volet **Activer les paramètres de sécurité par défaut**, cliquez sur **Désactivé**. Sélectionnez **Mon organisation utilise l’accès conditionnel** comme *Motif de désactivation*, cliquez sur **Enregistrer**, lisez l’avertissement, puis cliquez sur **Désactiver**.

    >**Remarque** : vérifiez que vous êtes connecté au locataire Azure AD **AdatumLab500-04**. Vous pouvez utiliser le filtre **Annuaire + abonnement** pour basculer entre les locataires Azure AD. Vérifiez que vous êtes connecté en tant qu’utilisateur avec le rôle Administrateur général dans le locataire Azure AD.

#### Tâche 6 : Valider la configuration de l’authentification multifacteur

Dans cette tâche, vous allez valider la configuration de l’authentification multifacteur en testant la connexion du compte d’utilisateur aaduser1. 

1. Ouvrez une fenêtre de navigation InPrivate.

2. Accédez au portail Azure, **`https://portal.azure.com/`**, et connectez-vous avec le compte d’utilisateur **aaduser1**. 

    >**Remarque** : pour vous connecter, vous devez fournir un nom complet du compte d’utilisateur **aaduser1**, y compris le nom de domaine DNS du locataire Azure AD dont vous avez pris note précédemment dans ce labo. Ce nom d’utilisateur est au format aaduser1@`<your_tenant_name>`.onmicrosoft.com, où `<your_tenant_name>` est l’espace réservé représentant votre nom de locataire Azure AD unique. 

3. Lorsque vous y êtes invité, dans la boîte de dialogue **Plus d’informations requises**, cliquez sur **Suivant**.

    >**Remarque** : la session du navigateur est redirigée vers la page de **vérification de sécurité supplémentaire**.

4. Dans la page **Conserver votre compte sécurisé**, sélectionnez le lien de la **méthode que je souhaite configurer**, dans la liste déroulante **Quelle méthode souhaitez-vous utiliser ?** sélectionnez **Téléphone**, puis cliquez sur **Confirmer**.

5. Dans la page **Conserver votre compte sécurisé**, sélectionnez votre pays ou votre région, tapez votre numéro de téléphone mobile dans la zone **Entrée de numéro de téléphone**, assurez-vous que l’option **M’envoyer moi-même un message texte** sélectionnée, puis cliquez sur **Suivant**.
 
6. Dans la page **Conserver votre compte sécurisé**, tapez le code que vous avez reçu dans le message texte sur votre téléphone mobile, puis cliquez sur **Suivant**.

7. Dans la page **Conserver votre compte sécurisé**, vérifiez que la vérification a réussi et cliquez sur **Suivant**.

8. Si vous êtes invité à spécifier une autre méthode d’authentification, cliquez sur **Je souhaite utiliser une autre méthode**, sélectionnez **E-mail** dans la liste déroulante, cliquez sur **Confirmer**, indiquez l’adresse e-mail que vous avez l’intention d’utiliser, puis cliquez sur **Suivant**. Une fois que vous recevez l’e-mail correspondant, identifiez le code dans le corps de l’e-mail, indiquez-le, puis cliquez sur **Terminé**.

9. Modifiez votre mot de passe lorsque vous y êtes invité. Veillez à noter le nouveau mot de passe.

10. Vérifiez que vous vous êtes bien connecté au Portail Azure.

11. Déconnectez-vous en tant que **aaduser1** et fermez la fenêtre du navigateur InPrivate.

> Résultat : vous avez créé un locataire AD, configuré des utilisateurs AD, configuré MFA et testé l’expérience MFA pour un utilisateur. 


### Exercice 3 : Implémenter des stratégies d’accès conditionnel Azure AD 

### Durée estimée : 15 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes : 

- Tâche 1 : Configurer une stratégie d’accès conditionnel.
- Tâche 2 : Tester la stratégie d’accès conditionnel.

#### Tâche 1 : configurer une stratégie d’accès conditionnel. 

Dans cette tâche, vous allez examiner les paramètres de stratégie d’accès conditionnel et créer une stratégie qui requiert l’authentification MFA lors de la connexion au Portail Azure. 

1. Dans le portail Azure, revenez au volet du locataire Azure Active Directory **AdatumLab500-04**.

2. Dans le volet **AdatumLab500-04**, dans la section **Gérer**, cliquez sur **Sécurité**.

3. Dans le volet **prise en main de la sécurité\|** , dans la section **Protéger**, cliquez sur **Accès conditionnel**. Dans le volet de navigation de gauche, cliquez sur **Stratégies**.

4. Dans le panneau **Accès conditionnel \| Stratégies**, cliquez sur **+ Nouvelle stratégie**. 

5. Dans le volet **Nouveau**, configurez les éléments suivants :

   - Dans la zone de texte **Nom**, tapez **AZ500Policy1**.
    
   - Sous **Utilisateurs**, cliquez sur **0 utilisateur et groupe sélectionné**. Sur le côté droit, sous Inclure, activez **Sélectionner des utilisateurs et des groupes** >> cochez la case **Utilisateurs et groupes**. Dans le volet **Sélectionner des utilisateurs et des groupes**, cochez la case **aaduser2** et cliquez sur **Sélectionner**.
    
   - Sous **Ressources cibles**, cliquez sur **Aucune ressource cible sélectionnée** et sur **Sélectionner des applications**. Sous Sélectionner, cliquez sur **Aucune**. Dans le volet **Sélectionner**, cochez la case correspondant à **Gestion Microsoft Azure** et cliquez sur **Sélectionner**. 

     >**Remarque** : Examinez l’avertissement indiquant que cette stratégie affecte l’accès au Portail Azure.
    
   - Sous **Conditions**, cliquez sur **0 condition sélectionnée**, puis sous **Risque de connexion**, cliquez sur **Non configuré**. Dans le volet **Risque de connexion**, passez en revue les niveaux de risque, mais n’apportez aucune modification et fermez le volet **Risque de connexion**.
    
   - Sous **Plateformes d’appareils**, cliquez sur **Non configuré**, passez en revue les plateformes d’appareils qui peuvent être incluses, mais n’apportez aucune modification et cliquez sur **Terminé**.
    
   - Sous **Localisations**, cliquez sur **Non configuré** et passez en revue les options de localisation sans apporter de modifications.
    
   - Sous **Accorder** dans la section **Contrôles d’accès**, cliquez sur **0 contrôle sélectionné**. Dans le volet **Accorder**, cochez la case **Exiger l’authentification multifacteur** et cliquez sur **Sélectionner**
    
   - Définissez **Activer la stratégie** sur **Activé**.

6. Dans le panneau **Nouveau**, cliquez sur **Créer**. 

    >**Remarque** : À ce stade, vous disposez d’une stratégie d’accès conditionnel qui nécessite l’authentification multifacteur pour se connecter au Portail Azure. 

#### Tâche 2 - Tester la stratégie d’accès conditionnel.

Dans cette tâche, vous allez vous connecter au Portail Azure en tant que **aaduser2** et vérifier que l’authentification multifacteur est requise. Vous allez également supprimer la stratégie avant de passer à l’exercice suivant. 

1. Ouvrez une fenêtre de Microsoft Edge InPrivate.

2. Dans la nouvelle fenêtre du navigateur, accédez au portail Azure, **`https://portal.azure.com/`**, et connectez-vous avec le compte d’utilisateur **aaduser2**.

3. Lorsque vous y êtes invité, dans la boîte de dialogue **Plus d’informations requises**, cliquez sur **Suivant**.

    >**Remarque** : La session du navigateur est redirigée vers la page **Protéger votre compte**.
    
4. Dans la page **Conserver votre compte sécurisé**, sélectionnez le lien de la **méthode que je souhaite configurer**, dans la liste déroulante **Quelle méthode souhaitez-vous utiliser ?** sélectionnez **Téléphone**, puis cliquez sur **Confirmer**.

5. Dans la page **Conserver votre compte sécurisé**, sélectionnez votre pays ou votre région, tapez votre numéro de téléphone mobile dans la zone **Entrée de numéro de téléphone**, assurez-vous que l’option **M’envoyer moi-même un message texte** sélectionnée, puis cliquez sur **Suivant**.

6. Dans la page **Conserver votre compte sécurisé**, tapez le code que vous avez reçu dans le message texte sur votre téléphone mobile, puis cliquez sur **Suivant**.

7. Dans la page **Conserver votre compte sécurisé**, vérifiez que la vérification a réussi et cliquez sur **Suivant**.

8. Dans la page **Protéger votre compte**, cliquez sur **Terminé**.

9. Modifiez votre mot de passe lorsque vous y êtes invité. Veillez à noter le nouveau mot de passe.

10. Vérifiez que vous vous êtes bien connecté au Portail Azure.

11. Déconnectez-vous en tant que **aaduser2** et fermez la fenêtre de navigateur InPrivate.

    >**Remarque** : Vous avez maintenant vérifié que la stratégie d’accès conditionnel nouvellement créée applique l’authentification multifacteur lorsque aaduser2 se connecte au Portail Azure.

12. De retour dans la fenêtre du navigateur avec le portail Azure, revenez au volet du locataire Azure Active Directory **AdatumLab500-04**.

13. Dans le volet **AdatumLab500-04**, dans la section **Gérer**, cliquez sur **Sécurité**.

14. Dans le volet **prise en main de la sécurité\|** , dans la section **Protéger**, cliquez sur **Accès conditionnel**. Dans le volet de navigation de gauche, cliquez sur **Stratégies**.

15. Dans le volet **Stratégies d’accès conditionnel\|** , cliquez sur les points de suspension en regard de **AZ500Policy1**, cliquez sur **Supprimer**, puis, lorsque vous êtes invité à confirmer, cliquez sur **Oui**.

    >**Remarque** : Résultat : Dans cet exercice, vous implémentez une stratégie d’accès conditionnel afin d’exiger l’authentification MFA lorsqu’un utilisateur se connecte au portail Azure. 

>Résultat : Vous avez configuré et testé l’accès conditionnel Azure AD.

### Exercice 4 : Déployer des stratégies basées sur les risques dans l’Accès conditionnel

### Durée estimée : 30 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Consulter les options d’Azure AD Identity Protection dans le portail Azure
- Tâche 2 : Configurer une stratégie de risque utilisateur
- Tâche 3 : Configurer une stratégie de connexion à risque
- Tâche 4 : Simuler des événements à risque par rapport aux stratégies Azure AD Identity Protection 
- Tâche 5 : Examiner les rapports Azure AD Identity Protection

#### Tâche 1 : Activer Azure AD Identity Protection

Dans cette tâche, vous allez consulter les options d’Azure AD Identity Protection dans le Portail Azure. 

1. Si besoin, connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : vérifiez que vous êtes connecté au locataire Azure AD **AdatumLab500-04**. Vous pouvez utiliser le filtre **Annuaire + abonnement** pour basculer entre les locataires Azure AD. Vérifiez que vous êtes connecté en tant qu’utilisateur avec le rôle Administrateur général dans le locataire Azure AD.

#### Tâche 2 : Configurer une stratégie de risque utilisateur

Dans cette tâche, vous allez créer une stratégie de risque utilisateur. 

1. Accédez à **AdatumLab500-04** Locataire Azure AD > **Sécurité** > **Accès conditionnel** > **Stratégies**.

2. Cliquez sur **+ Nouvelle stratégie**.

3. Tapez le nom de stratégie suivant dans la zone de texte **Nom**, puis tapez **AZ500Policy2**.

4. Sous **Affectations** > **Utilisateurs**, cliquez sur **0 utilisateur et groupe sélectionné**.

5. Sous **Inclure**, cliquez sur **Sélectionner des utilisateurs et des groupes** et sur **Utilisateurs et groupes**, sélectionnez **aaduser2** et **aaduser3**, puis cliquez sur **Sélectionner**.

6. Sous **Exclure**, cliquez sur **Utilisateurs et groupes**, sélectionnez **aaduser1** et cliquez sur **Sélectionner**. 

7. Sous **Ressources cibles**, cliquez sur **Aucune ressource cible sélectionnée**, vérifiez que **Applications cloud** est sélectionné dans la liste déroulante puis, sous **Inclure**, sélectionnez **Toutes les applications cloud**.

8. Sous **Conditions**, cliquez sur **0 condition sélectionnée**, puis sous **Utilisateur à risque**, cliquez sur **Non configuré**. Dans le panneau **Utilisateur à risque**, définissez **Configurer** sur **Oui**.

9. Sous **Configurer les niveaux de risque utilisateur nécessaires à l’application de la stratégie**, sélectionnez **Élevé**.

10. Cliquez sur **Done**.

11. Sous **Accorder** dans la section **Contrôles d’accès**, cliquez sur **0 contrôle sélectionné**. Dans le panneau **Accorder**, vérifiez que l’option **Autoriser l’accès** est sélectionnée.

12. Sélectionnez **Exiger l’authentification multifacteur** et **Exiger la modification du mot de passe**.

13. Cliquez sur **Sélectionner**.

14. Sous **Session**, cliquez sur **0 contrôle sélectionné**. Sélectionnez **Fréquence de connexion**, puis **À chaque fois**.

15. Cliquez sur **Sélectionner**.

16. Vérifiez que l’option **Activer la stratégie** est définie sur **Rapport uniquement**.

17. Cliquez sur **Créer** pour activer votre stratégie.

#### Tâche 3 : Configurer une stratégie de connexion à risque

1. Accédez à **AdatumLab500-04** Locataire Azure AD > **Sécurité** > **Accès conditionnel**> **Stratégies**.

2. Sélectionnez **+ Nouvelle stratégie**.

3. Tapez le nom de stratégie suivant dans la zone de texte **Nom**, puis tapez **AZ500Policy3**.

4. Sous **Affectations** > **Utilisateurs**, cliquez sur **0 utilisateur et groupe sélectionné**.

5. Sous **Inclure**, cliquez sur **Sélectionner des utilisateurs et des groupes** et sur **Utilisateurs et groupes**, sélectionnez **aaduser2** et **aaduser3**, puis cliquez sur **Sélectionner**.

6. Sous **Exclure**, cliquez sur **Utilisateurs et groupes**, sélectionnez **aaduser1** et cliquez sur **Sélectionner**. 

7. Sous **Ressources cibles**, cliquez sur **Aucune ressource cible sélectionnée**, vérifiez que **Applications cloud** est sélectionné dans la liste déroulante puis, sous **Inclure**, sélectionnez **Toutes les applications cloud**.

8. Sous **Conditions**, cliquez sur **0 condition sélectionnée**, puis sous **Risque de connexion**, cliquez sur **Non configuré**. Dans le panneau **Risque de connexion**, définissez **Configurer** sur **Oui**.

9. Sous **Sélectionner le niveau de risque de connexion auquel cette stratégie s’applique**, sélectionnez **Haut** et **Moyen**.

10. Cliquez sur **Done**.

11. Sous **Accorder** dans la section **Contrôles d’accès**, cliquez sur **0 contrôle sélectionné**. Dans le panneau **Accorder**, vérifiez que l’option **Autoriser l’accès** est sélectionnée.   

12. Sélectionnez **Exiger l’authentification multifacteur**.

13. Cliquez sur **Sélectionner**.

14. Sous **Session**, cliquez sur **0 contrôle sélectionné**. Sélectionnez **Fréquence de connexion**, puis **À chaque fois**.

15. Cliquez sur **Sélectionner**.

16. Confirmez vos paramètres et réglez **Activer la stratégie** sur **Activé**.

17. Cliquez sur **Créer** pour activer votre stratégie.

#### Tâche 4 : Simuler des événements à risque par rapport aux stratégies Azure AD Identity Protection 

> Avant de commencer cette tâche, vérifiez que le déploiement de modèle que vous avez démarré dans l’exercice 1 de ce labo est terminé. Le déploiement inclut une machine virtuelle Azure nommée **az500-04-vm1**. 

1. Dans le portail Azure, définissez le filtre **Annuaire + abonnement** sur le locataire Azure AD associé à l’abonnement Azure dans lequel vous avez déployé la machine virtuelle Azure **az500-04-vm1**.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Machines virtuelles** et appuyez sur la touche **Entrée**.

3. Dans le volet **Machines virtuelles**, cliquez sur l’entrée **az500-04-vm1**. 

4. Dans le panneau **az500-04-vm1**, cliquez sur **Se connecter**. Vérifiez que vous êtes sous l’onglet **RDP**.

5. Cliquez sur **Télécharger le fichier RDP**, puis utilisez-le pour vous connecter à la machine virtuelle Azure **az500-04-vm1** via le Bureau à distance. Lorsque vous êtes invité à vous authentifier, fournissez les informations d’identification suivantes :

    |Paramètre|Valeur|
    |---|---|
    |Nom d’utilisateur|**Étudiant**|
    |Mot de passe|**Utilisez votre mot de passe personnel créé dans le labo 04 > Exercice 1 > Tâche 1 > Étape 9.**|

    >**Remarque** : attendez que la session Bureau à distance et le **Gestionnaire de serveur** se chargent.  

    >**Remarque** : les étapes suivantes sont effectuées dans la session Bureau à distance sur la machine virtuelle Azure **az500-04-vm1**. 

6. Dans **Gestionnaire de serveur**, cliquez sur **Serveur local**, puis sur **Configuration de sécurité renforcée d’Internet Explorer**.

7. Dans la boîte de dialogue **Configuration de sécurité renforcée d’Internet Explorer**, définissez les deux options sur **Désactivé** et cliquez sur **OK**.

8. Démarrez **Internet Explorer**, cliquez sur l’icône de roue dentée dans la barre d’outils, dans le menu déroulant, cliquez sur **Sécurité**, puis sur **Navigation InPrivate**.

9. Dans la fenêtre InPrivate d’Internet Explorer, accédez au navigateur ToR Project à l’adresse **https://www.torproject.org/projects/torbrowser.html.en** .

10. Téléchargez et installez la version Windows du navigateur ToR avec les paramètres par défaut. 

11. Une fois l’installation terminée, démarrez le navigateur ToR, utilisez l’option **Se connecter** dans la page initiale et accédez au Volet d’accès à l’application sur **https://myapps.microsoft.com**.

12. Lorsque vous y êtes invité, essayez de vous connecter avec le compte **aaduser3**. 

    >**Remarque** : vous verrez un message indiquant que **votre connexion a été bloquée**. C’est normal, étant donné que ce compte n’est pas configuré avec l’authentification multifacteur, ce qui est obligatoire en raison du nombre accru de connexions à risque associées à l’utilisation du navigateur ToR.

13. Dans le navigateur ToR, sélectionnez la flèche vers la gauche pour vous connecter avec le compte **aaduser1** que vous avez créé et configuré pour l’authentification multifacteur dans ce labo.

    >**Remarque** : cette fois, vous verrez le message d’**activité suspecte détectée**. Là encore, c’est normal, car ce compte est configuré avec l’authentification multifacteur. Compte tenu du risque de connexion accru associé à l’utilisation de ToR Browser, vous devez utiliser l’authentification multifacteur.

14. Utilisez l’option **Vérifier** et spécifiez si vous souhaitez vérifier votre identité via un message texte ou un appel.

15. Effectuez la vérification et vérifiez que vous vous êtes connecté au volet d’accès de l’application.

16. Fermez la session Bureau à distance. 

    >**Remarque** : À ce stade, vous avez essayé deux méthodes de connexion. Vous allez ensuite examinez les rapports Azure AD Identity Protection.

#### Tâche 5 : Examiner les rapports Azure AD Identity Protection

Dans cette tâche, vous allez examiner les rapports Azure AD Identity Protection générés suite aux connexions via le navigateur ToR.

1. De retour dans le portail Azure, utilisez le filtre **Annuaire + abonnement** pour basculer vers le locataire **AdatumLab500-04** Azure Active Directory.

2. Dans le volet **AdatumLab500-04**, dans la section **Gérer**, cliquez sur **Sécurité**.

3. Dans le volet **Mise en route de la sécurité\|** , dans la section **Rapport**, cliquez sur **Utilisateurs à risque**. 

4. Passez en revue le rapport et identifiez les entrées faisant référence au compte d’utilisateur **aaduser3**.

5. Dans le volet **Mise en route de la sécurité\|** , dans la section **Rapport**, cliquez sur **Connexions à risque**. 

6. Passez en revue le rapport et identifiez les entrées correspondant au compte utilisateur **aaduser3**.

7. Sous **Rapports**, cliquez sur **Détections de risque**.

8. Passez en revue le rapport et identifiez les entrées représentant la connexion à partir de l’adresse IP anonyme générée par le navigateur ToR. 

    >**Remarque** : Il peut s’écouler 10 à 15 minutes avant que les risques n’apparaissent dans les rapports.

> **Result** : Vous avez activé Azure AD Identity Protection, configuré une stratégie de risque utilisateur et une stratégie de risque de connexion, ainsi que la configuration d’Azure AD Identity Protection validée en simulant des événements à risque.

**Nettoyer les ressources**

> Nous devons supprimer les ressources de protection des identités que vous n’utilisez plus. 

Utilisez les étapes suivantes pour désactiver les stratégies de protection des identités dans le locataire Azure AD **AdatumLab500-04**.

1. Dans le portail Azure, revenez au volet du locataire Azure Active Directory **AdatumLab500-04**.

2. Dans le volet **AdatumLab500-04**, dans la section **Gérer**, cliquez sur **Sécurité**.

3. Dans le volet **Prise en main de la sécurité\|** , dans la section **Protéger**, cliquez sur **Identity Protection**.

4. Dans le volet **Vue d’ensemble de Identity Protection\|** , cliquez sur **Stratégie de risque utilisateur**.

5. Dans le volet **Identity Protection \| Stratégie d’utilisateur à risque**, définissez **Appliquer la stratégie** sur **Désactivé**, puis cliquez sur **Enregistrer**.

6. Dans le volet **Identity Protection \| Stratégie de risque utilisateur**, cliquez sur **Stratégie de risque de connexion**.

7. Dans le volet **Identity Protection \| Stratégie de connexion à risque**, définissez **Appliquer la stratégie** sur **Désactivé**, puis cliquez sur **Enregistrer**.

Suivez les étapes suivantes pour arrêter la machine virtuelle Azure que vous avez approvisionnée précédemment dans le laboratoire.

1. Dans le portail Azure, définissez le filtre **Annuaire + abonnement** sur le locataire Azure AD associé à l’abonnement Azure dans lequel vous avez déployé la machine virtuelle Azure **az500-04-vm1**.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Machines virtuelles** et appuyez sur la touche **Entrée**.

3. Dans le volet **Machines virtuelles**, cliquez sur l’entrée **az500-04-vm1**. 
 
4. Dans le volet **az500-04-vm1**, cliquez sur **Arrêter** et, lorsque vous êtes invité à confirmer, cliquez sur **OK** 

>  Ne supprimez aucune ressource approvisionnée dans ce labo, car le laboratoire PIM a une dépendance sur eux.

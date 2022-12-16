---
lab:
  title: 01 - Contrôle d’accès en fonction du rôle
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-01-role-based-access-control"></a>Lab 01 : Contrôle d’accès en fonction du rôle
# <a name="student-lab-manual"></a>Manuel de labo pour l’étudiant

## <a name="lab-scenario"></a>Scénario du labo

Vous avez été invité à créer une preuve de concept montrant comment les utilisateurs et les groupes Azure sont créés. Et aussi comment le contrôle d’accès en fonction du rôle est utilisé pour attribuer des rôles à des groupes. Plus précisément, vous devez :

- Créer un groupe Administrateurs seniors contenant le compte d’utilisateur de Joseph Price en tant que membre.
- Créer un groupe Administrateurs juniors contenant le compte d’utilisateur d’Isabel Garcia en tant que membre.
- Créer un groupe Service Desk contenant le compte d’utilisateur de Dylan Williams en tant que membre.
- Attribuer le rôle Contributeur de machines virtuelles au groupe Service Desk. 

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## <a name="lab-objectives"></a>Objectifs du labo

Dans ce labo, vous allez effectuer les exercices suivants :

- Exercice 1 : Créer le groupe Administrateurs seniors avec le compte d’utilisateur Joseph Price comme membre (portail Azure). 
- Exercice 2 : Créer le groupe Administrateurs juniors avec le compte d’utilisateur d’Isabel Garcia en tant que membre (PowerShell).
- Exercice 3 : Créer le groupe Service Desk avec l’utilisateur Dylan Williams comme membre (Azure CLI). 
- Exercice 4 : Attribuer le rôle Contributeur de machines virtuelles au groupe Service Desk.

## <a name="role-based-access-control-architecture-diagram"></a>Diagramme d’architecture de contrôle d’accès en fonction du rôle

![image](https://user-images.githubusercontent.com/91347931/157751243-5aa6e521-9bc1-40af-839b-4fd9927479d7.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-create-the-senior-admins-group-with-the-user-account-joseph-price-as-its-member"></a>Exercice 1 : Créer le groupe Senior Admins avec le compte d’utilisateur Joseph Price comme membre. 

#### <a name="estimated-timing-10-minutes"></a>Durée estimée : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Utiliser le portail Azure afin de créer un compte d’utilisateur pour Joseph Price.
- Tâche 2 : Utiliser le portail Azure pour créer un groupe Senior Admins et ajouter le compte d’utilisateur de Joseph Price au groupe.

#### <a name="task-1-use-the-azure-portal-to-create-a-user-account-for-joseph-price"></a>Tâche 1 : Utiliser le portail Azure afin de créer un compte d’utilisateur pour Joseph Price. 

Dans cette tâche, vous allez créer un compte d’utilisateur pour Joseph Price. 

1. Démarrez une session de navigateur et connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte titulaire du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo, et du rôle Administrateur général dans le locataire Azure AD associé à cet abonnement.

2. Dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page du portail Azure, tapez **Azure Active Directory**, puis appuyez sur la touche **Entrée**.

3. Dans le panneau **Vue d’ensemble** du locataire Azure Active Directory, dans la section **Gérer**, sélectionnez **Utilisateurs**, puis **+ Nouvel utilisateur**.

4. Dans le panneau **Nouvel utilisateur**, assurez-vous que l’option **Créer un utilisateur** est sélectionnée et spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’utilisateur|**Joseph**|
   |Nom|**Joseph Price**|

5. Cliquez sur l’icône Copier en regard du **Nom d’utilisateur** pour copier le nom complet de l’utilisateur.

6. Vérifiez que le mot de passe **généré automatiquement** est sélectionné, activez la case à cocher **Afficher le mot de passe** pour identifier le mot de passe généré automatiquement. Vous devrez fournir ce mot de passe, ainsi que le nom d'utilisateur, à Joseph. 

7. Cliquez sur **Créer**.

8. Actualisez le volet **Utilisateurs \| Tous les utilisateurs** pour vérifier que le nouvel utilisateur a été créé dans votre locataire Azure AD.

#### <a name="task2-use-the-azure-portal-to-create-a-senior-admins-group-and-add-the-user-account-of-joseph-price-to-the-group"></a>Tâche 2 : Utiliser le portail Azure pour créer un groupe Senior Admins et ajouter le compte d’utilisateur de Joseph Price au groupe.

Dans cette tâche, vous allez créer le groupe *Senior Admins*, ajouter le compte d’utilisateur de Joseph Price au groupe, puis le configurer comme propriétaire du groupe.

1. Dans le portail Azure, revenez au volet affichant votre locataire Azure Active Directory. 

2. Dans la section **Gérer**, cliquez sur **Groupes**, puis sélectionnez **+ Nouveau groupe**.
 
3. Dans le volet **Nouveau groupe**, spécifiez les paramètres suivants (conservez les valeurs par défaut pour les autres) :

   |Paramètre|Valeur|
   |---|---|
   |Type de groupe|**Sécurité**|
   |Nom du groupe|**Senior Admins**|
   |Type d’appartenance|**Affecté**|
    
4. Dans le volet **Ajouter des propriétaires**, cliquez sur le lien **Aucun propriétaire sélectionné**, sélectionnez **Joseph Price**, puis cliquez sur **Sélectionner**.

5. Dans le volet **Ajouter des membres**, cliquez sur le lien **Aucun membre sélectionné**, sélectionnez **Joseph Price**, puis cliquez sur **Sélectionner**.

6. De retour dans le volet **Nouveau groupe**, cliquez sur **Créer**.

> Résultat : Vous avez utilisé le portail Azure pour créer un utilisateur et un groupe, et affecté l’utilisateur au groupe. 

### <a name="exercise-2-create-a-junior-admins-group-containing-the-user-account-of-isabel-garcia-as-its-member"></a>Exercice 2 : Créer un groupe Junior Admins contenant le compte d’utilisateur d’Isabel Garcia en tant que membre.

#### <a name="estimated-timing-10-minutes"></a>Durée estimée : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Utiliser PowerShell afin de créer un compte d’utilisateur pour Isabel Garcia.
- Tâche 2 : Utiliser PowerShell pour créer le groupe Junior Admins et ajouter le compte d’utilisateur d’Isabel Garcia au groupe. 

#### <a name="task-1-use-powershell-to-create-a-user-account-for-isabel-garcia"></a>Tâche 1 : Utiliser PowerShell afin de créer un compte d’utilisateur pour Isabel Garcia.

Dans cette tâche, vous allez créer un compte d’utilisateur pour Isabel Garcia à l’aide de PowerShell.

1. Ouvrez Cloud Shell en cliquant sur la première icône en haut à droite du portail Azure. Si vous y êtes invité, sélectionnez **PowerShell**, puis **Créer un stockage**.

2. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

   >**Remarque** : pour coller un texte copié dans le Cloud Shell, cliquez avec le bouton droit dans la fenêtre du volet, puis sélectionnez **Coller**. Vous pouvez également utiliser la combinaison de touches **Maj+Inser**.

3. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour créer un objet profil de mot de passe :

    ```powershell
    $passwordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    ```

4. Dans le volet Cloud Shell de la session PowerShell, exécutez la commande suivante pour définir la valeur du mot de passe dans l’objet profil :
    ```powershell
    $passwordProfile.Password = "Pa55w.rd1234"
    ```

5. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour vous connecter à Azure Active Directory :

    ```powershell
    Connect-AzureAD
    ```
      
6. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour identifier le nom de votre locataire Azure AD : 

    ```powershell
    $domainName = ((Get-AzureAdTenantDetail).VerifiedDomains)[0].Name
    ```

7. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour créer un compte d’utilisateur pour Isabel Garcia : 

    ```powershell
    New-AzureADUser -DisplayName 'Isabel Garcia' -PasswordProfile $passwordProfile -UserPrincipalName "Isabel@$domainName" -AccountEnabled $true -MailNickName 'Isabel'
    ```

8. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour répertorier les utilisateurs Azure AD (les comptes de Joseph et d’Isabel devraient figurer sur la liste) : 

    ```powershell
    Get-AzureADUser 
    ```

#### <a name="task2-use-powershell-to-create-the-junior-admins-group-and-add-the-user-account-of-isabel-garcia-to-the-group"></a>Tâche 2 : Utiliser PowerShell pour créer le groupe Junior Admins et ajouter le compte d’utilisateur d’Isabel Garcia au groupe.

Dans cette tâche, vous allez créer le groupe Junior Admins et ajouter le compte d’utilisateur d’Isabel Garcia au groupe à l’aide de PowerShell.

1. Dans la même session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour créer un groupe de sécurité nommé Junior Admins :
    
    ```powershell
    New-AzureADGroup -DisplayName 'Junior Admins' -MailEnabled $false -SecurityEnabled $true -MailNickName JuniorAdmins
    ```

2. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour répertorier les groupes dans votre locataire Azure AD (la liste devrait inclure les groupes Senior Admins et Junior Admins) :

    ```powershell
    Get-AzureADGroup
    ```

3. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour obtenir une référence au compte d’utilisateur d’Isabel Garcia :

    ```powershell
    $user = Get-AzureADUser -Filter "MailNickName eq 'Isabel'"
    ```

4. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour ajouter le compte d’utilisateur d’Isabel au groupe Junior Admins :
    
    ```powershell
    Add-AzADGroupMember -MemberUserPrincipalName $user.userPrincipalName -TargetGroupDisplayName "Junior Admins" 
    ```

5. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour vérifier que le groupe Junior Admins contient le compte d’utilisateur d’Isabel :

    ```powershell
    Get-AzADGroupMember -GroupDisplayName "Junior Admins"
    ```

> Résultat : Vous avez utilisé PowerShell pour créer un utilisateur et un compte de groupe, puis ajouté le compte d’utilisateur au compte de groupe. 


### <a name="exercise-3-create-a-service-desk-group-containing-the-user-account-of-dylan-williams-as-its-member"></a>Exercice 3 : Créer un groupe Service Desk contenant le compte d’utilisateur de Dylan Williams en tant que membre.

#### <a name="estimated-timing-10-minutes"></a>Durée estimée : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Utiliser Azure CLI afin de créer un compte d’utilisateur pour Dylan Williams.
- Tâche 2 : Utiliser Azure CLI pour créer le groupe Service Desk et y ajouter le compte d’utilisateur de Dylan. 

#### <a name="task-1-use-azure-cli-to-create-a-user-account-for-dylan-williams"></a>Tâche 1 : Utiliser Azure CLI afin de créer un compte d’utilisateur pour Dylan Williams.

Dans cette tâche, vous allez créer un compte d’utilisateur pour Dylan Williams.

1. Dans le menu déroulant, dans l’angle supérieur gauche du volet Cloud Shell, sélectionnez **Bash**, puis, lorsque vous y êtes invité, cliquez sur **Confirmer**. 

2. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour identifier le nom de votre locataire Azure AD :

    ```cli
    DOMAINNAME=$(az ad signed-in-user show --query 'userPrincipalName' | cut -d '@' -f 2 | sed 's/\"//')
    ```

3. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour créer un utilisateur, Dylan Williams. Utilisez *votre domaine*.
 
    ```cli
    az ad user create --display-name "Dylan Williams" --password "Pa55w.rd1234" --user-principal-name Dylan@$DOMAINNAME
    ```
      
4. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour répertorier les comptes d’utilisateur Azure AD (la liste devrait inclure les comptes d’utilisateur de Joseph, d’Isabel et de Dylan)
    
    ```cli
    az ad user list --output table
    ```

#### <a name="task-2-use-azure-cli-to-create-the-service-desk-group-and-add-the-user-account-of-dylan-to-the-group"></a>Tâche 2 : Utiliser Azure CLI pour créer le groupe Service Desk et y ajouter le compte d’utilisateur de Dylan. 

Dans cette tâche, vous allez créer le groupe Service Desk et y affecter Dylan. 

1. Dans la même session Bash dans le volet Cloud Shell, exécutez la commande suivante pour créer un groupe de sécurité nommé Service Desk.

    ```cli
    az ad group create --display-name "Service Desk" --mail-nickname "ServiceDesk"
    ```
 
2. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour répertorier les groupes Azure AD (la liste devrait inclure les groupes Service Desk, Senior Admins et Junior Admins) :

    ```cli
    az ad group list -o table
    ```

3. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour obtenir une référence au compte d’utilisateur de Dylan Williams : 

    ```cli
    USER=$(az ad user list --filter "displayname eq 'Dylan Williams'")
    ```

4. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour obtenir la propriété objectId du compte d’utilisateur de Dylan Williams : 

    ```cli
    OBJECTID=$(echo $USER | jq '.[].id' | tr -d '"')
    ```

5. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour ajouter le compte d’utilisateur de Dylan au groupe Service Desk : 

    ```cli
    az ad group member add --group "Service Desk" --member-id $OBJECTID
    ```

6. Dans la session Bash dans le volet Cloud Shell, exécutez la commande suivante pour répertorier les membres du groupe Service Desk et vérifier que celui-ci inclut le compte d’utilisateur de Dylan :

    ```cli
    az ad group member list --group "Service Desk"
    ```

7. Fermez le volet Cloud Shell.

> Résultat : À l’aide d’Azure CLI, vous avez créé un compte d’utilisateur et un groupe, puis ajouté le compte d’utilisateur au groupe. 


### <a name="exercise-4-assign-the-virtual-machine-contributor-role-to-the-service-desk-group"></a>Exercice 4 : Attribuer le rôle Contributeur de machines virtuelles au groupe Service Desk.

#### <a name="estimated-timing-10-minutes"></a>Durée estimée : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Créez un groupe de ressources. 
- Tâche 2 : Attribuer les autorisations de Contributeur de machines virtuelles de Service Desk au groupe de ressources.  

#### <a name="task-1-create-a-resource-group"></a>Tâche 1 : Créer un groupe de ressources

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Groupe de ressources**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Groupes de ressources**, cliquez sur **+ Créer**, puis spécifiez les paramètres suivants :

   |Paramètre|Valeur|
   |---|---|
   |Nom d’abonnement|le nom de votre abonnement Azure|
   |Nom de groupe ressources|**AZ500Lab01**|
   |Location|**USA Est**|

3. Cliquez sur **Vérifier + créer**, puis sur **Créer**.

   >**Remarque** : Attendez que le groupe de ressources soit déployé. Utilisez l’icône **Notification** (en haut à droite) pour suivre la progression de l’état du déploiement.

4. De retour dans le volet **Groupes de ressources**, actualisez la page et vérifiez que votre nouveau groupe de ressources apparaît dans la liste des groupes de ressources.


#### <a name="task-2-assign-the-service-desk-virtual-machine-contributor-permissions"></a>Tâche 2 : Attribuer les autorisations de Contributeur de machines virtuelles de Service Desk. 

1. Dans le volet **Groupes de ressources**, cliquez sur l’entrée du groupe de ressources **AZ500LAB01**.

2. Dans le volet **AZ500Lab01**, dans le panneau central, cliquez sur **Contrôle d’accès (IAM)** .

3. Dans le volet **AZ500Lab01 \| Contrôle d’accès (IAM)** , cliquez sur **+ Ajouter**, puis, dans le menu déroulant, sur **Ajouter une attribution de rôle**.

4. Dans le volet **Ajouter une attribution de rôle**, spécifiez les paramètres suivants et cliquez sur **Suivant** après chaque étape :

   |Paramètre|Valeur|
   |---|---|
   |Rôle sous l’onglet Recherche|**Contributeur de machine virtuelle**|
   |Attribuer l’accès à (dans le volet Membres)|**Utilisateur, groupe ou principal de service**|
   |Sélectionnez (+ Sélectionner des membres)|**Service Desk**|

5. Cliquez sur **Vérifier + attribuer** à deux reprises pour créer l’attribution de rôle.

6. Dans le volet **Contrôle d’accès (IAM),** sélectionnez **Attributions de rôles**.

7. Dans le volet **AZ500Lab01 \| Contrôle d’accès (IAM)** , sous l’onglet **Vérifier l’accès**, dans la zone de texte **Rechercher par nom ou adresse e-mail**, tapez **Dylan Williams**.

8. Dans la liste des résultats de recherche, sélectionnez le compte d’utilisateur de Dylan Williams, puis, dans le volet **Attributions de Dylan Williams - AZ500Lab01**, affichez l’attribution nouvellement créée.

9. Fermez le volet **Attributions de Dylan Williams - AZ500Lab01**.

10. Répétez les deux dernières étapes afin de vérifier l’accès pour **Joseph Price**. 

> Résultat : Vous avez attribué et vérifié les autorisations RBAC. 

**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus.

1. Dans le portail Azure, ouvrez Cloud Shell en cliquant sur la première icône située en haut à droite du portail Azure. 

2. Dans le menu déroulant dans le coin supérieur gauche du volet Cloud Shell, sélectionnez **PowerShell** et, lorsque vous y êtes invité, cliquez sur **Confirmer**. 

3. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour supprimer le groupe de ressources que vous avez créé dans ce labo :
  
    ```
    Remove-AzResourceGroup -Name "AZ500LAB01" -Force -AsJob
    ```

4.  Fermez le volet **Cloud Shell**. 

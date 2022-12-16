---
lab:
  title: 05 - Azure AD Privileged Identity Management
  module: Module 01 - Manage Identity and Access
---

# <a name="lab-05-azure-ad-privileged-identity-management"></a>Labo 05 : Azure AD Privileged Identity Management
# <a name="student-lab-manual"></a>Manuel de labos pour étudiant

## <a name="lab-scenario"></a>Scénario du labo

Vous avez été invité à créer une preuve de concept qui utilise Azure Privileged Identity Management (PIM) pour activer l’administration juste-à-temps et contrôler le nombre d’utilisateurs qui peuvent effectuer des opérations privilégiées. Voici les conditions spécifiques :

- Créer une affectation permanente de l’utilisateur Azure AD aaduser2 au rôle Administrateur de sécurité. 
- Configurer l’utilisateur Azure AD aaduser2 pour qu’il soit éligible aux rôles Administrateur de facturation et Lecteur général.
- Configurer l’activation du rôle Lecteur général pour exiger une approbation de l’utilisateur Azure AD aaduser3
- Configurer une révision d’accès du rôle de Lecteur global et passer en revue les fonctionnalités d’audit.

> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit de la région à utiliser pour la classe. 

> Avant de continuer, vérifiez que vous avez terminé le labo 04 : MFA, accès conditionnel et protection des identités AAD. Vous aurez besoin du locataire Azure AD, d’AdatumLab500-04 et des comptes d’utilisateur aaduser1, aaduser2 et aaduser3.

## <a name="lab-objectives"></a>Objectifs du labo

Dans ce labo, vous effectuerez les exercices suivants :

- Exercice 1 : Configurer les rôles et les utilisateurs PIM.
- Exercice 2 : Activer des rôles PIM avec et sans approbation.
- Exercice 3 : Créer une révision d’accès et passer en revue les fonctionnalités d’audit PIM.

## <a name="azure-ad-privileged-identity-management-diagram"></a>Diagramme Azure AD Privileged Identity Management

![image](https://user-images.githubusercontent.com/91347931/157522920-264ce57e-5c55-4a9d-8f35-e046e1a1e219.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1---configure-pim-users-and-roles"></a>Exercice 1 - Configurer les utilisateurs et les rôles PIM

#### <a name="estimated-timing-15-minutes"></a>Durée estimée : 15 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Rendre un utilisateur éligible pour un rôle.
- Tâche 2 : Configurer un rôle pour exiger l’approbation afin d’activer et d’ajouter un membre éligible.
- Tâche 3 : Donner à un utilisateur une affectation permanente à un rôle. 

#### <a name="task-1-make-a-user-eligible-for-a-role"></a>Tâche 1 : Rendre un utilisateur éligible pour un rôle

Dans cette tâche, vous allez rendre un utilisateur éligible pour un rôle d’annuaire Azure AD.

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : vérifiez que vous êtes connecté au locataire Azure AD **AdatumLab500-04**. Vous pouvez utiliser le filtre **Annuaire + abonnement** pour basculer entre les locataires Azure AD. Vérifiez que vous êtes connecté en tant qu’utilisateur avec le rôle Administrateur général.
    
    >**Remarque** : si vous ne voyez toujours pas l’entrée AdatumLab500-04, cliquez sur le lien Changer d’annuaire, sélectionnez la ligne AdatumLab500-04, puis cliquez sur le bouton Changer.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents,** située en haut de la page Portail Azure, tapez **Azure AD Privileged Identity Management** et appuyez sur la touche **Entrée**.

3. Dans le volet **Privileged Identity Management**, dans la section **Gérer**, cliquez sur **Rôles Azure AD**.

4. Dans le volet **AdatumLab500-04\| Démarrage rapide**, dans la section **Gérer**, cliquez sur **Rôles**.

5. Dans le volet **AdatumLab500-04 \| Rôles**, cliquez sur **+ Ajouter des affectations**.

6. Dans le panneau **Ajouter des affectations**, dans la liste déroulante **Sélectionner un rôle**, sélectionnez **Administrateur de facturation**.

7. Cliquez sur le lien **Aucun membre sélectionné**, dans le volet **Sélectionner un membre**, cliquez sur **aaduser2**, puis sur **Sélectionner**.

8. Dans le volet **Ajouter des affectations**, cliquez sur **Suivant**. 

9. Vérifiez que le **type d’affectation** est défini sur **Éligible**, puis cliquez sur **Affecter**.
 
10. De retour dans le volet **AdatumLab500-04 \| Rôles**, dans la section **Gérer**, cliquez sur **Affectations**.

11. De retour dans le volet **AdatumLab500-04 \| Affectations**, notez les onglets pour les **affectations éligibles**, les **affectations actives** et les **affectations expirées**.

12. Vérifiez sous l’onglet **Affectations éligibles** que **aaduser2** est affiché en tant qu’**administrateur de facturation**. 

    >**Remarque** : lors de la connexion, aaduser2 pourra utiliser le rôle d’administrateur de facturation. 

#### <a name="task-2-configure-a-role-to-require-approval-to-activate-and-add-an-eligible-member"></a>Tâche 2 : Configurer un rôle pour exiger l’approbation afin d’activer et d’ajouter un membre éligible

1. Dans le portail Azure, revenez au volet **Privileged Identity Management**, puis cliquez sur **Rôles Azure AD**.

2. Dans le volet **AdatumLab500-04\| Démarrage rapide**, dans la section **Gérer**, cliquez sur **Rôles**.

3. Dans le volet **AdatumLab500-04 \| Rôles**, cliquez sur l’entrée de rôle **Lecteur général**. 

4. Dans le volet **Lecteur général \| Affectations**, cliquez sur l’icône **Paramètres** dans la barre d’outils du volet et passez en revue les paramètres de configuration du rôle, y compris les exigences de l’authentification multifacteur d’Azure.

5. Cliquez sur **Modifier**.

6. Sous l’onglet **Activation**, cochez la case **Demander une approbation pour activation**.

7. Cliquez sur **Sélectionner des approbateurs**, dans le volet **Sélectionner un membre**, cliquez sur **aaduser3**, puis sur **Sélectionner**.

8. Cliquez sur **Suivant : Affectation**.

9. Désactivez la case **à cocher Autoriser l’affectation éligible permanente** et laissez les valeurs par défaut de tous les autres paramètres.

10. Cliquez sur **Suivant : Notification**.

11. Passez en revue les paramètres de **notification**, laissez tous les éléments définis par défaut, puis cliquez sur **Mettre à jour**.

    >**Remarque** : toute personne qui tente d’utiliser le rôle Lecteur général doit maintenant être approuvée par aaduser3. 

12. Dans le volet **Lecteur général \| Affectations**, cliquez sur **+ Ajouter des affectations**.

13. Dans le volet **Ajouter des affectations**, cliquez sur **Aucun membre sélectionné**, dans le volet **Sélectionner un membre**, cliquez sur **aaduser2**, puis sur **Sélectionner**.

14. Cliquez sur **Suivant**. 

15. Vérifiez que le **type d’affectation** est **Éligible** et passez en revue les paramètres de durée éligible.

16. Cliquez sur **Affecter**.

    >**Remarque** : l’utilisateur aaduser2 est éligible au rôle Lecteur général. 
 
#### <a name="task-3-give-a-user-permanent-assignment-to-a-role"></a>Tâche 3 : Donner à un utilisateur une affectation permanente à un rôle.

1. Dans le portail Azure, revenez au volet **Privileged Identity Management**, puis cliquez sur **Rôles Azure AD**.

2. Dans le volet **AdatumLab500-04\| Démarrage rapide**, dans la section **Gérer**, cliquez sur **Rôles**.

3. Dans le volet **AdatumLab500-04 \| Rôles**, cliquez sur **+ Ajouter des affectations**.

4. Dans le volet **Ajouter des affectations**, dans la liste déroulante **Sélectionner un rôle**, sélectionnez **Administrateur de sécurité**.

5. Dans le volet **Ajouter des affectations**, cliquez sur **Aucun membre sélectionné**, dans le volet **Sélectionner un membre**, cliquez sur **aaduser2**, puis sur **Sélectionner**.

6. Cliquez sur **Suivant**. 

7. Passez en revue les paramètres du **type d’affectation**, puis cliquez sur **Affecter**.

8. Dans la page **Affectations** sous l’onglet **Affectations éligibles**, sélectionnez **Mettre à jour** pour l’affectation **aaduser2**. Sélectionnez **Éligible et** **Enregistrez** définitivement.

    >**Remarque** : l’utilisateur aaduser2 est désormais éligible définitivement au rôle Administrateur de sécurité.
    
### <a name="exercise-2---activate-pim-roles-with-and-without-approval"></a>Exercice 2 - Activer des rôles PIM avec et sans approbation

#### <a name="estimated-timing-15-minutes"></a>Durée estimée : 15 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Activer un rôle qui ne nécessite pas d’approbation. 
- Tâche 2 : Activer un rôle qui nécessite une approbation. 

#### <a name="task-1-activate-a-role-that-does-not-require-approval"></a>Tâche 1 : Activer un rôle qui ne nécessite pas d’approbation.

Dans cette tâche, vous allez activer un rôle qui ne nécessite pas d’approbation.

1. Ouvrez une fenêtre de navigation InPrivate.

2. Dans la fenêtre du navigateur InPrivate, accédez au portail Azure et connectez-vous à l’aide du compte d’utilisateur **aaduser2**.

    >**Remarque** : pour vous connecter, vous devez fournir un nom complet du compte d’utilisateur **aaduser2**, y compris le nom de domaine DNS du locataire Azure AD dont vous avez pris note précédemment dans ce labo. Ce nom d’utilisateur est au format aaduser2@`<your_tenant_name>`.onmicrosoft.com, où `<your_tenant_name>` est l’espace réservé représentant votre nom de locataire Azure AD unique. 

3. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents,** située en haut de la page Portail Azure, tapez **Azure AD Privileged Identity Management** et appuyez sur la touche **Entrée**.

4. Dans le volet **Privileged Identity Management**, dans la section **Tâches**, cliquez sur **Mes rôles**.

5. Vous devez maintenant voir trois **rôles éligibles** pour **aaduser2** : **Lecteur général**, **Administrateur de la sécurité** et **Administrateur de la facturation**. 

6. Dans la ligne affichant l’entrée du rôle **Administrateur de la facturation**, cliquez sur **Activer**.

7. Si nécessaire, cliquez sur l’avertissement **Vérification supplémentaire requise. Cliquez pour continuer** et suivez les instructions pour vérifier votre identité.

8. Dans le volet **Activer - Administrateur de la facturation**, dans la zone de texte **Motif**, saisissez un texte justifiant l’activation, puis cliquez sur **Activer**.

    >**Remarque** : lorsque vous activez un rôle dans PIM, elle peut prendre jusqu’à 10 minutes. 
    
    >**Remarque** : une fois que votre attribution de rôle est active, votre navigateur s’actualise (si quelque chose ne va pas, déconnectez-vous et reconnectez-vous au portail Azure à l’aide du compte d’utilisateur **aaduser2**).

9. Retournez au volet **Privileged Identity Management**, et, dans la section **Tâches**, cliquez sur **Mes rôles**.

10. Dans le volet **Mes rôles \| Rôles Azure AD**, basculez vers l’onglet **Affectations actives**. Vous remarquez que le rôle **Administrateur de la facturation** est **activé**.

    >**Remarque** : une fois qu’un rôle a été activé, il se désactive automatiquement quand sa limite de temps **Heure de fin** (durée éligible) est atteinte.

    >**Remarque** : si vous terminez vos tâches d’administrateur en avance, vous pouvez désactiver un rôle manuellement.

11.  Dans la liste des **affectations actives**, dans la ligne représentant le rôle Administrateur de la facturation, cliquez sur le lien **Désactiver**.

12.  Dans le volet **Désactiver - Administrateur de la facturation**, cliquez à nouveau sur **Désactiver** pour confirmer.


#### <a name="task-2-activate-a-role-that-requires-approval"></a>Tâche 2 : Activer un rôle qui nécessite une approbation. 

Dans cette tâche, vous allez activer un rôle qui nécessite une approbation.

1. Dans la fenêtre du navigateur InPrivate, dans le portail Azure, lors de la connexion en tant qu’utilisateur **aaduser2**, revenez au volet **Privileged Identity Management \| Démarrage rapide**. 

2. Dans le volet **Privileged Identity Management \| Démarrage rapide**, dans la section **Tâches**, cliquez sur **Mes rôles**.

3. Dans le volet **Mes rôles \| Azure AD**, dans la liste des **affectations éligibles**, dans la ligne affichant le rôle **Lecteur général**, cliquez sur **Activer**. 

4. Dans le volet **Activer - Lecteur général**, dans la zone de texte **Motif**, saisissez un texte justifiant l’activation, puis cliquez sur **Activer**.

5. Cliquez sur l’icône **Notifications** dans la barre d’outils du portail Azure et affichez la notification indiquant que votre demande est en attente d’approbation.

    >**Remarque** : en tant qu’administrateur de rôle privilégié, vous pouvez passer en revue et annuler des demandes à tout moment. 

6. Dans le volet **Mes rôles \| Azure AD**, recherchez le rôle **Administrateur de la sécurité**, puis cliquez sur **Activer**. 

7. Si nécessaire, cliquez sur l’avertissement **Vérification supplémentaire requise. Cliquez pour continuer**. 

8. Suivez les instructions pour vérifier votre identité.

    >**Remarque** : Vous ne devez vous authentifier qu’une seule fois par session. 

9. Une fois que vous êtes de retour dans l’interface du portail Azure, dans le volet **Activer - Administrateur de la sécurité**, dans la zone de texte **Motif**, saisissez un texte justifiant l’activation, puis cliquez sur **Activer**.

    >**Remarque** : le processus d’auto-approbation doit se terminer.

10. Revenez dans le volet **Mes rôles \| Azure AD**, cliquez sur l’onglet **Affectations actives** et notez que la liste des **affectations actives** inclut **l’administrateur de la sécurité**, mais pas le rôle **Lecteur général**.

    >**Remarque** : vous allez maintenant approuver le rôle Lecteur général.

11. Déconnectez-vous du portail Azure en tant que **aaduser2**.

12. Connectez-vous au portail Azure en tant que **aaduser3**.

    >**Remarque** : si vous rencontrez des problèmes d’authentification en utilisant l’un des comptes d’utilisateur, vous pouvez vous connecter au locataire Azure AD à l’aide de votre compte d’utilisateur pour réinitialiser leurs mots de passe ou reconfigurer leurs options de connexion.

13. Dans le portail Azure, accédez à **Azure AD Privileged Identity Management** (Dans la zone de texte Rechercher des ressources, des services et des documents en haut de la page Portail Azure, tapez Azure AD Privileged Identity Management et appuyez sur la touche Entrée).

14. Dans le volet **Privileged Identity Management \| Démarrage rapide**, dans la section **Tâches**, cliquez sur **Approuver les demandes**.

15. Dans le volet **Approuver les demandes \| Rôles Azure AD**, dans la section **Demandes d’activations de rôle**, cochez la case de l’entrée représentant la demande d’activation de rôle au rôle **Lecteur général** par **aaduser2**.

16. Cliquez sur **Approuver**. Dans le volet **Approuver la demande**, dans la zone de texte **Justification**, tapez un motif pour l’activation, notez les heures de début et de fin, puis cliquez sur **Confirmer**. 

    >**Remarque** : vous avez également la possibilité de refuser des demandes.

17. Déconnectez-vous du portail Azure en tant que **aaduser3**.

18. Connectez-vous au portail Azure en tant que **aaduser2**

19. Dans le portail Azure, accédez à **Azure AD Privileged Identity Management** (Dans la zone de texte Rechercher des ressources, des services et des documents en haut de la page Portail Azure, tapez Azure AD Privileged Identity Management et appuyez sur la touche Entrée).

20. Dans le volet **Privileged Identity Management \| Démarrage rapide**, dans la section **Tâches**, cliquez sur **Mes rôles**.

21. Dans le volet **Mes rôles \| Rôles Azure AD**, cliquez sur l’onglet **Affectations actives** et vérifiez que le rôle Lecteur général est désormais actif.

    >**Remarque** : vous devrez peut-être actualiser la page pour afficher la liste mise à jour des affectations actives.

22. Déconnectez-vous et fermez la fenêtre du navigateur InPrivate.

> Résultat : vous avez pratiqué l’activation des rôles PIM avec et sans approbation. 

### <a name="exercise-3---create-an-access-review-and-review-pim-auditing-features"></a>Exercice 3 - Créer une révision d’accès et passer en revue les fonctionnalités d’audit PIM

#### <a name="estimated-timing-10-minutes"></a>Durée estimée : 10 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Configurer des alertes de sécurité pour les rôles d’annuaire Azure AD dans PIM
- Tâche 2 : Passer en revue les alertes PIM, les informations récapitulatives et les informations d’audit détaillées

#### <a name="task-1-configure-security-alerts-for-azure-ad-directory-roles-in-pim"></a>Tâche 1 : Configurer des alertes de sécurité pour les rôles d’annuaire Azure AD dans PIM

Dans cette tâche, vous allez réduire les risques liés aux attributions de rôles obsolètes. Pour ce faire, vous allez créer une révision d’accès PIM pour vous assurer que les rôles affectés sont toujours valides. Plus précisément, vous allez passer en revue le rôle Lecteur général. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** à l’aide de votre compte.

    >**Remarque** : vérifiez que vous êtes connecté au locataire Azure AD **AdatumLab500-04**. Vous pouvez utiliser le filtre **Annuaire + abonnement** pour basculer entre les locataires Azure AD. Vérifiez que vous êtes connecté en tant qu’utilisateur avec le rôle Administrateur général.
    
    >**Remarque** : si vous ne voyez toujours pas l’entrée AdatumLab500-04, cliquez sur le lien Changer d’annuaire, sélectionnez la ligne AdatumLab500-04, puis cliquez sur le bouton Changer.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents,** située en haut de la page Portail Azure, tapez **Azure AD Privileged Identity Management** et appuyez sur la touche **Entrée**.

3. Accédez au volet **Privileged Identity Management**. 

4. Dans le volet **Privileged Identity Management \| Démarrage rapide**, dans la section **Gérer**, cliquez sur **Rôles Azure AD**.

5. Dans le volet **AdatumLab500-04 \| Démarrage rapide**, dans la section **Gérer**, cliquez sur **Révision d’accès**.

6. Dans le volet **AdatumLab500-04 \| Révisions d’accès**, cliquez sur **Nouveau** :

7. Dans le volet **Créer une révision d’accès**, spécifiez les paramètres suivants (en laissant les autres avec leurs valeurs par défaut) : 

   |Paramètre|Valeur|
   |---|---|
   |Nom de la révision|**Révision du Lecteur général**|
   |Date de début|date du jour| 
   |Fréquence|**Une fois**|
   |Date de fin|fin du mois en cours|
   |Rôle, Sélectionner le ou les rôles privilégiés|**Lecteur général**|
   |Réviseurs|**Utilisateurs sélectionnés**|
   |Sélectionner des réviseurs|votre compte|

8. Dans le volet **Créer une révision d’accès**, cliquez sur **Démarrer** :
 
    >**Remarque** : il faudra environ une minute pour que la révision soit déployée et s’affiche dans le volet **AdatumLab500-04 \| Révisions d’accès**. Vous devrez peut-être actualiser la page web. L’état de révision est **Actif**. 

9. Dans le volet **AdatumLab500-04 \| Révisions d’accès**, sous l’en-tête **Révision du Lecteur général**, cliquez sur l’entrée **Lecteur général**. 

10. Dans le volet **Révision du Lecteur général**, examinez la page **Vue d’ensemble** et notez que les graphiques de **progression** affichent un seul utilisateur dans la catégorie **Non examinée**. 

11. Dans le volet **Révision du Lecteur général**, dans la section **Gérer**, cliquez sur **Résultats**. Notez que aaduser2 est répertorié comme ayant accès à ce rôle.

12. Cliquez sur **Afficher** sur la ligne **aaduser2** pour afficher un journal d’audit détaillé avec des entrées représentant des activités PIM qui impliquent cet utilisateur.

13. Revenez au panneau **AdatumLab500-04\| Révisions d’accès**.

14. Dans le volet **AdatumLab500-04 \| Révisions d’accès**, dans la section **Tâches**, cliquez sur **Vérifier l’accès**, puis cliquez sur l’entrée **Révision du Lecteur général**. 

15. Dans le volet **Révision du Lecteur général**, cliquez sur l’entrée **aaduser2**. 

16. Dans la zone de texte **Motif**, tapez une raison pour l’approbation, puis cliquez sur **Approuver** pour maintenir l’appartenance au rôle actuel ou **Refuser** pour le révoquer. 

17. Revenez au volet **Privileged Identity Management**, et, dans la section **Gérer**, cliquez sur **Rôles Azure AD**.

18. Dans le volet **AdatumLab500-04 \| Démarrage rapide**, dans la section **Gérer**, cliquez sur **Révision d’accès**.

19. Sélectionnez l’entrée représentant la révision du **Lecteur général**. Notez que le graphique **Progression** a été mis à jour pour afficher votre révision. 

#### <a name="task-2-review-pim-alerts-summary-information-and-detailed-audit-information"></a>Tâche 2 : Passer en revue les alertes PIM, les informations récapitulatives et les informations d’audit détaillées. 

Dans cette tâche, vous allez passer en revue les alertes PIM, les informations récapitulatives et les informations d’audit détaillées. 

1. Revenez au volet **Privileged Identity Management**, et, dans la section **Gérer**, cliquez sur **Rôles Azure AD**.

2. Dans le volet **AdatumLab500-04 \| Démarrage rapide**, dans la section **Gérer**, cliquez sur **Alertes**, puis sur **Paramètre**.

3. Dans le volet **Paramètres d’alerte**, passez en revue les alertes préconfigurées et les niveaux de risque. Cliquez sur l’un d’entre eux pour obtenir des informations plus détaillées. 

4. Revenez au volet **AdatumLab500-04 \| Démarrage rapide**, puis cliquez sur **Vue d’ensemble**. 

5. Dans le volet **AdatumLab500-04 \| Vue d’ensemble**, passez en revue les informations récapitulatives sur les activations de rôle, les activités PIM, les alertes et les attributions de rôles.

6. Dans le volet **AdatumLab500-04 \| Vue d’ensemble**, dans la section **Activité**, cliquez sur **Audit des ressources**. 

    >**Remarque** : l’historique d’audit est disponible pour toutes les attributions de rôles privilégiées et les activations au cours des 30 derniers jours.

7. Notez que vous pouvez récupérer des informations détaillées, notamment **l’heure**, le **demandeur**, **l’action**, le **nom de la ressource**, **l’étendue**, la **cible principale** et **l’objet.** 

> Résultat : vous avez configuré une révision d’accès et examiné les informations d’audit. 

**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus. 

1. Dans le portail Azure, définissez le filtre **Annuaire + abonnement** sur le locataire Azure AD associé à l’abonnement Azure dans lequel vous avez déployé la machine virtuelle Azure **az500-04-vm1**.

    >**Remarque** : si vous ne voyez pas votre entrée de locataire Azure AD principale, cliquez sur le lien Changer d’annuaire, sélectionnez votre ligne de locataire principale, puis cliquez sur le bouton Changer.

2. Dans le portail Azure, ouvrez Cloud Shell en cliquant sur la première icône située en haut à droite du portail Azure. Si vous y êtes invité, cliquez sur **PowerShell** et **Créer un stockage**.

3. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

4. Dans la session PowerShell dans le volet Cloud Shell, exécutez la commande suivante pour supprimer le groupe de ressources que vous avez créé dans le labo précédent :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB04" -Force -AsJob
    ```

5. Fermez le volet **Cloud Shell**. 

6. De retour dans le portail Azure, utilisez le filtre **Annuaire + abonnement** pour basculer vers le locataire **AdatumLab500-04** Azure Active Directory.

7. Accédez au volet **AdatumLab500-04 Azure Active Directory** et, dans la section **Gérer**, cliquez sur **Licences**.

8. Dans le volet **Licences** | Vue d’ensemble, cliquez sur **Tous les produits**, cochez la case **Azure Active Directory Premium P2** et cliquez dessus pour l’ouvrir.

    >**Remarque** : dans le Labo 4 - Exercice 2 - Tâche 4 **Affecter des licences Azure AD Premium P2 aux utilisateurs Azure AD** il s’agissait d’affecter les licences Premium aux utilisateurs **aaduser1, aaduser2 et aaduser3** ; assurez-vous de supprimer ces licences des utilisateurs affectés

9. Dans le volet **Azure Active Directory Premium P2 - Utilisateurs sous licence**, cochez les cases des comptes d’utilisateur auxquels vous avez attribué des licences **Azure Active Directory Premium P2**. Cliquez sur **Supprimer la licence** dans le volet supérieur et, lorsque vous êtes invité à confirmer, sélectionnez **Oui**.

10. Dans le portail Azure, accédez au volet **Utilisateurs - Tous les utilisateurs**, cliquez sur l’entrée représentant le compte d’utilisateur **aaduser1**. Dans le volet **aaduser1 - Profil**, cliquez sur **Supprimer**, puis, lorsque vous êtes invité à confirmer, cliquez sur **Oui**.

11. Répétez la même séquence d’étapes pour supprimer les comptes d’utilisateur restants que vous avez créés.

12. Accédez au volet **AdatumLab500-04 - Vue d’ensemble** du locataire Azure AD, sélectionnez **Gérer les locataires**, puis, dans l’écran suivant, cochez la case en regard d’**AdatumLab500-04**, puis sélectionnez **Supprimer**. Dans le volet **Supprimer le locataire AdatumLab500-04**, sélectionnez le lien **Obtenir l’autorisation de supprimer des ressources Azure**, dans le volet **Propriétés** d’Azure Active Directory, définissez sur **Gestion d’accès pour les ressources Azure** sur **Oui**, puis sélectionnez **Enregistrer**.

13. Déconnectez-vous du portail Azure et reconnectez-vous. 

14. Revenez au volet **Supprimer le répertoire AdatumLab500-04**, puis cliquez sur **Supprimer**.

    >**Remarque** : si vous n’êtes toujours pas en mesure de supprimer le locataire et que vous recevez l’erreur **Supprimer tous les abonnements basés sur une licence**, cela peut être dû à tous les abonnements qui ont été liés au locataire. Ici, la **licence gratuite Premium P2** peut générer l’erreur de validation. La suppression de l’abonnement d’évaluation de la licence Premium P2 à l’aide de l’ID d’administrateur général de l’administrateur M365>> **Vos produits** et du portail **Business Store** résout ce problème. Notez également que la suppression du locataire prend plus de temps. Vérifiez la date de fin de l’abonnement, une fois après la fin de la période d’évaluation, revisitez Azure Active Directory, puis essayez de supprimer le locataire.    

> Pour plus d’informations sur cette tâche, reportez-vous à [https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-delete-howto)

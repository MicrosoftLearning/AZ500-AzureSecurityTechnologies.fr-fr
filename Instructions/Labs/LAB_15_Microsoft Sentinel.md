---
lab:
  title: "15 - Microsoft\_Sentinel"
  module: Module 04 - Manage Security Operations
---

# Labo 15 : Microsoft Sentinel
# Manuel de labos pour étudiant

## Scénario du labo

**Remarque :** **Azure Sentinel** s’appelle désormais **Microsoft Sentinel** 

Vous avez été invité à créer une preuve de concept de détection et réponses aux menaces basées sur Microsoft Sentinel. Plus précisément, vous souhaitez :

- Commencer à collecter des données à partir d’Azure Activity et de Microsoft Defender pour le cloud.
- Ajouter des alertes intégrées et personnalisées 
- Passer en revue comment les playbooks peuvent être utilisés pour automatiser une réponse à un incident.

> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous effectuerez les exercices suivants :

- Exercice 1 : Implémenter Microsoft Sentinel

## Diagramme Microsoft Sentinel

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/509aa70d-de11-4470-a289-877fbfecbc00)

## Instructions

## Fichiers du labo :

- **\\Allfiles\\Labs\\15\\changeincidentseverity.json**

### Exercice 1 : Implémenter Microsoft Sentinel

### Durée estimée : 30 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Intégrer Microsoft Sentinel
- Tâche 2 : Connecter Azure Activity à Sentinel
- Tâche 3 : Créer une règle qui utilise le connecteur de données Azure Activity. 
- Tâche 4 : Créer un playbook
- Tâche 5 : Créer une alerte personnalisée et configurer le playbook en tant que réponse automatisée.
- Tâche 6 : Appeler un incident et passer en revue les actions associées.

#### Tâche 1 : Intégrer Azure Sentinel

Dans cette tâche, vous allez intégrer Microsoft Sentinel et connecter l’espace de travail Log Analytics. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au Portail Azure à l’aide d’un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce laboratoire.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents**, située en haut de la page Portail Azure, tapez **Microsoft Sentinel** et appuyez sur la touche **Entrée**.

    >**Remarque** : si c’est la première fois que vous tentez d’actionner Microsoft Sentinel dans le tableau de bord Azure, suivez les étapes suivantes : Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Microsoft Sentinel** et appuyez sur la touche **Entrée**. Sélectionnez **Microsoft Sentinel** dans la vue **Services**.
  
3. Dans le volet **Microsoft Sentinel**, cliquez sur **+ Créer**.

4. Dans le volet **Ajouter Microsoft Sentinel à un espace de travail**, sélectionnez l’espace de travail Log Analytics que vous avez créé dans le labo Azure Monitor, puis cliquez sur **Ajouter**.

    >**Remarque** : Microsoft Sentinel a des exigences très spécifiques pour les espaces de travail. Par exemple, les espaces de travail créés par Microsoft Defender pour le cloud ne peuvent pas être utilisés. En savoir plus sur [Démarrage rapide : Intégrer Azure Sentinel](https://docs.microsoft.com/en-us/azure/sentinel/quickstart-onboard)
    
#### Tâche 2 : Configurer Microsoft Sentinel pour utiliser le connecteur de données d’activité Azure. 

Dans cette tâche, vous allez configurer Sentinel pour utiliser le connecteur de données d’activité Azure.  

1. Dans le volet **Microsoft Sentinel \| Vue d’ensemble** du portail Azure, dans la section **Gestion de contenu**, cliquez sur **Hub de contenu**.

2. Dans le panneau **Microsoft Sentinel \| Hub de contenu**, passez en revue la liste du contenu disponible.

3. Tapez **Azure** dans la barre de recherche et sélectionnez l’entrée représentant **Activité Azure**. Passez en revue sa description à l’extrême droite, puis cliquez sur **Installer**.

4. Attendez la notification **Réussite de l’installation**. Dans le volet de navigation de gauche, dans la section **Configuration**, cliquez sur **Connecteurs de données**.

5. Dans le panneau **Connecteurs de données \| Microsoft Sentinel**, cliquez sur **Actualiser et passez** en revue la liste des connecteurs disponibles. Sélectionnez l’entrée représentant le connecteur **Activité Azure** (cachez la barre de menu à gauche à l’aide de \<< si besoin), passez en revue sa description et son statut tout à droite, puis cliquez sur **Ouvrir la page du connecteur**.

6. Dans le volet **Activité Azure**, l’onglet **Instructions** doit être sélectionné, notez les **prérequis** et faites défiler jusqu’à la **configuration**. Notez les informations décrivant la mise à jour du connecteur. Votre abonnement Pass Azure n’a jamais utilisé la méthode de connexion héritée si bien que vous pouvez ignorer l’étape 1 (le bouton **Déconnecter tout** sera grisé) et passer à l’étape 2.

7. À l’étape 2 **Connecter vos abonnements via le nouveau pipeline des paramètres de diagnostic**, passez en revue l’Assistant « Lancer l’Assistant Affectation Azure Policy et suivre les étapes », puis cliquez sur **Lancer l’Assistant Affectation Azure Policy\>** .

8. Sous l’onglet **Informations de base** (page Attribuer une stratégie) **Configurer les journaux d’activité Azure à diffuser en continu vers l’espace de travail Log Analytics**, cliquez sur le bouton **Étendue (...)** . Dans la page **Étendue**, choisissez votre abonnement Pass Azure dans la liste déroulante de l’abonnement, puis cliquez sur le bouton **Sélectionner** en bas de la page.

    >**Remarque** : *ne choisissez pas* un groupe de ressources

9. Cliquez deux fois sur le bouton **Suivant** en bas de l’onglet **Informations de base** pour continuer vers l’onglet **Paramètres**. Sous l’onglet **Paramètres**, cliquez sur le bouton **Espace de travail Log Analytics (...)** . Dans la page **Espace de travail Log Analytics principal**, vérifiez que votre abonnement Pass Azure est sélectionné et utilisez la liste déroulante **Espaces de travail** pour sélectionner l’espace de travail Log Analytics que vous utilisez pour Sentinel. Cliquez ensuite sur le bouton **Sélectionner** en bas de la page.

10. Cliquez sur le bouton **Suivant** en bas de l’onglet **Paramètres** pour continuer vers l’onglet **Correction**. Sous l’onglet **Correction**, cochez la case **Créer une tâche de correction**. Cela permettra de configurer les journaux d’activité Azure pour diffuser en continu l’espace de travail Log Analytics spécifié dans la liste déroulante **Stratégie pour corriger**. Dans la liste déroulante **Emplacement de l’identité affectée par le système**, sélectionnez la région (USA Est, par exemple) que vous avez sélectionnée précédemment pour votre espace de travail Log Analytics.

11. Cliquez sur le bouton **Suivant** en bas de l’onglet **Correction** pour continuer vers l’onglet **Message de non conformité**.  Entrez un message de non conformité si vous le souhaitez (optionnel) et cliquez sur le bouton **Vérifier + créer** en bas de l’onglet **Message de non conformité**.

12. Cliquez sur le bouton **Créer**. Vous devriez obtenir trois messages successifs de réussite : **La création de l’attribution de stratégie a réussi, Création d’attribution de rôle réussie et Création de tâche de correction réussie**.

    >**Remarque** : vous pouvez vérifier les notifications, l’icône en forme de cloche pour vérifier les trois tâches réussies.

13. Vérifiez que le volet **Activité Azure** affiche le graphique **Données reçues** (vous devrez peut-être actualiser la page du navigateur).  

    >**Remarque** : 15 minutes peuvent s’écouler avant que l’état affiche « Connecté » et que le graphique affiche les données reçues.

#### Tâche 3 : Créer une règle qui utilise le connecteur de données Azure Activity. 

Dans cette tâche, vous allez examiner et créer une règle qui utilise le connecteur de données d’activité Azure. 

1. Dans le volet **Microsoft Sentinel \| Configuration** , cliquez sur **Analytics**. 

2. Dans le volet **Microsoft Sentinel \| Analytics**, cliquez sur l’onglet **Modèles de règle**. 

    >**Remarque** : sélectionnez les types de règles que vous pouvez créer. Chaque règle est associée à une source de données spécifique.

3. Dans la liste des modèles de règle, tapez **Suspect** dans le formulaire de barre de recherche, puis cliquez sur l’entrée **Nombre suspect de création de ressources ou déploiement** associée à la source de données **d’activité Azure**. Ensuite, dans le volet affichant les propriétés du modèle de règle, cliquez sur **Créer une règle** (faites défiler vers la droite de la page si nécessaire).

    >**Remarque** : cette règle dispose d’une gravité moyenne. 

4. Sous l’onglet **Général** du volet **Assistant Règle analytique : Créer une nouvelle règle planifiée**, acceptez les paramètres par défaut et cliquez sur **Suivant : Définir la logique de la règle >** .

5. Sous l’onglet **Général** du volet **Assistant Règle analytique - Créer une nouvelle règle planifiée**, acceptez les paramètres par défaut et cliquez sur **Suivant : Paramètres des incidents (préversion) >** .

6. Sous l’onglet **Paramètres de l’incident** du volet **Assistant Règle analytique - Créer une nouvelle règle planifiée**, acceptez les paramètres par défaut et cliquez sur **Suivant : Réponse automatisée >** . 

    >**Remarque** : c’est là que vous pouvez ajouter un playbook, implémenté en tant qu’application logique, à une règle pour automatiser la correction d’un problème.

7. Sous l’onglet **Réponse automatisée** du volet **Assistant Règle analytique - Créer une nouvelle règle planifiée**, acceptez les paramètres par défaut et cliquez sur **Suivant : Vérifier et créer >** . 

8. Sous l’onglet **Vérifier et créer** du volet **Assistant Règle analytique - Créer une nouvelle règle planifiée**, cliquez sur **Enregistrer**.

    >**Remarque** : vous avez désormais une régle active.

#### Tâche 4 : Créer un playbook

Dans cette tâche, vous allez créer un playbook. Un playbook de sécurité est une collection de tâches qui peuvent être appelées par Microsoft Sentinel en réponse à une alerte. 

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Déployer un modèle personnalisé** et appuyez sur la touche **Entrée**.

2. Dans le volet **Déploiement personnalisé**, sélectionnez **Créer votre propre modèle dans l’éditeur**.

3. Dans le volet **Modifier le modèle**, cliquez sur **Charger le fichier**, recherchez le **\\fichier Allfiles\\Labs\\15\\changeincidentseverity.json**, puis cliquez sur **Ouvrir**.

    >**Remarque** : vous pouvez trouver des exemples de playbooks sur [https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks).

4. Dans le volet **Modifier le modèle**, cliquez sur **Enregistrer**.

5. Dans le volet **Déploiement personnalisé**, vérifiez que les paramètres suivants sont configurés (laissez les autres avec leurs valeurs par défaut) :

    |Paramètre|Valeur|
    |---|---|
    |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
    |Resource group|**AZ500LAB131415**|
    |Emplacement|**(États-Unis) USA Est**|
    |Nom du playbook|**Change-Incident-Severity**|
    |User Name|votre adresse e-mail|

6. Cliquez sur **Vérifier + créer**, puis sur **Créer**.

    >**Remarque** : Attendez la fin du déploiement.

7. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Groupes de ressources**, puis appuyez sur la touche **Entrée**.

8. Dans le volet **Groupes de ressources**, dans la liste des groupes de ressources, cliquez sur l’entrée **AZ500LAB131415**.

9. Dans le volet du groupe de ressources **AZ500LAB131415**, dans la liste des ressources, cliquez sur l’entrée représentant l’application logique **Change-Incident-Severity** nouvellement créée.

10. Dans le volet **Change-Incident-Severity**, cliquez sur **Modifier**.

    >**Note** : Dans le volet **Concepteur Logic Apps**, chacune des quatre affiche un avertissement. Cela signifie que chacune doit être passée en revue et configurée.

11. Dans le volet **Concepteur Logic Apps**, cliquez sur la première étape **s**.

12. Cliquez sur **Ajouter**, vérifiez que l’entrée dans la liste déroulante **Locataire** contient le nom de votre locataire Azure AD, puis cliquez sur **Se connecter**.

13. Lorsque vous y êtes invité, connectez-vous avec le compte utilisateur disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo.

14. Cliquez sur la deuxième étape de **s** et, dans la liste des s, sélectionnez la deuxième entrée représentant la  que vous avez créée à l’étape précédente.

15. Répétez les étapes précédentes pour les deux étapes de **s** restantes.

    >**Remarque** : vérifiez qu’aucun avertissement n’est affiché sur l’une des étapes.

16. Dans le volet **Concepteur Logic Apps**, cliquez sur **Enregistrer** pour enregistrer vos modifications.

#### Tâche 5 : Créer une alerte personnalisée et configurer un playbook en tant que réponse automatisée

1. Dans le portail Azure, revenez au volet **Microsoft Sentinel \| Vue d’ensemble**.

2. Dans le volet **Microsoft Sentinel \| Vue d’ensemble**, dans la section **Configuration**, cliquez sur **Analytics**.

3. Dans le volet **Microsoft Sentinel \| Analytics**, cliquez sur **+ Créer** puis, dans le menu déroulant, cliquez sur **Règle de requête planifiée**. 

4. Sous l’onglet **Général** de **l’Assistant Règle analytique - Créer une nouvelle règle planifiée**, spécifiez les paramètres suivants (laissez les autres avec leurs valeurs par défaut) :

    |Paramètre|Valeur|
    |---|---|
    |Nom|**Démonstration du playbook**|
    |Tactique|**Accès initial**|

5. Cliquez sur **Suivant : Définir la logique de la règle >** .

6. Sous l’onglet **Définir la logique de règle** de **l’Assistant Règle analytique - Créer une nouvelle règle planifiée**, dans la zone de texte **Requête de règle**, collez la requête de règle suivante. 

    ```
    AzureActivity
     | where ResourceProviderValue =~ "Microsoft.Security" 
     | where OperationNameValue =~ "Microsoft.Security/locations/jitNetworkAccessPolicies/delete" 
    ```

    >**Remarque** : cette règle identifie la suppression des stratégies d’accès Juste à temps à la machine virtuelle.

    >**Remarque** : si vous recevez une erreur d’analyse, IntelliSense peut avoir ajouté des valeurs à votre requête. Vérifiez que la requête correspond, dans le cas contraire, collez la requête dans le bloc-notes, puis, du bloc-notes à la requête de règle. 


7. Sous l’onglet **Définir la logique de règle** de **l’Assistant Règle analytique - Créez une nouvelle règle planifiée**, dans la section **Planification des requêtes**, définissez la **requête Exécuter toutes les** à **5 minutes**.

8. Sous l’onglet **Définir la logique de la règle** du volet **Assistant Règle analytique - Créer une nouvelle règle planifiée**, acceptez les valeurs par défaut et cliquez sur **Suivant : Paramètres de l’incident >** .

9. Sous l’onglet **Paramètres de l’incident** du volet **Assistant Règle analytique - Créer une nouvelle règle planifiée**, acceptez les paramètres par défaut et cliquez sur **Suivant : Réponse automatisée >** . 

10. Sous l’onglet **Réponse automatisée** de **l’Assistant Règle analytique - Créer une règle planifiée**, sous **Règles d’automatisation**, cliquez sur **+ Ajouter**.

11. Dans la fenêtre **Créer une règle d’automatisation** , **entrez Exécuter Change-Severity playbook** pour le **nom de la règle Automation** ; sous le champ **Déclencheur** , cliquez sur le menu déroulant et sélectionnez **Quand l’alerte est créée**.

12. Dans la fenêtre **Créer une règle d’automatisation** , sous **Actions**, lisez la note, puis cliquez sur **Gérer les autorisations du playbook**. Dans la fenêtre **Gérer les autorisations** , cochez la case en regard du **groupe de ressources créé précédemment AZ500LAB1314151** , puis cliquez sur **Appliquer**.

13.  Dans la fenêtre **Créer une règle d’automatisation** , sous **Actions**, cliquez sur le deuxième menu déroulant et sélectionnez l’application logique **Change-Incident-Severity** . Dans la fenêtre **Créer une règle d’automatisation** , cliquez sur **Appliquer**.

14. Sous l’onglet **Réponse automatisée** du volet **Assistant Règle analytique - Créer une nouvelle règle planifiée**, cliquez sur **Suivant : Vérifier et créer >** puis cliquez sur **Enregistrer**

    >**Remarque** : vous disposez maintenant d’une nouvelle règle active appelée **Démonstration du playbook**. Si un événement identifié par la logique de règle se produit, il génère une alerte de gravité moyenne, qui génère un incident correspondant.

#### Tâche 6 : Appeler un incident et passer en revue les actions associées.

1. Dans le portail Azure, accédez au volet **Microsoft Defender pour le cloud \| Vue d’ensemble**.

    >**Remarque** : Vérifiez votre niveau de sécurité. À l’heure actuelle, il doit avoir été mis à jour. 

2. Dans le **panneau Vue d’ensemble de la Microsoft Defender pour le cloud\|** , cliquez sur **Protections de charge de travail** sous **Sécurité cloud** dans le volet de navigation de gauche.

3. Dans le volet **Microsoft Defender pour le cloud \| Protections des charges de travail** défilez vers le bas et cliquez sur le vignette **Accès Juste à temps à la machine virtuelle** sous **Protection avancée**.

4. Dans le volet **Accès juste-à-temps à la machine virtuelle**, sur le côté droit de la ligne référençant la machine virtuelle **MyVM**, cliquez sur le bouton **ellipses (...)** , cliquez sur **Supprimer**, puis sur **Oui**.

    >**Remarque :** si la machine virtuelle n’est pas répertoriée dans les **machines virtuelles Juste à temps**, accédez au volet **Machine virtuelle**, puis cliquez sur **Configuration**. Cliquez sur l’option **Activer les machines virtuelles Juste à temps** sous **l’accès Juste à temps à la machine virtuelle**. Répétez l’étape ci-dessus pour revenir à la page **Microsoft Defender pour le cloud** et actualiser la page : la machine virtuelle s’affiche.

5. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Journal d’activité**, puis appuyez sur la touche **Entrée**.

6. Accédez au volet **Journal d’activité**, notez une entrée **Supprimer les stratégies d’accès réseau JIT**. 

    >**Note** : Cela peut mettre un peu de temps avant d’apparaître. **Actualisez** la page si elle n’apparaît pas.

7. Dans le portail Azure, revenez au volet **Microsoft Sentinel \| Vue d’ensemble**.

8. Dans le panneau **Microsoft Sentinel \| Vue d’ensemble**, passez en revue le tableau de bord et vérifiez qu’il affiche un incident correspondant à la suppression de la stratégie d’accès Juste à temps à la machine virtuelle.

    >**Remarque** : l’affichage des alertes dans le volet **Microsoft Sentinel \| Vue d’ensemble** peut prendre jusqu’à 5 minutes. Si vous ne voyez pas d’alerte à ce stade, exécutez la règle de requête référencée dans la tâche précédente pour vérifier que l’activité de suppression de la stratégie d’accès Juste à temps a été propagée à l’espace de travail Log Analytics associé à votre instance Microsoft Sentinel. Si ce n’est pas le cas, recréez la stratégie d’accès Juste à temps à la machine virtuelle et supprimez-la à nouveau.

9. Dans le volet **Microsoft Sentinel \| Vue d’ensemble**, dans la section **Gestion des menaces**, cliquez sur **Incidents**.

10. Vérifiez que le volet affiche un incident avec un niveau de gravité moyen ou élevé.

    >**Remarque** : l’affichage des alertes dans le volet **Microsoft Sentinel \| Incidents** peut prendre jusqu’à 5 minutes. 

    >**Remarque** : vérifiez le volet **Microsoft Sentinel\| Playbooks**. Vous y trouverez le nombre d’exécutions réussies et en échec.

    >**Remarque** : vous avez la possibilité d’attribuer un niveau de gravité et un état différents à un incident.

> Résultats : vous avez créé un espace de travail Microsoft Sentinel, l’avez connecté aux journaux d’activité Azure, créé un playbook et des alertes personnalisées qui se déclenchent en réponse à la suppression des stratégies d’accès Juste à temps à la machine virtuelle et vérifié que la configuration est valide.

**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus. 

1. Dans le portail Azure, ouvrez Cloud Shell en cliquant sur la première icône située en haut à droite du portail Azure. Si vous y êtes invité, cliquez sur **PowerShell** et **Créer un stockage**.

2. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

3. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour supprimer le groupe de ressources que vous avez créé dans ce labo :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB131415" -Force -AsJob
    ```
4. Fermez le volet **Cloud Shell**.

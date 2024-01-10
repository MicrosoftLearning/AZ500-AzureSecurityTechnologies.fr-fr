---
lab:
  title: 05 – Sécurisation d’Azure SQL Database
  module: Module 02 - Plan and implement security for Azure SQL Database and Azure SQL Managed Instance
---

# Labo 5 : Sécurisation d’Azure SQL Database
# Manuel de labo de l’étudiant

## Scénario du labo

Vous avez été invité à examiner les fonctionnalités de sécurité pour Azure SQL Database. Plus précisément, vos domaines d'intérêt sont les suivants :

- Protection contre les attaques telles que l’injection SQL et l’exfiltration des données. 
- Capacité à découvrir et à classer les informations des bases de données dans des catégories telles que Confidentiel. 
- Possibilité d’auditer le serveur de base de données et les requêtes de base de données et les événements de journal. 

> Pour toutes les ressources dans ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous allez effectuer l’exercice suivant :

- Exercice 1 : Mettre en œuvre les fonctions de sécurité de SQL Database

## Schéma de sécurisation d’Azure SQL Database

![image](https://user-images.githubusercontent.com/91347931/157533836-7250fa58-a109-4882-a55b-d3fa3baf34ab.png)

## Instructions

## Fichiers du labo :

- **\\Allfiles\\Labs\\11\\azuredeploy.json**

### Exercice 1 : Mettre en œuvre les fonctions de sécurité de SQL Database

### Durée estimée : 30 minutes

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Déployer une base de données Azure SQL Database
- Tâche 2 : Configurer la protection avancée des données
- Tâche 3 : Configurer la classification des données
- Tâche 4 : Configurer l’audit

#### Tâche 1 : Déployer une base de données Azure SQL Database

Dans cette tâche, vous allez utiliser un modèle pour déployer l’infrastructure du labo. 

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au portail Azure en utilisant un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce labo.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Déployer un modèle personnalisé**, puis appuyez sur la touche **Entrée**.

3. Dans le volet **Déploiement personnalisé**, sélectionnez **Créer votre propre modèle dans l’éditeur**.

4. Dans le volet **Modifier le modèle**, cliquez sur **Charger le fichier**, recherchez le fichier **\\Allfiles\\Labs\\11\\azuredeploy.json**, puis cliquez sur **Ouvrir**.

    >**Remarque** : passez en revue le contenu du modèle et notez qu’il déploie une base de données Azure SQL.

5. Dans le volet **Modifier le modèle**, cliquez sur **Enregistrer**.

6. Dans le volet **Déploiement personnalisé**, vérifiez que les paramètres suivants sont configurés (laissez les autres avec leurs valeurs par défaut) :

   |Paramètre|Valeur|
   |---|---|
   |Abonnement|le nom de l’abonnement Azure que vous utilisez dans ce labo|
   |Resource group|Cliquez sur **Créer** et tapez le nom **AZ500LAB11**.|
   |Emplacement|**(États-Unis) USA Est**|

7. Cliquez sur **Vérifier + créer**, puis cliquez sur **Créer**.

    >**Remarque** : Attendez la fin du déploiement. 

#### Tâche 2 : Configurer la protection avancée des données

1. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page, tapez **Groupes de ressources**, puis appuyez sur la touche **Entrée**.

2. Dans le volet **Groupes de ressources**, dans la liste des groupes de ressources, cliquez sur l’entrée **AZ500LAB11**.

3. Dans le volet **AZ500LAB11**, cliquez sur l’entrée représentant le nouveau SQL Server.

4. Dans le volet SQL Server, dans la section **Sécurité**, cliquez sur **Microsoft Defender pour le cloud**, sélectionnez **Activer Microsoft Defender pour SQL**.

      >**Remarque** : attendez que la notification indique qu’Azure Defender pour SQL a été activée.

5. Dans le volet SQL Server, dans la section **Sécurité**, dans la page **Microsoft Defender pour le cloud**, dans le paramètre **Microsoft Defender pour SQL : Activé au niveau abonnement (configurer)** , cliquez sur **(configurer)** .  

      >**Remarque** : actualisez le navigateur si **(configurer)** n’est pas affiché.
    
6. Dans le volet **Paramètres Server**, passez en revue les informations sur la tarification et la période d’évaluation, les **PARAMÈTRES D’ÉVALUATION DES VULNÉRABILITÉS** et les **PARAMÈTRES ADVANCED THREAT PROTECTION**.

7. Revenez au volet **Microsoft Defender pour le cloud**, passez en revue les **Recommandations** et les alertes de **sécurité**.

      >**Remarque** : il peut s’écouler entre 10 et 15 minutes pour que les recommandations apparaissent dans le panneau **Microsoft Defender pour le cloud**. Au lieu d’attendre, passez à la tâche suivante, mais envisagez de revenir à ce panneau une fois que vous avez terminé toutes les tâches restantes.


#### Tâche 3 : Configurer la classification des données

Dans cette tâche, vous allez découvrir et classer des informations dans une base de données SQL pour la conformité RGPD et la protection des données.

1. Dans le volet SQL Server, dans la section **Paramètres**, cliquez sur **Bases de données SQL**.

2. Dans la liste des bases de données, sélectionnez l’entrée **AZ500LabDb**. 

3. Dans le volet de la base de données SQL **AZ500LabDb**, dans la section **Sécurité**, cliquez sur **Découverte de données & Classification**.

4. Dans le volet **Découverte de données & Classification**, cliquez sur l’onglet **Classification**.

    >**Remarque** : Le moteur de classification analyse votre base de données à la recherche de colonnes contenant des données potentiellement sensibles, et il fournit une liste de classifications de colonnes recommandées.

5. Cliquez sur le message texte **Nous avons trouvé 15 colonnes avec des recommandations de classification** affichées dans la barre bleue en haut du volet.

6. Passez en revue les colonnes répertoriées et l’étiquette de confidentialité recommandée. 

7. Activez la case **Sélectionner tout**, puis cliquez sur **Accepter les recommandations sélectionnées**.

    >**Remarque** : vous pouvez également sélectionner uniquement certaines colonnes et ignorer d’autres. 

    >**Remarque** : vous avez la possibilité de modifier le type d’informations et l’étiquette de confidentialité. 

8. Lorsque vous avez terminé, cliquez sur **Enregistrer**. 

    >**Remarque** : cette opération termine la classification et étiquete de manière permanente les colonnes de base de données avec les nouvelles métadonnées de classification. 

9. Revenez à l’onglet **Vue d’ensemble** du volet **Découverte de données & classification**, notez qu’il a été mis à jour pour tenir compte des dernières informations de classification. 

#### Tâche 4 : Configurer l’audit 

Dans cette tâche, vous allez d’abord configurer l’audit au niveau serveur, puis configurer l’audit au niveau de la base de données. 

1. Dans le Portail Azure, revenez au volet SQL Server.

2. Dans le volet SQL Server, dans la section **Sécurité**, cliquez sur **Audit**.

    >**Remarque** : il s’agit de l’audit au niveau du serveur. Les paramètres d’audit par défaut incluent toutes les requêtes et procédures stockées exécutées sur la base de données, ainsi que les connexions réussies et échouées.

3. Définissez le commutateur **Activer Azure SQL Audit** sur **ON** pour activer l’audit. 

4. Activez la case à cocher **Stockage**. les zones d’entrée pour **Abonnement** et **Compte de stockage** s’affichent.

5. Sélectionnez votre **abonnement** dans la liste déroulante.

6. Cliquez sur **Compte de stockage** et choisissez **Créer**.

7. Dans le volet **Créer un compte de stockage**, dans la zone **Nom**, tapez un nom global unique composé de 3 à 24 lettres en minuscules et chiffres, cliquez sur **OK**. 

    >**Remarque** : vous devrez peut-être actualiser le navigateur avant que le compte de stockage ne soit disponible.

8. Revenez dans le volet **Audit**, sous **Propriétés avancées** définissez **Rétention (jours)** sur **5**. 

9. Dans le volet Audit, cliquez sur **Enregistrer** pour enregistrer les paramètres d’audit. 

    >**Remarque** : si vous recevez un message d’erreur concernant le chemin d’accès du conteneur de stockage non valide, le compte de stockage n’a peut-être pas encore été configuré. Patientez quelques minutes, cliquez sur **Compte de stockage**, dans le volet **Choisir un compte de stockage**, sélectionnez le compte de stockage nouvellement créé et revenez dans le volet Audit et cliquez sur **Enregistrer**.

10. Dans le volet serveur, dans la section **Paramètres**, cliquez sur **Bases de données SQL**.

11. Dans la liste des bases de données, sélectionnez l’entrée **AZ500LabDb**. 

12. Dans le volet de la base de données SQL **AZ500LabDb** SQL, dans la section **Sécurité**, cliquez sur **Audit**. 

    >**Remarque** : Il s’agit d’un audit au niveau de la base de données. L’audit au niveau du serveur est déjà activé. 
  
    >**Remarque** : les audits peuvent être écrits dans un compte de stockage Azure, dans un espace de travail Log Analytics ou dans Event Hub. Vous pouvez configurer n’importe quelle combinaison de ces options.

    >**Remarque** : Si l’audit basé sur la stockage est activé sur le serveur, il s’applique toujours à la base de données, quels que soit les paramètres de la base de données.
13. Sur le Portail Azure, dans le menu de gauche de la page Vue d’ensemble de votre base de données SQL, sélectionnez **Éditeur de requête (préversion)**. Essayez de vous connecter, vous risquez de rencontrer un problème avec le mot de passe, la règle de pare-feu de votre adresse IP, tout étant audité. Essayez également de réussir la connexion, exécutez la requête et vous pouvez trouver plus de détails dans les journaux d’audit

14. revenez à la base de données, Audit et cliquez sur **Afficher les journaux d’audit**.

15. Dans le volet **Enregistrements d’audit**, notez que vous pouvez basculer entre l’audit du serveur et l’audit de base de données. 

> Résultats : vous avez créé un serveur et une base de données SQL, configuré la classification des données et l’audit. 


**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus. 

1. Dans le portail Azure, ouvrez Cloud Shell en cliquant sur la première icône située en haut à droite du portail Azure. Si vous y êtes invité, cliquez sur **PowerShell** et **Créer un stockage**.

2. Vérifiez que **PowerShell** est sélectionné dans le menu déroulant en haut à gauche du volet Cloud Shell.

3. Dans la session PowerShell du volet Cloud Shell, exécutez ce qui suit pour supprimer le groupe de ressources que vous avez créé dans ce labo :
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB11" -Force -AsJob
    ```
4. Fermez le volet **Cloud Shell**. 

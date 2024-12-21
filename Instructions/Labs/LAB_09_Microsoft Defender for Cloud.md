---
lab:
  title: 09 – Microsoft Defender pour le cloud
  module: Module 03 - Manage security posture by using Microsoft Defender for Cloud
---

# Lab 09 : Microsoft Defender pour le cloud
# Manuel de labo de l’étudiant

## Scénario du labo

Vous avez été invité à créer une preuve de concept d’environnement Microsoft Defender pour le cloud. Plus précisément, vous souhaitez :

- Configurez les fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud pour les serveurs afin de surveiller une machine virtuelle.
- Passer en revue les recommandations de Microsoft Defender pour le cloud pour la machine virtuelle.
- Implémenter des recommandations pour la configuration des invités et l’accès juste-à-temps à la machine virtuelle. 
- Passer en revue la façon dont le niveau de sécurité peut être utilisé pour déterminer la progression vers la création d’une infrastructure plus sécurisée.

> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit bien de la région à utiliser. 

## Objectifs du labo

Dans ce labo, vous effectuerez les exercices suivants :

- Exercice 1 : Implémenter Microsoft Defender pour le cloud

## Diagramme Microsoft Defender pour le cloud

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/c31055cc-de95-41f6-adef-f09d756a68eb)

## Instructions

### Exercice 1 : Implémenter Microsoft Defender pour le cloud

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Configurer Microsoft Defender pour le cloud
- Tâche 2 : Passer en revue les recommandations de Microsoft Defender pour le cloud
- Tâche 3 : Implémenter la recommandation Microsoft Defender pour le cloud afin d’activer l’accès juste-à-temps aux machines virtuelles

#### Tâche 1 : configurer les fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud pour les serveurs

Dans cette tâche, vous allez intégrer et configurer les fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud pour les serveurs.

1. Démarrez une session de navigateur et connectez-vous à l’[abonnement Azure](https://azure.microsoft.com/en-us/free/?azure-portal=true). où vous disposez de droits d’accès administratif.

2. Dans le portail Azure, dans la zone de texte Rechercher des ressources, des services et des documents située en haut de la page du portail Azure, saisissez Microsoft Defender pour le cloud et appuyez sur la touche Entrée.

3. Dans Microsoft Defender pour le cloud, dans le panneau de gestion, accédez aux paramètres d’environnement. Développez les dossiers des paramètres d’environnement jusqu’à ce que la section abonnement s’affiche, puis cliquez sur l’abonnement pour afficher les détails.

4. Dans le panneau Paramètres, sous plans Defender, développez Protection de la charge de travail du cloud.
  
5. Dans la liste Plan de protection de charge de la charge de travail du cloud, sélectionnez Serveurs. À droite de la page, remplacez le Statut Désactivé par Activé, puis cliquez sur Enregistrer.
  
6. Pour passer en revue les détails du Plan 2 de Microsoft Defender pour serveurs, sélectionnez Modifier le plan >.

>**Remarque** : l’activation du plan Serveurs de la Protection de la charge de travail du cloud active également le Plan 2 de Microsoft Defender pour serveurs.

#### Tâche 2 : Passer en revue les recommandations de Microsoft Defender pour le cloud

Dans cette tâche, vous passerez en revue les recommandations de Microsoft Defender pour le cloud. 

1. Dans le Portail Azure, revenez au volet **Microsoft Defender pour le cloud \| Vue d’ensemble**. 

2. Dans le volet **Microsoft Defender pour le cloud \| Vue d’ensemble**, passez en revue la partie **Niveau de sécurité**.

    >**Remarque** : notez le niveau en cours s’il est disponible.

3. Revenez au panneau **Microsoft Defender pour le cloud \| Vue d’ensemble** et cliquez sur **Ressources évaluées**.

4. Dans le panneau **Inventaire**, cliquez sur l’entrée **myVM**.

    >**Remarque** : vous devrez peut-être attendre quelques minutes et actualiser la page du navigateur pour que l’entrée apparaisse.
    
5. Dans le volet **Intégrité des ressources**, sous l’onglet **Recommandations**, passez en revue la liste des recommandations pour **myVM**.

#### Tâche 3 : Implémenter la recommandation Microsoft Defender pour le cloud afin d’activer l’accès juste-à-temps aux machines virtuelles

Dans cette tâche, vous allez implémenter la recommandation Microsoft Defender pour le cloud pour activer l’accès Juste à temps à la machine virtuelle sur la machine virtuelle. 

1. Dans le portail Azure, revenez au panneau **Microsoft Defender pour le cloud \| Vue d’ensemble** et cliquez sur **Protections de charge de travail** sous **Sécurité cloud** dans le volet de navigation de gauche.

2. Dans le panneau **Microsoft Defender pour le cloud \| Protections de charge de travail**, faites défiler vers le bas jusqu’à la section **Protection avancée** et cliquez sur la vignette **Accès Juste à temps à la machine virtuelle**.

3. Dans le panneau **Accès Juste à temps à la machine virtuelle**, sous la section **Machines virtuelles**, sélectionnez **Non configurée**, puis cochez la case pour l’entrée **myVM**.

    >**Remarque** : Vous allez peut-être devoir attendre quelques minutes, actualiser la page du navigateur et resélectionner **Non configurée** pour que l’entrée apparaisse.

4. Cliquez sur l’option **Activer JIT sur 1 machine virtuelle** à l’extrême droite de la section **Machines virtuelles**.

5. Dans le volet **Configuration de l’accès à la machine virtuelle JIT**, à droite de la ligne faisant référence au port **22**, cliquez sur le bouton Points de suspension (...), puis cliquez sur **Supprimer**.

6. Dans le volet **Configuration de l’accès à la machine virtuelle JIT**, cliquez sur **Enregistrer**.

    >**Remarque** : surveillez la progression de la configuration en cliquant sur l’icône **Notifications** dans la barre d’outils et en affichant le panneau **Notifications**. 

    >**Remarque** : La mise en œuvre des recommandations de ce laboratoire peut prendre un certain temps avant d’être reflétée par le niveau de sécurité. Vérifiez régulièrement le niveau de sécurité pour déterminer l’impact de l’implémentation de ces fonctionnalités. 

> Résultats : vous avez lancé Microsoft Defender pour le cloud et mis en œuvre les recommandations sur les machines virtuelles. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.

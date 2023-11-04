---
lab:
  title: 14 - Microsoft Defender pour le cloud
  module: Module 04 - Microsoft Defender for Cloud
---

# Labo 14 : Microsoft Defender pour le cloud
# Manuel de labos pour étudiant

## Scénario du labo

Vous avez été invité à créer une preuve de concept d’environnement Microsoft Defender pour le cloud. Plus précisément, vous souhaitez :

- Configurer Microsoft Defender pour le cloud pour superviser une machine virtuelle.
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
- Tâche 3 : Implémenter la recommandation Microsoft Defender pour le cloud pour activer l’accès Juste à temps à la machine virtuelle

#### Tâche 1 : Configurer Microsoft Defender pour le cloud

Dans cette tâche, vous allez démarrer Microsoft Defender pour le cloud et le configurer.

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au Portail Azure à l’aide d’un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce laboratoire.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Microsoft Defender pour le cloud** et appuyez sur la touche **Entrée**.

3. Dans le volet de navigation de gauche, cliquez sur **Prise en main**. Dans le volet **Microsoft Defender pour le cloud \| Prise en main**, cliquez sur **Mettre à niveau**.
     
4. Dans le panneau **Microsoft Defender pour le cloud \| Prise en main**, sous l’onglet Installer des Agents, faites défiler vers le bas et cliquez sur **Installer des Agents**. 

5. Dans le volet **Microsoft Defender pour le cloud \| Prise en main**, sous l’onglet **Mettre à niveau** >> faites défiler jusqu’à la section **Sélectionner des espaces de travail avec des fonctionnalités de sécurité renforcée** >> activez le **plan Microsoft Defender** en sélectionnant votre espace de travail Log Analytics, puis cliquez sur le grand bouton bleu Mettre à niveau.  

    >**Remarque** : passez en revue toutes les fonctionnalités disponibles dans le cadre des plans Microsoft Defender. 

6. Accédez à **Microsoft Defender pour le cloud** et, dans le volet de navigation de gauche sous la section Administration, cliquez sur **Paramètres d’environnement**.

7. Dans le panneau **Microsoft Defender pour le cloud \| Paramètres d’environnement**, faites défiler vers le bas, développez jusqu’à ce que votre abonnement s’affiche, puis cliquez sur l’abonnement approprié. 

8. Dans le panneau **Paramètres \| Plans Defender**, sélectionnez **Activer tous les plans** et cliquez sur **Enregistrer** si besoin.

9. Revenez au panneau **Microsoft Defender pour le cloud \| Paramètres d’environnement**, développez jusqu’à voir votre abonnement, puis cliquez sur l’entrée représentant l’espace de travail Log Analytics que vous avez créé dans le labo précédent.

10. Dans le panneau **Paramètres \| Plans Defender**, vérifiez que toutes les options sont activées. Si nécessaire, cliquez sur **Activer tous les plans**, puis sur **Enregistrer**.

11. Sélectionnez **Collecte de données** dans le panneau **Paramètres \| Plans Defender**. Cliquez sur **Tous les événements** et sur **Enregistrer**.

#### Tâche 2 : Passer en revue les recommandations de Microsoft Defender pour le cloud

Dans cette tâche, vous passerez en revue les recommandations de Microsoft Defender pour le cloud. 

1. Dans le Portail Azure, revenez au volet **Microsoft Defender pour le cloud \| Vue d’ensemble**. 

2. Dans le volet **Microsoft Defender pour le cloud \| Vue d’ensemble**, passez en revue la partie **Niveau de sécurité**.

    >**Remarque** : notez le niveau en cours s’il est disponible.

3. Revenez au panneau **Microsoft Defender pour le cloud \| Vue d’ensemble** et cliquez sur **Ressources évaluées**.

4. Dans le panneau **Inventaire**, cliquez sur l’entrée **myVM**.

    >**Remarque** : vous devrez peut-être attendre quelques minutes et actualiser la page du navigateur pour que l’entrée apparaisse.
    
5. Dans le volet **Intégrité des ressources**, sous l’onglet **Recommandations**, passez en revue la liste des recommandations pour **myVM**.

#### Tâche 3 : Implémenter la recommandation Microsoft Defender pour le cloud pour activer l’accès Juste à temps à la machine virtuelle

Dans cette tâche, vous allez implémenter la recommandation Microsoft Defender pour le cloud pour activer l’accès Juste à temps à la machine virtuelle sur la machine virtuelle. 

1. Dans le portail Azure, revenez au panneau **Microsoft Defender pour le cloud \| Vue d’ensemble** et cliquez sur **Protections de charge de travail** sous **Sécurité cloud** dans le volet de navigation de gauche.

2. Dans le panneau **Microsoft Defender pour le cloud \| Protections de charge de travail**, faites défiler vers le bas jusqu’à la section **Protection avancée** et cliquez sur la vignette **Accès Juste à temps à la machine virtuelle**.

3. Dans le panneau **Accès Juste à temps à la machine virtuelle**, sous la section **Machines virtuelles**, sélectionnez **Non configurée**, puis cochez la case pour l’entrée **myVM**.

    >**Remarque** : Vous devrez peut-être attendre quelques minutes, actualiser la page du navigateur et resélectionner **Non configurée** pour que l’entrée apparaisse.

4. Cliquez sur l’option **Activer JIT sur 1 machine virtuelle** à l’extrême droite de la section **Machines virtuelles**.

5. Dans le volet **Configuration de l’accès à la machine virtuelle JIT**, à droite de la ligne faisant référence au port **22**, cliquez sur le bouton Points de suspension (...), puis cliquez sur **Supprimer**.

6. Dans le volet **Configuration de l’accès à la machine virtuelle JIT**, cliquez sur **Enregistrer**.

    >**Remarque** : surveillez la progression de la configuration en cliquant sur l’icône **Notifications** dans la barre d’outils et en affichant le panneau **Notifications**. 

    >**Remarque** : La mise en œuvre des recommandations de ce laboratoire peut prendre un certain temps avant d’être reflétée par le niveau de sécurité. Vérifiez régulièrement le niveau de sécurité pour déterminer l’impact de l’implémentation de ces fonctionnalités. 

> Résultats : vous avez lancé Microsoft Defender pour le cloud et mis en œuvre les recommandations sur les machines virtuelles. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Microsoft Sentinel lab.

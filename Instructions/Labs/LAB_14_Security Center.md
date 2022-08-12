---
lab:
  title: 14 - Microsoft Defender pour le cloud
  module: Module 04 - Microsoft Defender for Cloud
ms.openlocfilehash: 6ec274b75692321577c8966e07349211209eaa02
ms.sourcegitcommit: a8470295248a6363987bd5ea47154fe39f8535c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/09/2022
ms.locfileid: "145195907"
---
# <a name="lab-14-microsoft-defender-for-cloud"></a>Labo 14 : Microsoft Defender pour le cloud
# <a name="student-lab-manual"></a>Manuel de labos pour étudiant

## <a name="lab-scenario"></a>Scénario du labo

Vous avez été invité à créer une preuve de concept d’environnement Microsoft Defender pour le cloud. Plus précisément, vous souhaitez :

- Configurer Microsoft Defender pour le cloud pour superviser une machine virtuelle.
- Passer en revue les recommandations de Microsoft Defender pour le cloud pour la machine virtuelle.
- Implémenter des recommandations pour la configuration des invités et l’accès Juste à temps à la machine virtuelle. 
- Passer en revue la façon dont le niveau de sécurité peut être utilisé pour déterminer la progression vers la création d’une infrastructure plus sécurisée.

> Pour toutes les ressources de ce labo, nous utilisons la région **USA Est**. Vérifiez avec votre instructeur qu’il s’agit de la région à utiliser pour la classe. 

## <a name="lab-objectives"></a>Objectifs du labo

Dans ce labo, vous effectuerez les exercices suivants :

- Exercice 1 : Implémenter Microsoft Defender pour le cloud

## <a name="microsoft-defender-for-cloud-diagram"></a>Diagramme Microsoft Defender pour le cloud

![image](https://user-images.githubusercontent.com/91347931/157537800-94a64b6e-026c-41b2-970e-f8554ce1e0ab.png)

## <a name="instructions"></a>Instructions

### <a name="exercise-1-implement-microsoft-defender-for-cloud"></a>Exercice 1 : Implémenter Microsoft Defender pour le cloud

Dans cet exercice, vous allez effectuer les tâches suivantes :

- Tâche 1 : Configurer Microsoft Defender pour le cloud
- Tâche 2 : Passer en revue les recommandations de Microsoft Defender pour le cloud
- Tâche 3 : Implémenter la recommandation Microsoft Defender pour le cloud pour activer l’accès Juste à temps à la machine virtuelle

#### <a name="task-1-configure-microsoft-defender-for-cloud"></a>Tâche 1 : Configurer Microsoft Defender pour le cloud

Dans cette tâche, vous allez démarrer Microsoft Defender pour le cloud et le configurer.

1. Connectez-vous au portail Azure **`https://portal.azure.com/`** .

    >**Remarque** : connectez-vous au Portail Azure à l’aide d’un compte disposant du rôle Propriétaire ou Contributeur dans l’abonnement Azure que vous utilisez pour ce laboratoire.

2. Dans le portail Azure, dans la zone de texte **Rechercher des ressources, des services et des documents** en haut de la page Portail Azure, tapez **Microsoft Defender pour le cloud** et appuyez sur la touche **Entrée**.

3. Si cela n'a pas été fait précédemment, dans le volet **Prise en main\| de Microsoft Defender pour le cloud**, cliquez sur **Mettre à niveau**.
     
4. Si cela n'a pas été fait précédemment, dans le volet **Prise en main\| de Microsoft Defender pour le cloud**, sous l’onglet **Installer des agents**, faites défiler vers le bas et cliquez sur **Installer des agents**.

5. Dans le volet **Prise en main\| de Microsoft Defender pour le cloud**, sous l’onglet **Mettre à niveau** >> dans la section **Sélectionner des espaces de travail avec des fonctionnalités de sécurité renforcée** >> activez le **plan Microsoft Defender** en sélectionnant votre espace de travail Log Analytics. 

    >**Remarque** : passez en revue toutes les fonctionnalités disponibles dans le cadre des plans Microsoft Defender. 

6. Dans le volet **Plans Defender**, sélectionnez **Activer tous les plans Microsoft Defender pour le cloud**, puis cliquez sur **Enregistrer**.

7. Accédez à **Microsoft Defender pour le cloud** et cliquez sur **Paramètres d’environnement** sous les paramètres de gestion, dans la barre de menus verticale sur le côté gauche.

8. Dans le volet **Microsoft Defender pour le cloud | Paramètres d’environnement**, cliquez sur l’abonnement approprié. 

9. Dans le volet **Paramètres | Plans Defender**, dans le menu vertical situé à gauche, cliquez sur **Approvisionnement automatique**.

10. Dans le volet **Paramètres | Approvisionnement automatique**, assurez-vous que l’approvisionnement automatique est **activé** pour le premier **agent Log Analytics pour les machines virtuelles Azure**.

11. Dans le volet **Paramètres \| Automatisation du workflow**, cliquez sur **+ Ajouter une automatisation du workflow**.

12. Dans le volet **Ajouter une automatisation du workflow**, passez en revue les paramètres disponibles. 

    >**Remarque** : vous pouvez déclencher des actions basées sur des alertes de détection des menaces et des recommandations Microsoft Defender pour le cloud. Vous pouvez également configurer une action en fonction des applications logiques. 

13. Dans le panneau **Ajouter une automatisation du workflow**, cliquez sur **Annuler**.

    >**Remarque** : Microsoft Defender pour le cloud fournit de nombreuses informations sur les machines virtuelles, notamment l’état de mise à jour du système, les configurations de sécurité du système d’exploitation et la protection des points de terminaison.

14. Revenez au volet **Microsoft Defender pour le cloud \|Paramètres d’environnement**, développez votre abonnement, puis cliquez sur l’entrée représentant l’espace de travail Log Analytics que vous avez créé dans le labo précédent.

15. Dans le volet **Paramètres \| Plans Defender**, vérifiez que l’option **Activer tous les plans Microsoft Defender pour le cloud** est sélectionnée et cliquez sur **Enregistrer**.

16. Sélectionnez **Collecte de données** dans le volet **Paramètres\| Microsoft Defender pour le cloud**. Sélectionnez **Tous les événements** et **enregistrez**.


#### <a name="task-2-review-the-microsoft-defender-for-cloud-recommendation"></a>Tâche 2 : Passer en revue les recommandations de Microsoft Defender pour le cloud

Dans cette tâche, vous passerez en revue les recommandations de Microsoft Defender pour le cloud. 

1. Dans le Portail Azure, revenez au volet **Microsoft Defender pour le cloud \| Vue d’ensemble**. 

2. Dans le volet **Microsoft Defender pour le cloud \| Vue d’ensemble**, passez en revue la partie **Niveau de sécurité**.

    >**Remarque** : notez le niveau en cours s’il est disponible.

3. Revenez au volet **Microsoft Defender pour le cloud \| Vue d’ensemble**, sélectionnez **Ressources évaluées**.

4. Dans le panneau **Inventaire**, sélectionnez l’entrée **myVM**.

    >**Remarque** : vous devrez peut-être attendre quelques minutes et actualiser la page du navigateur pour que l’entrée apparaisse.
    
5. Dans le volet **Intégrité des ressources**, sous l’onglet **Recommandations**, passez en revue la liste des recommandations pour **myVM**.


#### <a name="task-3-implement-the-microsoft-defender-for-cloud-recommendation-to-enable-just-in-time-vm-access"></a>Tâche 3 : Implémenter la recommandation Microsoft Defender pour le cloud pour activer l’accès Juste à temps à la machine virtuelle

Dans cette tâche, vous allez implémenter la recommandation Microsoft Defender pour le cloud pour activer l’accès Juste à temps à la machine virtuelle sur la machine virtuelle. 

1. Dans le portail Azure, revenez au volet **Microsoft Defender pour le cloud \| Vue d’ensemble**, puis sélectionnez les **protections de charge de travail** sous la partie **Sécurité cloud**.

2. Dans le panneau **Protections de charge de travail**, dans la section **Protection avancée** , cliquez sur la vignette **Accès Juste à temps à la machine virtuelle** puis, dans le panneau **Accès Juste à temps à la machine virtuelle**, cliquez sur **Accès Juste à temps à la machine virtuelle**.

    >**Remarque** : si les machines virtuelles ne sont pas répertoriées, accédez au volet **Machine virtuelle** et cliquez sur **Configuration**. Cliquez sur l’option **Activer les machines virtuelles Juste à temps** sous **l’accès Juste à temps de la machine virtuelle**. Répétez l’étape ci-dessus pour revenir à la page **Microsoft Defender pour le cloud** et actualiser la page : la machine virtuelle s’affiche.

3. Dans la page **Accès Juste à temps à la machine virtuelle**, sélectionnez **Non configuré**, puis cliquez sur l’entrée **myVM**.

    >**Remarque** : vous devrez peut-être attendre quelques minutes avant que l’entrée **myVM** ne soit disponible.

4. Sélectionnez **Activer JIT sur 1 machine virtuelle**.

5. Dans le volet **Configuration de l’accès à la machine virtuelle JIT**, à droite de la ligne faisant référence au port **22**, cliquez sur le bouton Points de suspension (...), puis cliquez sur **Supprimer**.

6. Dans le volet **Configuration de l’accès à la machine virtuelle JIT**, cliquez sur **Enregistrer**.

    >**Remarque** : surveillez la progression de la configuration en cliquant sur l’icône **Notifications** dans la barre d’outils et en affichant le panneau **Notifications**. 

    >**Remarque** : La mise en œuvre des recommandations de ce laboratoire peut prendre un certain temps avant d’être reflétée par le niveau de sécurité. Vérifiez régulièrement le niveau de sécurité pour déterminer l’impact de l’implémentation de ces fonctionnalités. 

> Résultats : vous avez lancé Microsoft Defender pour le cloud et mis en œuvre les recommandations sur les machines virtuelles. 

    >**Note**: Do not remove the resources from this lab as they are needed for the Azure Sentinel lab.

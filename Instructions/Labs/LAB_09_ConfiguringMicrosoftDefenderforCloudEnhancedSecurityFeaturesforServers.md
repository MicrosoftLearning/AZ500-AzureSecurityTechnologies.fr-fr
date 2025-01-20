---
lab:
  title: "09 - Configuration des fonctionnalités de sécurité renforcée de Microsoft\_Defender for Cloud pour les serveurs"
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# Labo 09 : configuration des fonctionnalités de sécurité renforcée de Microsoft Defender for Cloud pour les serveurs

# Manuel de labo de l’étudiant

## Scénario du labo

En tant qu’ingénieur sécurité Azure pour une entreprise internationale de e-commerce, vous êtes responsable de la sécurisation de l’infrastructure cloud de l’entreprise. L’organisation s’appuie fortement sur des machines virtuelles Azure et des serveurs locaux pour exécuter des applications critiques, gérer les données client et traiter les transactions. Le directeur de la sécurité de l’information (CISO) a identifié la nécessité de mesures de sécurité renforcées pour protéger ces ressources contre les cybermenaces, les vulnérabilités et les mauvaises configurations. Vous avez été chargé d’activer Microsoft Defender pour serveurs dans Microsoft Defender for Cloud pour fournir une protection avancée contre les menaces et une surveillance de la sécurité pour les machines virtuelles Azure et les serveurs hybrides.

## Objectifs du labo

- Configuration des fonctionnalités de sécurité renforcée pour les serveurs de Microsoft Defender pour le cloud
  
- Passer en revue les fonctionnalités de sécurité renforcée pour le Plan 2 de Microsoft Defender pour serveurs

## Instructions de l’exercice

### Configuration des fonctionnalités de sécurité renforcée pour les serveurs de Microsoft Defender pour le cloud

1. Dans le portail Azure, dans la zone de texte Rechercher des ressources, des services et des documents située en haut de la page du portail Azure, saisissez **Microsoft Defender pour le cloud** et appuyez sur la touche **Entrée**.

2. Dans **Microsoft Defender pour le cloud**, dans le **panneau de gestion**, accédez aux **paramètres d’environnement**. Développez les dossiers des paramètres d’environnement jusqu’à ce que la section **abonnement** s’affiche, puis cliquez sur l’**abonnement** pour afficher les détails.

   ![image](https://github.com/user-attachments/assets/3b25dd82-e09e-4f8a-b85e-c9bc6c4bd488)
   
3. Dans le panneau **Paramètres**, sous **plans Defender**, développez **Protection de la charge de travail du cloud**.

4. Dans la liste **Plan de protection de charge de la charge de travail du cloud**, sélectionnez **Serveurs**. À droite de la page, remplacez le **Statut** **Désactivé** par **Activé**, puis cliquez sur **Enregistrer.**

5. Pour passer en revue les détails du **Plan 2 de Microsoft Defender pour serveurs**, sélectionnez **Modifier le plan >**.

   Remarque : l’activation du plan Serveurs de la Protection de la charge de travail du cloud active également le Plan 2 de Microsoft Defender pour serveurs.
 
   ![image](https://github.com/user-attachments/assets/de434a75-345a-4023-83f1-fa53fcb5f288)
   
> **Résultats** : vous avez activé le Plan 2 de Microsoft Defender pour serveurs pour votre abonnement.

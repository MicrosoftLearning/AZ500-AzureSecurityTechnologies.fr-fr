---
lab:
  title: 10 - Activer l’accès juste-à-temps sur des machines virtuelles
  module: Module 03 - Configure and manage threat protection by using Microsoft Defender for Cloud
---

# Labo 10 : activer l’accès juste-à-temps sur des machines virtuelles

# Manuel de labo de l’étudiant

## Scénario du labo

En tant qu’ingénieur sécurité Azure au sein d’une société de services financiers, vous êtes responsable de la sécurisation des ressources Azure, y compris des machines virtuelles qui hébergent des applications critiques. L’équipe de sécurité a identifié que l’accès ouvert continu aux machines virtuelles augmente le risque d’attaques par force brute et d’accès non autorisé. Pour atténuer ce problème, le directeur de la sécurité de l’information (CISO) vous a demandé d’activer l’accès juste-à-temps (JIT) sur une machine virtuelle Azure spécifique utilisée pour traiter les transactions financières.

## Objectifs du labo

Dans ce labo, vous allez effectuer les exercices suivants :

- Exercice 1 : activer l’accès JIT sur vos machines virtuelles depuis le portail Azure.

- Exercice 2 : demander l’accès à une machine virtuelle sur laquelle l’accès JIT est activé depuis le portail Azure.

## Instructions de l’exercice 

### Exercice 1 : activer l’accès JIT sur vos machines virtuelles à partir des machines virtuelles Azure

>**Remarque** : vous pouvez activer l’accès JAT sur une machine virtuelle depuis les pages des machines virtuelles Azure sur le portail Azure.

1. Dans la zone de recherche située en haut du portail, entrez **machines virtuelles**. Sélectionnez **Machines virtuelles** dans les résultats de la recherche.

2. Sélectionnez **myVM**.
 
3. Sélectionnez **Configuration** dans la section **Paramètres** de **myVM.**.
   
4. Dans **Accès juste-à-temps à la machine virtuelle**, sélectionnez **Activer l’accès juste-à-temps**.

5. Sous **Accès juste-à-temps à la machine virtuelle,** cliquez sur le lien intitulé **Ouvrir Microsoft Defender pour le cloud.**

6. Par défaut, l’accès juste-à-temps pour la machine virtuelle utilise les paramètres suivants :

   - Machines Windows
   
     - Port RDP : 3389
     - Accès autorisé au maximum : trois heures
     - Adresses IP sources autorisées : toutes les adresses IP

   - Machines Linux
     - Port SSH : 22
     - Accès autorisé au maximum : trois heures
     - Adresses IP sources autorisées : toutes les adresses IP
   
7. Par défaut, l’accès juste-à-temps pour la machine virtuelle utilise les paramètres suivants :

   - Dans l’onglet **Configuré**, cliquez avec le bouton droit sur la machine virtuelle à laquelle vous souhaitez ajouter un port, puis sélectionnez Modifier.

   ![image](https://github.com/user-attachments/assets/aa4ded55-c5b1-4d40-b5a0-a4c33b9eb81b)
   
   - Sous **JIT VM access configuration** (Configuration de l’accès juste-à-temps à la machine virtuelle), vous pouvez soit modifier les paramètres existants d’un port déjà protégé, soit ajouter un nouveau port personnalisé.
   - Lorsque vous avez fini de modifier les ports, sélectionnez **Enregistrer**.   

### Exercice 2 : demander l’accès à une machine virtuelle prenant en charge l’accès JIT depuis la page de connexion de la machine virtuelle Azure.

>**Remarque** :  lorsque l’accès JAT est activé pour une machine virtuelle, vous devez demander cet accès pour vous y connecter. Vous pouvez demander l’accès selon l’une des méthodes prises en charge, quelle que soit la façon dont vous avez activé l’accès JAT.
   
1. Dans le portail Azure, ouvrez la page des machines virtuelles.

2. Sélectionnez la machine virtuelle à laquelle vous souhaitez vous connecter, puis ouvrez la page **Se connecter**.

   - Azure vérifie si l’accès JAT est activé sur cette machine virtuelle.

        - Si JAT n’est pas activé pour la machine virtuelle, vous êtes invité à l’activer.
    
        - S’il est activé, sélectionnez **Demander l’accès** pour transmettre une demande d’accès avec l’adresse IP, la plage de temps et les ports de la requête qui ont été configurés pour cette machine virtuelle.
    
   ![image](https://github.com/user-attachments/assets/f5d0b67c-7731-4261-b0eb-a56c505dadd4)

> **Résultats** : vous avez étudié différentes méthodes pour activer l’accès JAT sur vos machines virtuelles et savoir comment demander l’accès aux machines virtuelles sur lesquelles il est activé dans Microsoft Defender pour le cloud.

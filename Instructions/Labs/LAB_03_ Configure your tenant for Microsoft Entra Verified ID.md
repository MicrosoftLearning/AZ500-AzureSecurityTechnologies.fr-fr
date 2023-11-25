---
lab:
  title: "03 – Configurer votre tenant pour l’identité vérifié Microsoft\_Entra"
  module: Module 01 - Manage Identity and Access
---
# Lab 03 : Configurer votre tenant pour l’identité vérifié Microsoft Entra
# Manuel de labo de l’étudiant

## Scénario de l’exercice

# Configurer votre locataire pour Vérification d’identité Microsoft Entra

>[!Note] 
> Les justificatifs vérifiables Azure Active Directory sont désormais ID vérifié Microsoft Entra et font partie de la famille de produits Microsoft Entra. En savoir plus sur la [famille Microsoft Entra](https://aka.ms/EntraAnnouncement) de solutions d’identité et bien démarrer dans le [centre d’administration unifié Microsoft Entra](https://entra.microsoft.com).

Vérification d'identité Microsoft Entra est une solution d’identité décentralisée qui vous aide à protéger votre organisation. Ce service vous permet d’émettre et de vérifier des informations d’identification. Les émetteurs peuvent utiliser le service d’ID vérifié pour émettre leurs propres justificatifs vérifiables personnalisés. Les vérificateurs peuvent utiliser l’API REST gratuite du service pour demander et accepter facilement les justificatifs vérifiables dans leurs applications et leurs services. Dans les deux cas, votre locataire Azure AD doit être configuré pour émettre vos propres justificatifs vérifiables ou vérifier la présentation des justificatifs vérifiables d’un utilisateur émis par un tiers. Dans le cas où vous êtes à la fois émetteur et vérificateur, vous pouvez utiliser le même locataire Azure AD pour émettre vos propres justificatifs vérifiables et vérifier ceux des autres.

Dans ce tutoriel, vous apprendrez à configurer votre locataire Azure AD pour utiliser le service de justificatifs vérifiables.

Plus précisément, vous apprenez à :

> - Créer une instance Azure Key Vault
> - Configurez le service ID vérifié.
> - Inscrire une application dans Azure AD
> - Vérifier la propriété du domaine auprès de votre identificateur décentralisé (DID)

Le diagramme suivant illustre l’architecture Vérification d’identité et le composant que vous configurez.

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/8d4d01c2-3110-421a-91a8-7b052bc8d793)


## Prérequis

- Vérifiez que vous disposez de l’autorisation d’administrateur général ou d’administrateur de stratégie d’authentification pour l’annuaire à configurer. Si vous n’êtes pas administrateur général, vous avez besoin de l’autorisation d’administrateur d’application pour effectuer l’inscription d’application, y compris octroyer le consentement d’administrateur.
- Vérifiez que vous disposez du rôle Contributeur pour l’abonnement Azure ou le groupe de ressources où vous allez déployer Azure Key Vault.

## Création d’un coffre de clés

Azure Key Vault est un service cloud qui permet l’accès et le stockage sécurisés des secrets et des clés. Le service Vérification d’ID stocke les clés publiques et privées dans Azure Key Vault. Ces clés sont utilisées pour signer et vérifier les justificatifs.

### Utilisez le Portail Azure pour créer une instance Azure Key Vault.

1. Démarrez une session de navigateur et connectez-vous au [menu du Portail Azure.](https://portal.azure.com/)
   
2. Dans la zone Rechercher du Portail Azure, entrez **Key Vault.**

3. Dans la liste des résultats, choisissez **Key Vault**.

4. Dans la section Key Vault, choisissez **Créer.**

5. Sous l’onglet **De base** de **Créer un coffre de clés**, entrez ou sélectionnez les informations suivantes :
   
   |Paramètre|Valeur|
   |---|---|
   |**Détails du projet**|
   |Abonnement|Sélectionnez votre abonnement.|
   |Resource group|Entrez **azure-rg-1.** Sélectionnez **OK**.|
   |**Détails de l’instance**|
   |Nom du coffre de clés|Entrez **Contoso-vault2.**|
   |Région|Sélectionnez **USA Est**.|
   |Niveau tarifaire|Valeur système par défaut **Standard**|
   |Jours de conservation des coffres supprimés|Valeur système par défaut **90**|

6. Sélectionnez l’**onglet Vérifier + créer** ou sélectionnez le bouton bleu Vérifier + créer en bas de la page.
  
7. Cliquez sur **Créer**.

Notez les deux propriétés ci-dessous :

* **Nom du coffre** : dans l’exemple, il s’agit de **Contoso-Vault2**. Vous utilisez ce nom dans les autres étapes.
* **URI du coffre** : dans l’exemple, l’URI du coffre est `https://contoso-vault2.vault.azure.net/`. Les applications qui utilisent votre coffre via son API REST doivent utiliser cet URI.

À ce stade, votre compte Azure est le seul autorisé à effectuer des opérations sur ce nouveau coffre.

>[!NOTE]
>Par défaut, le compte qui crée un coffre est le seul à pouvoir y accéder. Le service de vérification d’ID doit avoir accès au coffre de clés. Vous devez configurer votre coffre de clés avec des stratégies d’accès autorisant le compte utilisé lors de la configuration à créer et à supprimer des clés. Le compte utilisé lors de la configuration nécessite également des autorisations pour signer pour qu’il puisse créer la liaison de domaine pour l’ID vérifié. Si vous utilisez le même compte lors des tests, modifiez la stratégie par défaut pour accorder au compte l’autorisation de signature, en plus des autorisations par défaut accordées aux créateurs de coffres.

### Définir des stratégies d’accès pour le coffre de clés

Un coffre de clés définit si un principal de sécurité spécifié peut effectuer des opérations sur les clés et secrets de Key Vault. Définissez des stratégies d’accès dans votre coffre de clés pour le compte d’administrateur du service Vérification d’identité et pour le principal d’API du service de requête que vous avez créé.
Une fois votre coffre de clés créé, Justificatifs vérifiables génère un jeu de clés utilisé pour assurer la sécurité des messages. Ces clés sont stockées dans le coffre de clés. Vous utilisez un jeu de clés pour signer, mettre à jour et récupérer des justificatifs vérifiables.

### Définir des stratégies d’accès pour l’utilisateur administrateur de vérification d’ID

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Accédez au coffre de clés que vous utilisez pour ce tutoriel.

3. Sous **Paramètres**, sélectionnez **Stratégies d’accès**.

4. Dans **Ajouter des stratégies d’accès**, sous **UTILISATEUR**, sélectionnez le compte que vous utilisez pour ce tutoriel.

5. Pour **Autorisations de clé**, vérifiez que les autorisations suivantes sont sélectionnées : **Get**, **Créer**, **Supprimer** et **Signer**. Par défaut, les paramètres **Créer** et **Supprimer** sont déjà activés. **Signer** doit être la seule autorisation de clé que vous devez mettre à jour.

      ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/7c8a92ea-24f1-41e6-9656-869e8486af72)


6. Pour enregistrer les modifications, sélectionnez **Enregistrer**.

## Configurer un ID vérifié

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b4b857a2-24b8-4c3f-9f5c-43d23b58427f)


Pour configurer un ID vérifié, procédez comme suit :

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Recherche un *ID vérifié*. Ensuite, sélectionnez **ID vérifié**.

3. Dans le menu de gauche, sélectionnez **Configuration**.

4. Dans le menu central, sélectionnez **Choisir les paramètres de l’organisation**

5. Configurez votre organisation en fournissant les informations suivantes :

    1. **Nom de l’organisation** : Entrez un nom pour faire référence à votre entreprise dans le cadre des ID vérifiés. Vos clients ne voient pas ce nom.

    1. **Domaine approuvé** : Entrez un domaine ajouté à un point de terminaison de service dans votre document d’identité décentralisée (DID). Le domaine est ce qui lie votre DID à un élément tangible que l’utilisateur peut connaître sur votre entreprise. Microsoft Authenticator et d’autres portefeuilles numériques utilisent ces informations pour s’assurer que votre DID est lié à votre domaine. Si le portefeuille peut vérifier le DID, il affiche un symbole vérifié. Dans le cas contraire, il informe l’utilisateur que des justificatifs ont été émis par une organisation qu’il n’a pas pu valider.

        >[!IMPORTANT]
        > Le domaine ne peut pas être une redirection. Dans le cas contraire, le DID et le domaine ne peuvent pas être liés. Veillez à utiliser le protocole HTTPS pour le domaine. Par exemple : `https://did.woodgrove.com`.

    1. **Coffre de clés** : sélectionnez le coffre de clés que vous avez créé précédemment.

    1. Sous **Avancé**, vous pouvez choisir le **système de confiance** que vous souhaitez utiliser pour votre locataire. Vous pouvez choisir entre **Web** ou **ION**. Web signifie que votre locataire utilise [did:web](https://w3c-ccg.github.io/did-method-web/) comme méthode et ION signifie qu’il utilise [did:ion](https://identity.foundation/ion/).

        >[!IMPORTANT]
        > La seule façon de changer le système de confiance consiste à refuser le service ID vérifié et à recommencer l’intégration.

1. Cliquez sur **Enregistrer**.  

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/b191676e-03e3-423f-aa28-1e3d2887ea07)


### Définir des stratégies d’accès pour les principaux de service du service Vérification d’identité

Lorsque vous configurez le service Vérification d’identité à l’étape précédente, les stratégies d’accès dans Azure Key Vault sont automatiquement mises à jour pour accorder aux principaux de service les autorisations requises Vérification d’identité.  
Si vous avez un jour besoin de réinitialiser manuellement les autorisations, la stratégie d’accès doit ressembler à celle indiquée ci-dessous.

| Principal de service | AppId | Autorisations de clé |
| -------- | -------- | -------- |
| Service Justificatifs vérifiables | bb2a64ee-5d29-4b07-a491-25806dc854d3 | Obtenir, Signer |
| Demande de service Justificatifs vérifiables | 3db474b9-6a0c-4840-96ac-1fceb342124f | Signer |



![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/896b8ea0-b88b-43b8-a93b-4771defedfde)


## Inscrire une application dans Azure AD

Votre application doit obtenir des jetons d’accès quand elle a besoin d’appeler le service Vérification d’identité Microsoft Entra afin d’émettre ou de vérifier des informations d’identification. Pour obtenir des jetons d’accès, vous devez inscrire une application et accorder les autorisations d’API nécessaires pour le principal de service de demande de vérification d’identité. Par exemple, effectuez les étapes suivantes pour une application web :

1. Connectez-vous au [portail Azure](https://portal.azure.com) avec votre compte d’administrateur.

1. Si vous avez accès à plusieurs locataires, sélectionnez **Répertoire et abonnement**. Ensuite, recherchez et sélectionnez votre instance **Azure Active Directory**.

1. Sous **Gérer**, sélectionnez **Inscriptions d’applications** > **Nouvelle inscription**.  

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/03ca3d13-5564-4ce2-b42c-f8333bc97fb5)


1. Entrez un nom d’affichage pour votre application. Par exemple : *verifiable-credentials-app*.

1. Pour **Types de comptes pris en charge**, sélectionnez **Comptes dans cet annuaire organisationnel uniquement (Annuaire par défaut uniquement – Locataire unique)** .

1. Sélectionnez **Inscrire** pour créer l’application.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/d0a15737-c8a9-4522-990d-1cf6bc12cc1e)


### Accorder des autorisations pour obtenir des jetons d’accès

Lors de cette étape, vous accordez des autorisations au principal de service de **demande de service de justificatifs vérifiables**.

Pour ajouter les autorisations requises, suivez ces étapes :

1. Restez dans la page des détails de l’application **verifiable-credentials-app**. Sélectionnez **Autorisations de l’API** > **Ajouter une autorisation**.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/adf97022-2081-4273-8d61-f2b53628e989)

1. Sélectionnez **API utilisées par mon organisation**.

1. Recherchez le principal de service **Demande de service de justificatifs vérifiables** et sélectionnez-le.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/01691eb2-ab7f-4778-9911-342eb9350f99)

1. Choisissez **Permission d’application** et développez **VerifiableCredential.Create.All**.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/3c5e008e-2ba5-417a-90ff-22e8f622ad34)


1. Sélectionnez **Ajouter des autorisations**.

1. Sélectionnez **Accorder un consentement d’administrateur pour \<your tenant name\>**.

Vous pouvez choisir d’accorder des autorisations d’émission et de présentation séparément si vous préférez séparer les étendues à différentes applications.

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/640def45-e666-423e-bf69-598e8980b125)

## Inscrire l’ID décentralisé et vérifier la propriété du domaine

Après la configuration d’Azure Key Vault et que le service dispose d’une clé de signature, vous devez effectuer les étapes 2 et 3 de l’installation.

![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/40e10177-b025-4d10-b16a-d66c06762c3f)

1. Accédez au service ID vérifié dans le portail Azure.  
1. Dans le menu de gauche, sélectionnez **Configuration**.
1. Dans le menu du milieu, sélectionnez **Vérifier la propriété du domaine**.

# Vérifier la propriété du domaine auprès de votre identificateur décentralisé (DID)

## Vérifier la propriété du domaine et distribuer le fichier did-configuration.json

Le domaine doit être un domaine que votre contrôlez et être au format `https://www.example.com/`. 

1. Dans le Portail Azure, accédez à la page VerifiedID.

1. Sélectionnez **Configuration**, ensuite **Vérifier la propriété du domaine** et choisissez **Vérifier** pour le domaine

1. Copiez ou téléchargez le fichier `did-configuration.json`.

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/342fa12f-2d0d-40cf-a2f9-8796a86f0824)

1. Hébergez le fichier `did-configuration.json` à l’emplacement spécifié. Exemple : si vous avez spécifié le domaine `https://www.example.com`, le fichier doit être hébergé à l’URL `https://www.example.com/.well-known/did-configuration.json`.
   Il ne peut y avoir aucun autre chemin d’accès supplémentaire dans l’URL que le nom `.well-known path`.

1. Lorsque le `did-configuration.json` est disponible publiquement dans l’URL .well-known/did-configuration.json, vérifiez-le en appuyant sur le bouton **Actualiser la vérification de l’état** .

   ![image](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/assets/91347931/49a06251-af56-49b2-9059-0bd4ca678da6)

> Résultat : Vous avez correctement créé une instance Azure Key Vault, configuré le service d’identité vérifié, inscrit une application dans Azure AD et vérifié une propriété de domaine pour votre identificateur décentralisé (DID).

**Nettoyer les ressources**

> N’oubliez pas de supprimer toutes les nouvelles ressources Azure que vous n’utilisez plus. La suppression des ressources inutilisées garantit que vous n’encourrez pas de coûts imprévus. 

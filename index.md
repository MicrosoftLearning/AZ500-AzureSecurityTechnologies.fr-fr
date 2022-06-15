---
title: Instructions hébergées en ligne
permalink: index.html
layout: home
ms.openlocfilehash: 7ad42e4108b96ec131049fa1035ab1d112d95d90
ms.sourcegitcommit: 2eb153f2856445e5afaa218a012cb92e3d48f24b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195902"
---
# <a name="content-directory"></a>Répertoire de contenu

Les fichiers de labo requis peuvent être [téléchargés ici](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/archive/master.zip)

Les liens hypertexte vers chaque exercice et démonstration de labo sont répertoriés ci-dessous.

## <a name="labs"></a>Laboratoires

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| Module | Laboratoire |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

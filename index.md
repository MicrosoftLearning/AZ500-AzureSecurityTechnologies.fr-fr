---
title: Instructions hébergées en ligne
permalink: index.html
layout: home
---

# Répertoire de contenu

Les fichiers de labo requis peuvent être [téléchargés ici](https://github.com/MicrosoftLearning/AZ500-AzureSecurityTechnologies/archive/master.zip)

Les liens hypertexte vers chaque exercice et démonstration de labo sont répertoriés ci-dessous.

## Laboratoires

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| Module | Laboratoire |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

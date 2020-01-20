## Guide des Bonnes Pratiques et du Style dans PowerShell

[Table Of Contents](#table-of-contents)

<p align="center"><a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/" style="display: inline-block; float: left; vertical-align: middle; margin: 10px;"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a></p>

Ce travail fut créé sous license [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/), merci de l'attribuer à Don Jones, Matt Penny, Carlos Perez, Joel Bennett et la Communauté Powershell.

###### Vous êtes libre de :

**Partager** — copier et redistribuer ce matériau via tout medium ou format

**Adapter** — remixer, transformer, et construire à partir de ce matériau

Les auteurs vous encouragent à redistribuer ce contenu aussi largement que possible, mais exigent que vous attribuiez son crédit aux auteurs primaires ci-dessous, et que vous nous notifiiez sur GitHub de toute amélioration que vous y apportez.

### Que sont les Bonnes Pratiques ?

_Les Bonnes Pratiques dans Powershell_ sont ce par quoi vous devriez habituellement commencer. Ce sont des façons d'écrire, penser, et concevoir qui vous _éloignent_ des ennuis. Le principe d'une _Bonne Pratique_ est d'aidezr le lecteur à tomber dans le puits du succès :

> **Le Puits du Succès :** en contraste avec un sommet, un pic, ou un voyage au travers du désert pour trouver la victoire après moult épreuves et surprises, nous voulons que nos clients tombent simplement dans nos pratiques garantissant la victoire en utilisant notre plateforme et nos frameworks. Au point où si nous rendions les ennuis facile à atteindre, nous échouerions.

> -- Rico Mariani, MS Research MindSwap Oct 2003.

Comme les règles d'anglais et de grammaire, les règles de bonnes pratiques et de style PowerShell ont presque toujours des exceptions, mais nous documentons ici une référence de structure du code, design de commandes, programmation, formatage, et même de style qui vous aidera à éviter des problèmes communs, et vous aidera à écrire un code plus réutilisable et lisible -- parce que le code réutilisable n'a pas à être réécrit, et le code lisible peut être maintenu.

Ceci étant dit, rappelez-vous : les points abordés dans le Guide des Bonnes Pratiques et du Style dans PowerShell sont appelés _pratiques_ et _recommandations_, pas "règles". Si vous avez du mal à faire fonctionner quelque chose parce que vous essayez de ne pas _enfreindre_ une règle de style ou de bonne pratique, vous avez mal compris l'objectif : ce document est pragmatique, plutôt que dogmatique. Nous laisserons le dogmatisme aux équipes et projets qui exigent que vous suiviez leurs lignes directrices spécifiques.

### Sommaire

Les recommandations sont réparties dans ces sections :

* [Guide du Style (Introduction)](Style-Guide/Introduction.md)
  * [Agencement et Formatage du Code](Style-Guide/Code-Layout-and-Formatting.md)
  * [Structure des Fonctions](Style-Guide/Function-Structure.md)
  * [Documentation et Commentaires](Style-Guide/Documentation-and-Comments.md)
  * [Lisibilité](Style-Guide/Readability.md)
  * [Conventions de Nommage](Style-Guide/Naming-Conventions.md)
* [Bonnes Pratiques (Introduction)](Best-Practices/Introduction.md)
  * [Conventions de Nommage](Best-Practices/Naming-Conventions.md)
  * [Construire des Outils Réutilisables](Best-Practices/Building-Reusable-Tools.md)
  * [Sortie et Formatage](Best-Practices/Output-and-Formatting.md)
  * [Gestion d'Erreurs](Best-Practices/Error-Handling.md)
  * [Performance](Best-Practices/Performance.md)
  * [Sécurité](Best-Practices/Security.md)
  * [Langage, Interopérabilité et .Net](Best-Practices/Language-Interop-and-.Net.md)
  * [Metadonnées, Versioning, et Packaging](Best-Practices/Metadata-Versioning-and-Packaging.md)

### Current State:

Souvenez-vous de [ce que nous entendons par _Bonnes Pratiques_](#what-are-best-practices)

Les *Bonnes Pratiques dans PowerShell* sont en constante évolution, et continuent d'être éditées et mises à jour tant que les outils (et la compréhgension qu'a notre communauté de ceux-ci) évolue. Nous vous encourageaons à vérifier si une nouvelle édition est sortie au moins deux fois par an, en visitant [https://github.com/PoshCode/PowerShellPracticeAndStyle](https://github.com/PoshCode/PowerShellPracticeAndStyle)

Le *Guide du Style PowerShell* en particulier est une PREEDITION, et nous travaillons toujours activement à résoudre nos désaccords concernant les règles de ce guide via le système d'issues GitHub.
The *PowerShell Style Guide* in particular is in PREVIEW, and we are still actively working out our disagreements about the rules in the guide through the GitHub issues system.

#### Contribuer

Nous vous invitons à utiliser le système d'issues GitHub ou les requêtes pull pour toute correction, contribution, ou autre changement au texte - nous invitons vos contributions !

Pour plus d'informations, voir [CONTRIBUER](CONTRIBUTING.md)

#### Credits

_Le Livre Communautaire des Pratiques Powershell_ fut originellement compilé et éité par Don Jones et Matt Penny avec l'apport de la communauté Windows Powershell sur powershell.org

Copyright partiel (c) Don Jones, Matt Penny, 2014-2015

_Le Guide du Style Powershell_ fut originellement créé par Carlos Perez, pour ses étudiant.e.s, et toutes les bonnes parties ont été écrites par lui.

Copyright partiel (c) Carlos Perez, 2015

Toute erreur que vous puissiez découvrir dans un de ces documents est là parce que Joel Bennett s'en est mêlé. Merci de soumettre ces [issues](https://github.com/PoshCode/PowerShellPracticeAndStyle/issues) et de nous aider à les corriger.

Copyright partiel (c) Joel Bennett, 2015

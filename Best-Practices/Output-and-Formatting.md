### Formatage des sorties

A FAIRE : La totalité de ce document est TOUJOURS UN BROUILLON

## N'utilisez Write-Host que si vous êtes sûr.e de vous

Avant PowerShell 5, Write-Host n'a pas du tout de fonctionnalité dans les scripts non interactifs. Il ne peut pas être capturé ou redirigé, et devrait par conséquent n'être utilisé que dans des fonctions qui montrent ("Show") ou formatent ("Format") des sorties, ou pour afficher quelque chose qui fasse partie d'une invite interagissant avec l'utilisateur.

Autrement dit : vous ne devriez pas utiliser Write-Host pour créer des sorties de script à moins que votre script (ou fonction, ou quoi que ce soit) utilise le verbe "Show" (comme dans "Show-Performance") ou le verbe "Format" (comme dans "Format-Hex"), ou ait un paramètre `-Formatted`. Vous pourriez aussi l'utiliser pour construire des interactions avec l'utilisateur dans d'autres cas (e.g. pour ajouter des informations supplémentaires à l'écran avant d'inviter l'utilisateur à saisir un choix ou des entrées).

Vous devriez généralement commencer par envisager d'utiliser les autres commandes Write-* pour tenter de donnes des informations à l'utilisateur.ice.

## Utilisez Wirite-Progress pour donner des informations sur la progression de la tâche aux personnes utilisant votre script

Quand vous informez l'utilisateur.ice d'où ils en sont sur le script, ou que vous voulez être sûr.e qu'ils sachent que _quelque chose_ est en train de se passer, Write-Progress est la bonne commande. Dans le cas d'hôtes graphiques ou de travaux à distance, cette sortie peut être montrée à l'utilisateur.ice en temps réel, même quand le verbeux et d'autres flux de données sont collectés et loggés.

La sortie Progress est éphémère, cependant, dans le sens où son affichage n'est pas persistant. Vous ne devriez rien mettre que l'utilisateur.ice _doive_ voir ou puisse vouloir vérifier après complétion du script dans le flux de progrès.

## Utilisez Write-Verbose pour donner des détails à quelqu'un utilisant votre script

Vous devriez utiliser la sortie verbeuse pour les détails à propos de valeurs en calcul, ou expliquer pourquoi un certain chemin d'exécution a été choisi. Cela devrait comprendre les informations utiles _mais pas nécessaires_ pour toute personne utilisant le script, fournissant des informations de statut telles que "Serveur1 : tâche accomplie", ou des informations logiques comme "Serveur2 à jour, aucun traitement", mais ne devrait pas être utilisé pour les vrais résultats ou les informations importantes.

## Utilisez Write-Debugpour donner des informations à quelqu'un maintenant votre script

Vous devriez utiliser le flux de sortie de debug pour les sorties utiles au debug de scritp (i.e. "Entrée dans la boucle principale" ou "Résultat nul, avancée vers la fin de la boucle"), ou pour afficher la valeur d'une variable avant une structure conditionnelle, de façon à ce que la personne effectuant la maintenance puisse entrer dans le débogueur si nécessaire.

> ASTUCE : Quand vous déboguez vous pouvez paramétrer `$DebugPreference = "Continue"` pour avoir ces informations à l'écran sans être invité.e à utiliser un point d'arrêt.

## Utilisez CmdletBinding si vous utilisez des flux de sortie

Comme nous l'avons déjà écrit autre part, vous devriez probablement [toujours utiliser CmdletBinding](../Style-Guide/Code-Layout-and-Formatting.md#always-start-with-cmdletbinding).

Utiliser CmdletBinding est particulièrement important, de telle façon que, quand vous utilisez Write-Verbose et Write-Debug, alors que les flux de sortie Verbose et Debug sont désactivés par défaut, l'attribut `[CmdletBinding()]` active les commutateurs communs `-Verbose` et `-Debug` qui lancent ces flux.

CmdletBinding active aussi les commutateurs des flux Warning et Error, ainsi que des façons de collecter des flux dans des variables. Vous devriez lire le sujet [toujours utiliser CmdletBinding](../Style-Guide/Code-Layout-and-Formatting.md#always-start-with-cmdletbinding) pour plus d'informations.

## Utilisez des fichiers de format pour vos objets personnalisés

Vous ne devriez pas utiliser de commandes de format à l'intérieur des fonctions. Vous devriez plutôt inclure un fichier `nommodule.format.ps1xml` dans le champs FormatToProcess dans le manifeste PSD1 de votre module, et définissez un `PSTypeName` sur vos objets, fe façon à ce que PowerShell formate la sortie de la façon que vous voulez, automatiquement.

## Ne sortez qu'une "sorte" de chose à la fois

vous devriez évitez de mélanger différents types d'objets dans la sortie d'une seule commande, car vous pourriez avoir des lignes vides dans votre sortie ou casser une sortie "tableau" en sortie.s liste, etc.

Pour le bien des outils et de la recherche de commandes, vous devriez indiquer le.s type.s de sortie de vos scripts, fonctions ou Cmdlets avec `[OutputType()]` (voir la [documentation officielle d'outputType](https://docs.microsoft.com/fr-fr/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute) pour plus d'informations).

Quand vous combinez les sorties de plusieurs types d'objets, ils devraient généralement être dérivés d'un même type de base (de la même façon que FileInfo et DirectoryInfo viennent de System.IO.FileSystemInfo), ou devraient avoir des fichiers de type ou de format qui leur font sortir les même colonnes. Vous devez, en particulier, éviter d'entrecouper votre sortie de chaînes de caractères.

### Deux exceptions importantes à la règle du type unique

**Pour les fonctions internes** il est acceptable de renvoyer plusieurs types différents car ceux-ci ne seront pas "sortis" vers l'utilisateur.ice / hôte, et peuvent offrir des économies significatives (e.g. un appel de base de données avec trois jointures de tables, au lieu de trois appels avec deux ou trois jointures chacun). Vous pouvez ainsi appeler ces fonctions et assigner leur sortie à plusieurs variables, comme ceci :

```PowerShell
$user, $group, $org = Get-UserGroupOrg
```

**Quand vous appelez Out-Default.** Si vous devez renvoyer plusieurs types d'objets d'une commande externe, vous devriez nommer votre fonction de façon à ce qu'il soit évident pour l'utilisateur.ice que vous renvoyez plusieurs choses, et vous _devez_ appeler `Out-Default` séparément pour chaque type d'objet pour vous assurez que les sorties ne soient pas mélangées lors du formatage.

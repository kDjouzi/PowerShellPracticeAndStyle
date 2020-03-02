### Formatage des sorties

A FAIRE : La totalité de ce document est TOUJOURS UN BROUILLON

## N'utilisez Write-Host que si vous êtes sûr.e de vous

Avant PowerShell 5, Write-Host n'a pas du tout de fonctionnalité dans les scripts non interactifs. Il ne peut pas être capturé ou redirigé, et devrait par conséquent n'être utilisé que dans des fonctions qui montrent ("Show") ou formatent ("Format") des sorties, ou pour afficher quelque chose qui fasse partie d'une invite interagissant avec l'utilisateur.

Autrement dit : vous ne devriez pas utiliser Write-Host pour créer des sorties de script à moins que votre script (ou fonction, ou quoi que ce soit) utilise le verbe "Show" (comme dans "Show-Performance") ou le verbe "Format" (comme dans "Format-Hex"), ou ait un paramètre `-Formatted`. Vous pourriez aussi l'utiliser pour construire des interactions avec l'utilisateur dans d'autres cas (e.g. pour ajouter des informations supplémentaires à l'écran avant d'inviter l'utilisateur à saisir un choix ou des entrées).

Vous devriez généralement commencer par envisager d'utiliser les autres commandes Write-* pour tenter de donnes des informations à l'utilisateur.ice.

## Utilisez Wirite-Progress pour donner des informations sur la progression de la tâche aux personnes utilisant votre script

Quand vous informez l'utilisateur.ice d'où ils en sont sur le script, ou que vous voulez être sûr.e qu'ils sachent que _quelque chose_ est en train de se passer, Write-Progress est la bonne commande. Dans le cas d'hôtes graphiques ou de travaux à distance, cette sortie peut être montrée à l'utilisateur.ice en temps réel, même quand le verbeux et d'autres flux de données sont collectés et loggés.

La sortie Progress est éphémère, cependant, dans le sens où son affichage n'est pas persistant. Vous ne devriez rien mettre que l'utilisateur.ice _doive_ voir ou puisse vouloir vérifier après complétion du script dans le flux de progrès.

## utilisez Write-Verbose pour donner des détails à quelqu'un utilisant votre script

Vous devriez utiliser la sortie verbeuse pour les détails à propos de valeurs en calcul, ou expliquer pourquoi un certain chemin d'exécution a été choisi. Cela devrait comprendre les informations utiles _mais pas nécessaires_ pour toute personne utilisant le script, fournissant des informations de statut telles que "Serveur1 : tâche accomplie", ou des informations logiques comme "Serveur2 à jour, aucun traitement", mais ne devrait pas être utilisé pour les vrais résultats ou les informations importantes.

## Use Write-Debug to give information to someone maintaining your script

You should use the debug output stream for output that is useful for script debugging (ie: "Now entering main loop" or "Result was null, skipping to end of loop"), or to display the value of a variable before a conditional statement, so the maintainer can break into the debugger if necessary.

> TIP: When debugging you should be aware that you can set `$DebugPreference = "Continue"` to see this information on screen without entering a breakpoint prompt.

## Use CmdletBinding if you are using output streams

As we've already written elsewhere, you should probably [always use CmdletBinding](../Style-Guide/Code-Layout-and-Formatting.md#always-start-with-cmdletbinding). 

Using CmdletBinding is particularly important, however, when you're using Write-Verbose and Write-Debug, as the Verbose and Debug output streams are off by default, and the `[CmdletBinding()]` attribute enables the common `-Verbose` and `-Debug` switches which turn those streams on.

CmdletBinding also enables the switches for the Warning and Error streams, as well as ways of collecting those streams into variables. You should read the [always use CmdletBinding](../Style-Guide/Code-Layout-and-Formatting.md#always-start-with-cmdletbinding) topic for more information.

## Use Format Files for your custom objects

You should not use format commands inside functions. Instead you should include a `modulename.format.ps1xml` file in the FormatsToProcess field of your module's PSD1 manifest, and define a `PSTypeName` on your objects, so that PowerShell will format your output the way you want, automatically.

## Only output one "kind" of thing at a time

You should avoid mixing different types of objects in the output of a single command, because you may get empty rows in your output or cause table output to break into list output, etc.

For the sake of tools and command-search, you should indicate with the `[OutputType()]` attribute the output type(s) of your scripts, functions or cmdlets (see about_Functions_OutputTypeAttribute for more information).

When you combine the output of multiple types objects, they should generally be derived from a common basetype (like FileInfo and DirectoryInfo come from System.IO.FileSystemInfo), or should have format or type files which cause them to output the same columns. In particular, you must avoid outputting strings interspersed in your output.

### Two important exceptions to the single-type rule

**For internal functions** it's ok to return multiple different types because they won't be "output" to the user/host, and can offer significant savings (e.g. one database call with three table joins, instead of three database calls with two or three joins each).  You can then call these functions and assign the output to multiple variables, like so:

```PowerShell
$user, $group, $org = Get-UserGroupOrg
```

**When you call Out-Default.** If you must return multiple object types from an external command, you should name your function in such a way that it's obvious to users that you're returning multiple things, and you _must_ call `Out-Default` separately for each type of object to ensure that the outputs don't ever get mixed up by the formatter.

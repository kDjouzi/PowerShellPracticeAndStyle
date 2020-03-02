### Conventions de nommage

En général, préférez l'utilisation de noms complets et explicites pour les commandes et paramètres plutôt que des alias ou formes courtes. Des outils existent [Expand-Alias](https://github.com/PoshCode/ModuleBuilder/blob/master/ResolveAlias.psm1) pour réparer beaucoup de ces problèmes, mais pas tous.

#### Utilisez le nom complet de chaque commande.

Chaque scripteur PowerShell apprend les noms réels des commandes, mais différentes personnes apprennent différents aliases (e.g. : ls pour les utilisatueurs Linux, dir ppour les utilisateurs DOS, gci...). Dans vos scripts partagés vous devriez toujours utiliser les noms complets les plus connus des commandes. Et en bonus, certains sites comme GitHub mettront les commandes en lumière comme il le faut quand vous utilisez le nom Verbe-Nom complet de votre commande :

```PowerShell
# N'écrivez pas :
gps -Name Explorer

# Ecrivez plutôt :
Get-Process -Name Explorer
```

#### Utilisez les noms complets des paramètres.

Parce qu'il y a un très grand nombre de commandes dans PowerShell, il est impossible que chaque scripteur.se connaisse chaque commande. Il est ainsi utile d'être explicite au niveau des noms de paramètres pour les lecteur.ice.s qui pourraient ne pas être familier.ère.s de la commande que vous utilisez. Cela vous aidera aussi à éviter les bugs si un changement apporté à la commande altère les assignations de paramètres.

```PowerShell
# N'écrivez pas :
Get-Process Explorer

# Ecrivez plutôt :
Get-Process -Name Explorer
```

#### Utilisez autant que possible des chemins complets et explicites.

Quand vous écrivez des scripts, il n'est sûr d'utiliser `..` ou `.` dans un chemin que si vous en avez précédemment déterminé l'emplacement (à l'intérieur du script), et même dans ce cas vous devriez vous méfier de l'utilisation de chemins relatifs lorsque vous utilisez des méthodes .Net ou des aplications anciennes/natives, car elles utiliseront `[Environment]::CurrentDirectory` plutôt que le dossier de travail présent de PowerShell (`$PWD`). Puisque vérifier la présence de ce type d'erreurs est fastidieux (et parce qu'elles sont faciles à oublier) il vaut mieux éviter d'utiliser des chemins relatifs de façon générale, et plutôt, de baser vos chemins sur $PSScriptRoot (le dossier où votre script se trouve) si nécessaire.

```PowerShell
# N'écrivez pas :
Get-Content .\README.md

# N'écrivez surtout pas :
[System.IO.File]::ReadAllText(".\README.md")

# Ecrivez plutôt :
Get-Content -Path (Join-Path -Path $PSScriptRoot -ChildPath README.md)

# Vous pouvez même utiliser la concaténation de chaînes :
[System.IO.File]::ReadAllText("$PSScriptRoot\README.md")
```

##### Evitez d'utiliser `~` pour représenter le dossier d'origine.

Le sens de ~ est dépend malheureusement du fournisseur "actuel" au moment de l'exécution. Ce n'est pas, en soi, un problème de style, mais c'est de toute façon une règle importante pour le code que vous comptez partager. Utlisez plutôt `${Env:UserProfile}` ou `(Get-PSProvider -PSProvider FileSystem).Home` ...

```PowerShell
PS C:\Windows\system32> cd ~
PS C:\Users\Name> cd HKCU:\Software
PS HKCU:\Software> cd ~
cd : Home location for this provider is not set. To set the home location, call "(get-psprovider 'Registry').Home = 'path'".
At line:1 char:1
+ cd ~
+ ~~~~
    + CategoryInfo          : InvalidOperation: (:) [Set-Location], PSInvalidOperationException
    + FullyQualifiedErrorId : InvalidOperation,Microsoft.PowerShell.Commands.SetLocationCommand
```

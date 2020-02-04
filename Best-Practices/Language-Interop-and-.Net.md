# VER-01 Ecrivez pour la plus vieille version de PowerShell possible

Faites une règle, d'écrire pour la version de PowerShell la plus ancienne possible, plus spécialement avec les scripts que vous comptez partager avec d'autres personnes. Faire cela fournit la plus grande compatibilité aux autre gens.

Cela dit, ne sacrifiez pas de fonctionnalité ou de performance juste pour rester sur une vieille version. Si vous pouvez écrire pour une version plus récente sans risque (c'est à dire que vous l'avez déployée partout où le script devra fonctionner), alors tirez parti de cette version. Gardez à l'esprit que certains éléments qui semblent décoratifs peuvent en fait avoir des avantages quant à la performance de votre script. Par exemple, dans PowerShell v3 :

```PowerShell
Get-Service | Where-Object -FilterScript { $\_.Status -eq 'Running' }
```

Tournera beaucoup plus lentement que :

```PowerShell
Get-Service | Where Status -eq Running
```

à cause de la façon dont ces deux syntaxes sont traitées sous le capot.

_Plus d'informations :_ Vous pouvez obtenir plus de détails sur les différences entre les versions de PowerShell en tapant `help about\Windows\PowerShell\2.0` (ou 3.0 ou 4.0) dans PowerShell

# VER-02 Documentez la version de PowerShell pour laquelle le script a été écrit

Ceci étant dit, asurez-vous de spécifier la version de PowerShell pour laquelle vous avez écrit en utilisant une déclaration `#requires` appropriée :

```PowerShell
#requires -version 3.0
```

La déclaration `#requires` empêchera le script de tourner sur la mauvaise version de Powershell.

### Version de PowerShell supportée

En travaillant dans un environnement où il y a plusieurs versions de PowerShell assurez-vous de spécifier la version la plus basse que votre script supportera en ajoutant une décaration `#requires` en haut du script.

```PowerShell
    #Requires -Version 2.0
```

Quand un _module_ utilise des cmdlets spécifiques ou une syntaxe qui n'est présente qu'à partir d'une version minimale spécifique de PowerShell, utilisez ceci dans le fichier de manifeste ps1d :

```PowerShell
    PowerShellVersion = '3.0'
```


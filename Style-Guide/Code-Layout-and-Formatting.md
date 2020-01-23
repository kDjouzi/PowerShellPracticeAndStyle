### Agencement et formatage du code

Ces lignes directrices ont pour sujet l'amélioration de la lisibilité. Certaines sont des règles arbitraires, mais sont basées sur des décennies de tradition dans la programmation, ainsi même si vous pourriez être en désaccord avec certaines règles (et vous devriez toujours suivre les règles des projets individuellement), quand nous vous demandons de laisser une ligne vide après une accolade fermant une fonction, ou deux lignes avant les fonctions, nous ne sommes pas capricieux, nous le faisons car cela rend votre code plus facile à scanner pour les développeurs expérimentés.

#### Maintenir de la cohérence dans l'agencement

Les règles sur l'indentation, la longueur des lignes, et les majuscules sont là pour maintenir la cohérence entre différentes bases de code. L'expérience a montré qu'il est plus simple de lire et comprendre du code quand il a l'air familier et que vous n'êtes pas distraits par des détails, ce qui veut dire qu'il est plus profitable pour la communauté de suivre une seule combinaison de règles.

Nous ne nous attendons pas à ce que tout le monde suive ces lignes directrices, et les règles des projets individuels prennent toujours le pas sur celles-ci. Que ce soit pour des raisons d'ancienneté, ou pour suivre les lignes directrices de plusieurs langages dans un seul projet, des projets différents peuvent avoir des directives différentes quant au style. L'objectif étant la cohérence, vous devriez toujours demeurer respectueu.x.se des règles de style en place dans le projet auquel vous apportez votre contribution.

Si vous avez en effet un projet ancien en contrôle de source et que vous décidez d'en reformater le code pour adopter ces règles, essayez d'effectuer tous vos changements d'espacement (whitespace) en un seul commit qui ne fasse _rien d'autre_ que modifier l'espacement. Vous ne devriez jamais reformater les espaces d'un fichier dans _une partie_ d'un chagement de contenu car cela rend les autres changements difficilement trouvables.

#### Conventions de mise en majuscule

PowerShell n'est **pas** sensible à la casse, mais nous suivons des conventions de mise en majuscules pour rendre le code facile à lire. Elles sont basées sur les [conventions de mise en majuscules](https://docs.microsoft.com/fr-fr/dotnet/standard/design-guidelines/capitalization-conventions) que Microsoft a créées pour le framework .NET, puisque Powershell est un langage d'écriture de scripts .NET, et les cmdlets PowerShell sont principalement écrites dans des langages .NET suivant ces conventions.

##### Terminologie

* minuscules - tout en minuscules, mots non séparés
* MAJUSCULES - tout en majuscules, mots non séparés
* PascalCase - la première lettre de chaque mot est en majuscules
* camelCase - la première lettre de chaque mot _sauf_ le premier est en majuscules

PowerShell utilise la PascalCase pour _tous_ les identifiants publics : noms de modules, noms de fonctions ou cmdlets, classes, énumérateurs, champs ou pripriétés publiques, variables globales et constantes, etc. Et au fond, puisque les _paramètres_ des commandes PowerShell sont en fait des _propriétés_ de classes .Net, même les paramètres utilisent la PascalCase plutôt que la camelCase.

Les mots-clés du langage PowerShell sont écrits en minuscules (oui, même `foreach` et `dynamicparam`), de même que les opérateurs tels que `-eq`et `-match`. Les mots-clés dans les indications en commentaire sont écrits en MAJUSCULES pour les faire ressortir dans cette dense prose qu'est la documentation.

```powershell
function Write-Host {
    <#
    .SYNOPSIS
        Ecrit une sortie personnalisée dans un hôte.
    .DESCRIPTION
        Le cmdlet Write-Host personnalise la sortie. Vous pouvez spécifier la couleur du texte 
        en utilisant le paramètre ForegroundColor, et vous pouvez spécifier la couleur d'arrière-plan
        en utilisant le paramètre BackgroundColor. Le paramètre Separator vous laisse spécifier
        une chaîne de caractères pouvant être utilisée pour séparer les objets affichés. Le
        résultat obtenu dépendra du programme qui hoste Windows Powershell.
    #>
    [CmdletBinding()]
    param(
        [Parameter(Position = 0, ValueFromPipeline = $true, ValueFromRemainingArguments = $true)]
        [PSObject]
        $Object,

        [Switch]
        $NoNewline,

        [PSObject]
        $Separator,

        [System.ConsoleColor]
        $ForegroundColor,

        [System.ConsoleColor]
        $BackgroundColor
    )
    begin {
    ...
```

Comme dit précédemment, PowerShell utilise la PascalCase pour _tous_ les identifiants publics. Les noms de fonctions devraient suivre les conventions de nommage `Verbe-Nom` de PowerShell, en utilisant la PascalCase dans le Verbe et le Nom.

Une exception est faite pour les acronymes à deux lettres dans lesquelles les deux lettres sont en majuscule, comme dans la variable `$PSBoundParameters` ou la commande `Get-PSDrive`. Notez que [comme spécifié dans les lignes directrices .NET](https://docs.microsoft.com/fr-fr/dotnet/standard/design-guidelines/capitalization-conventions#capitalization-rules-for-identifiers) cela n'affecte pas "OK" et "ID",  mots usuellement mis en majuscules - ce ne sont pas des acronymes. Vous ne devriez pas non plus étendre cette règle aux acronymes composés, comme quand le Resource Manager d'Azure (RM) rencontre une Machine Virtuelle (VM) dans `Start-AzureRmVM`...

> Nous sommes informés du fait que ces règles n'ont pas été respectées pour une raison ou une autre à **beaucoup** d'occasions -- vous devriez considérer ces occasions comme des _exceptions_ (comme pour COM Interop) ou des _erreurs_ (comme `System.Data.SqlClient.SQLDebugging`), mais pas comme une raison de ne tenir aucun compte des conventions voire les mépriser.

Si vous le voulez, vous pouvez utiliser la camelCase pour les variables dans vos fonctions (ou modules) pour distinguer les variables _privées_ des paramètres, mais c'est uniquement une question de goûts personnels. Les variables partagées devraient se reconnaître par l'utilisation leur portée, comme  `$Script:PSBoundParameters` ou `$Global:DebugPreference`. Si vous utilisez la camelCase pour une variable qui commence par un acronyme de deux lettres (et dont les deux lettres sont en majuscules), ces deux lettres devraient être mises en minuscules (comme dans `adComputer`).

#### Le Seul Vrai Style d'Indentation

Ce guide recommande la [variante du K&R appelée "Seul Vrai Style d'Indentation"](https://github.com/PoshCode/PowerShellPracticeAndStyle/issues/81#issuecomment-285835313)(nom anglais : One True Brace Style, abrégé OTBS), qui requiert que chaque _déclaration_ qui puisse être entre des accolades ait son accolade ouvrante _en fin de ligne_ et son accolade fermante _en début de ligne_.

Le passage de petits blocs de script aux paramètres est une exception notable (où K&R autoriserait de ne pas utiliser d'accolades), nous autorisons à mettre la déclaration complète sur une seule ligne.

```powershell
enum Color {
    Black,
    White
}

function Test-Code {
    [CmdletBinding()]
    param(
        [int]$ParameterOne
    )
    end {
        if (10 -gt $ParameterOne) {
            "Greater"
        } else {
            "Lesser"
        }
    }
}

# Un cas exceptionnel :
Get-ChildItem | Where-Object { $_.Length -gt 10mb }
```
La raison principale pour laquelle nous faisons cette recommandation est pratique : aucune exception n'est nécessaire quand on suit cette règle, et quand le code est écrit en suivant ce style, de _nouvelles lignes_ de code peuvent être insérées entre deux lignes sans risquer de le casser accidentellement en séparant les accolades de leur bloc de déclaration. Ainsi, le code est plus facile à suivre, et cela rend les erreurs moins probables.

Parce que choix était quelque peu sujet à controverse dans la communauté (environ 1/3 des voteurs s'y est opposé), il vaut la peine d'étoffer notre raisonnement : 
* Premièrement : dans certaines consoles historiques, il était nécessaire d'écrire de cette manière, donc beaucoup du code PowerShell des premières heures suit ce style.
* Deuxièmement : Les fonctions PowerShell qui acceptent des blocs de script (telles que `ForEach-Object` et `Where-Object`) son nombreuses, et sont des parties _inhérentes_ des langages à domaines spécifiques basés sur PowerShell comme DSC. Puisqu'il est **requis** de mettre l'accolade ouvrante en fin de ligne dans ces cas de figure, la seule option _cohérente_ est de suivre l'OTBS.

#### Commencez toujours par les CmdletBinding

Tous vos scripts devraient commencer leur vie en ressemblant à cet extrait :
```powershell
[CmdletBinding()]
param()
process {
}
end {
}
```

Vous pouvez toujours effacer ou ignorer un des blocs (ou ajouter le bloc `begin`), ajouter des paramètres et validation nécessaire et ainsi de suite, mais vous devriez **éviter** d'écrire des scripts ou fonctions sans `[CmdletBinding()]`, et vous devriez toujours au moins _penser à_ lui faire accepter des entrées de pipelines.

#### Préférez : param(), begin, process, end

Avoir un script écrit dans l'ordre d'exécution rend l'intention plus claire. Puisqu'il n'y a pas de raison fonctionnelle de mettre ces blocs dans le désordre (ils _seront_ exécutés dans l'ordre normal), les écrire dans le désordre peut être déroutant, et rendre le code plus difficile à maintenir et déboguer.

Un code plus explicite est mainteable. Même si PowerShell autorise à se passer du bloc `end` (et a même un mot-clé `filter` qui convertit le bloc anonyme en bloc `process`), nous vous recommandons de ne pas utiliser ces caractéristiques car cela rend le code moins explicite.

#### L'Indentation

##### Utilisez quatre *espaces* par niveau d'indentation

Dans la plupart des cas vous n'auriez qu'à appuyer sur la touche `[Tab]` pour indenter, et la plupart des éditeurs peuvent être configurés à insérer quatre espaces au lieu d'un caractère de tabulation. La plupart des langages de programmation et éditeurs (PowerSHell ISE compris) utilisent quatre espaces par défaut, et c'est ce que nous recommandons. Des équipes et projets différent.e.s peuvent avoir des standards différents, et quand vous contribuez à un projet, vous devriez vous conformer au style prédominant, bien évidemment.

```powershell
function Test-Code {
    foreach ($exposant in 1..10) {
        [Math]::Pow(2, $exposant)
    }
}
```

Indenter de plus de 4 espaces est acceptable pour les lignes de continuation (quand vous divisez une ligne qui était trop longue). Dans ces cas vous pourriez indenter de plus d'un niveau, ou même indenter d'un nombre d'espaces impair pour vous aligner avec un appel de méthode ou un bloc de paramètre sur la ligne précédente.

```powershell
function Test-Code {
    foreach ($base in 1,2,4,8,16) {
        foreach ($exposant in 1..10) {
            [System.Math]::Pow($base,
                               $exposant)
    }
}
```

#### Longueur de ligne maximale

Limitez les lignes à 115 caractères si possible.

Se tenir à des lignes peu larges permet au script d'être lu dans _une_ direction (de haut en bas) sans faire d'allers-retours horizontaux. La largeur exacte qu'une ligne devrait faire est un des sujets de dispute favoris des développeurs sur internet (plus fragmenté encore que emacs contre vi ou gnu GPL contre MIT).

Nous utilisons dans ce guide deux sources en particulier pour la largeur de ligne maximale :

La console PowerShell fait, par défaut, 120 caractères de large, mais n'autorise que 119 caractères sur les lignes de sortie, et à l'entrée d'un texte multilignes, Powershell utilise une amorce de continuation de ligne : `>>>` ce qui limite de toute manière votre longueur de ligne à 116 caractères.

La longueur de ligne maximale de Github est actuellement entre 121 et 126 selon votre navigateur et votre OS (et donc, votre police). Toutefois, la longueur de ligne de 115 que PowerShell suggère pourrait suffir à permettre d'afficher des comparaisons côte-à-côte sans défiler ou aller à la ligne sur un moniteur 1080p "standard".

Encore une fois, c'est une règle particulièrement flexible, et vous devriez toujours suivre les lignes directrices du projet quand vous contribuez aux projets d'autres personnes. Même si la plupart d'entre nous travaillent sur des écrans larges, tout le monde ne peut pas voir sans agrandir le texte ou sans utiliser de très grandes polices.

Pour évitéer les lignes top longues, nous préférons utiliser le splatting (voir [about_Splatting](https://technet.microsoft.com/en-us/library/jj672955.aspx)) et les continuations de lignes implicites que PowerShell a dans les parenthèses, accoladse et crochets -- il faut **toujours** les utiliser 
The preferred way to avoid long lines is to use splatting (see [Get-Help about_Splatting](https://technet.microsoft.com/en-us/library/jj672955.aspx)) and PowerShell's implied line continuation inside parentheses, brackets, and braces -- these should **always** be used in preference to the backtick for line continuation when applicable, even for strings:

```powershell
Write-Host -Object ("This is an incredibly important, and extremely long message. " +
                         "We cannot afford to leave any part of it out, " + 
                         "nor do we want line-breaks in the output. " +
                         "Using string concatenation lets us use short lines here, " +
                         "and still get a long line in the output")
```

#### Blank Lines and Whitespace

Surround function and class definitions with _two_ blank lines.

Method definitions within a class are surrounded by a single blank line.

Blank lines may be omitted between a bunch of related one-liners (e.g. empty functions)

Additional blank lines may be used sparingly to separate groups of related functions, or within functions to indicate logical sections (e.g. before a block comment).

End each file with a single blank line.

#### Trailing spaces

Lines should not have trailing whitespace. Extra spaces result in future edits where the only change is a space being added or removed, making the analysis of the changes more difficult for no reason.

#### Spaces around parameters and operators

You should use a single space around parameter names and operators, including comparison operators and math and assignment operators, even when the spaces are not necessary for PowerShell to correctly parse the code.

One notable exception is when using colons to pass values to switch parameters:

```PowerShell
# Do not write:
$variable=Get-Content $FilePath -Wai:($ReadCount-gt0) -First($ReadCount*5)

# Instead write:
$variable = Get-Content -Path $FilePath -Wait:($ReadCount -gt 0) -TotalCount ($ReadCount * 5)
```

Another exception is when using [Unary Operators](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_operators#unary-operators):

```PowerShell
# Do not write:
$yesterdaysDate = (Get-Date).AddDays( - 1)

$i = 0
$i ++

# Instead write:
$yesterdaysDate = (Get-Date).AddDays(-1)

$i = 0
$i++

# Same principle should be applied when using a variable.
$yesterdaysDate = (Get-Date).AddDays(-$i)
```

#### Spaces around special characters

White-space is (mostly) irrelevant to PowerShell, but its proper use is key to writing easily readable code.

Use a single space after commas and semicolons, and around pairs of curly braces.

Subexpressions `$( ... )` and scriptblocks `{ ... }` should have a single space on the _inside_ of the braces or parentheses to improve readability by making code blocks stand out -- and to further distinguish scriptblocks from variable delimiter braces `${...}`

Avoid unnecessary spaces inside parenthesis or square braces.

```powershell
$Var = 1
"This is a string with one (${Var}) delimited variable."

"There are $( (Get-ChildItem).Count ) files."
```

Obviously, these rules should not be applied in such a way as to affect output.

#### Avoid Using Semicolons (`;`) as Line Terminators

PowerShell will not complain about extra semicolons, but they are unnecessary, and can get in the way when code is being edited or copy-pasted. They also result in extra do-nothing edits in source control when someone finally decides to delete them.

They are also unnecessary when declaring hashtables if you are already putting each element on its own line:

```PowerShell
# This is the preferred way to declare a hashtable if it extends past one line:
$Options = @{
    Margin   = 2
    Padding  = 2
    FontSize = 24
}
```

TODO: This section should probably be merged to [Code Layout and Formatting](Code-Layout-and-Formatting.md), and based on the [#15](https://github.com/PoshCode/PowerShellPracticeAndStyle/issues/15), we should remove or rewrite the backticks section.

A FAIRE : Cette section devrait être fusionnée avec [Code Layout and Formatting](Code-Layout-and-Formatting.md), et sur la base du [#15](https://github.com/PoshCode/PowerShellPracticeAndStyle/issues/15), nous devrions effacer ou réécrire la section sur le caractère d'accent grave.

# LIRE-01 Indentez votre code

Voyez cet exemple de code :

```PowerShell
if ($ceci -gt $cela) {
    Do-QuelqueChose -avec $cela
}
```

Et maintenant celui-ci :

```PowerShell
if ($ceci -gt $cela)
{
    Do-QuelqueChose -avec $cela
}
```

Aucun de ces deux exemples n'est meilleur que l'autre. Demandez à 100 codeur.euse.s lequel ielles préfèrent et chacun sera préféré par environ la moitié d'entre elleux. Maintenant, quand vous commencez à travailler avec des commandes qui acceptent des blocs de script en paramètres, les choses peuvent devenir plus épineuses à cause de la façon dont PowerShell analyse la syntaxe. "Mauvais" est mauvais. Avec les constructions de scripts, comme les deux exemples ci-dessus, il n'y a pas de différence fonctionnelle.

Pour rester dans cette veine, comprenez que ce qui suit est basiquement un ensemble de directives vers un consensus de masse ; ce ne sont pas des règles gravées dans la roche. Ceci étant dit, il y a des arguments en leur faveur, et vous devriez donner à ces arguments votre considération avant de rejeter ces idées.

Premièrement, formatez votre code convenablement. La convention est d'indenter dans les structures, pour rendre plus clair ce qui "appartient" aux structures.

```PowerShell
foreach ($ordinateur in $ordinateurs) {
    Do-This
    Get-Those
}
```

Vous serez probablement vilipendé.e si vous ne faites pas attention à votre formatage.

# LIRE-02 Evitez les accents graves

Voyez ceci :

```PowerShell
Get-WmiObject -Class Win32_LogicalDisk `
              -Filter "DriveType=3" `
              -ComputerName SERVER2
```

En général, la communauté pense que vous devriez autant que possible éviter d'utiliser ces accents graves comme "caractères de continuation de ligne". Ils sont difficiles à lire, faciles à rater, et faciles à mal taper. De plus, si vous ajoutez un espace vide en fin de ligne dans l'exemple ci-dessus, alors la commande ne fonctionnera pas. L'erreur résultante est difficile à corréler avec le problème réel, rendant le débogage de ce problème plus ardu.

Voici une alternative :

```PowerShell
$GetWmiObjectParams = @{
    Class = "Win32_LogicalDisk"
    Filter = "DriveType=3"
    ComputerName = "SERVER2"
}
Get-WmiObject @GetWmiObjectParams
```

Cette technique s'appelle le _splatting_. Elle vous laisse avoir ce même formatage sympathique et éclaté sans utiliser l'accent grave. Vous pouvez aussi casser la ligne après quasiment n'importe quel caractère virgule, point-virgule, ou autre caractère _pipeline_ sans utiliser d'accent grave.

L'accent grave n'est pas universellement détesté - mais il peut être inconvenant. Si vous devez l'utiliser pour casser les lignes, hé bien, utilisez-le. Essayez juste de ne pas avoir à le faire.

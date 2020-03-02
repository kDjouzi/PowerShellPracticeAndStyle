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

# READ-02 Avoid backticks

Consider this:

```PowerShell
Get-WmiObject -Class Win32_LogicalDisk `
              -Filter "DriveType=3" `
              -ComputerName SERVER2
```

In general, the community feels you should avoid using those backticks as "line continuation characters" when possible. They're hard to read, easy to miss, and easy to mistype. Also, if you add an extra whitespace after the backtick in the above example, then the command won't work. The resulting error is hard to correlate to the actual problem, making debugging the issue harder.

Here's an alternative:

```PowerShell
$GetWmiObjectParams = @{
    Class = "Win32_LogicalDisk"
    Filter = "DriveType=3"
    ComputerName = "SERVER2"
}
Get-WmiObject @GetWmiObjectParams
```

The technique is called _splatting_. It lets you get the same nice, broken-out formatting without using the backtick. You can also line break after almost any comma, pipe character, or semicolon without using a backtick.

The backtick is not universally hated - but it can be inconvenient. If you have to use it for line breaks, well then, use it. Just try not to have to.

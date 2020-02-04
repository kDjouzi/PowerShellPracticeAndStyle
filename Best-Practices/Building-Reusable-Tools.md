# OUTIL-01 Décidez si vous codez un script "outil" ou "contrôleur"

Pour cette discussion, il est important de décider d'une terminologie commune. Bien que la terminologie que nous utilisons ici ne soit pas utilisée universellement, la communauté s'accorde sur le fait qu'il existe plusieurs types de "scripts" :

1. Certains scripts contiennent des outils, ayant pour but d'être réutilisables. Ce sont typiquement des fonctions ou des fonctions avancées, et elles sont géneralement contenues dans un type de module de script ou de librairie de fonctions. Ces outils sont conçus pour avoir une haute réutilisabilité.
2. Certains scripts sont des contrôleurs, c'est à dire qu'ils sont faits pour utiliser un ou plusieurs outils (fonctions, commandes, etc) pour automatiser un processus spécifique. Un tel script n'est pas fait pour être réutilisable; il est fait pour utiliser la réutilisabilité en mettant en oeuvre des fonctions et autres commandes.

Par exemple, vous pourriez écrire un script "Nouveau-UtilEntreprise", qui ajoute les nouveaux utilisateurs. Dedans, vous pourriez appeler de nombreuses commandes et fonctions pour créer un compte utilisateur, activer son adresse mail, lui fournir un dossier personnel, et ainsi de suite. Ces tâches uniques pourraient avoir une utilité dans d'autres processus, donc vous les construisez en tant que fonctions. Le script "Nouveau-UtilEntreprise" n'a pour but d'automatiser qu'un seul processus, et donc n'a pas besoin de comporter de concepts de réutilisabilité. C'est un élément autonome.

Les contrôleurs, d'un autre côté, produisent souvent des sorties directement à l'écran (quand ils sont créés pour être interactifs), ou peuvent déposer le journal (log) de leurs actions dans un fichier (s'ils sont créés pour fonctionner de façon automatisée).


# OUTIL-02 Rendez votre code modulaire

Généralement, les gens ont tendance à penser que la plupart du code fonctionnel - c'est à dire, votre code qui fait des choses - devrait être modularisé dans des fonctions et idéalement stocké dans des modules de script.

Cela rend ces fonctions plus simple à réutiliser. Ces fonctions devraient montrer un haut niveau de réutilisabilité, comme en n'acceptant des entrées qu'au travers de paramètres et en ne produisant des sorties qu'en tant qu'objets dans le pipeline.


# OUTIL-03 Rendez vos outils aussi réutilisables que possible

Les outils devraient accepter des entrées à partir de paramètre et devraient (dans la plupart des cas) produire toute sortie dans le pipeline; cette approche aide à maximiser la réutilisabilité.

# OUTIL-04 Utilisez le standard PowerShell pour nommer vos cmdlets

Utilisez la convention verbe-nom, et utilisez les verbes standard de PowerShell.

Vous pouvez obtenir une liste des verbes en tapant 'get-verb' dans l'invite de commande.

# OUTIL-05 Utilisez le standard Powershell pour nommer vos paramètres

Les outils devraient être cohérents avec les cmdlets natifs de PowerShell au niveau du nommage des paramètres.

Par exemple, utilisez $ComputerName et $ServerInstance plutôt que $Param_Computer et $InstanceName.

# OUTIL-06 Les outils devraient renvoyer des données brutes

La communauté s'accorde généralement sur le fait que les outils devraient renvoyer des données brutes. C'est à dire, que leurs sorties devraient être manipulées aussi peu que possible. Si un outil récupère une information représentée en bytes, il devrait renvoyer des bytes, plutôt que de convertir cette valeur dans une autre unité de mesure. Un outil renvoyant des données peu ou pas manipulées est réutilisable dans un plus grand nombre de situations.

# OUTIL-07 Les contrôleurs devraient généralement renvoyer des données formatées

Les contrôleurs, d'un autre côté, peuvent reformater ou manipuler des données car ils n'ont pas pour but d'être réutilisables; leur objectif est d'être aussi bons que possibles à une chose en particulier.

Par exemple, un fonction nommée Get-InfosDisque retournerait l'information qu'est la taille du disque en bytes, car c'est l'unité de mesure la plus fine que le système a à offrir. Un contrôleur qui créerait un inventaire de l'espace libre du disque pourrait traduire cette information en gigabytes, car c'est l'unité de mesure la plus pratique pour les gens qui verront le rapport de cet inventaire.

Une étape intermédiaire est utile pour les outils contenus dans des modules de script : les vues. En construisant un manifeste pour le module, vous pouvez faire en sorte que le module inclue aussi un fichier de définition de vue .format.ps1xml personnalisé. La vue peut spécifier des valeurs de données manipulées, comme la vue par défaut utilisée par PowerShell pour afficher la sortie de Get-Process. La vue ne manipule pas les données sous-jacentes, laissant ainsi les données brutes intactes à toutes fins utiles.


# GACHIS-01 Ne réinventez pas la roue

Il existe de nombreuses approches pour "faire le taff" dans PowerShell. Dans certains cas, il est possible que d'autres membres de la communauté aient déjà écrit le code qui permet d'atteindre vos ubjectifs. Si ce code répond à vos besoins, alors vous pouriez gagner du temps en le mettant en oeuvre, plutôt que de l'écrire vous-même.

Par exemple :

```PowerShell
function Ping-Ordinateur ($nomordinateur) {
    $ping = Get-WmiObject Win32_PingStatus -filter "Address='$nomordinateur'"
    if ($ping.StatusCode -eq 0) {
        return $true
    } else {
        return $false
    }
}
```

Cette fonction a quelques axes d'amélioration. Premièrement, le bloc de paramètre est déclaré comme une fonction basique; on préférera généralement utiliser des fonctions avancées. Deuxièmement, le verbe de la commande ("Ping") n'est pas un verbe de commande PowerShell approuvé. Cela causera des avertissements si la fonction est exportée en tant que partie d'un module PowerShell, à moins que lesdits avertissements aient été désactivés à l'import.

Troisièmement, il y a peu d'intérêt à écrire cette fonction dans PowerShell v2 ou plus récent. PowerShell v2 intègre une commande qui permet d'accéder à une fonction similaire. Utilisez simplement :

```PowerShell
Test-Connection $nomordinateur -Quiet
```

Cette commande intégrée accomplit exactement la même tâche en vous demandant moins de travail - e.g. vous n'avez pas à écrire votre propre fonction.

Certains arguent que, "je ne savais pas que telle ou telle chose existaient, donc je l'ai écrite moi-même." Cet argument échoue généralement avec la communauté, qui tend à penser que l'ignorance n'est pas une excuse. Avant de faire l'effort d'écrire une fonction ou toute autre unité de code, vérifiez si PowerShell le fait déjà nativement. Demandez autour de vous. Et, si vous finissez par écrire la vôtre, soyez ouverts si quelqu'un vous montre une façon native de le faire.

En revanche, il est important de noter qu'écrire votre propre code à partir de zéro peut être utile si vous essayez d'apprendre un concept en particulier, ou si vous avez des besoins spécifiques qui ne sont pas comblés par une solution pré-existante.


# GACHIS-02 Rapportez les bugs à Microsoft

Une exception : si vous savez qu'une technique native ne fonctionne pas bien (e.g., elle bugge ou n'accomplit pas sa tâche exacte), alors il est évidemment bon de réinventer la roue. Cependant, dans les cas où vous le faites pour éviter un bug ou une faille dans le design, alors vous devriez aussi - en tant qu'honnête membre de la communauté - rapporter ce bug.


_NdT : La section suivante est décrite comme "douteuse, au mieux" par les auteur.e.s anglophones. Je lui donne donc une priorité basse, et la traduirai à la fin de ce projet._

TODO: The "PURE" section is dubious at best. We need to discuss it.


# PURE-01 Use native PowerShell where possible

This means not using COM, .NET Framework classes, and so on when there is a native Windows PowerShell command or technique that gets the job done.

# PURE-03 Document why you haven't used PowerShell

So when is it okay to move from one item on this list to another? Obviously, if a task can't be accomplished with a more-preferred way, you move on to a less-preferred way.

If a less-preferred approach offers far superior performance, and performance is a potential issue, then go for the better-performing approach. For example, Robocopy is superior in nearly every way to Copy-Item, and there are probably numerous circumstances where Robocopy would do a much better job.

Document the reason for using tools other than PowerShell in your comments.

# PURE-04 Wrap other tools in an advanced function or cmdlet

That said, you truly become a better PowerShell person if you take the time to wrap a less-preferred way in an advanced function or cmdlet. Then you get the best of both worlds: the ability to reach outside the shell itself for functionality, while keeping the advantages of native commands.

Ignorance, however, is no excuse. If you've written some big wrapper function around Ping.exe simply because you were unaware of Test-Connection, then you've wasted a lot of time, and that is not commendable. Before you move on to a less-preferred approach, make sure the shell doesn't already have a way to do what you're after.


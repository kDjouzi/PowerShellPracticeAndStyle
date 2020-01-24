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

Les contrôleurs, d'un autre côté, 
Controllers, on the other hand, may reformat or manipulate data because controllers do not aim to be reusable; they instead aim to do as good a job as possible at a particular task.

For example, a function named Get-DiskInfo would return disk sizing information in bytes, because that's the most-granular unit of measurement the operating system offers. A controller that was creating an inventory of free disk space might translate that into gigabytes, because that unit of measurement is the most convenient for the people who will view the inventory report.

An intermediate step is useful for tools that are packaged in script modules: views. By building a manifest for the module, you can have the module also include a custom .format.ps1xml view definition file. The view can specify manipulated data values, such as the default view used by PowerShell to display the output of Get-Process. The view does not manipulate the underlying data, leaving the raw data available for any purpose.


# WAST-01 Don't re-invent the wheel

There are a number of approaches in PowerShell that will "get the job done." In some cases, other community members may have already written the code to achieve your objectives. If that code meets your needs, then you might save yourself some time by leveraging it, instead of writing it yourself.

For example:

```PowerShell
function Ping-Computer ($computername) {
    $ping = Get-WmiObject Win32_PingStatus -filter "Address='$computername'"
    if ($ping.StatusCode -eq 0) {
        return $true
    } else {
        return $false
    }
}
```

This function has a few opportunities for enhancement. First of all, the parameter block is declared as a basic function; using advanced functions is generally preferred. Secondly, the command verb ("Ping") isn't an approved PowerShell command verb. This will cause warnings if the function is exported as part of a PowerShell module, unless they are otherwise suppressed on import.

Thirdly, there's little reason to write this function in PowerShell v2 or later. PowerShell v2 has a built-in command that will allow you to perform a similar function. Simply use:

```PowerShell
Test-Connection $computername -Quiet
```

This built-in command accomplishes the exact same task with less work on your part - e.g., you don't have to write your own function.

It has been argued by some that, "I didn't know such-and-such existed, so I wrote my own." That argument typically fails with the community, which tends to feel that ignorance is no excuse. Before making the effort to write some function or other unit of code, find out if the shell can already do that. Ask around. And, if you end up writing your own, be open to someone pointing out a built-in way to accomplish it.

On the flip side, it's important to note that writing your own code from the ground up can be useful if you are trying to learn a particular concept, or if you have specific needs that are not offered by another existing solution.


# WAST-02 Report bugs to Microsoft

An exception: if you know that a built-in technique doesn't work properly (e.g., it is buggy or doesn't accomplish the exact task), then obviously it's fine to re-invent the wheel. However, in cases where you're doing so to avoid a bug or design flaw, then you should - as an upstanding member of the community - report the bug on [connect.microsoft.com](http://connect.microsoft.com/) also.



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


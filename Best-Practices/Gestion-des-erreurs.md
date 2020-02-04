# ERR-01 Utilisez -ErrorAction Stop quand vous appelez des cmdlets

Quand vous attrapez une erreur, essayez d'utiliser -ErrorAction Stop sur les cmdlets pour générer des exceptions stoppantes et attrapables.

# ERR-02 Utilisez $ErrorActionPreference = 'Stop' ou 'Continue' quand vous appelez des non-cmdlets

Quand vous exécutez autre chose qu'un cmdlet, paramétrez $ErrorActionPreference = 'Stop' avant l'exécution, et réinitialisez ensuite à Continue. Si vous ressentez une certaine inquiétude à utiliser -ErrorAction du fait que ça laisse le pipeline entier en plan, alors vous avez probablement sur-construit le pipeline. Envisagez de penser "construction de procédures", car penser en terme de procédures permet une meilleure gestion automatique des erreurs

Idéalement, toute commande que vous pensez capable de se foirer devrait gérer une seule tâche : envoyer une requête à un ordinateur, supprimer un fichier, mettre à jour un utilisateur. De cette façon, s'il y a une erreur, vous pouvez la gérer et passer à la suite.

# ERR-03 Eviter d'utiliser des drapeaux pour gérer les erreurs

Essayez d'éviter de créer des drapeaux :

```PowerShell
try {
    $continuer = $true
    Do-Quelque-Chose -ErrorAction Stop
} catch {
    $continuer = $false
}

if ($continuer) {
    Do-Ca
    Set-Ceci
    Get-Ceux-La
}
```

Mettez plutôt la "transaction" complète dans le boc Try :

```PowerShell
try {
    Do-Quelque-Chose -ErrorAction Stop
    Do-Ca
    Set-Ceci
    Get-Ceux-La
} catch {
    Handle-Error
}
```

Il est alors beaucoup plus simple de suivre la logique.

# ERR-04 Eviter d'utiliser "$"?

Quand vous examinez une erreur qui est arrivée, Evitez d'utiliser $?. Ca ne veut en fait pas dire qu'une erreur est arrivée ou pas; ça sert à vérifier si la dernière commande exécutée a considérée être finie avec succès. Vous n'avez aucun détail sur ce qui est arrivé.

# ERR-05 Evitez de tester si une variable est null en tant que condition d'erreur

Evitez aussi de tester si une variable est null en tant que condition d'erreur :

```
$utilisateurice = Get-ADUser -Identity DonJ

if ($utilisateurice) {
    $utilisateurice | Do-Something
} else {
    Write-Warning "Could not get user $utilisateurice"
}
```

A certains moments et avec certaines technologies ce sera la seule approche qui marche, surtout si la commande que vous lancez ne voudra pas produire d'erreur stoppante et attrapable. Mais c'est une approche qui tord la logique, et peut rendre le déboguage plus complexe.

# ERR-06 Copiez $Error[0] dans votre propre variable

Dans un bloc `catch`, `$_` contiendra la dernière erreur arrivée, tout comme `$Error[0]`. Utilisez l'une ou l'autre - mais copiez ça immédiatement dans votre propre variable, car l'exécution de commandes additionnelles peut causer un "kidnapping" de `$_` ou un changement de la valeur de `$Error[0]`.

It n'est pas nécessaire de libérer `$Error` dans la plupart des cas. `$Error[0]` sera la dernière erreur, and PowerShell entretiendra automatiquement le reste de la collection `$Error`.

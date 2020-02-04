# PERF-01 Si la performance est importante, testez-la

PowerShell est équipé de 3.2 millions de bizarreries dans ses performances. Environ.

Par exemple, la première ligne ci-dessous d'exécute beaucoup plus vite que la deuxième :

```PowerShell
[void]Do-Something
Do-Something | Out-Null
```

Si vous connaissez plusieurs façons de faire quelque chose, et que vous écrivez un script de production qui gérera de larges ensembles de données (c'est-à-dire que la performance deviendra un facteur cumulatif), alors testez-en les performances avec Measure-Command ou tout autre outil.

# PERF-02 Pensez aux compromis entre performances et lisibilité

La performance n'est pas la seule réison d'écrire un script. Si un script doit gérer 10 données simples, une amélioration de 30% des performances ne vous fera pas vraiment gagner de temps. Il est tout à fait acceptable d'utiliser une technique plus simple à lire, comprendre, et maintenir - quoique "plus simple" soit un terme très subjectif. Des deux commandes ci-dessus, une personne pourrait penser que l'une ou l'autre est "plus simple" à comprendre ou lire.

C'est un domaine important pour les membres de la communauté PowerShell. Bien que tout le monde s'accorde sur l'importance de l'esthétique - elle aide à rendre les scripts plus lisibles, maintenable, et ainsi de suite - la performance a aussi son importance. Toutefois, les avantages d'un petit gain en performance ne pèsent pas toujours aussi lourd sur la balance que les avantages "doux" d'une qualité esthétique.

Par exemple :

```PowerShell
$contenu = Get-Content fichier.txt

ForEach ($ligne in $contenu) {
  Do-Something -input $ligne
}
```

La plupart des gens approuveront la bonne qualité de base de l'esthétique de cet exemple. Cet extrait utilise une approche PowerShell native, est facile à suivre, et parce qu'il utilise une approche de programmation structurelle, il est facile à étendre (disons, si vous avez besoin d'exécuter plusieurs autre commandes à chaque ligne de contenu). Toutefois, cette approche offre des performances extrêmement basses. Si fichier.txt fait quelques centaines de kilo-octets, pas de problème; s'il fait quelques centaines de méga-octets, problème potentiel. Get-Content est obligé de lire le fichier entier en mémoire d'un seul coup, en le stockant dans ladite mémoire (dans la variable $contenu).

Envisagez maintenant cette approche alternative :

```PowerShell
Get-Content fichier.txt |
ForEach-Object -Process {
  Do-Something -input $\_
}
```

Comme décrit autre part dans ce guide, beaucoup de membres de la communauté n'aimeraient pas cette approche pour des raisons esthétiques. Cependant, cette approche a l'avantage d'utiliser le pipeline de PowerShell pour "streamer" le contenu de fichier.txt. En partant du principe que la commande imaginaire "Do-Something" le bloque pas le pipeline (à la Sort-Object), le shell peut envoyer des lignes de contenu (techniquement, des objets String) au travers du pipeline en un flot continu, plutôt que d'avoir à tous les stocker dans la mémoire tampon.

Certains argueraient que cette seconde approche est toujours mauvaise, et que si la performance est un souci vous devriez retomber d'une approche native PowerShell à une approche .NET Framework plus bas-niveau :

```PowerShell
$sr = New-Object -Type System.IO.StreamReader -Arg fichier.txt

while ($sr.Peek() -ge 0) {
   $ligne = $sr.ReadLine()
   Do-Something -input $ligne
}
```

Il y a des myriades de variations de cette approche, évidemment, mais elles règlent le problème en lisant une ligne à la fois, plutôt que de charger le fichier entier en mémoire. Cela maintient l'approche "programmation structurelle" du premier exemple, au coût de l'utilisation d'un modèle .NET Framework potentiellement plus dur à suivre que des commandes natives PowerShell. Beaucoup considèrent ce troisième exemple comme une étape intermédiaire, et suggèrent qu'une vraie approche nénéfique serait d'écrire des commandes PowerShell en tant qu'"emballage" autour du code .NET. Par exemple (en notant que ce quatrième exemple utilise des commandes imaginaire comme illustration) :


```PowerShell
$gestion = Open-TextFile file.txt

while (-not Test-TextFile -handle $gestion) {
    Do-Something -input (Read-TextFile -handle $handle)
}
```

Cet exemple repart sur une approche native PowerShell, en uilisant des commandes et des paramètres. Les commandes proposées (Open-TextFile, Test-TextFile, et Read-TextFile) sont simplement des "emballages" autour de classes .NET Framework, telles que la classe StreamReader montrée dans le troisième exemple.

Vous vous rendrez généralement compte qu'il est possible de se conformer aux préférences esthéétiques généralement partagées par la communauté tout en maintenant un bon niveau de performance. Le faire peut demander plus de travail - comme par exemple écrire des des commandes d'"emballage" PowerShell autour de classes .NET Framework sous-jacentes. Beaucoup argueraient que, pour un outil que vous comptez utiliser sur un long terme, le travail additionnel est un investissement plus que raisonnable.

La morale est ici que l'esthétique et la performance sont des considérations importantes, et sans contexte de travail, aucune n'est plus importante que l'autre. Il est souvent possible, avec la bonne technique, d'obtenir les deux. De façon générale, vous devriez éviter d'abandonner l'esthétique pour des questions de performance - tant que possible, faites l'effort de satisfaire vos besoins en matière de performance autant qu'en matière d'esthétique.

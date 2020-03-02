### Fonctions

Evitez d'utiliser le mot-clé `return` dans vos fonctions. Placez juste la variable d'objet quelque part toute seule.

Quand vous déclarez des fonctions simple, laissez un espace entre le nom de la fonction et les paramètres.

```PowerShell
function MaFonction ($param1, $param2) {
    ...  
}
```

### Fonctions avancées

Nommez les fonctions avancées et les scripts en utilisant le format **\<verbe\>-\<nom\>**. Vous pouvez obtenir la liste des verbes approuvés avec le cmdlet `Get-Verb`. Le côté "nom" peut être composé d'un ou plusieurs noms utilisant la PascalCase, et étant toujours accordés au singulier.

Dans des foncitons avancées, n'utilisez pas le mot-clef `return` pour renvoyer un objet.

Dans les focntions avancées, vous retournez les objets dans le bloc `Process {}` et pas dans `Begin {}` ou `End {}` puisque cela vous empêcherait de profiter des avantages du pipeline.

```PowerShell
# Pas bien
function Get-USCitizenCapability {
    [CmdletBinding()]
    [OutputType([psobject])]
    param (
        [Parameter(Mandatory = $true,
                   ValueFromPipelineByPropertyName = $true,
                   Position = 0)]
        [int16]
        $Age
    )
    process {
        $Capabilities = @{
            MilitaryService = $false
            DrinkAlcohol = $false
            Vote = $false
        }
        if ($Age -ge 18) {
            $Capabilities['MilitaryService'] = $true
            $Capabilities['Vote'] = $true
        }

        $Obj = New-Object -Property $Capabilities -TypeName psobject
    }
    end { return $Obj }
}
  
# Bien
function Get-USCitizenCapability {
    [CmdletBinding()]
    [OutputType([psobject])]
    param (
        [Parameter(Mandatory = $true,
                   ValueFromPipelineByPropertyName = $true,
                   Position = 0)]
        [int16]
        $Age
    )
    process {
        $Capabilities = @{
            MilitaryService = $false
            DrinkAlcohol = $false
            Vote = $false
        }

        if ($Age -ge 18) {
            $Capabilities['MilitaryService'] = $true
            $Capabilities['Vote'] = $true
        }

        New-Object -Property $Capabilities -TypeName psobject
    }
}
```

#### Utilisez toujours l'attribut CmdletBinding.

#### Ayez toujours au moins un bloc de code `process {}` si au moins un de vos paramètres prend des valeurs du pipeline.

#### Spécifiez un attribut OutputTypesi la fonction avancée revoie un objet ou une collection d'objets.

Si la fonction retourne des types d'objets différents selon l'ensemble de paramètres, fournissez un type de retour par ensemble de paramètres.

```PowerShell
[OutputType([<TypeLiteral>], ParameterSetName = "<Name>")]
[OutputType("<TypeNameString>", ParameterSetName = "<Name>")]
```

#### Quand un ParameterSetName est utilisé dans les paramètres, fournissez toujours un DefaultParameterSetName dans l'attribut CmdletBinding.

```PowerShell
function Get-User {
    [CmdletBinding(DefaultParameterSetName = "ID")]
    [OutputType("System.Int32", ParameterSetName = "ID")]
    [OutputType([String], ParameterSetName = "Name")]
    param (
        [parameter(Mandatory = $true, ParameterSetName = "ID")]
        [Int[]]
        $UserID,

        [parameter(Mandatory = $true, ParameterSetName = "Name")]
        [String[]]
        $UserName
    )
    <# function body #>
}
```

#### Quand vous utilisez des fonctions avancées ou des scripts avec l'attribut CmdletBinding, évitez si possible de valider les paramètres dans le corps du script et utilisez plutôt des attributs de validation de paramètres.

* Attribut de validation **AllowNull**

  L'attribut de validation AllowNull permet que la valeur d'un paramètre obligatoire soit nulle ($null).

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [AllowNull()]
      [String]
      $NomOrdinateur
  )
  ```

* Attribut de validation **AllowEmptyString**

  L'attribut de validation AllowEmptyString permet que la valeur d'un paramètre obligatoire soit une chaîne de caractères vide ("").

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [AllowEmptyString()]
      [String]
      $NomOrdinateur
  )
  ```

* Attribut de validation **AllowEmptyCollection**

  L'attribut de validation AllowEmptyCollection permet que la valeur d'un paramètre obligatoire soit une collection vide (@()).

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [AllowEmptyCollection()]
      [String[]]
      $NomOrdinateur
  )
  ```

* Attribut de validation **ValidateCount**

  L'attribut de validation ValidateCount spécifie le nombre de valeurs
  minimum et maximum qu'un paramètre accepte. Windows PowerShell génère
  une erreur si le nombre de valeurs du paramètre dans la commande qui
  appelle la fonction est en-dehors de cette portée.

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [ValidateCount(1,5)]
      [String[]]
      $NomOrdinateur
  )
  ```

* Attribut de validation **ValidateLength**

  L'attribut ValidateLength spécifie le nombre minimum et maximum de
  caractères dans la valeur d'un paramètre ou d'une variable.
  Windows PowerShell génère une erreur si la longueur d'une valeur spécifiée
  pour un paramètre ou une variable est en-dehors de cette portée.

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [ValidateLength(1,10)]
      [String[]]
      $NomOrdinateur
  )
  ```

* Attribut de validation **ValidatePattern**

  L'attribut ValidatePattern spécifie une expression régulière qui est
  comparée à la valeur du paramètre ou de la variable. Windows PowerShell
  génère une erreur si la valeur ne correspond pas aux paramètres de cette
  expression régulière.

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [ValidatePattern("[0-9][0-9][0-9][0-9]")]
      [String[]]
      $NomOrdinateur
  )
  ```

* Attribut de validation **ValidateRange**

  L'attribut ValidateRange spécifie une portée numérique pour chaque
  valeur de paramètre ou variable. Windows PowerShell génère une erreur
  si une valeur est en-dehors de cette portée.

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [ValidateRange(0,10)]
      [Int]
      $Tentatives
  )
  ```

* Attribut de validation **ValidateScript**

  L'attribut ValidateScript spécifie un script utilisé pour
  valider la valeur d'un paramètre ou d'une variable.
  Windows PowerShell envoie la valeur dans le script, et
  génère une erreur si le script renvoie "faux" ou s'il lance
  une exception.
  
  Quand vous utilisez l'attribut ValidateScript, la valeur
  en validation est attachée à la variable $_ . Vous pouvez
  utiliser la variable $_ pour vous référer à cette valeur
  dans le script.
  
  ```PowerShell
  param (
      [Parameter()]
      [ValidateScript({$_ -ge (get-date)})]
      [DateTime]
      $DateEvenement
  )
  ```

* Attribut de validation **ValidateSet**

  L'attribut ValidateSet spécifie un ensemble de valeurs valides pour
  un paramètre ou une variable. Windows PowerShell génère une erreur
  si la valeur d'un paramètre ou une variable ne correspond à aucune
  valeur de l'ensemble. Dans l'exemple suivant, la valeur du paramètre
  $Detail peut seulement être "Bas", "Moyen" ou "Haut".

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [ValidateSet("Bas", "Moyen", "Haut")]
      [String[]]
      $Detail
  )
  ```

* Attribut de validation **ValidateNotNull**

  L'attribut de validation ValidateNotNull spécifie que la
  valeur du paramètre ne peut pas être nulle ($null).
  Windows PowerShell génère une erreur si la valeur du paramètre
  est nulle.
  
  L'attribut ValidateNotNull est fait pour être utilisé quand le type
  de valeur de paramètre n'est pas spécifiée ou quand le type spécifié
  accepte une valeur nulle. (Si vous spécifiez un type qui n'acceptera
  pas de valeur nulle, comme le type string, la valeur nulle sera rejetée
  même sans l'attribut ValidateNotNull, parce qu'elle ne correspond pas
  au type spécifié.)
  
  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [ValidateNotNull()]
      $ID
  )
  ```

* Attribut de validation **ValidateNotNullOrEmpty**

  L'attribut ValidateNullOrEmpty spécifie que la valeur de paramètre
  ne peut pas être nulle ($null), ni être une chaîne vide ("").
  Windows PowerShell génère une erreur si le paramètre est utilisé
  dans un appel de fonction, mais que sa valeru est nulle, une chaîne
  vide, ou un tableau vide.

  ```PowerShell
  param (
      [Parameter(Mandatory = $true)]
      [ValidateNotNullOrEmpty()]
      [String[]]
      $NomUtilisateur
  )
  ```

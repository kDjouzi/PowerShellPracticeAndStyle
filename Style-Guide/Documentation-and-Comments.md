### Documentation et commentaires

Les commentaires qui contredisent le code sont pires que l'absence de commentaires. Faites de la mise à jour de vos commentaires à chaque changement du code une priorité !

Les commentaires devraient être en anglais, et devraient être des phrases complètes. Si le commentaire est court, le point à la fin peut être omis.

Rappelez-vous que les commentaires devraient servir votre raisonnement et votre prise de décision, pas tenter d'expliquer ce qu'une commande fait. A l'exception des expressions régulières, le PowerShell bien écrit a le potentiel de s'expliquer lui-même.

```PowerShell
# N'écrivez pas :
# Incrémenter Marge de 2
$Marge = $Marge + 2

# Ecrivez peut-être :
# La boîte de rendu cache quelques pixels.
$Marge = $Marge + 2
```

#### Blocs de commentaires

N'en faites pas trop avec les commentaires. A moins que votre code ne soit particulièrement obscur, ne précédez pas chaque ligne d'un commentaire -- cela "casse" le code et le rend plus diffifila à lire. A la place, écrivez un seul bloc de commentaires.

Les blocs de commentaires s'appliquent généralement à une partie ou l'ensemble du code qui les suit, et sont indentés au même niveau que le code. Chaque ligne devrait commencer par un croisillon (#) et un seul espace.

Si le bloc est particulièrement long (comme dans le cas de texte de documentation) il est recommandé d'utiliser la syntaxe de bloc de commentaire `<# ... #>`, mais vous devriez placer les caractères de commentaires sur leur propre ligne, et indenter le commentaire :

```PowerShell
# L'espace en début de ligne rend les choses plus lisibles et évite une certaine confusion.
# Ecrire un commentaire par ligne permet une plus grande clarté de ceux-ci dans la console.

<#
    .SYNOPSIS
        Les blocs de commentaire longs sont fastidieux à garder commentés 
        en mode "ligne unique" (#).
    .DESCRIPTION
        Plus particulièrement quand le commentaire doit être modifié fréquemment,
        comme dans les cas de l'aide et la documentation d'une fonction ou
        d'un script.
#>
```

#### Commentaires à l'intérieur de lignes

Les commentaires sur la même ligne qu'une déclaration peuvent empêcher de se concentrer, mais quand ils ne répètent pas l'évidence, et particulièrement quand plusieurs courtes lignes de code doivent être expliquées, ils peuvent être utiles.

Ils devraient être séparés du code par au moins deux espaces et, idéalement, ils devraient être alignés avec les autres commentaires contenus dans le même bloc de code.

```PowerShell
$Options = @{
    Margin = 2          # La boîte de rendu cache quelques pixels.
    Padding = 2         # On a besoin d'espace entre la bordure et le texte.
    FontSize = 24       # A garder au-dessus de 16 pour que rester lisible pendant les présentations.
}
```

#### Commentaires de documentation

L'aide des commentaires devrait être écrite dans un langage simple.

vous n'écrivez pas une thèse pour votre cours d'Ecriture Technique à la fac - vous écrivez quelque chose qui décrit comment une fonction marche. Evitez les mots longs non nécessaires, et faites en sorte que vos explications restent courtes. Vous n'essayez pas d'impressionner qui que ce soit, et les seules personnes qui liront jamais ça essaieront simplement de comprendre comment utiliser la fonction.

Si vous écrivez dans ce qui est, pour vous, une langue étrangère, les mots et structures de phrases les plus simples sont préférables, et ont plus de chances d'être comprises par un.e lecteur.ice natif.ve.

Expliquez les choses de façon complète, mais concise.

##### Placement

Afin de s'assurer que la documentation reste avec la fonction, les commentaires de documentation devraient être placés DANS la fonction, plutôt qu'au-dessus. Pour rendre l'oubli de la mise à jour desdits commentaires plus difficile, vous devriez les garder en haut de la fonction, plutôt qu'en bas.

Evidemment, cela ne veut pas dire que les mettre autre part est une erreur -- mais il est plus simple de faire comme ça, et cela rend plus difficile d'oublier de les mettre à jour.

##### Mettez les détails dans les notes

Si vous voulez fournir des explications détaillées quant au fonctionnement de votre outil, utilisez la section `Notes` à cette fin.

##### Décrivez la fonction

Chaque commande de fonction de script devrait avoir un court texte décrivant sa fonction. C'est le `Synopsis`.

##### Documentez chaque paramètre

Chaque paramètre devrait être documenté. Pour simplifier la synchronisation des commentaires aux changements fait aux paramètre, l'emplacement privilégié des commentaires de documentation des paramètres est _l'intérieur_ du bloc `param`n directement au-dessus de chaque paramètre.
Nous pouvons trouver des exemples dans ces extraits de code ISE :

```PowerShell
param(
    # Description et aide de $Param1
    [Parameter(Mandatory = $true,
                ValueFromPipelineByPropertyName = $true,
                Position = 0)]
    $Param1,

    # Description et aide de $Param2
    [int]
    $Param2
)
```

Il est aussi possible de placer les déclarations `.PARAMETER` avec le reste des commentaires de documentation, mais l'expérience montre que les commentaires plus proches du code qu'ils documentent tendent à être mis à jour plus assidûment.

##### Fournissez des exemples d'utilisation

Votre aide devrait toujours fournir un exemple pour chaque cas d'utilisation majeur. Un "exemple d'utilisation" est juste un exemple de ce que vous taperiez dans PowerShell pour faire fonctionner le script - vous pouvez même copier et coller un exemple à partir de l'invite de commande lors de tests de votre fonction.

```PowerShell
function Test-Help {
    <#
        .SYNOPSIS
            Une fonction d'exemple pour montrer comment l'aide devrait
            être écrite.
            
        .EXAMPLE
            Get-Help -Name Test-Help
            
            Affiche l'aide pour la fonction d'exemple.
    #>
    [CmdletBinding()]
    param(
        # Ce paramètre ne fait rien.
        # Alias: MP
        [Parameter(Mandatory = $true)]
        [Alias("MP")]
        [String]$MandatoryParameter
    )

    <# ici : du code ... #>
}
```

### DOC-01 Ecrivez de l'aide à basée sur les commentaires

Vous devriez toujours écrire de l'aide basée sur les commentaires dans vos scripts et fonctions.

L'aide basée sur les commentaires est formatée comme suit :

```PowerShell
function Get-Example {
    <#
    .SYNOPSIS
        Une brève description de votre fonction ou script.

    .DESCRIPTION
        Une description plus longue.

    .PARAMETER PremierParametre
        Description de chaque paramètre.
        Note:
        Pour faciliter une mise à jour des commentaires synchronisée avec les changements
        faits aux paramètres, la localisation favorisée des commentaires de documentation 
        des paramètres n'est pas ici, mais dans le bloc param, directement au-dessus de
        chaque paramètre.

    .PARAMETER SecondParametre
        Description de chaque paramètre.

    .INPUTS
        Description des objets qui peuvent être "tuyautés" (insérés) dans le script.

    .OUTPUTS
        Description des objets qui sont renvoyés par le script.

    .EXAMPLE
        Example expliquant comment faire fonctionner le script.

    .LINK
        Lien vers une documentation plus détaillée.

    .NOTES
        Détails concernant les fonctionnalités du script, s'ils sont nécessaires.

    #>
```

L'aide basée sur les commentaires est ce qui est affiché quand l'utilisateur tape `help Get-Example` ou `Get-Example -?`, etc.

Votre aide devrait aider. C'est à dire, que si vous avez écrit un outil appelé `Get-UtilisateursAppLOB`, n'écrivez pas d'aide qui dise juste "Obtient les utilisateurs de l'application LOB". Eh ouais.

**Plus d'informations :** Vous pouvez en savoir plus sur l'aide basée sur les commentaires en tapant `help about_Comment_Based_Help` dans PowerShell.

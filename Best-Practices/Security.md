### Sécurité

#### Utilisez toujours PSCredential pour les identifiants/mots de passe

Vous devez éviter de stocker le mot de passe dans un objet string ordinaire, ou d'autoriser l'utilisateur à les taper directement en tant que paramètres (où ils pourraient finir dans l'historique ou être exposés à des malwares "gratteurs d'écran"). La meilleure méthode est de toujours utiliser des objets PSCredential (qui stockent le mot de passe dans un objet SecureString).

Plus spécifiquement, vous devriez toujours prendre le PSCredential en tant que paramètre (et ne jamais appeler Get-Credential dans votre fonction) pour permettre à l'utilisateur de réutiliser des identifiants stockés dans une variable.

En outre, vous devriez utiliser l'attribut Credential comme les commandes natives le font, de façon à ce que si l'utilisateurice passe son identifiant (plutôt qu'un objet PSCredential), iel soit invité.e à entrer son mot de passe dans une boîte de dialogue sécurisée Windows.

```PowerShell
param (
    [System.Management.Automation.PSCredential]
    [System.Management.Automation.Credential()]
    $Credentials
)
```

Si vous devez absolument passer un mot de passe d'une chaîne simple à un appel API .Net ou une librairie tierce il vaut mieux décrypter les identifiants lors de leur passage plutôt que de les stocker dans une variable.

```PowerShell
    # Obtenir le mot de passe en clair pour un appel de méthode :
    $Insecure.SetPassword( $Credentials.GetNetworkCredential().Password )
```

#### Autres chaînes sécurisées


Pour les autres chaînes qui pourraient être sensibles, utilisez le type SecureString pour protéger la valeur de la chaîne. Assurez-vous de toujours fournir un exemple à l'utilisateur passant la valeur en utilisant `Read-Host -AsSecureString`.

Notez que, si vous avez un jour besoin de convertir une SecureString en string, vous pouvez utiliser cette méthose, mais assurez-vous d'appeler ZeroFreeBSTR afin d'éviter une fuite de mémoire :

```PowerShell
    # Décryptage d'une chaîne sécurisée.
    $BSTR = [System.Runtime.InteropServices.marshal]::SecureStringToBSTR($this);
    $plaintext = [System.Runtime.InteropServices.marshal]::PtrToStringAuto($BSTR);
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($BSTR);
    return $plaintext
```

* Pour les identifiants qui doivent être enregistrés sur le disque, sérialisez l'objet en utilisant Export-CliXml pour protéger la valeur du mot de passe. Le mot de passe sera protégé car contenu dans un objet SecureString et sera uniquement accessible à l'utilisateur ayant généré le fichier, et seulement sur l'ordinateur où ce fichier a été généré.

```PowerShell
    # Enregistrer l'objet Credential sur le disque
    Get-Credential | Export-CliXml -Path c:\creds\credential.xml

    # Importer l'objet Credential précédemment enregistré
    $Credential = Import-CliXml -Path c:\creds\credential.xml
```

* Pour les chaînes qui peuvent être sensibles et doivent être enregistrées sur le disque, utilisez ConvertFrom-SecureString afin d'en garder une version cryptée dans une chaîne qui peut être enregistrée sur le disque. Vous pouvez utiliser ConvertTo-SecureString pour convertir la string standard en SecureString. NOTE : ces commandes utilisent l'API de Protection Windows (DPAPI) pour crypter les données, donc les strings cryptées ne peuvent être décryptées que par le même utilisateursur la même machine, mais il y a l'option d'utiliser AES avec une clef partagée.

```PowerShell
    # Invitez l'utilisateur à entrer une SecureString (en automation, acceptez-la en paramètre)
    $Secure = Read-Host -Prompt "Enter the Secure String" -AsSecureString

    # Cryptage dans une string standard et stockage sur le disque
    ConvertFrom-SecureString -SecureString $Secure | Out-File -Path "${Env:AppData}\Sec.bin"

    # Lecture de la string standard à partir du disque et conversion en SecureString
    $Secure = Get-Content -Path "${Env:AppData}\Sec.bin" | ConvertTo-SecureString
```


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

* For credentials that need to be saved to disk, serialize the credential object using
Export-CliXml to protect the password value. The password will be protected as a secure
string and will only be accessible to the user who generated the file on the same 
computer where it was generated.

```PowerShell
    # Save a credential to disk
    Get-Credential | Export-CliXml -Path c:\creds\credential.xml

    # Import the previously saved credential
    $Credential = Import-CliXml -Path c:\creds\credential.xml
```

* For strings that may be sensitive and need to be saved to disk, use ConvertFrom-SecureString to encrypt it into a standard string that can be saved to disk. You can then use ConvertTo-SecureString to convert the encrypted standard string back into a SecureString. NOTE: These commands use the Windows Data Protection API (DPAPI) to encrypt the data, so the encrypted strings can only be decrypted by the same user on the same machine, but there is an option to use AES with a shared key.

```PowerShell
    # Prompt for a Secure String (in automation, just accept it as a parameter)
    $Secure = Read-Host -Prompt "Enter the Secure String" -AsSecureString

    # Encrypt to Standard String and store on disk
    ConvertFrom-SecureString -SecureString $Secure | Out-File -Path "${Env:AppData}\Sec.bin"

    # Read the Standard String from disk and convert to a SecureString
    $Secure = Get-Content -Path "${Env:AppData}\Sec.bin" | ConvertTo-SecureString
```


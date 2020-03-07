---
title: Azure Disk Encryption minta parancsfájlok
description: Ez a cikk a Windows rendszerű virtuális gépek Microsoft Azure lemezes titkosításának függeléke.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 50addbec1717c7bb76a248053dd889b09441f6f6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78391491"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption minta parancsfájlok 

Ez a cikk példákat tartalmaz az előre titkosított virtuális merevlemezek és egyéb feladatok előkészítéséhez.

 

## <a name="list-vms-and-secrets"></a>Virtuális gépek és titkok listázása

Az előfizetésben található összes titkosított virtuális gép listázása:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
A Key vaultban lévő virtuális gépek titkosításához használt összes lemezes titkosítási titok listázása:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>A Azure Disk Encryption előfeltételek parancsfájljai
Ha már ismeri a Azure Disk Encryption előfeltételeit, használhatja az [Azure Disk Encryption előfeltételek PowerShell-szkriptet](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). A PowerShell-szkriptek használatára példát a [virtuális gépek titkosítása](disk-encryption-powershell-quickstart.md)– gyors útmutató című témakörben talál. Eltávolíthatja a megjegyzéseket a parancsfájl egy szakaszában sor 211, az összes lemez titkosítása egy meglévő erőforráscsoportot a meglévő virtuális gépek indítása. 

Az alábbi táblázat mutatja, hogy mely paraméterek is használható a PowerShell-parancsfájlt: 

|Paraméter|Leírás|Kötelező?|
|------|------|------|
|$resourceGroupName| Az erőforrás nevét, amelyhez a KeyVault tartozik.  Ezen a néven egy új erőforráscsoport létrejön, ha egy nem létezik.| True (Igaz)|
|$keyVaultName|A KeyVault a melyik titkosítási kulcsai elhelyezni kívánt nevét. Ezen a néven egy új tároló létrejön, ha egy nem létezik.| True (Igaz)|
|$location|A KeyVault helye. Győződjön meg arról a KeyVault és a virtuális gépek titkosítását ugyanazon a helyen. A helyek listáját a következővel érheti el: `Get-AzLocation`.|True (Igaz)|
|$subscriptionId|Használható az Azure-előfizetés azonosítója.  Az előfizetés-azonosítóját a következővel érheti el: `Get-AzSubscription`.|True (Igaz)|
|$aadAppName|Neve az Azure AD-alkalmazást, amely a KeyVault titkos kódok írása történik. Ha a megadott néven még nem létezik alkalmazás, a rendszer létrehoz egyet a beírt néven. Ha az alkalmazás már létezik, aadClientSecret a paramétert átadhatja a parancsfájlt.|False (Hamis)|
|$aadClientSecret|A korábban létrehozott Azure AD-alkalmazás titkos ügyfélkódja.|False (Hamis)|
|$keyEncryptionKeyName|A KeyVault választható kulcstitkosítási kulcs neve. Ezen a néven egy új kulcsot létrejön, ha egy nem létezik.|False (Hamis)|

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Titkosítása és visszafejtése a virtuális gépek az Azure AD-alkalmazás nélkül

- [Lemez titkosításának engedélyezése meglévő vagy Windows rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Titkosítás letiltása egy futó Windowsos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Titkosítása és visszafejtése a virtuális gépek az Azure AD-alkalmazás (előző kiadás) 
 
- [Lemez titkosításának engedélyezése meglévő vagy Windows rendszerű virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Titkosítás letiltása egy futó Windowsos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Új titkosított felügyelt lemez létrehozása egy előre titkosított VHD/Storage-blobból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Létrehoz egy új titkosított felügyelt lemezt előre titkosított virtuális merevlemez és a megfelelő titkosítási beállítások

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Előre titkosított Windows virtuális merevlemez előkészítése
A következő szakaszok szükség annak előre titkosított Windows virtuális merevlemez előkészítése az Azure IaaS-titkosított merevlemezként üzembe helyezéshez. Az információk segítségével készítheti elő, és indítsa el az Azure Site Recovery vagy az Azure friss Windows virtuális gép (VHD). A virtuális merevlemezek előkészítésével és feltöltésével kapcsolatos további információkért lásd: [általánosított virtuális merevlemez feltöltése és használata új virtuális gépek létrehozásához az Azure-ban](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Nem TPM-OS védelem engedélyezése csoportházirend frissítése
Konfigurálja a BitLocker Csoportházirend beállítást **BitLocker meghajtótitkosítás**, amely a **helyi számítógép házirendje** > **Számítógép konfigurációja** > **Felügyeleti sablonok** > Windows- **összetevők**területen található. Módosítsa ezt a beállítást **operációsrendszer-meghajtókra** > ha **további hitelesítésre van szükség indításkor** > **engedélyezze a BitLockert kompatibilis TPM nélkül**, az alábbi ábrán látható módon:

![Microsoft Antimalware szolgáltatás az Azure-ban](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>A BitLocker a szolgáltatás-összetevők telepítése
Windows Server 2012 és újabb verziók használja a következő parancsot:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

A Windows Server 2008 R2 a következő paranccsal:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>A BitLocker operációsrendszer-kötetének előkészítése `bdehdcfg` használatával
Az operációsrendszer-partíció tömörítéséhez és a számítógép BitLockerhez való előkészítéséhez hajtsa végre a [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) , ha szükséges:

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Az operációsrendszer-kötet védelmét a BitLocker használatával
A [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) parancs használatával engedélyezze a titkosítást a rendszerindító köteten egy külső kulcstartó használatával. A külső meghajtók vagy kötetek is elhelyezhető a külső kulcs (.bek fájl). Titkosítás után a következő újraindításkor engedélyezve van a rendszerlemez vagy rendszerindító köteten.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Készítse elő a virtuális Gépet egy külön data/erőforrás VHD-t a bitlockerrel a külső kulcs beolvasása.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Titkosított virtuális merevlemez feltöltése Azure Storage-fiókba
A DM-Crypt titkosítás engedélyezése után a helyi titkosított VHD-t fel kell tölteni a Storage-fiókjába.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Töltse fel az előre titkosított virtuális gép titkos kulcsát a kulcstartóba
A korábban beszerzett titkosítási titkot titkos kulcsként kell feltölteni a kulcstartóba.  Ehhez meg kell adni a titkos kulcs beállítása engedélyt és a wrapkey engedélyt ahhoz a fiókhoz, amely fel fogja tölteni a titkot.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>A lemez titkosítási titka nem titkosított KEK-sel
A titkos kulcs a Key vaultban történő beállításához használja a [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A jelszó Base64 karakterláncként van kódolva, majd feltöltve a kulcstartóba. Emellett ellenőrizze, hogy a titkos kulcsot a key vaultban történő létrehozásakor a következő címkék vannak-e beállítva.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


A következő lépésben az [operációsrendszer-lemez csatlakoztatásához a KEK használata nélkül](#without-using-a-kek)`$secretUrl`.

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>A lemez titkosítási titka egy KEK-sel titkosítva
Mielőtt feltölti a titkos kulcsot a key vaulthoz, igény szerint titkosíthatók, kulcstitkosítási kulcs használatával. A wrap [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) használatával először Titkosítsa a titkos kulcsot a kulcs titkosítási kulcsával. Ennek a körbefuttatási műveletnek a kimenete Base64 URL-kódolású karakterlánc, amelyet aztán titkosként tölthet fel a [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmag használatával.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

A következő lépésben használja a `$KeyEncryptionKey` és `$secretUrl` elemet az [operációsrendszer-lemez a KEK használatával történő csatlakoztatásához](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Titkos URL-cím megadása operációsrendszer-lemez csatlakoztatásakor

###  <a name="without-using-a-kek"></a>KEK használata nélkül
Az operációsrendszer-lemez csatlakoztatása közben `$secretUrl`kell átadnia. Az URL-címet az "a-lemeztitkosítás titkos kulcs egy KEK nem titkosított" szakaszban jött létre.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK használata
Az operációsrendszer-lemez csatlakoztatásakor adja át `$KeyEncryptionKey` és `$secretUrl`. Az URL-címet az "Lemeztitkosítás titkos kódja egy KEK titkosított" szakaszban jött létre.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```

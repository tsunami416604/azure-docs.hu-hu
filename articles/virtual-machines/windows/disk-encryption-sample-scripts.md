---
title: Azure Disk Encryption – mintaszkriptek
description: Ez a cikk a Microsoft Azure lemeztitkosítás windowsos virtuális gépekhez függeléke.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: e5e0a970df680df43a7bd303636b3d81bda3e141
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085705"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption – mintaszkriptek 

Ez a cikk mintaparancsfájlokat tartalmaz az előre titkosított virtuális gépek és egyéb feladatok előkészítéséhez.

 

## <a name="list-vms-and-secrets"></a>Virtuális gépek és titkos kulcsok listázása

Az összes titkosított virtuális gép listázása az előfizetésben:

```azurepowershell-interactive
$osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
$dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
```
A kulcstartóban lévő virtuális gépek titkosításához használt összes lemeztitkosítási titok listázása:

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

## <a name="the-azure-disk-encryption-prerequisites-scripts"></a>Az Azure Disk Encryptions előfeltételei parancsfájlok
Ha már ismeri az Azure disk encryption előfeltételeit, használhatja az [Azure Disk Encryption előfeltételei PowerShell-parancsfájlt.](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ) A PowerShell-parancsfájl használatával például tekintse meg a [Virtuálisgép titkosítása rövid útmutató című témakört.](disk-encryption-powershell-quickstart.md) Eltávolíthatja a megjegyzéseket a parancsfájl egy szakaszáról a 211-es sortól kezdve, hogy titkosítsa a meglévő virtuális gépek összes lemezét egy meglévő erőforráscsoportban. 

Az alábbi táblázat bemutatja, hogy mely paraméterek használhatók a PowerShell-parancsfájlban: 

|Paraméter|Leírás|Kötelező?|
|------|------|------|
|$resourceGroupName| Annak az erőforráscsoportnak a neve, amelyhez a KeyVault tartozik.  Egy új erőforráscsoport jön létre ezzel a névvel, ha nem létezik.| True (Igaz)|
|$keyVaultName|Annak a KeyVault-nak a neve, amelyben a titkosítási kulcsokat el kell helyezni. Egy új tároló ezzel a névvel jön létre, ha nem létezik.| True (Igaz)|
|$location|A KeyVault helye. Győződjön meg arról, hogy a keyvault és a virtuális gépek titkosítani kell ugyanazon a helyen. A helyek listáját a következővel érheti el: `Get-AzLocation`.|True (Igaz)|
|$subscriptionId|A használandó Azure-előfizetés azonosítója.  Az előfizetés-azonosítóját a következővel érheti el: `Get-AzSubscription`.|True (Igaz)|
|$aadAppName|Az Azure AD-alkalmazás neve, amely titkos kulcsok keyvault-írási használatával lesz használva. Ha a megadott néven még nem létezik alkalmazás, a rendszer létrehoz egyet a beírt néven. Ha ez az alkalmazás már létezik, adja át az aadClientSecret paramétert a parancsfájlnak.|False (Hamis)|
|$aadClientSecret|A korábban létrehozott Azure AD-alkalmazás ügyféltkati titka.|False (Hamis)|
|$keyEncryptionKeyName|A keyvaultban nem kötelező kulcstitkosítási kulcs neve. Ha nem létezik ilyen nevű új kulcs jön létre.|False (Hamis)|

## <a name="resource-manager-templates"></a>Resource Manager-sablonok

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Virtuális gépek titkosítása vagy visszafejtése Azure AD-alkalmazás nélkül

- [Lemeztitkosítás engedélyezése meglévő vagy windowsos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Titkosítás letiltása windowsos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Virtuális gépek titkosítása vagy visszafejtése Egy Azure AD-alkalmazással (előző kiadás) 
 
- [Lemeztitkosítás engedélyezése meglévő vagy windowsos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Titkosítás letiltása windowsos virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Új titkosított felügyelt lemez létrehozása előre titkosított virtuális merevlemez/tárolóblobból](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Létrehoz egy új, titkosított felügyelt lemezt, amely előre titkosított virtuális merevlemezt és a hozzá tartozó titkosítási beállításokat biztosít

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Előre titkosított Windows virtuális merevlemez előkészítése
A következő szakaszok szükségesek egy előre titkosított Windows-virtuális merevlemez előkészítéséhez az Azure IaaS-ben titkosított virtuális merevlemezként való üzembe helyezéshez. Az információk segítségével előkészítheti és elindíthatja a friss Windows virtuális gép (VHD) az Azure Site Recovery vagy az Azure. A virtuális merevlemez ek előkészítéséről és feltöltéséről további információt a [Generalizált virtuális merevlemez feltöltése és új virtuális gépek létrehozása az Azure-ban című témakörben talál.](upload-generalized-managed.md)

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Csoportházirend frissítése a nem TPM szolgáltatás engedélyezéséhez az operációs rendszer védelméhez
Konfigurálja a **BitLocker meghajtótitkosítás**BitLocker-házirend-beállítását, amely a **Helyi számítógépházirend-konfiguráció** > –**konfigurációs számítógép konfigurációja** > **felügyeleti sablonok** > **Windows-összetevők**területen található . Módosítsa ezt a beállítást **operációsrendszer-meghajtókra** > **További hitelesítést igényel indításkor** > **A BitLocker engedélyezése kompatibilis TPM nélkül**, ahogy az az alábbi ábrán látható:

![Microsoft Antimalware szolgáltatás az Azure-ban](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>A BitLocker szolgáltatásösszetevőinek telepítése
Windows Server 2012 és újabb rendszer esetén használja a következő parancsot:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Windows Server 2008 R2 esetén használja a következő parancsot:

    ServerManagerCmd -install BitLockers

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Az operációs rendszer kötetének előkészítése a BitLocker számára a`bdehdcfg`
Az operációs rendszer partíciójának tömörítéséhez és a gép BitLocker-hez való előkészítéséhez szükség esetén hajtsa végre a [bdehdcfg-et:](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment)

    bdehdcfg -target c: shrink -quiet 

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Az operációs rendszer kötetének védelme a BitLocker használatával
A [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) paranccsal engedélyezheti a titkosítást a rendszerindító köteten egy külső kulcsvédő használatával. Helyezze a külső kulcsot (.bek fájlt) a külső meghajtóra vagy kötetre. A titkosítás engedélyezve van a rendszer/rendszerindító köteten a következő újraindítás után.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Készítse elő a virtuális gép egy külön adat/erőforrás virtuális merevlemez a külső kulcs beszerzése a BitLocker használatával.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Titkosított virtuális merevlemez feltöltése Azure-tárfiókba
A DM-Crypt titkosítás engedélyezése után a helyi titkosított virtuális merevlemezt fel kell tölteni a tárfiókba.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Töltse fel az előre titkosított virtuális gép titkos kulcsát a key vaultba
A korábban beszerzett lemeztitkosítási titkos kulcsot titkos kulcsként kell feltölteni a kulcstartóba.  Ehhez meg kell adnia a készlet titkos és a wrapkey engedélyt a fiók, amely feltölti a titkos kulcsokat.

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

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>A lemeztitkosítás titkos kulcsa nincs kek-vel titkosítva
A titkos kulcs beállítása a key vaultban használja a [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A jelszó base64 karakterláncként van kódolva, majd feltöltve a key vaultba. Emellett győződjön meg arról, hogy a következő címkék vannak beállítva, amikor létrehozza a titkos kulcsot a key vaultban.

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


Használja `$secretUrl` a következő lépésben [az operációs rendszer lemezének kek használata nélküli csatlakoztatásához.](#without-using-a-kek)

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>KEK-vel titkosított lemeztitkosítási titkoskulcs
Mielőtt feltöltene a titkos kulcsot a key vaultba, tetszés szerint titkosíthatja egy kulcstitkosítási kulcs használatával. A wrap [API segítségével](https://msdn.microsoft.com/library/azure/dn878066.aspx) először titkosítsa a titkos kulcsot a kulcs titkosítási kulcs használatával. A wrap művelet kimenete egy base64 URL-kódolású karakterlánc, amelyet a [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) parancsmag használatával titkosként tölthet fel.

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

`$KeyEncryptionKey` Használja, `$secretUrl` és a következő lépésben [az operációs rendszer lemezének kek használatával történő csatlakoztatásához.](#using-a-kek)

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Titkos URL megadása operációsrendszer-lemez csatolásakor

###  <a name="without-using-a-kek"></a>KEK használata nélkül
Az operációs rendszer lemezének csatlakoztatása közben `$secretUrl`át kell adnia a . Az URL-cím a "KEK-vel nem titkosított lemeztitkosítási titok" szakaszban jött létre.
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
Amikor csatlakoztatja az operációs `$KeyEncryptionKey` `$secretUrl`rendszer lemezét, adja át, és . Az URL-cím a "KEK-vel titkosított lemeztitkosítási titkosítatlan" szakaszban jött létre.
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

---
title: Key Vault kulcs és titok visszaállítása a titkosított virtuális gépekhez Azure Backup használatával
description: Megtudhatja, hogyan állíthatja vissza Azure Backup Key Vault kulcsát és titkos kódját a PowerShell használatával
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: geg
ms.openlocfilehash: bdc732ef02e9995e3d6dc17aa1f79eb97d895a73
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465581"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Key Vault kulcs és titok visszaállítása a titkosított virtuális gépekhez Azure Backup használatával

Ez a cikk az Azure-beli virtuális gépek biztonsági mentésének használatáról nyújt útmutatást a titkosított Azure-beli virtuális gépek visszaállításához, ha a kulcs és a titkos kód nem létezik a kulcstartóban. Ezeket a lépéseket akkor is használhatja, ha a kulcs (kulcs titkosítási kulcsa) és a titkos (BitLocker-titkosítási kulcs) egy különálló másolatát szeretné fenntartani a visszaállított virtuális géphez.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* **Titkosított virtuális gépek biztonsági mentése** – a titkosított Azure-beli virtuális gépek biztonsági mentése a Azure Backup használatával történt. Tekintse át az Azure-beli [virtuális gépek biztonsági mentésének és visszaállításának kezelése a PowerShell használatával](backup-azure-vms-automation.md) című cikket a titkosított Azure-beli virtuális gépek biztonsági mentéséhez.
* **Azure Key Vault konfigurálása** – győződjön meg arról, hogy az a kulcstartó, amelybe vissza kell állítani a kulcsokat és a titkos kulcsokat, már jelen van. A Key Vault kezelésével kapcsolatos részletekért tekintse meg a [Azure Key Vault első lépéseivel foglalkozó](../key-vault/key-vault-get-started.md) cikket.
* **Lemez visszaállítása** – ellenőrizze, hogy a [PowerShell-lépések](backup-azure-vms-automation.md#restore-an-azure-vm)használatával a titkosított virtuális gép lemezei visszaállítására szolgáló visszaállítási feladatot aktiválta-e. Ennek az az oka, hogy ez a művelet a titkosított virtuális gép visszaállításához szükséges kulcsokat és titkos kódokat tartalmazó JSON-fájlt hoz létre a Storage-fiókban.

## <a name="get-key-and-secret-from-azure-backup"></a>Kulcs és titok beolvasása Azure Backup

> [!NOTE]
> Miután visszaállította a lemezt a titkosított virtuális géphez, ügyeljen arra, hogy:
> * a $details a lemez-visszaállítási feladatok részleteivel van feltöltve, ahogy azt a [lemezek visszaállítása szakasz PowerShell-lépései részben](backup-azure-vms-automation.md#restore-an-azure-vm) említettük.
> * A virtuális gépet csak a **kulcs és a titkos kulcs visszaállítása után**lehet helyreállított lemezekről létrehozni.

A visszaállított lemez tulajdonságainak lekérdezése a feladatok részleteihez.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Állítsa be az Azure Storage-környezetet, és állítsa vissza a titkosított virtuális gép kulcs-és titkos adatait tartalmazó JSON konfigurációs fájlt.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Kulcs visszaállítása

Miután a rendszer létrehozta a JSON-fájlt a fent említett cél elérési úton, hozza létre a kulcs blob-fájlját a JSON-ből, és táplálja azt a Key parancsmag visszaállításához, hogy a kulcs (KEK) vissza legyen helyezve a kulcstartóba.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Titkos kód visszaállítása

A fent generált JSON-fájllal titkos nevet és értéket kaphat, és megadhatja a titkos parancsmagot, hogy a titkos kulcs (BEK) vissza legyen helyezve a kulcstartóba. Akkor használja ezeket a parancsmagokat, ha a **virtuális gép a BEK és a KEK használatával van titkosítva**.

**Akkor használja ezeket a parancsmagokat, ha a Windows rendszerű virtuális gép a BEK és a KEK használatával van titkosítva.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Ezek a parancsmagok akkor használhatók, ha a linuxos virtuális gép a BEK és a KEK használatával van titkosítva.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

A fent generált JSON-fájllal titkos nevet és értéket kaphat, és megadhatja a titkos parancsmagot, hogy a titkos kulcs (BEK) vissza legyen helyezve a kulcstartóba. Akkor használja ezeket a parancsmagokat **, ha a virtuális gép csak BEK használatával van titkosítva** .

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
> * Az $secretname értékének megszerzéséhez a $encryptionObject. OsDiskKeyAndSecretDetails. SecretUrl kimenetére és a titkokat követő szöveg használatára, illetve a titkos titkos URL-cím https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 , a titkos név pedig B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * A címke DiskEncryptionKeyFileName értéke megegyezik a titkos névvel.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Virtuális gép létrehozása visszaállított lemezről

Ha titkosított virtuális gépet készített biztonsági mentést az Azure VM Backup használatával, a fent említett PowerShell-parancsmagok segítenek visszaállítani a kulcsot és a titkos kulcsot a kulcstartóba. A visszaállítást követően tekintse át az Azure-beli [virtuális gépek biztonsági mentésének és visszaállításának kezelése a PowerShell használatával](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) című cikket, amely titkosított virtuális gépeket hoz létre a visszaállított lemezről, kulcsról és

## <a name="legacy-approach"></a>Örökölt megközelítés

A fent említett módszer a helyreállítási pontok esetében is működni fog. A kulcs-és titkos adatok helyreállítási pontról történő lekérésének régebbi megközelítése azonban a 2017. július 11-én régebbi helyreállítási pontok esetében érvényes a BEK és a KEK használatával titkosított virtuális gépek esetében. Ha a visszaállítási lemez feladata befejeződött a titkosított virtuális gép számára a [PowerShell-lépések](backup-azure-vms-automation.md#restore-an-azure-vm)használatával, győződjön meg arról, hogy a $RP érvényes értékkel van feltöltve.

### <a name="restore-key"></a>Kulcs visszaállítása

A következő parancsmagok segítségével lekérheti a kulcs (KEK) adatait a helyreállítási pontról, és megtáplálhatja a kulcs-parancsmag visszaállításával, hogy vissza lehessen állítani a kulcstartóban.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Titkos kód visszaállítása

A következő parancsmagok segítségével szerezheti be a titkos (BEK) adatokat a helyreállítási pontról, és megadhatja, hogy a titkos parancsmagot állítsa vissza a Key vaultba.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> * $Secretname értékének megszerzéséhez a $rp 1 kimenetére hivatkozva lehet. KeyAndSecretDetails. SecretUrl és szöveg használata a titkok után/például a kimeneti titkos URL https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 -cím, a titkos név pedig B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> * A címke DiskEncryptionKeyFileName értéke megegyezik a titkos névvel.
> * A DiskEncryptionKeyEncryptionKeyURL értékének beszerzése a Key vaultból a kulcsok visszaállítása után és a [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) parancsmag használatával
>
>

## <a name="next-steps"></a>További lépések

Miután visszaállította a kulcsot és a titkos kulcsot a Key vaultba, tekintse át az Azure-beli [virtuális gépek biztonsági mentésének és visszaállításának kezelése a PowerShell használatával](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) című cikket, amely titkosított virtuális gépeket hoz létre a helyreállított lemez

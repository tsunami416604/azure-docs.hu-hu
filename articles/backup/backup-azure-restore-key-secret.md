---
title: A Key Vault kulcsának visszaállítása titkos & a titkosított virtuális gép számára
description: Megtudhatja, hogyan állíthatja vissza a Key Vault-kulcsot és titkos kulcsot az Azure Backupban a PowerShell használatával
ms.topic: conceptual
ms.date: 08/28/2017
ms.openlocfilehash: 35bcb919cadd46c603b1f2ad49742c5435f873d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75450052"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>A titkosított virtuális gépekhez tartozó Key Vault-kulcs és titkos kulcs visszaállítása az Azure Backup használatával

Ez a cikk az Azure VM Biztonsági mentés használatával a titkosított Azure virtuális gépek visszaállítását, ha a kulcs és a titkos kulcs nem létezik a key vaultban. Ezek a lépések akkor is használhatók, ha a visszaállított virtuális gép kulcsának (kulcstitkosítási kulcs) és titkos (BitLocker titkosítási kulcs) külön példányát szeretné karbantartani.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

* **Biztonsági mentés titkosított virtuális gépek** – a titkosított Azure-beli virtuális gépekről az Azure Backup használatával történt biztonsági másolat. Olvassa el a [cikk kezelése biztonsági mentés és visszaállítás az Azure-beli virtuális gépek a PowerShell használatával](backup-azure-vms-automation.md) a titkosított Azure-virtuális gépek biztonsági mentésének részleteit.
* **Azure Key Vault konfigurálása** – Győződjön meg arról, hogy a kulcskulcsok és kulcsok visszaállításának kulcsait és titkos kulcsait már jelen van. Tekintse meg az Azure Key Vault gal az [Azure Key Vault](../key-vault/key-vault-get-started.md) – első lépések című cikket a key vault-kezelés részleteiért.
* **Lemez visszaállítása** – Győződjön meg arról, hogy a [PowerShell-lépésekkel](backup-azure-vms-automation.md#restore-an-azure-vm)aktiválta a titkosított virtuális gép lemezeinek visszaállítására vonatkozó visszaállítási feladatot. Ennek az az oka, hogy ez a feladat létrehoz egy JSON-fájlt a tárfiókban, amely kulcsokat és titkos kulcsokat tartalmaz a titkosított virtuális gép visszaállításához.

## <a name="get-key-and-secret-from-azure-backup"></a>Kulcs és titok beszerezni az Azure Backup szolgáltatásától

> [!NOTE]
> A titkosított virtuális gép lemezének visszaállítása után győződjön meg arról, hogy:
>
> * $details a lemezfeladat-visszaállítási feladat részleteivel van feltöltve, ahogy azt [a Lemezek visszaállítása szakasz PowerShell-lépései](backup-azure-vms-automation.md#restore-an-azure-vm) is említik
> * A virtuális gép csak a **kulcs és titkos kulcs és titkos kulcs kulcstárolóba való visszaállítása után**hozható létre a visszaállított lemezekről.

A feladat részleteinek lekérdezése a visszaállított lemez tulajdonságairól.

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Állítsa be az Azure storage-környezetben, és állítsa vissza a JSON konfigurációs fájl, amely kulcs és titkos részleteket a titkosított virtuális gép.

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Kulcs visszaállítása

Miután a JSON-fájl a fent említett célelérési úton jön létre, hozzon létre kulcsblob-fájlt a JSON-ból, és adja meg a kulcsparancsmag visszaállításához, hogy a kulcs (KEK) visszakerüljön a key vaultba.

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Titkos titok visszaállítása

Használja a fent létrehozott JSON-fájl, hogy titkos nevet és értéket kapjon, és adja meg a titkos parancsmag beállításához, hogy a titkos kulcsot (BEK) visszahelyezze a key vaultba.Használja ezeket a parancsmagokat, ha a **virtuális gép bek és KEK használatával van titkosítva.**

**Használja ezeket a parancsmagokat, ha a Windows virtuális gép bek és KEK használatával van titkosítva.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Használja ezeket a parancsmagokat, ha a Linux virtuális gép bek és KEK használatával van titkosítva.**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = <Key_url_of_newly_restored_key>;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Használja a fent létrehozott JSON-fájl, hogy titkos nevet és értéket kapjon, és adja meg a titkos parancsmag beállításához, hogy a titkos kulcsot (BEK) visszahelyezze a key vaultba.Használja ezeket a parancsmagokat, ha a virtuális gép csak **BEK használatával van titkosítva.**

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
>
> * Az érték $secretname lehet beszerezni hivatkozva a kimeneti $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl és a szöveg használata után https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 secrets/ pl. kimeneti titkos URL-cím és titkos neve B3284AAA-DAAA-4AAA-B393-60CAAAA848AAAAA
> * A lemeztitkosításikulcsfájlnév címkéje megegyezik a titkos névvel.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Virtuális gép létrehozása a visszaállított lemezről

Ha az Azure VM Biztonsági mentés használatával biztonsági másolatot készít a titkosított virtuális gépről, a fent említett PowerShell-parancsmagok segítenek a kulcs és a titkos kulcs visszaállításában a key vaultba. Visszaállításuk után olvassa el a cikk [az Azure-beli virtuális gépek biztonsági mentésének és visszaállításának a PowerShell használatával](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) című cikket a visszaállított lemezről, kulcsból és titkos kulcsból származó titkosított virtuális gépek létrehozásához.

## <a name="legacy-approach"></a>Örökölt megközelítés

A fent említett megközelítés minden helyreállítási ponton működne. Azonban a helyreállítási pontról érkező kulcs- és titkos információk beszerzésének régebbi megközelítése érvényes lenne a BEK és a KEK használatával titkosított virtuális gépek 2017 július 11-én régebbi helyreállítási pontokra. Miután a visszaállítási lemezfeladat befejeződött a titkosított virtuális gép [powershell-lépésekkel,](backup-azure-vms-automation.md#restore-an-azure-vm)győződjön meg arról, hogy $rp érvényes értékkel van feltöltve.

### <a name="restore-key"></a>Kulcs visszaállítása

A következő parancsmagok segítségével kulcs (KEK) információkat a helyreállítási pont, és a feed vissza a kulcs parancsmag, hogy tegye vissza a key vault.

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Titkos titok visszaállítása

A következő parancsmagok segítségével titkos (BEK) információkat kaphat a helyreállítási pontról, és előállíthatja azt titkos parancsmag beállításához, hogy visszahelyezze a kulcstárolóba.

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
>
> * A $secretname értéke a $rp1 kibocsátására való nevezéssel érhető el. KeyAndSecretDetails.SecretUrl és a szöveg használata után titkok / https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 pl. kimeneti titkos URL-t és titkos név B3284AAA-DAAA-4AAA-B393-60CAAA848AAAA
> * A címke értéke A DiskEncryptionKeyFileName megegyezik a titkos névvel.
> * A DiskEncryptionKeyEncryptionKeyURL értéke a kulcstárolóból beszerezhető a kulcsok visszaállítása és a [Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) parancsmag használata után
>
>

## <a name="next-steps"></a>További lépések

A kulcs és a titkos kulcs visszaállítása után a key vault, olvassa el a cikk [az Azure virtuális gépek biztonsági mentése és visszaállítása a PowerShell használatával](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) titkosított virtuális gépek létrehozása a visszaállított lemezről, kulcs és titkos kulcs.

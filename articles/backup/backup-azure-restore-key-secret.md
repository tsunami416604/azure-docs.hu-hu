---
title: "Állítsa vissza a Key Vault kulcs és a titkos kulcs titkosított virtuális gépek Azure Backup használatával |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet visszaállítani a Key Vault kulcsot és titkos kulcsot az Azure Backup szolgáltatáshoz a PowerShell használatával"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Állítsa vissza a Key Vault kulcs és a titkos kulcs titkosított virtuális gépek Azure Backup segítségével
Ez a cikk beszél biztonsági mentéssel Azure VM visszaállításhoz titkosított Azure virtuális gépeken, ha a kulcs és a titkos kulcs nem létezik a kulcstároló kulcsain. Ezeket a lépéseket is használható, ha meg szeretné tartani a (Key titkosítási kulcsot) és a titkos kulcs (a BitLocker titkosítási kulcs) egy külön példányát a visszaállított virtuális géphez.

## <a name="prerequisites"></a>Előfeltételek
* **Biztonsági másolat a virtuális gépek titkosítva** - titkosított Azure virtuális gépek mentett Azure Backup segítségével. Tekintse meg a cikk [biztonsági mentéséhez és visszaállításához a PowerShell használata Azure virtuális gépek kezelése](backup-azure-vms-automation.md) titkosított Azure virtuális gépek biztonsági mentés részletei.
* **Konfigurálja az Azure Key Vault** – győződjön meg arról, hogy a kulcstároló, amelyhez kulcsok és titkos kell állítani már létezik. Tekintse meg a cikk [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md) kulcstároló-kezeléssel kapcsolatos részletekért.
* **Állítsa vissza a lemez** -győződjön meg arról, hogy Ön léptetnie visszaállítási feladat lemezeket a használatával titkosított virtuális gép visszaállítására [PowerShell lépéseket](backup-azure-vms-automation.md#restore-an-azure-vm). Ennek oka az, a feladatot hoz létre egy JSON-fájlt tartalmazó kulcsok és titkos állítani a titkosított virtuális gép tárfiókját.

## <a name="get-key-and-secret-from-azure-backup"></a>Az Azure Backup kulcs és a titkos kulcs beszerzése

> [!NOTE]
> Miután lemez helyre lett állítva a titkosított virtuális gép számára, győződjön meg arról, hogy:
> 1. $details feltöltődik a visszaállítási lemez feladat részleteit, ahogyan az [visszaállítási lépések PowerShell a lemez szakasz](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. Virtuális gép kell létrehozni, csak a visszaállított lemezekből **kulcs és a titkos kulcs tárolóba történt visszaállítása után**.
>
>

A feladat részleteit a visszaállított lemez tulajdonságainak lekérdezése.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Állítsa be az Azure storage-környezetben, és állítsa vissza a kulcsot és titkos adatait tartalmazó titkosított virtuális gép JSON-konfigurációs fájlt.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Kulcs helyreállítása
Miután a JSON-fájl jön létre a fent említett elérési utat, hozza létre a JSON kulcs fájlját, és úgy, hogy a kulcs parancsmag használatával helyezze vissza a kulcsot (KEK) a key vault visszaállítása hírcsatorna.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Titkos kulcs visszaállítása
A létrehozott fenti titkos nevét és értékét, és az adatcsatorna JSON-fájl használatával állítsa vissza a titkos (BEK) kerüljenek bele a key vault titkos parancsmag. **Ezeket a parancsmagokat használja, ha a virtuális gép titkosítása BEK és KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Ha a virtuális gép **BEK csak használatával titkosított**, hozza létre a JSON titkos blob fájlját, és úgy, hogy a titkos parancsmagot, hogy a titkos kulcsot (BEK) helyezze vissza a key vault visszaállítása hírcsatorna.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. $Secretname értéke kérhetők le $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl kimenete hivatkozó és titkos kulcsok után pedig SMS használatával / pl. kimeneti titkos URL-cím https://keyvaultname.vault.azure.net/secrets/ B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 és titkos neve B3284AAA-DAAA-4AAA-B393-60CAA848AAAA:
> 2. A címke DiskEncryptionKeyFileName értéke ugyanaz, mint a titkos nevét.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Virtuális gép létrehozása a visszaállított lemezről
Ha a biztonsági másolatban titkosított virtuális gép Azure virtuális gép biztonsági mentéssel, a PowerShell-parancsmagok fent említett súgó állítja vissza a kulcsot és titkos vissza a key vault. Után a visszaállítás, tekintse meg a cikk [biztonsági mentéséhez és visszaállításához a PowerShell használata Azure virtuális gépek kezelése](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) a visszaállított lemez, a kulcs és a titkos kulcs titkosított virtuális gépek létrehozásához.

## <a name="legacy-approach"></a>A hagyományos megközelítés
A fent említett módszer a helyreállítási pontok csatlakoztatás működik. Azonban a régebbi módszert is, amely kulcsot és titkos információk lekérése a helyreállítási pont lesz érvényes a 2017. július 11. BEK és KEK használatával titkosított virtuális gépek régebbi helyreállítási pontok. A visszaállítási lemez feladatot titkosított virtuális gép használatának befejezése után [PowerShell lépéseket](backup-azure-vms-automation.md#restore-an-azure-vm), győződjön meg arról, hogy $rp érvényes értéket a telepítéskor.

### <a name="restore-key"></a>Kulcs helyreállítása
A következő parancsmagok használatával (KEK) kapcsolatos információkat lekérni a helyreállítási pont és az adatcsatorna úgy, hogy a kulcs parancsmag használatával helyezze vissza a key vault visszaállítása.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Titkos kulcs visszaállítása
A következő parancsmagok használatával beolvasni a titkos (BEK) adatait a helyreállítási ponttal, és úgy, hogy titkos parancsmag használatával helyezze vissza a key vault hírcsatorna.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. $Secretname értéke $rp1 kimenetével hivatkozással érhető el. KeyAndSecretDetails.SecretUrl és titkos kulcsok után pedig SMS használatával / pl. kimeneti titkos URL-cím https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 és titkos neve B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. A címke DiskEncryptionKeyFileName értéke ugyanaz, mint a titkos nevét.
> 3. Kulcstároló DiskEncryptionKeyEncryptionKeyURL értéke beszerezhető visszaállítása vissza a kulcsokat, és használata után [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) parancsmag
>
>

## <a name="next-steps"></a>Következő lépések
Kulcstároló kulcsot és titkos vissza visszaállítása, után tekintse meg a cikk [biztonsági mentéséhez és visszaállításához a PowerShell használata Azure virtuális gépek kezelése](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) a visszaállított lemez, a kulcs és a titkos kulcs titkosított virtuális gépek létrehozásához.

---
title: Azure lemeztitkosítás Linuxhoz
description: Az Azure Disk Encryption for Linux üzembe helyezése egy virtuális gép egy virtuális gép bővítmény használatával.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: 22568c7c23771f143f6cd583114949c380d15e3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066922"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure lemeztitkosítás Linuxhoz (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Áttekintés

Az Azure Disk Encryption a Linux dm-crypt alrendszerét használja fel a teljes lemeztitkosítás biztosításához [bizonyos Azure Linux-disztribúciókon.](https://aka.ms/adelinux)  Ez a megoldás integrálva van az Azure Key Vault lemeztitkosítási kulcsok és titkos kulcsok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listáját az [Azure Disk Encryption for Linux virtuális gépek](../linux/disk-encryption-overview.md)című témakörben, különösen a következő szakaszokban láthatja:

- [Támogatott virtuális gépek és operációs rendszerek](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [További virtuális gépre vonatkozó követelmények](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Hálózati követelmények](../linux/disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](../linux/disk-encryption-overview.md#encryption-key-storage-requirements)

## <a name="extension-schema"></a>Bővítményséma

Az Azure Disk Encryption (ADE) bővítménysémának két verziója van:
- 1.1-es vagy újabb, az Azure Active Directory (AAD) tulajdonságait nem használó séma.
- v0.1 – Egy régebbi séma, amely az Azure Active Directory (AAD) tulajdonságait igényli. 

A célséma kiválasztásához `typeHandlerVersion` a tulajdonságot a használni kívánt séma verziójával egyenlőnek kell beállítani.

### <a name="schema-v11-no-aad-recommended"></a>1.1-es séma: Nincs AAD (ajánlott)

A v1.1-es séma ajánlott, és nem igényel Azure Active Directory (AAD) tulajdonságait.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "1.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KeyVaultResourceId": "[KeyVaultResourceId]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KekVaultResourceId": "[KekVaultResourceId",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v01-with-aad"></a>Séma v0.1: a AAD 

A 0.1 séma `AADClientID` `AADClientSecret` szükséges, és vagy vagy `AADClientCertificate`.

A `AADClientSecret`következők használata:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```

A `AADClientCertificate`következők használata:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "0.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    }
  }
}
```


### <a name="property-values"></a>Tulajdonság értékek

| Név | Érték / Példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2019-07-01 | dátum |
| közzétevő | Microsoft.Azure.Biztonság | sztring |
| type | AzureDiskEncryptionForLinux | sztring |
| typeHandlerVersion | 1.1, 0.1 | int |
| (0,1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Guid | 
| (0,1 séma) AADClientSecret | jelszó | sztring |
| (0,1 séma) AADClientCertificate | Ujjlenyomat | sztring |
| (nem kötelező) (0,1 séma) Jelszót | jelszó | sztring |
| DiskFormatQuery | {"dev_path":"","név":"","file_system":"}} | JSON szótár |
| Titkosítási művelet | EnableEncryption, EnableEncryptionFormatAll | sztring | 
| (nem kötelező - alapértelmezett RSA-OAEP) KeyEncryptionAlgorithm (KeyEncryptionAlgorithm) | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sztring |
| KeyVaultURL | url | sztring |
| KeyVaultResourceId azonosító | url | sztring |
| (nem kötelező) KeyEncryptionKeyURL | url | sztring |
| (nem kötelező) KekVaultResourceId | url | sztring |
| (nem kötelező) SequenceVersion (SequenceVersion) | uniqueidentifier | sztring |
| VolumeType (Kötettípusa) | OS, Adatok, Minden | sztring |

## <a name="template-deployment"></a>Sablonalapú telepítés

A sablon 1.1-es számú sémaalapú központi telepítésének példáját az Azure [201-encrypt-encrypt-running-linux-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)című témakörben talál.

A sablon központi telepítésének például a séma v0.1-es, tekintse meg az Azure Quickstart Template [201-encrypt-running-linux-vm.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)

>[!WARNING]
> - Ha korábban már használta az Azure Lemeztitkosítás t az Azure AD-vel egy virtuális gép titkosításához, továbbra is ezt a lehetőséget kell használnia a virtuális gép titkosításához.
> - Linux operációs rendszer kötetek titkosításakor a virtuális gép nem érhető el. Javasoljuk, hogy kerülje az SSH bejelentkezéseket, amíg a titkosítás folyamatban van, hogy elkerülje a nyitott fájlok blokkolását, amelyeket a titkosítási folyamat során el kell érni. A folyamat ellenőrzéséhez használja a [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell parancsmag vagy a [vm titkosítási show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI parancsot. Ez a folyamat várhatóan néhány órát vesz igénybe egy 30 GB-os operációs rendszer kötet, valamint további időt az adatkötetek titkosítására. Az adatkötet-titkosítási idő arányos lesz az adatkötetek méretével és mennyiségével, kivéve, ha az összes titkosítási formátumot használja. 
> - A titkosítás letiltása linuxos virtuális gépeken csak az adatkötetek esetén támogatott. Adat- vagy operációsrendszer-kötetek en nem támogatott, ha az operációs rendszer kötete titkosítva van. 

>[!NOTE]
> Ha `VolumeType` a paraméter beállítása Mind, az adatlemezek csak akkor lesznek titkosítva, ha megfelelően vannak csatlakoztatva.

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A hibaelhárításról az [Azure lemeztitkosításhiba-elhárítási útmutatójában](../linux/disk-encryption-troubleshooting.md)talál.

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/community/) 

Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure-támogatás,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

## <a name="next-steps"></a>További lépések

* A virtuálisgép-bővítményekről további információt a [Virtuálisgép-bővítmények és a Linux-szolgáltatások című témakörben talál.](features-linux.md)
* Az Azure Disk Encryption for Linux szolgáltatásról további információt a [Linux-alapú virtuális gépek című témakörben talál.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#linux-virtual-machines)

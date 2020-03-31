---
title: Azure lemeztitkosítás Windows hoz
description: Az Azure Disk Encryption telepítése egy Windows virtuális gép egy virtuális gép bővítmény használatával.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: ejarvi
ms.openlocfilehash: e975e1757b77b4aab52a59d1f0709ef9cadae94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066866"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure lemeztitkosítás Windows hoz (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Áttekintés

Az Azure Disk Encryption a BitLocker segítségével teljes lemeztitkosítást biztosít a Windows rendszert futtató Azure virtuális gépeken.  Ez a megoldás integrálva van az Azure Key Vault lemeztitkosítási kulcsok és titkos kulcsok kezelése a key vault-előfizetés. 

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listáját az Azure Disk Encryption for Windows virtuális gépek című [témakörében,](../windows/disk-encryption-overview.md)különösen a következő szakaszokban tartalmazza:

- [Támogatott virtuális gépek és operációs rendszerek](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](../windows/disk-encryption-overview.md#networking-requirements)
- [Csoportházirend-követelmények](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schema"></a>Bővítményséma

Az Azure Disk Encryption (ADE) bővítménysémának két verziója van:
- 2.2-es érték – Egy újabb ajánlott séma, amely nem használja az Azure Active Directory (AAD) tulajdonságait.
- 1.1-es érték: Egy régebbi séma, amely az Azure Active Directory (AAD) tulajdonságait igényli. 

A célséma kiválasztásához `typeHandlerVersion` a tulajdonságot a használni kívánt séma verziójával egyenlőnek kell beállítani.

### <a name="schema-v22-no-aad-recommended"></a>2.2-es séma: Nincs AAD (ajánlott)

A v2.2-es séma minden új virtuális géphez ajánlott, és nem igényel Azure Active Directory-tulajdonságokat.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "AzureDiskEncryption",
        "typeHandlerVersion": "2.2",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyVaultURL": "[keyVaultURL]",
          "KekVaultResourceId": "[keyVaultResourceID]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultResourceId": "[keyVaultResourceID]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        }
  }
}
```


### <a name="schema-v11-with-aad"></a>Séma v1.1: a AAD 

Az 1.1-es `aadClientID` séma `AADClientCertificate` igényel, és vagy, `aadClientSecret` és nem ajánlott az új virtuális gépek.

A `aadClientSecret`következők használata:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2019-07-01",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "type": "AzureDiskEncryption",
    "typeHandlerVersion": "1.1",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
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
| type | AzureDiskEncryption | sztring |
| typeHandlerVersion | 2.2, 1.1 | sztring |
| (1.1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Guid | 
| (1.1 séma) AADClientSecret | jelszó | sztring |
| (1.1 séma) AADClientCertificate | Ujjlenyomat | sztring |
| Titkosítási művelet | EnableEncryption, EnableEncryptionFormatAll | sztring | 
| (nem kötelező - alapértelmezett RSA-OAEP) KeyEncryptionAlgorithm (KeyEncryptionAlgorithm) | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sztring |
| KeyVaultURL | url | sztring |
| KeyVaultResourceId azonosító | url | sztring |
| (nem kötelező) KeyEncryptionKeyURL | url | sztring |
| (nem kötelező) KekVaultResourceId | url | sztring |
| (nem kötelező) SequenceVersion (SequenceVersion) | uniqueidentifier | sztring |
| VolumeType (Kötettípusa) | OS, Adatok, Minden | sztring |

## <a name="template-deployment"></a>Sablonalapú telepítés

A 2.2-es séma alapú sablontelepítést az Azure QuickStart Template [201-encrypt-running-windows-vm-without-aad](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)című témakörben talál.

A sablon 1.1-es számú sémaalapú központi telepítésének példáját az Azure QuickStart Template [201-encrypt-running-windows-vm](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)című témakörben talál.

>[!NOTE]
> Ha `VolumeType` a paraméter beállítása Minden, az adatlemezek csak akkor lesznek titkosítva, ha megfelelően vannak formázva. 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A hibaelhárításról az [Azure lemeztitkosításhiba-elhárítási útmutatójában](../windows/disk-encryption-troubleshooting.md)talál.

### <a name="support"></a>Támogatás

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel az [MSDN Azure és a Stack Overflow fórumokon.](https://azure.microsoft.com/support/community/) 

Másik lehetőségként benyújthat egy Azure-támogatási incidenst. Nyissa meg az [Azure-támogatás,](https://azure.microsoft.com/support/options/) és válassza a Támogatás beszerezni. Az Azure-támogatás használatáról a [Microsoft Azure támogatási gyIK](https://azure.microsoft.com/support/faq/)című területén olvashat.

## <a name="next-steps"></a>További lépések

* A bővítményekről a [Virtuálisgép-bővítmények és -szolgáltatások windowsos](features-windows.md)című témakörben talál további információt.
* Az Azure Disk Encryption for Windows szolgáltatásról további információt a Windows virtuális gépek című [témakörben talál.](../../security/fundamentals/azure-disk-encryption-vms-vmss.md#windows-virtual-machines)

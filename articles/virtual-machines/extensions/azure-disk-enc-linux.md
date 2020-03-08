---
title: Linux-Azure Disk Encryption
description: Üzembe helyezi Azure Disk Encryption Linux rendszerű virtuális gépre egy virtuálisgép-bővítmény használatával.
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
ms.date: 06/10/2019
ms.author: ejarvi
ms.openlocfilehash: 4fa7f7d1419a8cd1006a632ba67587ab3434bf5a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78383220"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption for Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Áttekintés

Azure Disk Encryption kihasználja a Linux dm-crypt alrendszerét, hogy teljes lemezes titkosítást biztosítson az [Azure Linux-disztribúciók kiválasztása](https://aka.ms/adelinux)során.  Ez a megoldás integrálva van Azure Key Vault a lemezes titkosítási kulcsok és titkos kódok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listájáért lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](../linux/disk-encryption-overview.md), konkrétan a következő részekben:

- [Azure Disk Encryption Linux rendszerű virtuális gépekhez](../linux/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [További virtuális gépekre vonatkozó követelmények](../linux/disk-encryption-overview.md#additional-vm-requirements)
- [Hálózati követelmények](../linux/disk-encryption-overview.md#networking-requirements)

## <a name="extension-schemata"></a>Bővítmény sémák

Két sémák van a Azure Disk Encryption: v 1.1, egy újabb, ajánlott séma, amely nem használ Azure Active Directory (HRE) tulajdonságokat és v 0.1-et, amely egy régebbi, HRE-tulajdonságokat igénylő séma. Az Ön által használt bővítménynek megfelelő séma-verziót kell használnia: schema v 1.1 a AzureDiskEncryptionForLinux Extension 1,1-es verziójához, a Schema v 0.1 a AzureDiskEncryptionForLinux Extension 0,1-es verziójához.
### <a name="schema-v11-no-aad-recommended"></a>Séma v 1.1: nincs HRE (ajánlott)

A v 1.1 séma használata javasolt, és nem igényel Azure Active Directory tulajdonságokat.

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
        "publisher": "Microsoft.Azure.Security",
        "settings": {
          "DiskFormatQuery": "[diskFormatQuery]",
          "EncryptionOperation": "[encryptionOperation]",
          "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
          "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
          "KeyVaultURL": "[keyVaultURL]",
          "SequenceVersion": "sequenceVersion]",
          "VolumeType": "[volumeType]"
        },
        "type": "AzureDiskEncryptionForLinux",
        "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="schema-v01-with-aad"></a>A Schema v 0.1: a HRE 

Az 0,1 sémához `aadClientID` és `aadClientSecret` vagy `AADClientCertificate`szükséges.

`aadClientSecret`használata:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientSecret": "[aadClientSecret]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```

`AADClientCertificate`használata:

```json
{
  "type": "extensions",
  "name": "[name]",
  "apiVersion": "2015-06-15",
  "location": "[location]",
  "properties": {
    "protectedSettings": {
      "AADClientCertificate": "[aadClientCertificate]",
      "Passphrase": "[passphrase]"
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "DiskFormatQuery": "[diskFormatQuery]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
    "type": "AzureDiskEncryptionForLinux",
    "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Azure.Security | sztring |
| type | AzureDiskEncryptionForLinux | sztring |
| typeHandlerVersion | 0,1, 1,1 | int |
| (0.1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (0,1 séma) AADClientSecret | jelszó | sztring |
| (0,1 séma) AADClientCertificate | thumbprint | sztring |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-szótár |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sztring | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | sztring |
| KeyEncryptionKeyURL | url | sztring |
| választható KeyVaultURL | url | sztring |
| Passphrase | jelszó | sztring | 
| SequenceVersion | uniqueidentifier | sztring |
| VolumeType | Operációs rendszer, az összes | sztring |

## <a name="template-deployment"></a>Sablonalapú telepítés

A sablonok központi telepítésének példáját lásd: a [titkosítás engedélyezése futó Linux rendszerű virtuális](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)gépeken.

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az utasítások az [Azure CLI legújabb dokumentációjában](/cli/azure/vm/encryption?view=azure-cli-latest)találhatók. 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Hibaelhárításhoz tekintse meg a [Azure Disk Encryption hibaelhárítási útmutatót](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések

További információ a virtuálisgép-bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).
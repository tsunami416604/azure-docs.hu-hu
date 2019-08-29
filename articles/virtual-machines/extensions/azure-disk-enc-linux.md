---
title: Linux-Azure Disk Encryption | Microsoft Docs
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
ms.openlocfilehash: 6a81f105f9632a7ca7e2bf7188e358274020c78f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084768"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption for Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Áttekintés

Azure Disk Encryption kihasználja a Linux dm-crypt alrendszerét, hogy teljes lemezes titkosítást biztosítson az [Azure Linux-disztribúciók kiválasztása](https://aka.ms/adelinux)során.  Ez a megoldás integrálva van Azure Key Vault a lemezes titkosítási kulcsok és titkos kódok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listáját itt tekintheti meg: [Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md)előfeltételek.

### <a name="operating-system"></a>Operációs rendszer

A Azure Disk Encryption jelenleg támogatott a disztribúciók és verziók kiválasztása esetén.  Tekintse [meg a Azure Disk Encryption támogatott operációs rendszereket: Linux](../../security/azure-security-disk-encryption-prerequisites.md#linux) a támogatott Linux-disztribúciók listájához.

### <a name="internet-connectivity"></a>Internetkapcsolat

A Linux rendszerhez készült Azure Disk Encryption internetkapcsolatot igényel Active Directory-, Key Vault-, tárolási és csomagkezelő végpontokhoz való hozzáféréshez.  További információ: [Azure Disk Encryption előfeltételek](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Bővítmény sémák

Két sémák van a Azure Disk Encryption: v 1.1, egy újabb, ajánlott séma, amely nem használ Azure Active Directory (HRE) tulajdonságokat és v 0.1-et, amely egy régebbi, HRE-tulajdonságokat igénylő séma. Az Ön által használt bővítménynek megfelelő séma-verziót kell használnia: schema v 1.1 a AzureDiskEncryptionForLinux Extension 1,1-es verziójához, a Schema v 0.1 a AzureDiskEncryptionForLinux Extension 0,1-es verziójához.
### <a name="schema-v11-no-aad-recommended"></a>Séma v 1.1: Nincs HRE (ajánlott)

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

Az 0,1 `aadClientID` `AADClientCertificate`-es séma használatához és a vagyavagya.`aadClientSecret`

Használat `aadClientSecret`:

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

Használat `AADClientCertificate`:

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
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | Karakterlánc |
| type | AzureDiskEncryptionForLinux | Karakterlánc |
| typeHandlerVersion | 0,1, 1,1 | int |
| (0,1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (0,1 séma) AADClientSecret | password | Karakterlánc |
| (0,1 séma) AADClientCertificate | thumbprint | Karakterlánc |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-szótár |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | Karakterlánc | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | Karakterlánc |
| KeyEncryptionKeyURL | url | Karakterlánc |
| választható KeyVaultURL | url | Karakterlánc |
| Passphrase | password | Karakterlánc | 
| SequenceVersion | uniqueidentifier | Karakterlánc |
| VolumeType | Operációs rendszer, az összes | Karakterlánc |

## <a name="template-deployment"></a>Sablonalapú telepítés

A sablonok központi telepítésének példáját lásd: a [titkosítás engedélyezése futó Linux rendszerű virtuális](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)gépeken.

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az utasítások az [Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest)legújabb dokumentációjában találhatók. 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Hibaelhárításhoz tekintse meg a [Azure Disk Encryption hibaelhárítási útmutatót](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések

További információ a virtuálisgép-bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások Linux rendszerhez](features-linux.md).
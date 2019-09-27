---
title: Azure Disk Encryption a Windows rendszerhez | Microsoft Docs
description: Azure Disk Encryption üzembe helyezése egy Windows rendszerű virtuális gépen a virtuálisgép-bővítmény használatával.
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
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 11394f692765cc1df5db0eb5c0dd06425026505d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092647"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows Azure Disk Encryption (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Áttekintés

Azure Disk Encryption a BitLockert a Windows rendszerű Azure-beli virtuális gépek teljes lemezes titkosításának biztosítására használja.  Ez a megoldás integrálva van Azure Key Vault a lemez titkosítási kulcsainak és a titkos kulcsoknak a Key Vault-előfizetésben való kezeléséhez. 

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listáját itt tekintheti meg: [Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md)előfeltételek.

### <a name="operating-system"></a>Operációs rendszer

A jelenleg Windows-verziók listáját itt tekintheti meg: [Azure Disk Encryption előfeltételek](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Internetkapcsolat

A Azure Disk Encryption internetkapcsolatot igényel Active Directory-, Key Vault-, tárolási és csomagkezelő végpontokhoz való hozzáféréshez.  További információ a hálózati biztonsági beállításokról: [Azure Disk Encryption](
../../security/azure-security-disk-encryption-prerequisites.md)előfeltételek.

## <a name="extension-schemata"></a>Bővítmény sémák

Két sémák van a Azure Disk Encryption: v 1.1, egy újabb, ajánlott séma, amely nem használ Azure Active Directory (HRE) tulajdonságokat és v 0.1-et, amely egy régebbi, HRE-tulajdonságokat igénylő séma. Az Ön által használt bővítménynek megfelelő séma-verziót kell használnia: schema v 1.1 a AzureDiskEncryption Extension 1,1-es verziójához, a Schema v 0.1 a AzureDiskEncryption Extension 0,1-es verziójához.

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
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientSecret": "[aadClientSecret]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
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
      "AADClientCertificate": "[aadClientCertificate]"
    },    
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KekVaultResourceId": "[keyVaultResourceID]",
      "KeyVaultURL": "[keyVaultURL]",
      "KeyVaultResourceId": "[keyVaultResourceID]",
      "SequenceVersion": "sequenceVersion]",
      "VolumeType": "[volumeType]"
    },
  "type": "AzureDiskEncryption",
  "typeHandlerVersion": "[extensionVersion]"
  }
}
```


### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0,1, 1,1 | int |
| (0.1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (0.1 séma) AADClientSecret | password | string |
| (0.1 séma) AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-szótár |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| választható Passphrase | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Operációs rendszer, az összes | string |

## <a name="template-deployment"></a>Sablonalapú telepítés
A sablonok központi telepítésének példáját lásd: [új titkosított Windows rendszerű virtuális gép létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)a katalógus rendszerképből.

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az utasítások az [Azure CLI](/cli/azure/vm/encryption?view=azure-cli-latest)legújabb dokumentációjában találhatók. 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Tekintse át a [Azure Disk Encryption hibaelhárítási útmutatót](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](features-windows.md).

---
title: Windows Azure Disk Encryption
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
ms.openlocfilehash: 8435663dcf92e2617ea2fe9218649e94243272d2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073208"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Windows Azure Disk Encryption (Microsoft. Azure. Security. AzureDiskEncryption)

## <a name="overview"></a>Áttekintés

Azure Disk Encryption a BitLockert a Windows rendszerű Azure-beli virtuális gépek teljes lemezes titkosításának biztosítására használja.  Ez a megoldás integrálva van Azure Key Vault a lemez titkosítási kulcsainak és a titkos kulcsoknak a Key Vault-előfizetésben való kezeléséhez. 

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek teljes listájáért lásd: [Azure Disk Encryption Linux rendszerű virtuális gépekhez](../linux/disk-encryption-overview.md), konkrétan a következő részekben:

- [Azure Disk Encryption Linux rendszerű virtuális gépekhez](../windows/disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](../windows/disk-encryption-overview.md#networking-requirements)
- [Csoportházirend követelmények](../windows/disk-encryption-overview.md#group-policy-requirements)

## <a name="extension-schemata"></a>Bővítmény sémák

A Windows AzureDiskEncryption bővítményhez két sémák tartozik: v 2.2, egy újabb, ajánlott séma, amely nem használja a Azure Active Directory (HRE) tulajdonságokat és a v 1.1-et, egy régebbi, HRE-tulajdonságokat igénylő sémát. Az Ön által használt bővítménynek megfelelő séma-verziót kell használnia: schema v 2.2 a AzureDiskEncryption bővítmény 2,2-es verziójához, a Schema v 1.1-es verzióját a AzureDiskEncryption Extension 1,1-es verziójában.

### <a name="schema-v22-no-aad-recommended"></a>Schema v 2.2: nincs HRE (ajánlott)

A v 2.2 séma minden új virtuális gép esetében ajánlott, és nem igényel Azure Active Directory tulajdonságokat.

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


### <a name="schema-v11-with-aad"></a>Schema v 1.1: a HRE 

Az 1,1 sémához `aadClientID` és `aadClientSecret` vagy `AADClientCertificate` szükséges, és az új virtuális gépek esetében nem ajánlott.

`aadClientSecret`használata:

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

`AADClientCertificate`használata:

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
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Azure.Security | sztring |
| type | AzureDiskEncryptionForLinux | sztring |
| typeHandlerVersion | 1,1, 2,2 | sztring |
| (1,1 séma) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID | 
| (1,1 séma) AADClientSecret | jelszó | sztring |
| (1,1 séma) AADClientCertificate | thumbprint | sztring |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-szótár |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sztring | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | sztring |
| KeyEncryptionKeyURL | url | sztring |
| KeyVaultURL | url | sztring |
| választható Jelszót | jelszó | sztring | 
| SequenceVersion | uniqueidentifier | sztring |
| VolumeType | Operációs rendszer, az összes | sztring |

## <a name="template-deployment"></a>Sablonalapú telepítés
A sablonok központi telepítésének példáját lásd: [új titkosított Windows rendszerű virtuális gép létrehozása a katalógus rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Az utasítások az [Azure CLI legújabb dokumentációjában](/cli/azure/vm/encryption?view=azure-cli-latest)találhatók. 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Tekintse át a [Azure Disk Encryption hibaelhárítási útmutatót](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](features-windows.md).

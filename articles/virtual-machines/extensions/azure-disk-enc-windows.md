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
ms.openlocfilehash: 00891122015bb3e6adb500b6f6c30fa031161b92
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597997"
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

Két sémák van a Azure Disk Encryption: v 1.1, egy újabb, ajánlott séma, amely nem használ Azure Active Directory (HRE) tulajdonságokat és v 0.1-et, amely egy régebbi, HRE-tulajdonságokat igénylő séma. Az Ön által használt bővítménynek megfelelő séma-verziót kell használnia: schema v 1.1 a AzureDiskEncryption Extension 1,1-es verziójához, a Schema v 0.1 a AzureDiskEncryption Extension 0,1-es verziójához.

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

Az 0,1 sémához `aadClientID` és `aadClientSecret` vagy `AADClientCertificate` szükséges.

@No__t_0 használata:

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

@No__t_0 használata:

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


### <a name="property-values"></a>Tulajdonságértékek

| Név | Érték/példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | dátum |
| Publisher | Microsoft. Azure. Security | sztring |
| type | AzureDiskEncryptionForLinux | sztring |
| typeHandlerVersion | 0,1, 1,1 | int |
| (0,1 séma) AADClientID | XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX | GUID | 
| (0,1 séma) AADClientSecret | jelszó | sztring |
| (0,1 séma) AADClientCertificate | ujjlenyomat | sztring |
| DiskFormatQuery | {"dev_path": "", "Name": "", "file_system": ""} | JSON-szótár |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sztring | 
| KeyEncryptionAlgorithm | "RSA-OAEP", "RSA-OAEP-256", "RSA1_5" | sztring |
| keyEncryptionKeyURL | url | sztring |
| keyVaultURL | url | sztring |
| választható Jelszót | jelszó | sztring | 
| sequenceVersion | uniqueidentifier | sztring |
| volumeType | Operációs rendszer, az összes | sztring |

## <a name="template-deployment"></a>Sablonalapú telepítés
A sablonok központi telepítésének példáját lásd: [új titkosított Windows rendszerű virtuális gép létrehozása a katalógus rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Azure CLI üzembe helyezése

Az utasítások az [Azure CLI legújabb dokumentációjában](/cli/azure/vm/encryption?view=azure-cli-latest)találhatók. 

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Tekintse át a [Azure Disk Encryption hibaelhárítási útmutatót](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Támogatás

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel az [MSDN Azure-ban, és stack overflow fórumokat](https://azure.microsoft.com/support/community/)is. Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Következő lépések
További információ a bővítményekről: [virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](features-windows.md).

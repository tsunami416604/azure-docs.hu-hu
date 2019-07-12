---
title: Az Azure Disk Encryption for Windows |} A Microsoft Docs
description: Az Azure Disk Encryption telepíti egy Windows virtuális gép, virtuálisgép-bővítmények használatával.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 9a3e135172f0744c053da816b3c77762dbe783c3
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706103"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Azure Disk Encryption for Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Áttekintés

Az Azure Disk Encryption kihasználja a BitLocker a teljes lemeztitkosítás adja meg a Windows rendszert futtató Azure virtuális gépeken.  Ez a megoldás integrálva van az Azure Key Vault kezeléséhez a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. 

## <a name="prerequisites"></a>Előfeltételek

Előfeltételek teljes listáját lásd: [Azure Disk Encryption titkosítási előfeltétel](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Operációs rendszer

Jelenleg a Windows-verziók listáját lásd: [Azure Disk Encryption titkosítási előfeltétel](../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="internet-connectivity"></a>Internetkapcsolat

Az Azure Disk Encryption internetkapcsolatra van szükség az Active Directory, a Key Vault, a Storage és a csomag felügyeleti végpontok való hozzáféréshez.  További információ a hálózati biztonsági beállítások,: [Azure Disk Encryption titkosítási előfeltétel](
../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schemata"></a>Bővítmény sémák szerializálása

Nincsenek az Azure Disk Encryption két sémák szerializálása: v1.1, egy újabb, ajánlott sémát, amely nem használ Azure Active Directory (AAD) tulajdonságait, és v0.1, egy régebbi séma, amely szükséges az AAD-tulajdonságok. Kell használnia a kiterjesztést használja a megfelelő verziójú sémába: a bővítmény verziója 1.1-es, a bővítmény verziója 0,1 AzureDiskEncryption a séma v0.1 AzureDiskEncryption a séma v1.1.

### <a name="schema-v11-no-aad-recommended"></a>Séma v1.1: Nincs aad-ben (ajánlott)

A v1.1 séma ajánlott, és nem igényel Azure Active Directory-tulajdonságok.

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


### <a name="schema-v01-with-aad"></a>Séma v0.1: az aad-vel 

Az 0.1-sémát igényel `aadClientID` és `aadClientSecret` vagy `AADClientCertificate`.

Használatával `aadClientSecret`:

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

Használatával `AADClientCertificate`:

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
| publisher | Microsoft.Azure.Security | sztring |
| type | AzureDiskEncryptionForLinux | Karakterlánc |
| typeHandlerVersion | 0.1, 1.1 | int |
| (0,1 Technologie ASP.NET) AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID azonosítója | 
| (0,1 Technologie ASP.NET) AADClientSecret | password | Karakterlánc |
| (0,1 Technologie ASP.NET) AADClientCertificate | thumbprint | Karakterlánc |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-szótárt |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | Karakterlánc | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | sztring |
| KeyEncryptionKeyURL | url | Karakterlánc |
| KeyVaultURL | url | sztring |
| (nem kötelező) Hozzáférési kód | password | sztring | 
| SequenceVersion | uniqueidentifier | Karakterlánc |
| VolumeType | Az operációs rendszer, az adatok, az összes | Karakterlánc |

## <a name="template-deployment"></a>Sablonalapú telepítés
Sablon telepítése egy példa: [ új titkosított Windows virtuális gép létrehozása katalógus-lemezkép](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image).

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Útmutató a legújabb [Azure CLI dokumentációját](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

Tekintse meg a [– hibaelhárítási útmutató az Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések
Bővítmények kapcsolatos további információkért lásd: [virtuálisgép-bővítmények és szolgáltatások Windows](features-windows.md).

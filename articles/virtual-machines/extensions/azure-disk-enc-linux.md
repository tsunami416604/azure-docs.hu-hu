---
title: Az Azure Disk Encryption for Linux |} A Microsoft Docs
description: Az Azure Disk Encryption egy virtuális gép, virtuálisgép-bővítmények használatával Linux rendszeren telepíti.
services: virtual-machines-linux
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: ejarvi
ms.openlocfilehash: 3ce881da4b683cf7034100d5044dd0f3c93edb52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800189"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Azure Disk Encryption for Linux (Microsoft.Azure.Security.AzureDiskEncryptionForLinux)

## <a name="overview"></a>Áttekintés

Az Azure Disk Encryption kihasználja a dm-crypt alrendszer Linux rendszeren, hogy biztosítsa a teljes lemeztitkosítás [válassza ki az Azure Linux-disztribúciók](https://aka.ms/adelinux).  Ez a megoldás integrálva van az Azure Key Vault lemeztitkosítási kulcsokat és titkos kulcsok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Előfeltételek teljes listáját lásd: [Azure Disk Encryption titkosítási előfeltétel](
../../security/azure-security-disk-encryption-prerequisites.md).

### <a name="operating-system"></a>Operációs rendszer

Az Azure Disk Encryption válassza disztribúciók és verziók a jelenleg támogatott.  Tekintse meg a [Azure Disk Encryption – gyakori kérdések](../../security/azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) által támogatott Linux-disztribúciók listáját.

### <a name="internet-connectivity"></a>Internetkapcsolat

Az Azure Disk Encryption for Linux internetkapcsolatra van szükség az Active Directory, a Key Vault, a Storage és a csomag felügyeleti végpontok való hozzáféréshez.  További információkért lásd: [Azure Disk Encryption titkosítási előfeltétel](../../security/azure-security-disk-encryption-prerequisites.md).

## <a name="extension-schema"></a>Bővítményséma

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

### <a name="property-values"></a>Tulajdonságok értékei

| Name (Név) | Érték és példa | Adattípus |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.Azure.Security | string |
| type | AzureDiskEncryptionForLinux | string |
| typeHandlerVersion | 0.1-ES FRISSÍTÉS, 1.1-ES (VMSS) | int |
| AADClientID | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID azonosítója | 
| AADClientSecret | password | string |
| AADClientCertificate | thumbprint | string |
| DiskFormatQuery | {"dev_path":"","name":"","file_system":""} | JSON-szótárt |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | string | 
| KeyEncryptionAlgorithm | 'RSA-OAEP', 'RSA-OAEP-256', 'RSA1_5' | string |
| KeyEncryptionKeyURL | url | string |
| KeyVaultURL | url | string |
| Hozzáférési kód | password | string | 
| SequenceVersion | uniqueidentifier | string |
| VolumeType | Az operációs rendszer, az adatok, az összes | string |

## <a name="template-deployment"></a>Sablonalapú telepítés

Sablon telepítése egy példa: [futó Linux rendszerű virtuális gépek titkosításának engedélyezése](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm).

## <a name="azure-cli-deployment"></a>Az Azure CLI-telepítés

Útmutató a legújabb [Azure CLI dokumentációját](/cli/azure/vm/encryption?view=azure-cli-latest). 

## <a name="troubleshoot-and-support"></a>Hibaelhárítás és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A hibaelhárításhoz, tekintse meg a [– hibaelhárítási útmutató az Azure Disk Encryption](../../security/azure-security-disk-encryption-tsg.md).

### <a name="support"></a>Támogatás

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a a [MSDN Azure-ban és a Stack Overflow-fórumok](https://azure.microsoft.com/support/community/). Másik lehetőségként a egy Azure-támogatási esemény is fájl. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , és válassza ki a Get-támogatást. Azure-támogatási használatával kapcsolatos információkért olvassa el a [Microsoft Azure-támogatás – gyakori kérdések](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>További lépések

Virtuálisgép-bővítményekkel kapcsolatos további információkért lásd: [virtuális gépi bővítmények és szolgáltatások Linux](features-linux.md).
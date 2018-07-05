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
ms.author: danis
ms.openlocfilehash: e7926fa488ad38784a3db96035055a80f841aed8
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449907"
---
# <a name="azure-disk-encryption-for-linux-microsoftazuresecurityazurediskencryptionforlinux"></a>Az Azure Disk Encryption (Microsoft.Azure.Security.AzureDiskEncryptionForLinux) Linux rendszeren

## <a name="overview"></a>Áttekintés

Az Azure Disk Encryption kihasználja a dm-crypt alrendszer Linux rendszeren, hogy biztosítsa a teljes lemeztitkosítás [válassza ki az Azure Linux-disztribúciók](https://aka.ms/adelinux).  Ez a megoldás integrálva van az Azure Key Vault lemeztitkosítási kulcsokat és titkos kulcsok kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Előfeltételek teljes listáját lásd: [Azure Disk Encryption titkosítási előfeltétel](
../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="operating-system"></a>Operációs rendszer

Az Azure Disk Encryption válassza disztribúciók és verziók a jelenleg támogatott.  Tekintse meg a [Azure Disk Encryption – gyakori kérdések](../../security/azure-security-disk-encryption-faq.md#what-linux-distributions-does-azure-disk-encryption-support) által támogatott Linux-disztribúciók listáját.

### <a name="internet-connectivity"></a>Internetkapcsolat

Az Azure Disk Encryption for Linux internetkapcsolatra van szükség az Active Directory, a Key Vault, a Storage és a csomag felügyeleti végpontok való hozzáféréshez.  További információkért lásd: [Azure Disk Encryption titkosítási előfeltétel](
../../security/azure-security-disk-encryption.md#prerequisites).

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
| apiVersion | 2015-06-15 | dátum |
| publisher | Microsoft.Azure.Security | sztring |
| type | AzureDiskEncryptionForLinux | sztring |
| typeHandlerVersion | 0.1-ES FRISSÍTÉS, 1.1-ES (VMSS) | int |
| AADClientID | : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID azonosítója | 
| AADClientSecret | jelszó | sztring |
| AADClientCertificate | ujjlenyomat | sztring |
| DiskFormatQuery | {"dev_path": "", "name": "","file_system": ""} | JSON-szótárt |
| EncryptionOperation | EnableEncryption, EnableEncryptionFormatAll | sztring | 
| KeyEncryptionAlgorithm | "AZ RSA-OAEP", 'RSA-OAEP – 256', "RSA1_5" | sztring |
| KeyEncryptionKeyURL | url | sztring |
| KeyVaultURL | url | sztring |
| Hozzáférési kód | jelszó | sztring | 
| SequenceVersion | UniqueIdentifier | sztring |
| VolumeType | Az operációs rendszer, az adatok, az összes | sztring |

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
---
title: Az Azure Disk Encryption for Windows |} A Microsoft Docs
description: Az Azure Disk Encryption telepíti egy Windows virtuális gép, virtuálisgép-bővítmények használatával.
services: virtual-machines-windows
documentationcenter: ''
author: ejarvi
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: danis
ms.openlocfilehash: f9b8c64aadf813ab1157820fc4051d48989e48c4
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449890"
---
# <a name="azure-disk-encryption-for-windows-microsoftazuresecurityazurediskencryption"></a>Az Azure Disk Encryption for Windows (Microsoft.Azure.Security.AzureDiskEncryption)

## <a name="overview"></a>Áttekintés

Az Azure Disk Encryption kihasználja a Bitlocker a teljes lemeztitkosítás adja meg a Windows rendszert futtató Azure virtuális gépeken.  Ez a megoldás integrálva van az Azure Key Vault kezeléséhez a lemeztitkosítási kulcsokat és titkos kulcsokat a key vault-előfizetés. 

## <a name="prerequisites"></a>Előfeltételek

Előfeltételek teljes listáját lásd: [Azure Disk Encryption titkosítási előfeltétel](
../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="operating-system"></a>Operációs rendszer

Jelenleg a Windows-verziók listáját lásd: [Azure Disk Encryption titkosítási előfeltétel](../../security/azure-security-disk-encryption.md#prerequisites).

### <a name="internet-connectivity"></a>Internetkapcsolat

Az Azure Disk Encryption internetkapcsolatra van szükség az Active Directory, a Key Vault, a Storage és a csomag felügyeleti végpontok való hozzáféréshez.  További információ a hálózati biztonsági beállítások,: [Azure Disk Encryption titkosítási előfeltétel](
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
    },
    "publisher": "Microsoft.Azure.Security",
    "settings": {
      "AADClientID": "[aadClientID]",
      "EncryptionOperation": "[encryptionOperation]",
      "KeyEncryptionAlgorithm": "[keyEncryptionAlgorithm]",
      "KeyEncryptionKeyURL": "[keyEncryptionKeyURL]",
      "KeyVaultURL": "[keyVaultURL]",
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
| type | AzureDiskEncryptionForWindows| sztring |
| typeHandlerVersion | 1.0-S, 2.2-ES (VMSS) | int |
| (nem kötelező) AADClientID | : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | GUID azonosítója | 
| (nem kötelező) AADClientSecret | jelszó | sztring |
| (nem kötelező) AADClientCertificate | ujjlenyomat | sztring |
| EncryptionOperation | EnableEncryption | sztring | 
| KeyEncryptionAlgorithm | AZ RSA-OAEP | sztring |
| KeyEncryptionKeyURL | url | sztring |
| KeyVaultURL | url | sztring |
| SequenceVersion | UniqueIdentifier | sztring |
| VolumeType | Az operációs rendszer, az adatok, az összes | sztring |

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
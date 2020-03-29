---
title: Az Azure Disk Encryption és az Azure virtuálisgép-méretezési készletek bővítmény-szekvenálást állít a
description: Ez a cikk a Microsoft Azure lemeztitkosítás linuxos IaaS virtuális gépekhez való engedélyezésével kapcsolatos utasításokat tartalmaz.
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.openlocfilehash: aa638b86b0788b8c274f9dcb3c04c1fc385b4ae1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279031"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Az Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének szekvenálásával

A bővítmények, például az Azure lemeztitkosítás hozzáadható egy Azure virtuális gépek méretezési készlet egy adott sorrendben. Ehhez használja [a bővítményszekvenálást](virtual-machine-scale-sets-extension-sequencing.md). 

Általában a titkosítást kell alkalmazni a lemezre:

- A lemezeket vagy köteteket előkészítő bővítmények vagy egyéni parancsfájlok után.
- A titkosított lemezeken vagy köteteken lévő adatokat elérő vagy használó bővítmények vagy egyéni parancsfájlok előtt.

Mindkét esetben a `provisionAfterExtensions` tulajdonság azt jelöli, hogy melyik kiterjesztést kell hozzáadni a sorozat későbbi részében.

## <a name="sample-azure-templates"></a>Minta Azure-sablonok

Ha azt szeretné, hogy az Azure Disk Encryption `provisionAfterExtensions` egy másik bővítmény után alkalmazva, helyezze a tulajdonságot az AzureDiskEncryption bővítmény blokkba. 

Íme egy példa a "CustomScriptExtension" használatával, amely egy Windows-lemezinicializáló és formázó Powershell-parancsfájl, amelyet az "AzureDiskEncryption" követ:

```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "CustomScriptExtension",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Compute",
          "type": "CustomScriptExtension",
          "typeHandlerVersion": "1.9",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "fileUris": [
              "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/ade-vmss/FormatMBRDisk.ps1"
            ]
          },
          "protectedSettings": {
           "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File FormatMBRDisk.ps1"
          }
        }
      },
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "name": "AzureDiskEncryption",
        "location": "[resourceGroup().location]",
        "properties": {
          "provisionAfterExtensions": [
            "CustomScriptExtension"
          ],
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
    ]
  }
}
```

Ha azt szeretné, hogy az Azure Disk Encryption `provisionAfterExtensions` egy másik bővítmény előtt alkalmazva, helyezze a tulajdonságot a bővítmény blokkjában, hogy kövesse.

Íme egy példa az "AzureDiskEncryption" használatával, amelyet a "VMDiagnosticsSettings" bővítmény követ, amely egy Windows-alapú Azure virtuális gép figyelési és diagnosztikai képességeit biztosítja:


```json
"virtualMachineProfile": {
  "extensionProfile": {
    "extensions": [
      {
        "name": "AzureDiskEncryption",
        "type": "Microsoft.Compute/virtualMachineScaleSets/extensions",
        "location": "[resourceGroup().location]",
        "properties": {
          "publisher": "Microsoft.Azure.Security",
          "type": "AzureDiskEncryption",
          "typeHandlerVersion": "2.2",
          "autoUpgradeMinorVersion": true,
          "forceUpdateTag": "[parameters('forceUpdateTag')]",
          "settings": {
            "EncryptionOperation": "EnableEncryption",
            "KeyVaultURL": "[reference(variables('keyVaultResourceId'),'2018-02-14-preview').vaultUri]",
            "KeyVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionKeyURL": "[parameters('keyEncryptionKeyURL')]",
            "KekVaultResourceId": "[variables('keyVaultResourceID')]",
            "KeyEncryptionAlgorithm": "[parameters('keyEncryptionAlgorithm')]",
            "VolumeType": "[parameters('volumeType')]",
            "SequenceVersion": "[parameters('sequenceVersion')]"
          }
        }
      },
      { 
        "name": "Microsoft.Insights.VMDiagnosticsSettings", 
        "type": "extensions", 
        "location": "[resourceGroup().location]", 
        "apiVersion": "2016-03-30", 
        "dependsOn": [ 
          "[concat('Microsoft.Compute/virtualMachines/myVM', copyindex())]" 
        ], 
        "properties": { 
          "provisionAfterExtensions": [
            "AzureDiskEncryption"
          ],
        "publisher": "Microsoft.Azure.Diagnostics", 
          "type": "IaaSDiagnostics", 
          "typeHandlerVersion": "1.5", 
          "autoUpgradeMinorVersion": true, 
          "settings": { 
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), 
            variables('wadmetricsresourceid'), 
            concat('myVM', copyindex()),
            variables('wadcfgxend')))]", 
            "storageAccount": "[variables('storageName')]" 
          }, 
          "protectedSettings": { 
            "storageAccountName": "[variables('storageName')]", 
            "storageAccountKey": "[listkeys(variables('accountid'), 
              '2015-06-15').key1]", 
            "storageAccountEndPoint": "https://core.windows.net" 
          } 
        } 
      },
    ]
  }
}
```

Részletesebb sablon:
* Alkalmazza az Azure Disk Encryption bővítményt egy egyéni rendszerhéj-parancsfájl után, amely formázza a lemezt (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)


## <a name="next-steps"></a>További lépések
- További információ a bővítmény-szekvenálásról: [Sorozatbővítmény kiépítése a virtuálisgép-méretezési készletekben.](virtual-machine-scale-sets-extension-sequencing.md)
- További információ `provisionAfterExtensions` a tulajdonságról: [Microsoft.Compute virtualMachineScaleSets/extensions template reference](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).
- [Azure lemeztitkosítás a virtuálisgép-méretezési készletekhez](disk-encryption-overview.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure PowerShell használatával](disk-encryption-powershell.md)
- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása](disk-encryption-key-vault.md)

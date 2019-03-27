---
title: Az Azure Disk Encryption és az Azure virtuálisgép-méretezési csoportok végrehajtási
description: Ez a cikk útmutatást nyújt a Microsoft Azure Disk Encryption engedélyezése a Linux rendszerű IaaS virtuális gépekhez.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/21/2019
ms.openlocfilehash: e98e501806971f3cf1bec29960ad15ef9c0024fc
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498145"
---
# <a name="use-azure-disk-encryption-with-virtual-machine-scale-set-extension-sequencing"></a>Használja az Azure Disk Encryption a virtuálisgép-méretezési csoport sorrendről beállítása

Bővítmények, mint például az Azure disk encryption egy Azure-beli virtuális gépek méretezési csoporthoz lehet hozzáadni egy megadott sorrendben állítsa be. Ehhez használja [sorrendről](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md). 

Általánosságban véve titkosítási lemezre lehet alkalmazni:

- Bővítmények vagy egyéni parancsfájlok készíti elő a lemezeket és köteteket.
- Mielőtt bővítmények vagy egyéni szkripteket, vagy azokat az adatokat a titkosított lemezek vagy kötetek használata.

Mindkét esetben a `provisionAfterExtensions` tulajdonság azt jelzi, hogy mely bővítményt később a sorrendben kell adni.

## <a name="sample-azure-templates"></a>Példa az Azure-sablonok

Ha azt szeretné, hogy az Azure Disk Encryption alkalmazásra, egy másik kiterjesztést, helyezze a `provisionAfterExtensions` tulajdonság a bővítmény AzureDiskEncryption blokkban. 

Íme egy példa használatával "CustomScriptExtension", egy Powershell-parancsprogram, inicializálja és formázza a Windows lemezt, a "AzureDiskEncryption" követ:

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

Ha azt szeretné, hogy az Azure Disk Encryption alkalmazása előtt egy másik bővítmény, helyezze a `provisionAfterExtensions` tulajdonság a bővítmény kövesse a blokkban.

Íme egy példa, egy Windows-alapú Azure virtuális gépen "VMDiagnosticsSettings", a bővítménye által biztosított figyelési és diagnosztikai funkciókkal kiegészítve "AzureDiskEncryption" használatával:


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

További részletes sablonokat lásd:
* Az Azure Disk Encryption bővítmény alkalmazása után egy egyéni héjparancsfájlt, amely formázza a lemezt (Linux): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-linux-ADE-after-customscript.json)
* Az Azure Disk Encryption bővítmény alkalmazása után egy egyéni Powershell-parancsprogram, inicializálja és formázza a lemezt (Windows): [deploy-extseq-linux-ADE-after-customscript.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-ADE-after-customscript.json)
* Az Azure Disk Encryption bővítményt, mielőtt egy egyéni Powershell-parancsprogram, inicializálja és formázza a lemezt (Windows) a alkalmazni: [deploy-extseq-windows-CustomScript-after-ADE.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/ade-vmss/deploy-extseq-windows-CustomScript-after-ADE.json)

## <a name="next-steps"></a>További lépések
- További információ a sorrendről: [A feladatütemezési bővítmény kiépítése folyamatban van a virtual machine scale sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md).
- Tudjon meg többet a `provisionAfterExtensions` tulajdonság: [Microsoft.Compute virtualMachineScaleSets/bővítmények sablonreferenciája](/azure/templates/microsoft.compute/2018-10-01/virtualmachinescalesets/extensions).

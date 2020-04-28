---
title: A Windowsos virtuális gépek bővítményével kapcsolatos hibák elhárítása
description: Tudnivalók az Azure-beli Windows virtuálisgép-bővítményekkel kapcsolatos hibák elhárításáról
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: bc99a9c9e9ff985730ec97dbacd1d7c1de06a45e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74073660"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure-beli Windows VM-bővítményekkel kapcsolatos hibák elhárítása
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>A bővítmény állapotának megtekintése
Azure Resource Manager-sablonok Azure PowerShellból is végrehajthatók. A sablon végrehajtása után a bővítmény állapota Azure Erőforrás-kezelőból vagy a parancssori eszközökből is megtekinthető.

Például:

Azure PowerShell:

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Itt látható a minta kimenete:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Bővítményekkel kapcsolatos hibák elhárítása
### <a name="rerun-the-extension-on-the-vm"></a>A bővítmény újrafuttatása a virtuális gépen
Ha a virtuális gépen parancsfájlokat futtat egyéni szkriptek használatával, időnként előfordulhat, hogy a virtuális gép sikeres létrehozásakor hiba történt, de a parancsfájl végrehajtása sikertelen volt. Ilyen körülmények között a hiba helyreállításának ajánlott módja a bővítmény eltávolítása és újbóli újrafuttatása.
Megjegyzés: a későbbiekben ez a funkció továbbra is kibővült, hogy eltávolítsa a bővítmény eltávolításának szükségességét.

#### <a name="remove-the-extension-from-azure-powershell"></a>Távolítsa el a bővítményt Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

A bővítmény eltávolítását követően a sablon újra végrehajtható a parancsfájlok futtatásához a virtuális gépen.


---
title: Windows virtuális gép bővítmény hibáinak elhárítása |} Microsoft Docs
description: További tudnivalók az Azure Windows virtuális gép bővítmény hibáinak elhárítása
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: 5c99f4a9209595fd58021fd8da9cd67f1e034748
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30914032"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows virtuális gép bővítmény hibáinak elhárítása
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Bővítmény állapotának megtekintése
Az Azure Resource Manager-sablonok az Azure PowerShell-lel hajtható végre. A sablon végrehajtása, amennyiben a bővítmény állapotát tekintheti meg az Azure erőforrás-kezelővel vagy a parancssori eszközöket.

Például:

Azure PowerShell:

      Get-AzureRmVM -ResourceGroupName $RGName -Name $vmName -Status

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

## <a name="troubleshooting-extension-failures"></a>Bővítmény hibáinak elhárítása
### <a name="re-running-the-extension-on-the-vm"></a>Ismételt futtatásával a bővítményt a virtuális Gépen
Ha futtatja parancsfájlok a virtuális Gépre egyéni parancsprogramok futtatására szolgáló bővítmény használatával, néha futtatható hiba történt, amikor a virtuális gép sikeresen létrejött, de a parancsfájl futtatása sikertelen volt. Ezek a feltételek alapján ajánlott módja a hiba helyreállításához, távolítsa el a bővítményt, és futtassa újra a sablont.
Megjegyzés: A jövőben ez a funkció javítani fogja a szükséges a bővítmény eltávolítása eltávolítja.

#### <a name="remove-the-extension-from-azure-powershell"></a>Távolítsa el a kiterjesztés Azure PowerShell
    Remove-AzureRmVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Ha a kiterjesztés eltávolításra került, a sablon lehet az Újrafuttatja a parancsfájlok futtatásához a virtuális Gépen.


---
title: A Windows virtuális gép bővítményhibáinak elhárítása
description: Tudnivalók az Azure Windows VM-bővítmények hibáinak elhárításáról
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073660"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Az Azure Windows VM-bővítmény hibáinak elhárítása
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Bővítmény állapotának megtekintése
Az Azure Resource Manager-sablonok az Azure PowerShellből hajthatók végre. A sablon végrehajtása után a bővítmény állapota megtekinthető az Azure Resource Explorer vagy a parancssori eszközök.

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

## <a name="troubleshooting-extension-failures"></a>A bővítményhibák elhárítása
### <a name="rerun-the-extension-on-the-vm"></a>Futtassa újra a bővítményt a virtuális gépen
Ha parancsfájlokat futtat a virtuális gép en egyéni parancsfájl-bővítmény használatával, néha előfordulhat, hogy egy hiba, ahol a virtuális gép sikeresen létrejött, de a parancsfájl nem sikerült. Ilyen körülmények között a hiba helyreállításának ajánlott módja a bővítmény eltávolítása és a sablon újbóli futtatása.
Megjegyzés: A jövőben ez a funkció tovább fejleszthető lenne, hogy megszüntesse a bővítmény eltávolításának szükségességét.

#### <a name="remove-the-extension-from-azure-powershell"></a>A bővítmény eltávolítása az Azure PowerShellből
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

A bővítmény eltávolítása után a sablon újra végrehajtható a parancsfájlok futtatásához a virtuális gépen.


---
title: Windows VM-bővítmény hibáinak elhárítása |} A Microsoft Docs
description: További tudnivalók az Azure Windows VM-bővítmény hibáinak elhárítása
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
ms.openlocfilehash: cf53df30dfccb76a6f33621038ba7f031a69f6de
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979693"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Azure Windows VM-bővítmény hibáinak elhárítása
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Bővítmény állapotának megtekintése
Az Azure Resource Manager-sablonok az Azure PowerShell-lel hajtható végre. A sablon hajtja végre, ha a bővítmény állapotát az Azure Resource Explorerben vagy a parancssori eszközök tekinthetők meg.

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

## <a name="troubleshooting-extension-failures"></a>Bővítmény hibáinak elhárítása
### <a name="rerun-the-extension-on-the-vm"></a>Futtassa újra a bővítményt a virtuális gépen
Ha futtat szkripteket a virtuális gép egyéni Szkriptbővítmény használatával, néha futtatható hiba történt, ahol a virtuális gép sikeresen létrejött, de a parancsfájl futtatása sikertelen volt. Ezen feltételek mellett az ajánlott módszer a helyreállítás a hiba, távolítsa el a bővítményt, és futtassa újra a sablont.
Megjegyzés: A jövőben ezt a funkciót szeretné továbbfejlesztett távolítsa el a kiterjesztés eltávolítása van szükség.

#### <a name="remove-the-extension-from-azure-powershell"></a>Távolítsa el a bővítményt az Azure PowerShell-lel
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

A bővítmény az Eltávolítás után, a sablon lehet Újrafuttatja a parancsfájlok futtatásához a virtuális gépen.


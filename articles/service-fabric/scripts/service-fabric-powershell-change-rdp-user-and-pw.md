---
title: "Az Azure PowerShell-parancsfájl minta - RDP-felhasználónevet és jelszót frissítése |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - RDP-felhasználónevet és jelszót a Service Fabric fürt összes csomópontján, egy adott csomópont típusú frissítése."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/17/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: c4a02bd06b8d0b3b99055760d345b5a824bbb946
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>A rendszergazda felhasználónevét és jelszavát, amely a virtuális gépeket a fürt frissítése

A Service Fabric-fürt minden csomópont típusa a virtuálisgép-méretezési készlet. Ez a parancsfájlpélda frissíti, a rendszergazda felhasználónevét és jelszavát a fürt virtuális gépek az adott csomópont típusát.  Adja hozzá a VMAccessAgent kiterjesztést a méretezési, mivel a rendszergazdai jelszó nem módosítható méretezési tulajdonság állítható be.  A felhasználónév vagy a jelszó módosítása a méretezési csoportban lévő összes csomópontra érvényes. Szabja testre a paramétereket szükség szerint.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Parancsfájl ismertetése

Ezt a parancsfájlt az alábbi parancsokat használja: minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Fürt csomópont típusú (egy virtuális gép méretezési) tulajdonságait olvassa be.   |
| [Adja hozzá AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension)| Egy bővítmény hozzáadása a virtuálisgép-méretezési készlet.|
| [Frissítés-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|Frissíti a virtuálisgép-méretezési helyi VMSS objektum állapotának beállítása az állapotát.|

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure Service Fabric további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../service-fabric-powershell-samples.md).

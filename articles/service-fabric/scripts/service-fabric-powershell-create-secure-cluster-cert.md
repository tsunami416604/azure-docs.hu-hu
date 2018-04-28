---
title: Azure PowerShell-példaszkript – Service Fabric-fürt létrehozása | Microsoft Docs
description: Azure PowerShell-példaszkript – Service Fabric-fürt létrehozása.
services: service-fabric
documentationcenter: ''
author: rwike77
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/19/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: ad3c51f0f43d63fd784156eca680218850897e8f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása

Ez a példaszkript egy öt csomópontos Service Fabric-tesztfürtöt hoz létre, amelyet egy X.509 tanúsítvány tesz biztonságossá.  A parancs létrehoz egy önaláírt tanúsítványt, és feltölti azt egy új kulcstartóba. A rendszer emellett a tanúsítványt egy helyi könyvtárba is átmásolja.  Állítsa be úgy az *-OS* paramétert, hogy a fürtcsomópontokon futó Windows vagy Linux verzióját válassza.  Szabja testre a paramétereket szükség szerint.

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzureRmAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a fürt és az összes kapcsolódó erőforrás.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Létrehoz egy új Service Fabric-fürtöt. |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

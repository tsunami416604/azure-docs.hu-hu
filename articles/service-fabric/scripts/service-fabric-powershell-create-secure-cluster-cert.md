---
title: "Az Azure PowerShell-parancsfájl minták – a Service Fabric-fürt létrehozása |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minták – a Service Fabric-fürt létrehozása."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0f9c8bc5-3789-4eb3-8deb-ae6e2200795a
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 09/29/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: dc0412b7595e9a84049223561c7695a86480fe4a
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="create-a-service-fabric-cluster"></a>A Service Fabric-fürt létrehozása

Ez a parancsfájlpélda létrehoz egy X.509 tanúsítvánnyal védett öt csomópontból Service Fabric-fürt.  A parancs létrehoz egy önaláírt tanúsítványt, és feltölti azt egy új kulcstartóba. A rendszer emellett a tanúsítványt egy helyi könyvtárba is átmásolja.  Állítsa be úgy az *-OS* paramétert, hogy a fürtcsomópontokon futó Windows vagy Linux verzióját válassza.  Szabja testre a paramétereket szükség szerint.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview) , majd futtassa `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral. 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-secure-cluster/create-secure-cluster.ps1 "Create a Service Fabric cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után a következő parancs segítségével távolítsa el az erőforráscsoport, a fürt és az összes kapcsolódó erőforrások.

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) | Egy új Service Fabric-fürtöt hoz létre. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure Service Fabric további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../service-fabric-powershell-samples.md).

---
title: "Az Azure PowerShell-parancsfájl minták – a Service Fabric-fürt létrehozása |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minták – hozzon létre egy három csomópontos teszt Service Fabric-fürt."
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
ms.date: 12/12/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 03348efa4ebdaed987df73756c6b57da0cc76fb5
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="create-a-three-node-test-service-fabric-cluster"></a>Egy három csomópontos teszt Service Fabric-fürt létrehozása

Ez a parancsfájlpélda létrehoz egy három csomópontos teszt Service Fabric-fürt által védett egy X.509 tanúsítvány. A három csomópont fürtkonfiguráció fejlesztési és tesztelési utasítás támogatott, mert a biztonságos frissítések végrehajtása és után is megmaradnak az egyes csomópontok hibáit, (feltéve, hogy nem fordulhat elő egyszerre). Éles fürt öt vagy több csomópont csak egyidejű hibák rugalmasak lehetnek.  

A parancs létrehoz egy önaláírt tanúsítványt, és feltölti azt egy új kulcstartó, amelynek ugyanabban az erőforráscsoportban, a fürt létrehozása. A rendszer emellett a tanúsítványt egy helyi könyvtárba is átmásolja.  Állítsa be úgy az *-OS* paramétert, hogy a fürtcsomópontokon futó Windows vagy Linux verzióját válassza.  Szabja testre a paramétereket szükség szerint.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview) , majd futtassa `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral. 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/create-test-cluster/create-test-cluster.ps1 "Create a test Service Fabric cluster")]

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

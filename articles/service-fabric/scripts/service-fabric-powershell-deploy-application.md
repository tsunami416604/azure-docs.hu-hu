---
title: Alkalmazás központi telepítése fürtre a PowerShellben
description: Azure PowerShell-parancsfájlminta – egy alkalmazás üzembe helyezése egy Service Fabric-fürtre.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 207f2a4e8173aa1e5009435665532973045d9198
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610301"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Alkalmazás üzembe helyezése egy Service Fabric-fürtön

Ez a mintaparancsfájl egy alkalmazáscsomagot másol egy fürtlemezkép-tárolóba, regisztrálja az alkalmazástípusát a fürtben, eltávolítja a felesleges alkalmazáscsomagot, és létrehoz egy alkalmazáspéldányt az alkalmazástípusból.  Ha a célalkalmazás típusának alkalmazásjegyzékében definiált alapértelmezett szolgáltatásokat, akkor ezek a szolgáltatások most jönnek létre. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájlminta futtatása után az [alkalmazás eltávolítása az alkalmazáspéldány](service-fabric-powershell-remove-application.md) eltávolításához, az alkalmazástípus regisztrációjának megszüntetéséhez és az alkalmazáscsomag nak a lemezképtárolóból való törléséhez használható.

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Kapcsolatot hoz létre egy Service Fabric-fürttel. |
|[Copy-ServiceFabricApplicationPackage csomag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Egy alkalmazáscsomagot másol a fürtlemezkép-tárolóba.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Regisztrál egy alkalmazástípust és -verziót a fürtön. |
|[Új-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Alkalmazás létrehozása regisztrált alkalmazástípusból. |
| [Remove-ServiceFabricApplicationPackage csomag](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Eltávolítja a Service Fabric alkalmazáscsomagot a lemezképtárból.|

## <a name="next-steps"></a>További lépések

A Service Fabric PowerShell-modulról az [Azure PowerShell dokumentációjában](/powershell/azure/service-fabric/?view=azureservicefabricps)olvashat bővebben.

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

---
title: Azure PowerShell-Példaszkript – üzembe az alkalmazást egy fürtön |} A Microsoft Docs
description: Azure PowerShell-Példaszkript – az alkalmazás egy Service Fabric-fürt üzembe.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: e205315530b0dc89037c1253c571c72c55f00a67
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661428"
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>Alkalmazás üzembe helyezése egy Service Fabric-fürtön

Ez a példaszkript egy alkalmazáscsomagot a fürt lemezképtárolójába másolja, regisztrálja az alkalmazás típusát a fürt, eltávolítja a felesleges alkalmazások csomagot és az alkalmazástípus alkalmazáspéldány hoz létre.  Ha bármely alapértelmezett szolgáltatások definiálva az alkalmazásjegyzékben, a célalkalmazás típusa, akkor ezeket a szolgáltatásokat most jönnek létre. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A példaszkript futtatása után, a parancsfájl [alkalmazás eltávolítása](service-fabric-powershell-remove-application.md) segítségével távolítsa el az alkalmazáspéldány, törölje az alkalmazástípus regisztrációját, és az alkalmazáscsomag törlése a képet tárból.

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Létrehoz egy kapcsolatot a Service Fabric-fürtön. |
|[Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Másolja egy alkalmazáscsomagot a fürt lemezképet tárolja.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Regisztrálja az alkalmazástípus és -verziót a fürtön. |
|[New-ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Létrehoz egy alkalmazást egy regisztrált alkalmazás írja be. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | A lemezképtároló távolít el egy Service Fabric-alkalmazáscsomagot.|

## <a name="next-steps"></a>További lépések

A Service Fabric PowerShell-modul további információkért lásd: [Azure PowerShell-dokumentáció](/powershell/azure/service-fabric/?view=azureservicefabricps).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

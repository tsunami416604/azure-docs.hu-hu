---
title: "Az Azure PowerShell-parancsfájl minta - fürtre alkalmazás központi telepítése |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - alkalmazás üzembe helyezése a Service Fabric-fürt."
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
ms.date: 09/29/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 159b856606885c4ee206ba42ec72a8bd1ec5b0f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-to-a-service-fabric-cluster"></a>A Service Fabric-fürt alkalmazás központi telepítése

Ez a parancsfájlpélda másolja egy alkalmazáscsomagot a fürt lemezképtárolóhoz, az alkalmazástípus regisztrálása a fürt, eltávolítja a szükségtelen alkalmazáscsomag és az alkalmazástípus létrehoz egy alkalmazáspéldányt.  Ha a célalkalmazás típusa, az alkalmazás jegyzékében meghatározott alapértelmezett szolgáltatások, akkor ezek a szolgáltatások jelenleg jönnek létre. Szabja testre a paramétereket szükség szerint. 

Szükség esetén telepítse a Service Fabric PowerShell-modulját és a [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/deploy-application/deploy-application.ps1 "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása 

A parancsfájl-minta futtatása után, a parancsfájl [alkalmazás eltávolítása](service-fabric-powershell-remove-application.md) az alkalmazáspéldány eltávolítása az alkalmazástípus regisztrációjának törlése és az alkalmazáscsomag törlése az image store használható.

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
|[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)| Kapcsolatot hoz létre a Service Fabric-fürt. |
|[Másolás-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Másolja az alkalmazáscsomagot a fürt lemezképet tárolja.  |
|[Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps)| Regisztrálja az alkalmazástípus és -verzió a fürtön. |
|[Új ServiceFabricApplication](/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps)| Alkalmazást hoz létre a regisztrált alkalmazáshoz típusból. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | A Service Fabric alkalmazáscsomag eltávolítása az image store.|

## <a name="next-steps"></a>Következő lépések

További információ a Service Fabric PowerShell-modul: [Azure PowerShell dokumentációs](/powershell/azure/service-fabric/?view=azureservicefabricps).

Azure Service Fabric további Powershell-példák találhatók a [Azure PowerShell-példák](../service-fabric-powershell-samples.md).

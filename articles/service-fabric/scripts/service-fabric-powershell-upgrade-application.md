---
title: "Az Azure PowerShell-parancsfájl minta - frissítés a Service Fabric-alkalmazás |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - frissítés a Service Fabric-alkalmazás."
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
ms.openlocfilehash: 8f6ab60861c422d083686a6ad5fb880c3e236f59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-service-fabric-application"></a>A Service Fabric-alkalmazás frissítése

Ez a parancsfájlpélda futó Service Fabric-alkalmazás példány 1.3.0 verzióra frissíti. A parancsfájl másolja az új alkalmazáscsomagot a fürt lemezképtárolóhoz, az alkalmazástípus regisztrálása, és eltávolítja a szükségtelen alkalmazáscsomagot.  A parancsfájl egy figyelt frissítés elindul, és folyamatosan ellenőrzi a frissítési állapot, amíg nem fejeződik a frissítés, vagy visszaállítja a. Szabja testre a paramétereket szükség szerint. 

Szükség esetén telepítse a Service Fabric PowerShell-modulját és a [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Lekérdezi a Service Fabric-fürt összes alkalmazást vagy egy adott alkalmazáshoz.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | A Service Fabric-alkalmazás frissítés állapotát olvassa be. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | A Service Fabric a Service Fabric-fürt regisztrált típusok beolvasása. |
| [ServiceFabricApplicationType regisztrációjának törlése](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | A Service Fabric alkalmazástípus regisztrációjának törlése.  |
| [Másolás-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | A Service Fabric alkalmazáscsomagok másolja az image store.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Regisztrálja a Service Fabric-alkalmazás típusa. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | A Service Fabric-alkalmazás a megadott alkalmazás típusa verzióra frissíti. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | A Service Fabric alkalmazáscsomag eltávolítása az image store.|


## <a name="next-steps"></a>Következő lépések

További információ a Service Fabric PowerShell-modul: [Azure PowerShell dokumentációs](/powershell/azure/service-fabric/?view=azureservicefabricps).

Azure Service Fabric további Powershell-példák találhatók a [Azure PowerShell-példák](../service-fabric-powershell-samples.md).

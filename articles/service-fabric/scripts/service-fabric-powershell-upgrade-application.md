---
title: Service Fabric-alkalmazás frissítése a Powershellben
description: Azure PowerShell-parancsfájlminta – Frissítsen és figyeljen egy Azure Service Fabric-alkalmazást a Powershell használatával.
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
ms.openlocfilehash: 3a4ef9fad8567eb145d51c6fef61773cc3a00b11
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614740"
---
# <a name="upgrade-a-service-fabric-application"></a>Service Fabric-alkalmazás frissítése

Ez a mintaparancsfájl egy futó Service Fabric-alkalmazáspéldányt frissít az 1.3.0-s verzióra. A parancsfájl átmásolja az új alkalmazáscsomagot a fürtlemezkép-tárolóba, regisztrálja az alkalmazás típusát, és eltávolítja a szükségtelen alkalmazáscsomagot.  A parancsfájl elindítja a figyelt frissítést, és folyamatosan ellenőrzi a frissítés állapotát, amíg a frissítés befejeződik, vagy vissza nem gördül. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Letöltője a Service Fabric-fürt ben vagy egy adott alkalmazásban lévő összes alkalmazás letöltődése.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | A Service Fabric-alkalmazás frissítésállapotának lekért. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Letöltője a Service Fabric-alkalmazástípusok regisztrálva a Service Fabric-fürtben. |
| [Regisztráció megszüntetése-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Törölje a Service Fabric alkalmazástípus regisztrációját.  |
| [Copy-ServiceFabricApplicationPackage csomag](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Egy Service Fabric-alkalmazáscsomagot másol a lemezképtárolóba.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Regisztrálja a Service Fabric alkalmazástípusát. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Egy Service Fabric-alkalmazást frissít a megadott alkalmazástípus-verzióra. |
| [Remove-ServiceFabricApplicationPackage csomag](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Eltávolítja a Service Fabric alkalmazáscsomagot a lemezképtárból.|


## <a name="next-steps"></a>További lépések

A Service Fabric PowerShell-modulról az [Azure PowerShell dokumentációjában](/powershell/azure/service-fabric/?view=azureservicefabricps)olvashat bővebben.

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

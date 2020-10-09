---
title: Service Fabric alkalmazás frissítése a PowerShellben
description: Azure PowerShell szkript minta – Azure Service Fabric-alkalmazás frissítése és figyelése a PowerShell használatával.
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
ms.openlocfilehash: 1b04a0c4d2865cf72d9fbccb51f0f083872ea799
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87037882"
---
# <a name="upgrade-a-service-fabric-application"></a>Service Fabric alkalmazás frissítése

Ez a minta parancsfájl egy futó Service Fabric alkalmazás-példányt frissít a 1.3.0 verzióra. A szkript átmásolja az új alkalmazáscsomag a fürt rendszerkép-tárolójába, regisztrálja az alkalmazás típusát, és eltávolítja a szükségtelen alkalmazáscsomag-csomagot.  A szkript elindít egy figyelt frissítést, és folyamatosan ellenőrzi a frissítési állapotot, amíg a frissítés be nem fejeződik vagy vissza nem áll. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | A Service Fabric-fürtben vagy egy adott alkalmazásban lévő összes alkalmazás beolvasása.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Lekéri egy Service Fabric alkalmazás frissítésének állapotát. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Beolvassa a Service Fabric-fürtön regisztrált Service Fabric alkalmazás-típusokat. |
| [Regisztráció törlése – ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric-alkalmazás típusának törlése.  |
| [Másolás – ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Service Fabric-alkalmazáscsomag másolása a rendszerkép-tárolóba.  |
| [Regisztráció – ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric alkalmazás típusának regisztrálása. |
| [Start – ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Egy Service Fabric alkalmazás frissítése a megadott Application Type-verzióra. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Eltávolít egy Service Fabric alkalmazáscsomag a rendszerkép-tárolóból.|


## <a name="next-steps"></a>További lépések

További információ a Service Fabric PowerShell-modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/service-fabric/overview?view=azureservicefabricps).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

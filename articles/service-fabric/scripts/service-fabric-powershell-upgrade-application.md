---
title: Azure PowerShell szkript minta – Service Fabric alkalmazás frissítése | Microsoft Docs
description: Azure PowerShell szkript minta – Service Fabric alkalmazás frissítése.
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
ms.openlocfilehash: 45186f497371b533451ff374e68b38f9a7eebe51
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035483"
---
# <a name="upgrade-a-service-fabric-application"></a>Service Fabric alkalmazás frissítése

Ez a minta parancsfájl egy futó Service Fabric alkalmazás-példányt frissít a 1.3.0 verzióra. A szkript átmásolja az új alkalmazáscsomag a fürt rendszerkép-tárolójába, regisztrálja az alkalmazás típusát, és eltávolítja a szükségtelen alkalmazáscsomag-csomagot.  A szkript elindít egy figyelt frissítést, és folyamatosan ellenőrzi a frissítési állapotot, amíg a frissítés be nem fejeződik vagy vissza nem áll. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | A Service Fabric-fürtben vagy egy adott alkalmazásban lévő összes alkalmazás beolvasása.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | Lekéri egy Service Fabric alkalmazás frissítésének állapotát. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Beolvassa a Service Fabric-fürtön regisztrált Service Fabric alkalmazás-típusokat. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric-alkalmazás típusának törlése.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | Service Fabric-alkalmazáscsomag másolása a rendszerkép-tárolóba.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Service Fabric alkalmazás típusának regisztrálása. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Egy Service Fabric alkalmazás frissítése a megadott Application Type-verzióra. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | Eltávolít egy Service Fabric alkalmazáscsomag a rendszerkép-tárolóból.|


## <a name="next-steps"></a>További lépések

További információ a Service Fabric PowerShell-modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/service-fabric/?view=azureservicefabricps).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

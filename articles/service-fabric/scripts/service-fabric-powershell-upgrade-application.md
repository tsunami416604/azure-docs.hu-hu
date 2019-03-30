---
title: Az Azure PowerShell-Példaszkript – Service Fabric-alkalmazás frissítése |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – Service Fabric-alkalmazás frissítése.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 8a291e49272c47035f73ad70534ac5393060940a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663553"
---
# <a name="upgrade-a-service-fabric-application"></a>Service Fabric-alkalmazás frissítése

Ez a példaszkript egy futó Service Fabric-alkalmazás példány 1.3.0 verzióra frissíti. A parancsfájl az új alkalmazáscsomagot a fürt lemezképtárolójába másolja, regisztrálja az alkalmazás típusát, és eltávolítja a felesleges alkalmazások csomagot.  A parancsfájl egy figyelt frissítés elindul, és folyamatosan ellenőrzi a frissítési állapot, mindaddig, amíg a frissítés befejeződik, vagy visszavonásához szükséges. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication?view=azureservicefabricps) | Lekérdezi a Service Fabric-fürtben szereplő összes alkalmazás vagy egy adott alkalmazást.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade?view=azureservicefabricps) | A Service Fabric-alkalmazás frissítése állapotát olvassa be. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype?view=azureservicefabricps) | Lekérdezi a Service Fabric alkalmazástípusok regisztrálva van a Service Fabric-fürtön. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Megszünteti a Service Fabric-alkalmazás típusát.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage?view=azureservicefabricps) | A lemezképtároló másol egy Service Fabric-alkalmazáscsomagot.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype?view=azureservicefabricps) | Regisztrálja a Service Fabric-alkalmazás típusát. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade?view=azureservicefabricps) | Frissíti egy Service Fabric-alkalmazást a megadott alkalmazástípus-verzió. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage?view=azureservicefabricps) | A lemezképtároló távolít el egy Service Fabric-alkalmazáscsomagot.|


## <a name="next-steps"></a>További lépések

A Service Fabric PowerShell-modul további információkért lásd: [Azure PowerShell-dokumentáció](/powershell/azure/service-fabric/?view=azureservicefabricps).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

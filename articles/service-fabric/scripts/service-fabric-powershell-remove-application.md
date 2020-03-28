---
title: Alkalmazás eltávolítása fürtből a Powershellben
description: Azure PowerShell-parancsfájlminta – alkalmazás eltávolítása egy Service Fabric-fürtből.
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
ms.openlocfilehash: 9ac4c23468c74b5a2c6874de2cb6b8d0e6b9e7dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75610284"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Alkalmazás eltávolítása egy Service Fabric-fürtből a PowerShell használatával

Ez a mintaparancsfájl töröl egy futó Service Fabric-alkalmazáspéldányt, és törli az alkalmazás típusát és verzióját a fürtből.  Az alkalmazáspéldány törlésével az adott alkalmazáshoz kapcsolódó összes éppen futó szolgáltatáspéldányt is törli. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Eltávolítja a futó Service Fabric alkalmazáspéldányt a fürtből.  |
| [Regisztráció megszüntetése-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | A Service Fabric-alkalmazás típusának és verziójának regisztrációja nem regisztrálása a fürtből. |

## <a name="next-steps"></a>További lépések

A Service Fabric PowerShell-modulról az [Azure PowerShell dokumentációjában](/powershell/azure/service-fabric/?view=azureservicefabricps)olvashat bővebben.

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

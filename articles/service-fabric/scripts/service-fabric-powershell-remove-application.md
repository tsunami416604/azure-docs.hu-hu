---
title: Alkalmazás eltávolítása egy fürtről a PowerShellben
description: Azure PowerShell parancsfájl-minta – alkalmazás eltávolítása egy Service Fabric-fürtből.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75610284"
---
# <a name="remove-an-application-from-a-service-fabric-cluster-using-powershell"></a>Alkalmazás eltávolítása Service Fabric-fürtről a PowerShell használatával

Ez a parancsfájl töröl egy futó Service Fabric alkalmazás-példányt, és törli a fürtből az alkalmazás típusának és verziójának regisztrációját.  Az alkalmazáspéldány törlésével az adott alkalmazáshoz kapcsolódó összes éppen futó szolgáltatáspéldányt is törli. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Egy futó Service Fabric alkalmazás példányának eltávolítása a fürtből.  |
| [Regisztráció törlése – ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Egy Service Fabric alkalmazás típusának és verziójának a fürtből való regisztrációjának törlése. |

## <a name="next-steps"></a>További lépések

További információ a Service Fabric PowerShell-modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/service-fabric/?view=azureservicefabricps).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

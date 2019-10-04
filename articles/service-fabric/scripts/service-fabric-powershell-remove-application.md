---
title: Azure PowerShell parancsfájl-minta – alkalmazás eltávolítása egy fürtből | Microsoft Docs
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
ms.openlocfilehash: f572996b4e460480335dbe32c4599bde206251d4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69035508"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Alkalmazás eltávolítása egy Service Fabric fürtről

Ez a parancsfájl töröl egy futó Service Fabric alkalmazás-példányt, és törli a fürtből az alkalmazás típusának és verziójának regisztrációját.  Az alkalmazáspéldány törlésével az adott alkalmazáshoz kapcsolódó összes éppen futó szolgáltatáspéldányt is törli. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Egy futó Service Fabric alkalmazás példányának eltávolítása a fürtből.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Egy Service Fabric alkalmazás típusának és verziójának a fürtből való regisztrációjának törlése. |

## <a name="next-steps"></a>További lépések

További információ a Service Fabric PowerShell-modullal kapcsolatban: [Azure PowerShell dokumentáció](/powershell/azure/service-fabric/?view=azureservicefabricps).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

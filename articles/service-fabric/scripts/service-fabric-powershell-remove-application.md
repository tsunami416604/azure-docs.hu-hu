---
title: Az Azure PowerShell-Példaszkript – egy fürt alkalmazás eltávolítása |} A Microsoft Docs
description: Az Azure PowerShell-Példaszkript – Service Fabric-fürtön az alkalmazás eltávolítása.
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
ms.openlocfilehash: 05edc6bce6744acd14dea2358663c4097922f2ce
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667463"
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Alkalmazás eltávolítása egy Service Fabric fürtről

Ez a példaszkript egy futó Service Fabric-alkalmazás példány törli, és megszünteti az alkalmazás típusát és a fürt verzióját.  Az alkalmazáspéldány törlésével az adott alkalmazáshoz kapcsolódó összes éppen futó szolgáltatáspéldányt is törli. Szabja testre a paramétereket szükség szerint. 

Ha szükséges, telepítse a Service Fabric PowerShell-modult a [Service Fabric SDK](../service-fabric-get-started.md)-val. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Egy futó Service Fabric-alkalmazás példány eltávolítja a fürtről.  |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Megszünteti egy Service Fabric-alkalmazás típusát és a fürt verzióját. |

## <a name="next-steps"></a>További lépések

A Service Fabric PowerShell-modul további információkért lásd: [Azure PowerShell-dokumentáció](/powershell/azure/service-fabric/?view=azureservicefabricps).

További Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

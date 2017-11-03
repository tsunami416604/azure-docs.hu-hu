---
title: "Az Azure PowerShell-parancsfájl minta - fürtök törlése alkalmazás |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minta - alkalmazás a Service Fabric-fürt eltávolítása."
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
ms.openlocfilehash: 5f65d7ea40be56280ba41255d1a005506bd2810a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="remove-an-application-from-a-service-fabric-cluster"></a>Alkalmazások eltávolítása a Service Fabric-fürt

Ez a parancsfájlpélda futó Service Fabric-alkalmazás példány törlése, és megszünteti az alkalmazástípus és -verzió a fürtből.  Az alkalmazáspéldány törlésekor is törlődnek a futó szolgáltatás az adott alkalmazáshoz tartozó példányok. Szabja testre a paramétereket szükség szerint. 

Szükség esetén telepítse a Service Fabric PowerShell-modulját és a [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/remove-application/remove-application.ps1 "Remove an application from a cluster")]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Remove-ServiceFabricApplication](/powershell/module/servicefabric/remove-servicefabricapplication?view=azureservicefabricps) | Egy futó Service Fabric-alkalmazás példány eltávolítja a fürtből.  |
| [ServiceFabricApplicationType regisztrációjának törlése](/powershell/module/servicefabric/unregister-servicefabricapplicationtype?view=azureservicefabricps) | Megszünteti a Service Fabric alkalmazástípus és -verzió a fürtből. |

## <a name="next-steps"></a>Következő lépések

További információ a Service Fabric PowerShell-modul: [Azure PowerShell dokumentációs](/powershell/azure/service-fabric/?view=azureservicefabricps).

Azure Service Fabric további Powershell-példák találhatók a [Azure PowerShell-példák](../service-fabric-powershell-samples.md).

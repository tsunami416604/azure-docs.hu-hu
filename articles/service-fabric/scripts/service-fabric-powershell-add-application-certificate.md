---
title: "Az Azure PowerShell-parancsfájl minta - alkalmazás tanúsítvány hozzáadása a fürthöz |} Microsoft Docs"
description: "Az Azure PowerShell-parancsfájl minták – az alkalmazás tanúsítványának felvétele a Service Fabric-fürt."
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
ms.date: 01/18/2018
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: c9cf6485c2621f839b93da162e5f4d82a8d287a4
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Az alkalmazás tanúsítványának felvétele a Service Fabric-fürt

Ez a parancsfájlpélda létrehoz egy önaláírt tanúsítványt a megadott Azure-tárolóban, és a Service Fabric-fürt összes csomópontjára telepíti azt. A tanúsítvány is letölti egy helyi mappába. A letöltött tanúsítvány neve megegyezik a key vault a tanúsítvány neve. Szabja testre a paramétereket szükség szerint.

Szükség esetén telepítse az Azure PowerShell található utasítás használatával a [Azure PowerShell útmutató](/powershell/azure/overview) , majd futtassa `Login-AzureRmAccount` kapcsolat létrehozása az Azure-ral. 

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Parancsfájl ismertetése

Ezt a parancsfájlt az alábbi parancsokat használja: minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Add-AzureRmServiceFabricApplicationCertificate](/powershell/module/azurerm.servicefabric/Add-AzureRmServiceFabricApplicationCertificate) | Új alkalmazás tanúsítvány hozzáadása a virtuálisgép-méretezési csoport a fürtöt alkotó.  |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modul további információkért lásd: [Azure PowerShell dokumentációs](/powershell/azure/overview).

Azure Service Fabric további Azure Powershell-példák találhatók a [Azure PowerShell-példák](../service-fabric-powershell-samples.md).

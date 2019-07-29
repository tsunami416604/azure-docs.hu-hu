---
title: Azure PowerShell-példaszkript – Alkalmazástanúsítvány hozzáadása egy fürthöz | Microsoft Docs
description: Azure PowerShell-példaszkript – Alkalmazástanúsítvány hozzáadása egy Service Fabric-fürthöz.
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
ms.openlocfilehash: 1f2bd57a799bb00e9328721a08eb93665650f7f4
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592306"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Alkalmazástanúsítvány hozzáadása egy Service Fabric-fürthöz

Ez a példaszkript létrehoz egy önaláírt tanúsítványt a megadott Azure Key Vaultban, és telepíti a Service Fabric-fürt minden csomópontján. A tanúsítvány emellett egy helyi mappába is letöltődik. A letöltött tanúsítvány neve megegyezik a kulcstartóban található tanúsítvány nevével. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/overview) utasításait követve, majd a `Connect-AzAccount` futtatásával hozza létre a kapcsolatot az Azure-ral. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-application-certificate/add-new-application-certificate.ps1 "Add an application certificate to a cluster")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Add-AzServiceFabricApplicationCertificate](/powershell/module/az.servicefabric/Add-azServiceFabricApplicationCertificate) | Adjon hozzá új alkalmazástanúsítványt a fürtöt alkotó virtuálisgép-méretezési csoporthoz.  |

## <a name="next-steps"></a>További lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.

---
title: Azure PowerShell-példaszkript – Alkalmazástanúsítvány hozzáadása egy fürthöz | Microsoft Docs
description: Azure PowerShell-példaszkript – Alkalmazástanúsítvány hozzáadása egy Service Fabric-fürthöz.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 01/18/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: d5e1540a347bb14e7479bc445afa98e220b5475a
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497061"
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

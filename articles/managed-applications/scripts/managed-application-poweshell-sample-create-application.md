---
title: Az Azure PowerShell-parancsfájl minták – kezelt alkalmazás telepítéséhez |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl minták – a felügyelt alkalmazási definíció telepítése
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 2429d561beffed5bc171b9dbc2c2c9c88eba3313
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
ms.locfileid: "23941249"
---
# <a name="deploy-a-managed-application-for-a-service-catalog-with-powershell"></a>A szolgáltatáskatalógus a PowerShell segítségével a kezelt alkalmazás központi telepítése

Ezt a parancsfájlt a kezelt alkalmazás-definíció a szolgáltatáskatalógus telepíti.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-application/create-application.ps1 "Create application")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsot a kezelt alkalmazás központi telepítése. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új AzureRmManagedApplication](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplication) | Hozzon létre egy felügyelt alkalmazást. Adja meg a sablon a definíció azonosítója és a paraméterek. |


## <a name="next-steps"></a>Következő lépések

* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](../overview.md).
* A PowerShell további információkért lásd: [Azure PowerShell dokumentációs](https://docs.microsoft.com/powershell/azure/get-started-azureps).

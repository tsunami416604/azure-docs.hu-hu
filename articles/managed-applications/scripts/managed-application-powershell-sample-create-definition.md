---
title: Az Azure PowerShell-parancsfájl minták – a kezelt alkalmazás-definíció létrehozása |} Microsoft Docs
description: Az Azure PowerShell-parancsfájl minták – a kezelt alkalmazás-definíció létrehozása
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
ms.openlocfilehash: ee721b7c68924f5e05fbdc4bc4b919837d4cc05b
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
ms.locfileid: "23940955"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>A kezelt alkalmazás-definíció létrehozása a PowerShell használatával

Ezt a parancsfájlt a szolgáltatáskatalógus teszi közzé a kezelt alkalmazás definícióját.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsot a kezelt alkalmazás-definíció létrehozása. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [Új AzureRmManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermmanagedapplicationdefinition) | A kezelt alkalmazás-definíció létrehozása. Adja meg a szükséges fájlokat tartalmazó csomagot. |


## <a name="next-steps"></a>Következő lépések

* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](../overview.md).
* A PowerShell további információkért lásd: [Azure PowerShell dokumentációs](https://docs.microsoft.com/powershell/azure/get-started-azureps).

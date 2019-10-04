---
title: Azure PowerShell-példaszkript – felügyelt alkalmazás definíciójának létrehozása |} A Microsoft Docs
description: Azure PowerShell-példaszkript – felügyelt alkalmazás definíciójának létrehozása
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
ms.openlocfilehash: 35f1b805323da03f1622e5c355d6ffa6d960b275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171510"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Felügyelt alkalmazás definíciójának létrehozása a PowerShell használatával

Ez a szkript közzéteszi egy felügyelt alkalmazás definícióját egy szolgáltatáskatalógusban.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja a felügyelt alkalmazás definíciójának létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplicationdefinition) | Létrehozza egy felügyelt alkalmazás definícióját. Megadja a szükséges fájlokat tartalmazó csomagot. |


## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).

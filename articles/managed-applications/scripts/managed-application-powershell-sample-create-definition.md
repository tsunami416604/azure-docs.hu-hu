---
title: Azure PowerShell parancsfájl-minta – felügyelt alkalmazás definíciójának létrehozása | Microsoft Docs
description: Egy Azure PowerShell parancsfájlt tartalmaz, amely létrehoz egy felügyelt alkalmazás definícióját az Azure-előfizetésben.
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
ms.openlocfilehash: f0491a5025886317faf7cb4601f8db3915527b80
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332584"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Felügyelt alkalmazás definíciójának létrehozása a PowerShell-lel

Ez a szkript közzéteszi egy felügyelt alkalmazás definícióját egy szolgáltatáskatalógusban.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja a felügyelt alkalmazás definíciójának létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Új – AzManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplicationdefinition) | Létrehozza egy felügyelt alkalmazás definícióját. Megadja a szükséges fájlokat tartalmazó csomagot. |


## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* A PowerShell-lel kapcsolatos további tudnivalókért tekintse meg az [Azure PowerShell dokumentációját](https://docs.microsoft.com/powershell/azure/get-started-azureps).

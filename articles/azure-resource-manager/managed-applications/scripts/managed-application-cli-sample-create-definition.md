---
title: Felügyelt alkalmazás definíciójának létrehozása – Azure CLI
description: Egy Azure CLI parancsfájl-mintát biztosít, amely létrehoz egy felügyelt alkalmazás definícióját az előfizetésben.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: f4d5a0036ba44f7e0054db7ce820b91b0de629b8
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650190"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Felügyelt alkalmazás definíciójának létrehozása az Azure CLI használatával

Ez a szkript közzéteszi egy felügyelt alkalmazás definícióját egy szolgáltatáskatalógusban. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsot használja a felügyelt alkalmazás definíciójának létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az managedapp definition create](https://docs.microsoft.com/cli/azure/managedapp/definition#az-managedapp-definition-create) | Létrehozza egy felügyelt alkalmazás definícióját. Megadja a szükséges fájlokat tartalmazó csomagot. |


## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

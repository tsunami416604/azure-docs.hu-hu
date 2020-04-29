---
title: Azure CLI parancsfájl-minta – felügyelt alkalmazás üzembe helyezése
description: Olyan Azure CLI-parancsfájlt biztosít, amely egy Azure által felügyelt alkalmazás-definíciót telepít az előfizetésre.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 346ea59209bc2f74970e708c947f5caa158a0338
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650064"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Felügyelt alkalmazás üzembe helyezése a szolgáltatáskatalógusból az Azure CLI használatával

Ez a szkript üzembe helyezi egy felügyelt alkalmazás definícióját a szolgáltatáskatalógusból. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsot használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | Létrehoz egy felügyelt alkalmazást. Megadja a sablonhoz szükséges definícióazonosítót és paramétereket. |


## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

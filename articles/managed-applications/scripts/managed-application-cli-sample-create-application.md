---
title: Azure CLI-példaszkript – Felügyelt alkalmazás üzembe helyezése | Microsoft Docs
description: Azure CLI-példaszkript – Felügyelt alkalmazás definíciójának üzembe helyezése
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: 9939bfb08031b3062fd65fbdeed908a09e5e124c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39432225"
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>Felügyelt alkalmazás üzembe helyezése a szolgáltatáskatalógusból az Azure CLI használatával

Ez a szkript üzembe helyezi egy felügyelt alkalmazás definícióját a szolgáltatáskatalógusból. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsot használja egy felügyelt alkalmazás üzembe helyezéséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az managedapp create](https://docs.microsoft.com/cli/azure/managedapp#az-managedapp-create) | Létrehoz egy felügyelt alkalmazást. Megadja a sablonhoz szükséges definícióazonosítót és paramétereket. |


## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazásokra vonatkozó részleteket az [Azure felügyelt alkalmazásokat áttekintő](../overview.md) cikk ismerteti.
* Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

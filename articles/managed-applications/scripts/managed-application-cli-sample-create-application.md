---
title: "Az Azure CLI-parancsfájlt minták – kezelt alkalmazás telepítéséhez |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minták – a felügyelt alkalmazási definíció telepítése"
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
ms.openlocfilehash: 62d0247df3b3d9f242877e4ea27ccc871cf797c0
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2017
---
# <a name="deploy-a-managed-application-for-service-catalog-with-azure-cli"></a>A szolgáltatáskatalógus Azure parancssori felület segítségével kezelt alkalmazás központi telepítése

Ezt a parancsfájlt a kezelt alkalmazás-definíció a szolgáltatáskatalógus telepíti. 


[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/managed-applications/create-application/create-application.sh "Create application")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsot a kezelt alkalmazás központi telepítése. Minden egyes parancsa a tábla-parancs-specifikus dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az managedapp létrehozása](https://docs.microsoft.com/cli/azure/managedapp#az_managedapp_create) | Hozzon létre egy felügyelt alkalmazást. Adja meg a sablon a definíció azonosítója és a paraméterek. |


## <a name="next-steps"></a>Következő lépések

* Felügyelt alkalmazások bemutatása, lásd: [Azure kezelt alkalmazás – áttekintés](../overview.md).
* További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

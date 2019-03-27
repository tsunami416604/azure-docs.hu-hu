---
title: Azure CLI telepítési példaszkriptje
description: Biztonságos Service Fabric-fürt létrehozása az Azure-ban az Azure CLI használatával.
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
ms.openlocfilehash: be93436a7cff8a5486db7d4c6c4dc2b0238ece9e
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497295"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Biztonságos Service Fabric-fürt létrehozása az Azure-ban

Ez a parancs létrehoz egy önaláírt tanúsítványt, hozzáadja egy kulcstartóhoz, és helyileg letölti a tanúsítványt.  A rendszer az új tanúsítványt a fürt védelmére használja a telepítést követően.  Meglévő tanúsítványt is használhat egy új létrehozása helyett.  A tanúsítvány tulajdonosnevének mindkét esetben egyeznie kell a Service Fabric-fürthöz való hozzáféréshez használt tartománnyal. Erre a megfeleltetésre azért van szükség, hogy a rendszer SSL-kapcsolatot biztosíthasson a fürt HTTPS-felügyeleti végpontjai és a Service Fabric Explorer számára. Nem szerezhető be SSL-tanúsítvány hitelesítésszolgáltatótól (CA) a `.cloudapp.azure.com` tartomány számára. Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

Amennyiben szükséges, telepítse az [Azure CLI-t](/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sample-script"></a>Példaszkript

[!code-sh[main](../../../cli_scripts/service-fabric/create-cluster/create-cluster.sh "Deploy an application to a cluster")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport, a fürt és az összes kapcsolódó erőforrás.

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az sf cluster create](https://docs.microsoft.com/cli/azure/sf/cluster?view=azure-cli-latest) | Létrehoz egy új Service Fabric-fürtöt.  |

## <a name="next-steps"></a>További lépések

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.

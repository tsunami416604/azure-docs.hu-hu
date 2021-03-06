---
title: Azure CLI telepítési példaszkriptje
description: Biztonságos Service Fabric Linux-fürt létrehozása az Azure-ban az Azure parancssori felület (CLI) használatával.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a0a17e3f352c9e39f118baec7e045521289f5ed0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87502409"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Biztonságos Service Fabric-fürt létrehozása az Azure-ban

Ez a parancs létrehoz egy önaláírt tanúsítványt, hozzáadja egy kulcstartóhoz, és helyileg letölti a tanúsítványt.  A rendszer az új tanúsítványt a fürt védelmére használja a telepítést követően.  Meglévő tanúsítványt is használhat egy új létrehozása helyett.  A tanúsítvány tulajdonosnevének mindkét esetben egyeznie kell a Service Fabric-fürthöz való hozzáféréshez használt tartománnyal. Ez a megfeleltetés szükséges ahhoz, hogy a TLS-t biztosítani lehessen a fürt HTTPS-felügyeleti végpontjai és Service Fabric Explorer számára. Nem szerezhet be TLS/SSL-tanúsítványt a `.cloudapp.azure.com` tartomány hitelesítésszolgáltatótól. Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

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

| Parancs | Jegyzetek |
|---|---|
| [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest) | Létrehoz egy új Service Fabric-fürtöt.  |

## <a name="next-steps"></a>További lépések

A [Service Fabric parancssori felület példái](../samples-cli.md) között további Service Fabric parancssori felületi példákat talál az Azure Service Fabrichez.

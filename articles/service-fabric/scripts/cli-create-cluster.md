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
ms.custom: mvc
ms.openlocfilehash: 2ef8f322ff17eeb5d75d3cc8e4f8604f02d4ef0e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366543"
---
# <a name="create-a-secure-service-fabric-linux-cluster-in-azure"></a>Biztonságos Service Fabric-fürt létrehozása az Azure-ban

Ez a parancs létrehoz egy önaláírt tanúsítványt, hozzáadja egy kulcstartóhoz, és helyileg letölti a tanúsítványt.  A rendszer az új tanúsítványt a fürt védelmére használja a telepítést követően.  Meglévő tanúsítványt is használhat egy új létrehozása helyett.  A tanúsítvány tulajdonosnevének mindkét esetben egyeznie kell a Service Fabric-fürthöz való hozzáféréshez használt tartománnyal. Ez az egyezés szükséges a Fürt HTTPS-kezelési végpontjaihoz és a Service Fabric Explorer t ad meg. A `.cloudapp.azure.com` tartomány hitelesítésszolgáltatójától nem szerezhet be TLS/SSL-tanúsítványt. Egyéni tartománynevet kell beszereznie a fürt számára. Amikor tanúsítványt igényel egy hitelesítésszolgáltatótól, a tanúsítvány tulajdonosnevének meg kell felelnie a fürthöz használt egyéni tartománynévnek.

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

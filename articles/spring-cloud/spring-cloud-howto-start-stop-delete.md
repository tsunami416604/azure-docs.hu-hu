---
title: Az Azure Spring Cloud-alkalmazás indítása, leállítása és törlése | Microsoft dokumentumok
description: Az Azure Spring Cloud-alkalmazás indítása, leállítása és törlése
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276835"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Az Azure Spring Cloud-alkalmazás indítása, leállítása és törlése

Ez az útmutató bemutatja, hogyan módosíthatja egy alkalmazás állapotát az Azure Spring Cloud ban az Azure Portal vagy az Azure CLI használatával.

## <a name="using-the-azure-portal"></a>Az Azure Portal használata

Egy alkalmazás üzembe helyezése után elindíthatja, leállíthatja és törölheti az Azure Portal használatával.

1. Nyissa meg az Azure Spring Cloud szolgáltatáspéldányát az Azure Portalon.
1. Válassza az **Alkalmazás irányítópultja** lapot.
1. Jelölje ki azt az alkalmazást, amelynek állapotát módosítani szeretné.
1. Az **Alkalmazás Áttekintés lapján** válassza a **Start/Stop**, **Restart**vagy **Delete**lehetőséget.

## <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

> [!NOTE]
> Használhatja a választható paramétereket, és konfigurálja az alapértelmezett az Azure CLI. Tudjon meg többet az Azure CLI-ről a [dokumentáció ban.](spring-cloud-cli-reference.md)  

Először telepítse az Azure Spring Cloud bővítményt az Azure CLI-hez az alábbiak szerint:

```azurecli
az extension add --name spring-cloud
```

Ezután válassza ki az Alábbi Azure CLI-műveletek egyikét:

* Az alkalmazás elindításához:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás leállítása:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás újraindítása:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás törlése:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

---
title: Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése | Microsoft Docs
description: Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: brendm
ms.openlocfilehash: daa549e248668add54530e90174134c4e0059b3a
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276835"
---
# <a name="start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése

Ez az útmutató azt ismerteti, hogyan lehet módosítani az alkalmazások állapotát az Azure Spring Cloud-ban a Azure Portal vagy az Azure CLI használatával.

## <a name="using-the-azure-portal"></a>Az Azure Portal használata

Az alkalmazások központi telepítése után elindíthatja, leállíthatja és törölheti a Azure Portal használatával.

1. Nyissa meg az Azure Spring Cloud Service-példányt a Azure Portal.
1. Válassza az **alkalmazás-irányítópult** fület.
1. Válassza ki azt az alkalmazást, amelynek az állapotát módosítani szeretné.
1. Az alkalmazás **Áttekintés** lapján válassza a **Start/leállítás**, **Újraindítás**vagy **Törlés**lehetőséget.

## <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

> [!NOTE]
> Az Azure CLI-vel választható paramétereket és az alapértelmezett értékeket is konfigurálhatja. További információt az Azure CLI-ről a [dokumentációban talál](spring-cloud-cli-reference.md).  

Először telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi módon:

```azurecli
az extension add --name spring-cloud
```

Ezután válassza ki az alábbi Azure CLI-műveletek bármelyikét:

* Az alkalmazás elindítása:

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

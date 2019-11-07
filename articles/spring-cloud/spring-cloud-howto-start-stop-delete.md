---
title: Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése | Microsoft Docs
description: Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 9f537ab425428728137e04713e434d8dc09e065a
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607730"
---
# <a name="how-to-start-stop-and-delete-your-azure-spring-cloud-application"></a>Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése

Ez az útmutató bemutatja, hogyan módosíthatja egy alkalmazás állapotát az Azure Spring Cloud-ban a Azure Portal vagy a parancssori felület használatával.

## <a name="using-the-azure-portal"></a>Az Azure Portal használata

Miután telepítette az alkalmazást, **elindíthatja**, **leállíthatja**és **törölheti** a Azure Portal használatával.

1. Nyissa meg az Azure Spring Cloud Service-példányt a Azure Portal.
1. Válassza az **alkalmazás-irányítópult** fület.
1. Válassza ki azt az alkalmazást, amelynek az állapotát módosítani szeretné.
2. Az alkalmazás **Áttekintés** lapján keresse meg a gombokat az alkalmazás **elindításához/leállításához**, **újraindításához**és **törléséhez** .

## <a name="using-the-azure-cli"></a>Az Azure parancssori felületének használata

> [!NOTE]
> Az Azure CLI-vel választható paramétereket és az alapértelmezett értékeket is konfigurálhatja. További információt a [dokumentációban](spring-cloud-cli-reference.md)olvashat.  

A Spring Cloud bővítmény telepítése az Azure CLI-hez:

```azurecli
az extension add --name spring-cloud
```

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

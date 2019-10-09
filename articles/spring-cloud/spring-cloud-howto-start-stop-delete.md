---
title: Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése | Microsoft Docs
description: Azure Spring Cloud-alkalmazás elindítása, leállítása és törlése
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: f7f76644d13c20704d2c3bd908176ac452df2a20
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039083"
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

* Az alkalmazás elindítása:
    ```Azure CLI
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás leállítása:
    ```Azure CLI
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás újraindítása:
    ```Azure CLI
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* Az alkalmazás törlése:
    ```Azure CLI
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

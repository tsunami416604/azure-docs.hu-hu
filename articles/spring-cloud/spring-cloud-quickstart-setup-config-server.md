---
title: Gyors útmutató – Azure Spring Cloud konfigurációs kiszolgáló beállítása
description: Leírja, hogyan állítható be az Azure Spring Cloud config Server az alkalmazás telepítéséhez.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 47f74b551919177b13f5a72d6eedeae3c77b9f14
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951911"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Gyors útmutató: az Azure Spring Cloud konfigurációs kiszolgáló beállítása

Az Azure Spring Cloud config Server az elosztott rendszerek központi konfigurációs szolgáltatása. Egy olyan csatlakoztatható adattárház-réteget használ, amely jelenleg támogatja a helyi tárolást, a git-t és az alverziót.  A konfigurációs kiszolgáló beállítása a Service-alkalmazások Azure Spring Cloud-ba való üzembe helyezésére.

## <a name="prerequisites"></a>Előfeltételek

* [A JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , és telepítse az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és a [Bejelentkezés](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) telepítése

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud config Server-eljárások

#### <a name="portal"></a>[Portál](#tab/Azure-portal)

Az alábbi eljárással állítható be a konfigurációs kiszolgáló a Azure Portal használatával a [Piggymetrics-minta](spring-cloud-quickstart-sample-app-introduction.md)telepítéséhez.

1. Lépjen a szolgáltatás **áttekintése** lapra, és válassza a **konfigurációs kiszolgáló**lehetőséget.

2. Az **alapértelmezett adattár** szakaszban állítsa be az **URI** -t "" értékre https://github.com/Azure-Samples/piggymetrics-config .

3. A módosítások mentéséhez válassza az **Alkalmaz** elemet.

    ![Képernyőfelvétel az ASC-portálról](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

Az alábbi eljárás az Azure CLI használatával állítja be a konfigurációs kiszolgálót a [Piggymetrics-minta](spring-cloud-quickstart-sample-app-introduction.md)üzembe helyezéséhez.

Állítsa be a konfigurációs kiszolgálóját a projekt git-tárházának helyére:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

---

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Alkalmazások készítése és üzembe helyezése](spring-cloud-quickstart-deploy-apps.md)

---
title: Gyors útmutató – Azure Spring Cloud konfigurációs kiszolgáló beállítása
description: Leírja, hogyan állítható be az Azure Spring Cloud config Server az alkalmazás telepítéséhez.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2f6051277f1ddb89e67ce8013c78571a2a7314b7
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089128"
---
# <a name="quickstart-set-up-azure-spring-cloud-configuration-server"></a>Gyors útmutató: az Azure Spring Cloud konfigurációs kiszolgáló beállítása

Az Azure Spring Cloud config Server egy központi konfigurációs szolgáltatás az elosztott rendszerek számára. Egy olyan csatlakoztatható adattárház-réteget használ, amely jelenleg támogatja a helyi tárolást, a git-t és az alverziót. Ebben a rövid útmutatóban úgy állíthatja be a konfigurációs kiszolgálót, hogy a git-tárházból olvassa be az adatgyűjtést.

::: zone pivot="programming-language-csharp"

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be az előző rövid útmutatót ebben a sorozatban: [Azure Spring Cloud Service kiépítése](spring-cloud-quickstart-provision-service-instance.md).

## <a name="azure-spring-cloud-config-server-procedures"></a>Azure Spring Cloud config Server-eljárások

Állítsa be a konfigurációs kiszolgálóját a projekt git-tárházának helyére a következő parancs futtatásával. Cserélje le a `<service instance name>` nevet a korábban létrehozott szolgáltatás nevére. Az előző rövid útmutatóban beállított szolgáltatási példány nevének alapértelmezett értéke nem működik ezzel a paranccsal.

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples --search-paths steeltoe-sample/config
```

Ez a parancs arra utasítja a konfigurációs kiszolgálót, hogy a [steeltoe-Sample/config](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample/config) mappában keresse meg a konfigurációs adattárolót. Mivel annak az alkalmazásnak a neve, amely a konfigurációs adatmennyiséget kapja `planet-weather-provider` , a használni kívánt fájl a [Planet-Weather-Provider. YML](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/blob/master/steeltoe-sample/config/planet-weather-provider.yml)lesz.

::: zone-end

::: zone pivot="programming-language-java"
Az Azure Spring Cloud config Server az elosztott rendszerek központi konfigurációs szolgáltatása. Egy olyan csatlakoztatható adattárház-réteget használ, amely jelenleg támogatja a helyi tárolást, a git-t és az alverziót.  A konfigurációs kiszolgáló beállítása a Service-alkalmazások Azure Spring Cloud-ba való üzembe helyezésére.

## <a name="prerequisites"></a>Előfeltételek

* [A JDK 8 telepítése](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) , és telepítse az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és a [Bejelentkezés](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) telepítése

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
::: zone-end

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban olyan Azure-erőforrásokat hozott létre, amelyek továbbra is felhalmozzák a díjakat, ha az előfizetésben maradnak. Ha nem szeretne továbblépni a következő rövid útmutatóra, tekintse meg az [erőforrások tisztítása](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources)című témakört. Ellenkező esetben folytassa a következő rövid útmutatóval:

> [!div class="nextstepaction"]
> [Alkalmazások létrehozása és üzembe helyezése](spring-cloud-quickstart-deploy-apps.md)
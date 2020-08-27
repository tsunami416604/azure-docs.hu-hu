---
title: Gyors útmutató – Azure Spring Cloud-szolgáltatás kiépítése
description: Leírja, hogyan kell létrehozni az Azure Spring Cloud Service-példányt az alkalmazás telepítéséhez.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951917"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Gyors útmutató: Azure Spring Cloud Service kiépítése

Az Azure Spring Cloud a Azure Portal vagy az Azure CLI használatával hozható létre.  Mindkét módszert az alábbi eljárásokban ismertetjük.
## <a name="prerequisites"></a>Előfeltételek

* [A JDK 8 telepítése](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)
* Választható [Telepítse az Azure CLI 2.0.67 vagy újabb verzióját](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , és telepítse az Azure Spring Cloud bővítményt a paranccsal: `az extension add --name spring-cloud`
* Választható [A Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) és a [Bejelentkezés](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) telepítése

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Azure Spring Cloud-példány kiépítése

#### <a name="portal"></a>[Portál](#tab/Azure-portal)

Az alábbi eljárás az Azure Spring Cloud egy példányát hozza létre a Azure Portal használatával.

1. Az új lapon nyissa meg a [Azure Portal](https://ms.portal.azure.com/). 

2. A felső keresőmezőbe keressen az **Azure Spring Cloud**kifejezésre.

3. Válassza az **Azure Spring Cloud** lehetőséget az eredmények közül.

    ![ASC ikon kezdete](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Az Azure Spring Cloud oldalon kattintson a **+ Hozzáadás**gombra.

    ![ASC ikon hozzáadása](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Töltse ki az űrlapot az Azure Spring Cloud **create** oldalon.  Vegye figyelembe a következő irányelveket:
    - **Előfizetés**: válassza ki azt az előfizetést, amelyet számlázni szeretne az erőforráshoz.
    - **Erőforráscsoport**: új erőforráscsoportok létrehozása új erőforrásokhoz az ajánlott eljárás. Vegye figyelembe, hogy ezt a későbbi lépések során fogjuk használni **\<resource group name\>** .
    - **Szolgáltatás adatai/neve**: adja meg a **\<service instance name\>** .  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.
    - **Hely**: válassza ki a szolgáltatás példányának helyét.

    ![ASC-portál indítása](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kattintson az **Áttekintés és létrehozás** elemre.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[Parancssori felület](#tab/Azure-CLI)

Az alábbi eljárás az Azure CLI bővítményt használja az Azure Spring Cloud egy példányának kiépítéséhez.

1. Jelentkezzen be az Azure CLI-be, és válassza ki az aktív előfizetését. Ügyeljen arra, hogy az Azure Spring Cloud számára engedélyezett aktív előfizetést válassza

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Készítse elő az Azure Spring Cloud-szolgáltatás nevét.  A névnek 4 – 32 karakter hosszúnak kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat.  A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.

1. Hozzon létre egy erőforráscsoportot, amely tartalmazza az Azure Spring Cloud Service-t.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Itt tekinthet meg további információt az [Azure-erőforráscsoportokkal](../azure-resource-manager/management/overview.md) kapcsolatban.

1. Nyisson meg egy Azure CLI-ablakot, és futtassa az alábbi parancsokat az Azure Spring Cloud egy példányának kiépítéséhez.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    A szolgáltatás üzembe helyezése körülbelül öt percet vesz igénybe.
---

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Konfigurációs kiszolgáló beállítása](spring-cloud-quickstart-setup-config-server.md)



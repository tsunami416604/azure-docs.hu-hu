---
title: Az állandó tárhely használata az Azure Spring Cloud szolgáltatásban | Microsoft dokumentumok
description: Az állandó tárhely használata az Azure Spring Cloud ban
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 0e49d59386b19aa8da46b8c8e6acfe50e2124541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278530"
---
# <a name="use-persistent-storage-in-azure-spring-cloud"></a>Állandó tárolók használata az Azure Spring Cloudban

Az Azure Spring Cloud kétféle tárhelyet biztosít az alkalmazásszámára: állandó és ideiglenes.

Alapértelmezés szerint az Azure Spring Cloud ideiglenes tárolást biztosít az egyes alkalmazáspéldányok számára. Az ideiglenes tárolás példányonként legfeljebb 5 GB lehet az alapértelmezett csatlakoztatási elérési úttal /tmp.

> [!WARNING]
> Ha újraindít egy alkalmazáspéldányt, a társított ideiglenes tároló véglegesen törlődik.

Az állandó tároló egy, az Azure által felügyelt és alkalmazásonként lefoglalt fájlmegosztási tároló. Az állandó tárolóban tárolt adatokat az alkalmazás összes példánya megosztja. Egy Azure Spring Cloud-példánylegfeljebb 10 olyan alkalmazással rendelkezhet, amelyeken engedélyezve van az állandó tárhely. Minden alkalmazás 50 GB állandó tárolót foglal le. Az állandó tároló alapértelmezett csatlakoztatási elérési útja /persistent.

> [!WARNING]
> Ha letiltja egy alkalmazás állandó tárolóját, az összes tároló fellesz osztva, és az összes tárolt adat elvész.

## <a name="use-the-azure-portal-to-enable-persistent-storage"></a>Az Azure Portal használata az állandó tárolás engedélyezéséhez

1. Az Azure Portal **kezdőlapján** válassza a **Minden erőforrás lehetőséget.**

    >![A Minden erőforrás ikon megkeresése](media/portal-all-resources.jpg)

1. Válassza ki az Azure Spring Cloud erőforrást, amely állandó tárolást igényel. Ebben a példában a kiválasztott alkalmazás neve **upspring**.

    > ![Válassza ki a jelentkezését](media/select-service.jpg)

1. A **Beállítások** cím alatt válassza az **Alkalmazások**lehetőséget.

1. Az Azure Spring Cloud-szolgáltatások egy táblázatban jelennek meg.  Válassza ki azt a szolgáltatást, amelyhez állandó tárolót szeretne hozzáadni. Ebben a példában az **átjárószolgáltatás** van kiválasztva.

    > ![Válassza ki a szolgáltatást](media/select-gateway.jpg)

1. A szolgáltatás konfigurációs lapján válassza a **Konfiguráció lehetőséget**

1. Válassza az **Állandó tároló** lapot, majd az **Engedélyezés**lehetőséget.

    > ![Állandó tárolás engedélyezése](media/enable-persistent-storage.jpg)

Az állandó tárolás engedélyezése után a mérete és az elérési útja megjelenik a konfigurációs lapon.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Az Azure CLI használata az állandó tároló módosításához

Ha szükséges, telepítse a Spring Cloud bővítményt az Azure CLI-hez:

```azurecli
az extension add --name spring-cloud
```
Egyéb műveletek:

* Olyan alkalmazás létrehozása, amelyen engedélyezve van az állandó tároló:

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Egy meglévő alkalmazás állandó tárolásának engedélyezése:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* Az állandó tárhely letiltása egy meglévő alkalmazásban:

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```

    > [!WARNING]
    > Ha letiltja egy alkalmazás állandó tárolóját, az összes tároló fellesz osztva, és az összes tárolt adat véglegesen elveszik.

## <a name="next-steps"></a>További lépések

* További információ [az alkalmazás- és szolgáltatáskvótákról.](spring-cloud-quotas.md)
* További információ [az alkalmazás manuális méretezéséhez.](spring-cloud-tutorial-scale-manual.md)

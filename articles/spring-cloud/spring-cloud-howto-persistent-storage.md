---
title: Az állandó tárolás használata az Azure Spring Cloud-ban | Microsoft Docs
description: Az állandó tárterület használata az Azure Spring Cloud-ban
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: f14da7d8e64c58a54a9da4d851ac22fd710ea8ca
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163734"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Az állandó tárterület használata az Azure Spring Cloud-ban

Az Azure Spring Cloud két típusú tárolót biztosít az alkalmazáshoz: állandó és ideiglenes.  Az Azure Spring Cloud alapértelmezés szerint minden egyes alkalmazás-példány esetében engedélyezi az ideiglenes tárolást. Az ideiglenes tárterület az alapértelmezett csatlakoztatási útvonallal rendelkező 5GB-ra korlátozódik: `/tmp`.

> [!WARNING]
> Egy alkalmazás példányának újraindítása véglegesen törli a hozzá tartozó ideiglenes tárolót.

Az állandó tárterület egy, az Azure által lefoglalt fájlmegosztási tároló. Az állandó tárolóban tárolt adatmennyiség az alkalmazás összes példánya között meg van osztva. Az Azure Spring Cloud Service-példányok legfeljebb 10 olyan alkalmazást tartalmazhatnak, amelyeken engedélyezve van az állandó lemez. Minden alkalmazás állandó tárterületet 50 GB kap. Az állandó tárterület alapértelmezett csatlakoztatási útvonala `/persistent`.

> [!WARNING]
> Az állandó tárterület *letiltása* felszabadítja az adott alkalmazás tárterületét.  A Storage-fiókban lévő összes érték elvész. 

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Az állandó tárterület engedélyezése a Azure Portal használatával

1. A Azure Portal kezdőképernyőn válassza a **minden erőforrás**elemet.

     >![A minden erőforrás ikon megkeresése](media/portal-all-resources.jpg)

1. Keresse meg és válassza ki azt az Azure Spring Cloud-erőforrást, amelynek állandó tárterületre van szüksége.  Ebben a példában az alkalmazás neve *jpspring*.

    > ![A applicationb megkeresése](media/select-service.jpg)

1. A **Beállítások** fejléc alatt válassza az **alkalmazások**lehetőséget.

1. A Spring Cloud Services a táblázatban fog megjelenni.  Válassza ki azt a szolgáltatást, amelyhez állandó tárolót kíván hozzáadni.  Ebben a példában az **átjáró** szolgáltatást fogjuk kiválasztani.

    > ![Válassza ki a szolgáltatást](media/select-gateway.jpg)

1. A szolgáltatás konfiguráció paneljén válassza a **Konfigurálás** lehetőséget.

1. Válassza ki az **állandó tárterület** fület, és engedélyezze az állandó tárterületet.

    > ![Állandó tárterület engedélyezése](media/enable-persistent-storage.jpg)

Ha az állandó tárterület engedélyezve van, a mérete és elérési útja is megjelenik ezen az oldalon.

## <a name="use-the-azure-cli-to-modify-persistent-storage"></a>Az állandó tároló módosítása az Azure CLI használatával

Állandó lemezzel rendelkező alkalmazás létrehozása:
 
```azurecli
az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
```

Állandó tárterület engedélyezése egy meglévő alkalmazásban:

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
``` 

Állandó tárterület letiltása egy meglévő alkalmazásban:

> [!WARNING]
> Az állandó tárterület letiltásával felszabadítja az adott alkalmazás tárterületét, és véglegesen elveszíti az ott tárolt összes adatvesztést. 

```azurecli
az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [alkalmazás-és szolgáltatási kvótákat](spring-cloud-quotas.md), vagy Ismerje meg, hogyan [méretezheti manuálisan az alkalmazást](spring-cloud-tutorial-scale-manual.md).
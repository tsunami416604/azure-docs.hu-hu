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
ms.openlocfilehash: 8c57698471d1363438c10e5806f9ed6f1da5333f
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72039090"
---
# <a name="how-to-use-persistent-storage-in-azure-spring-cloud"></a>Az állandó tárterület használata az Azure Spring Cloud-ban

Az Azure Spring Cloud két típusú tárolót biztosít az alkalmazáshoz: állandó és ideiglenes.  Az Azure Spring Cloud alapértelmezés szerint minden egyes alkalmazás-példány esetében engedélyezi az ideiglenes tárolást. Az ideiglenes tárterület az 5 GB-ra korlátozódik, az alapértelmezett csatlakoztatási útvonala pedig `/tmp`.

> [!WARNING]
> Egy alkalmazás példányának újraindítása véglegesen törli a hozzá tartozó ideiglenes tárolót.

Az állandó tárterület egy, az Azure által kezelt fájlmegosztási tároló, amely alkalmazási hatókörben van lefoglalva. Az állandó tárolóban tárolt adatmennyiség az alkalmazás összes példánya között meg van osztva. Az Azure Spring Cloud Service-példányok legfeljebb 10 olyan alkalmazást tartalmazhatnak, amelyeken engedélyezve van az állandó lemez, és minden alkalmazás 50 GB állandó tárterületet kap. Az állandó tárterület alapértelmezett csatlakoztatási útvonala `/persistent`.

## <a name="enable-persistent-storage-using-the-azure-portal"></a>Az állandó tárterület engedélyezése a Azure Portal használatával

1. Az Azure Spring Cloud Service oldalán válassza az **alkalmazás irányítópult**lehetőséget, majd válassza ki az állandó tárterületet igénylő alkalmazást.
1. Az **Áttekintés** lapon keresse meg az **állandó tárolási** attribútumot, és válassza a **Letiltva**lehetőséget.
1. Kattintson az **Engedélyezés** lehetőségre az állandó tárterület engedélyezéséhez, majd az **OK** gombra kattintva alkalmazza a módosítást.

Ha az állandó tárterület engedélyezve van, a mérete és elérési útja az **Áttekintés** oldal **állandó tárolás** attribútumában jelenik meg.

> [!WARNING]
> Az állandó tárterület *letiltása* felszabadítja az adott alkalmazás tárterületét.  A Storage-fiókban lévő összes érték elvész. 

## <a name="enable-persistent-storage-using-the-azure-cli"></a>Állandó tárterület engedélyezése az Azure CLI használatával

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

## <a name="next-steps"></a>További lépések

Ismerje meg az [alkalmazás-és szolgáltatási kvótákat](spring-cloud-quotas.md), vagy Ismerje meg, hogyan [méretezheti manuálisan az alkalmazást](spring-cloud-tutorial-scale-manual.md).
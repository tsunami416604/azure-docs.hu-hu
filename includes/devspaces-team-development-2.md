---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: include
manager: yuvalm
ms.openlocfilehash: e0f768b876b49ec006ce98decf121d73d334b6d8
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439519"
---
### <a name="run-the-service"></a>A szolgáltatás futtatása

A szolgáltatás futtatásához nyomja le az F5 billentyűt (vagy `azds up` a Terminál ablakában) futtatni a szolgáltatást. A szolgáltatás automatikusan futtatni fogják az újonnan kijelölt terület _dev/scott_. Ellenőrizze, hogy a szolgáltatás fut-e a saját térben futtatásával `azds list-up`:

```cmd
$ azds list-up

Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

Figyelje meg, hogy egy példányát *mywebapi* most már fut a _dev/scott_ terület. A futó verzió _fejlesztési_ továbbra is fut, de nem szerepel.

Az aktuális hely URL-lista futtatásával `azds list-uris`.

```cmd
$ azds list-uris

Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```

Figyelje meg, hogy a nyilvános hozzáférési pont URL-Címének *webfrontend* a következő előtaggal kezdődik *scott.s*. Az URL-címet egyedi érték a _dev/scott_ terület. Az URL-előtagot a Bejövőforgalom-vezérlőt, átirányíthatja a kéréseket a arra utasítja a _dev/scott_ szolgáltatás verzióját. Ezen az URL-kérelmet a fejlesztői, szóközök történik, ha a Bejövőforgalom-vezérlőjéhez először próbál irányítsa át a kérést, hogy a *webfrontend* szolgáltatásának a _dev/scott_ terület. Ha ez nem sikerül, a kérést a rendszer átirányítja a *webfrontend* szolgáltatást a a _fejlesztési_ terület tartalékként. Is megfigyelheti, hogy létezik egy localhost URL-címet a Kubernetes használatával localhost keresztül a szolgáltatás eléréséhez *port-továbbító* funkciót. További információ az URL-címek és az Azure fejlesztési tárolóhelyek útválasztás: [hogyan Azure fejlesztési tárolóhelyek működik, és konfigurált](../articles/dev-spaces/how-dev-spaces-works.md).



![A lemezterület-Útválasztás](../articles/dev-spaces/media/common/Space-Routing.png)

Az Azure Dev Spaces ezen beépített szolgáltatása lehetővé teszi, hogy tesztelhesse a kódot egy megosztott térben anélkül, hogy minden egyes fejlesztőnek ismételten létre kellene hoznia a saját tere teljes szolgáltatási vermét. Ez az útválasztás megköveteli az alkalmazáskódtól a propagálási fejlécek továbbítását, ahogy az az útmutató előző lépésében is látható.

### <a name="test-code-in-a-space"></a>Kód tesztelése egy térben
Új verziójának tesztelése *mywebapi* a *webfrontend*, nyilvános hozzáférési pont URL-CÍMÉT a böngészőben nyissa meg *webfrontend* , és lépjen a névjegy lapra. Meg kell jelennie az új üzenetnek.

Most távolítsa el az URL-cím „scott.s.” előtagját, és frissítse a böngészőt. Megjelenik a régi viselkedése (az a *mywebapi* futó verzió _fejlesztési_).

Ha már van egy _fejlesztési_ szóköz, amely mindig tartalmazza a legutóbbi módosításoknak, és feltéve, hogy az alkalmazás célja az oktatóanyag ezen szakaszában leírt DevSpace a terület-alapú útválasztás kihasználásához, remélhetőleg könnyen lesz Tekintse meg, hogyan fejlesztési tárolóhelyek nagy mértékben végrehajtásában segít a nagyobb alkalmazás környezetében lévő új szolgáltatások tesztelése. Ahelyett, hogy üzembe helyezéséhez _összes_ szolgáltatások a személyes tárhelyre, létrehozhat egy privát területet származó _fejlesztési_, és "csak be" a ténylegesen dolgozik a szolgáltatásokat. A fejlesztői, szóközök útválasztási infrastruktúra fogja kezelni a többi felügyelniük annyi szolgáltatások a privát lemezterületét talál, alapértelmezés szerint a legújabb verzióra futtatása közben a _fejlesztési_ terület. Továbbra is, hatékonyabb és _több_ fejlesztők aktívan fejleszthetnek különböző szolgáltatásokat egy időben a saját térben egymással megszakítása nélkül.

### <a name="well-done"></a>Remek!
Elvégezte az első lépéseket ismertető útmutatót! Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Dev Spaces beállítása Managed Kubernetes-fürttel az Azure-ban.
> * Iteratív kódfejlesztés a tárolókban.
> * Két külön szolgáltatás egymástól függetlenül történő fejlesztése, és a használt Kubernetes DNS-szolgáltatás észlelésével hívásindítás egy másik szolgáltatásba.
> * A kód hatékony fejlesztése és tesztelése, csapatkörnyezetben.
> * Fejlesztői tárolóhelyek használatával egyszerűen tesztelheti az elkülönített módosításokat egy nagyobb mikroszolgáltatás-alkalmazás összefüggésében funkció megállapítja az alapértékeket

Most, hogy már megismerte az Azure fejlesztési szóközöket, [fejlesztési tárhely megosztása egy csapattag](../articles/dev-spaces/how-to/share-dev-spaces.md) és közös munka megkezdéséhez.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása
Ha egy fürt egy Azure Dev Spaces-példányát teljesen, az összes Dev Spaces-térrel és benne futó szolgáltatással együtt törölni szeretné, használja az `az aks remove-dev-spaces` parancsot. Tartsa észben, hogy ez a művelet nem vonható vissza. Újra hozzáadhat Azure Dev Spaces-támogatást a fürtön, de azt a rendszer úgy kezeli, mintha elölről kezdené a folyamatot. A régi szolgáltatások és a tárolóhelyek nem állíthatók vissza.

Az alábbi példa listázza az aktív előfizetése Azure Dev Spaces-vezérlőit, majd törli a myaks-rg erőforráscsoportban lévő myaks AKS-fürthöz társított Azure Dev Spaces-vezérlőt.

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
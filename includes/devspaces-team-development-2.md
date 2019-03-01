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
ms.openlocfilehash: 5d66dcaccc6ca2e40fbd516f535ec56c1baf6b17
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195597"
---
### <a name="run-the-service"></a>A szolgáltatás futtatása

1. A szolgáltatás futtatásához nyomja le az F5 billentyűt (vagy írja be a terminálablakba az `azds up` parancsot). A szolgáltatás automatikusan futtatni fogják az újonnan kijelölt terület _dev/scott_. 
1. Az `azds list-up` ismételt futtatásával ellenőrizheti, hogy a szolgáltatás a saját területén fut-e. Látható, hogy egy példányát *mywebapi* most már fut a _dev/scott_ terület (futó verzió _fejlesztési_ továbbra is fut, de nem szerepel).

    ```
    Name                      DevSpace  Type     Updated  Status
    mywebapi                  scott     Service  3m ago   Running
    mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
    webfrontend               dev       Service  26m ago  Running
    ```

1. Futtatás `azds list-uris`, és figyelje meg, hogy a hozzáférési pont a URL-címe *webfrontend*.

    ```
    Uri                                                                        Status
    -------------------------------------------------------------------------  ---------
    http://localhost:53831 => mywebapi.scott:80                                Tunneled
    http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
    ```

1. Az URL-CÍMÉT használja a *scott.s* keresse meg az alkalmazás-előtagot. Figyelje meg a frissített URL-cím feloldása továbbra is. Az URL-címet egyedi érték a _dev/scott_ terület. A speciális URL-cím azt jelzi, hogy, hogy a "Scott URL-címe" küldött kérelmek megpróbálja szolgáltatások első útvonal a _dev/scott_ tárhelyen, de ha nem jár sikerrel, rendszer visszavált a szolgáltatások a _fejlesztési_ terület.

<!--
TODO: replace 2 & 3 with below once bug#753164 and PR#158827 get pushed to production.

You can confirm that your service is running in its own space by running `azds list-up` again. First, you'll notice an instance of *mywebapi* is now running in the _dev/scott_ space (the version running in _dev_ is still running but it is not listed). If you run `azds list-uris`, you will notice that the access point URL for *webfrontend* is prefixed with the text "scott.s.". This URL is unique to the _dev/scott_ space. The special URL signifies that requests sent to the "Scott URL" will try to first route to services in the _dev/scott_ space, but if that fails, they will fall back to services in the _dev_ space.

```
Name                      DevSpace  Type     Updated  Status
mywebapi                  scott     Service  3m ago   Running
mywebapi-bb4f4ddd8-sbfcs  scott     Pod      3m ago   Running
webfrontend               dev       Service  26m ago  Running
```

```
Uri                                                                        Status
-------------------------------------------------------------------------  ---------
http://localhost:53831 => mywebapi.scott:80                                Tunneled
http://scott.s.dev.webfrontend.6364744826e042319629.ce.azds.io/  Available
```
-->

![](../articles/dev-spaces/media/common/space-routing.png)

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
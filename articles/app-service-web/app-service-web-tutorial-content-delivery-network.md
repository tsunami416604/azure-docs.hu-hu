---
title: "Content Delivery Network hozzáadása Azure App Service platformon | Microsoft Docs"
description: "A Content Delivery Network hozzáadásával az Azure App Service platformon élcsomópontokból kézbesítheti a statikus fájlokat."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 04/03/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 7ba3737566401152a3171e8926beca188045230c
ms.lasthandoff: 04/22/2017

---
# <a name="add-a-content-deliver-network-on-an-azure-app-service"></a>Content Delivery Network hozzáadása Azure App Service platformon

Az oktatóanyag során hozzáadhat egy Content Delivery Network (CDN) hálózatot az Azure App Service platformhoz, hogy közzé tehesse a statikus tartalmakat egy élkiszolgálón. Létrehoz majd egy CDN-profilt, amely legfeljebb 10 CDN-végpont gyűjteménye.

A Content Delivery Network (CDN) a statikus webtartalmakat stratégiailag kiválasztott helyeken gyorsítótárazza, így maximális átviteli sebességgel tudja kézbesíteni a tartalmakat a felhasználók számára. A webhelyek objektumainak CDN használatával történő gyorsítótárazása a következő előnyökkel jár:

* Jobb teljesítmény és felhasználói élmény a végfelhasználók számára – különösen az alkalmazások használata esetén, amikor a tartalom betöltéséhez a kiszolgálóval végzett több adatváltásra is szükség van.
* A teljesítmény könnyedén méretezhető az azonnali nagy terheléshez, például a termékbevezetési események kezdetén fellépő magas igényekhez.
* A felhasználói kérelmek elosztásával és a tartalom peremhálózati kiszolgálókról történő szolgáltatásával csökken a forrásra jutó forgalom.

> [!TIP]
> Ellenőrizze az [Azure CDN POP-helyek](https://docs.microsoft.com/en-us/azure/cdn/cdn-pop-locations) aktuális listáját.
>

## <a name="deploy-the-sample"></a>A minta üzembe helyezése

Az oktatóanyag elvégzéséhez szüksége lesz egy WebAppban üzembe helyezett alkalmazásra. A jelen oktatóanyaghoz szükséges alapok biztosításához kövesse [a statikus HTML gyorsútmutatót](app-service-web-get-started-html.md).

## <a name="step-1---login-to-azure-portal"></a>1. lépés – Belépés az Azure Portalba

Először nyissa meg a kedvenc böngészőjét, és keresse fel az [Azure Portalt](https://portal.azure.com).

## <a name="step-2---create-a-cdn-profile"></a>2. lépés – CDN-profil létrehozása

A bal oldali navigációs menüben kattintson az `+ New` gombra, majd kattintson a **Web és mobil** elemre. A Web és mobil kategória alatt válassza ki a **CDN** lehetőséget.

Töltse ki a következő mezőket:

| Mező | Mintaérték | Leírás |
|---|---|---|
| Név | myCDNProfile | A CDN-profil neve. |
| Hely | Nyugat-Európa | Ez az az Azure-hely, ahol a rendszer a CDN-profil adatait tárolja majd. Ez nincs hatással a CDN-végpontok helyére. |
| Erőforráscsoport | myResourceGroup | További információ az erőforráscsoportokkal kapcsolatban: [Az Azure Resource Manager áttekintése](../azure-resource-manager/resource-group-overview.md#resource-groups). |
| Tarifacsomag | Akamai Standard | A tarifacsomagok összehasonlításához lásd a [CDN áttekintésével](../cdn/cdn-overview.md#azure-cdn-features) foglalkozó cikket. |

Kattintson a **Létrehozás** gombra.

Nyissa meg az erőforráscsoportok központját a bal oldali navigációs menüben, majd válassza ki a **myResourceGroup** elemet. Az erőforrások listájából válassza ki a **myCDNProfile** elemet.

![azure-cdn-profile-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-profile-created.png)

## <a name="step-3---create-a-cdn-endpoint"></a>3. lépés – CDN-végpont létrehozása

Kattintson a **+ Végpont** elemre a keresőmező melletti parancsok között a Végpontlétrehozás panel elindításához.

Töltse ki a következő mezőket:

| Mező | Mintaérték | Leírás |
|---|---|
| Név |  | A rendszer ezt a nevet fogja használni a gyorsítótárazott erőforrások eléréséhez a `<endpointname>.azureedge.net` tartományban. |
| Forrás típusa | Webalkalmazás | A forrástípus kiválasztásával helyi menük válnak elérhetővé a többi mezőhöz. Egyéni forrás kiválasztása esetén egy szövegmező jelenik meg a forrás gazdanevének megadásához. |
| Forrás gazdaneve | |  A legördülő menüben szerepelni fog a megadott forrástípusú összes rendelkezésre álló forrás. Ha a Forrása típusa számára az Egyéni forrás beállítást választotta ki, akkor be kell írnia az egyéni forrás tartományát.  |

Kattintson a **Hozzáadás** parancsra.

A végpont ekkor létrejön. Ha a tartalomkézbesítési hálózat végpontja létrejön, az állapot **futás** állapotúra frissül.

![azure-cdn-endpoint-created](media/app-service-web-tutorial-content-delivery-network/azure-cdn-endpoint-created.png)

## <a name="step-4---serve-from-azure-cdn"></a>4. lépés – Kiszolgálás az Azure CDN-ről

Most, hogy a CDN-végpont **fut**, el kell tudnia érni a rajta lévő tartalmakat róla.

Mivel az oktatóanyag a [statikus HTML gyorsútmutatón alapul](app-service-web-get-started-html.md), a CDN-en rendelkezésre kell állnia a következő mappáknak: `css`, `img`, `js`.

A tartalmak elérési útjai a WebApp `http://<app_name>.azurewebsites.net/img/` URL-címe és a CDN-végpont `http://<endpointname>.azureedge.net/img/` URL-címe közt megegyeznek, ami azt jelenti, hogy a CDN-végpont tartományát egyszerűen lecserélheti bármely statikus tartalomra, hogy a tartalom a CDN-ről legyen szolgáltatva.

Kérje le az első képet a CDN-végpontról. Ehhez lépjen a kedvenc böngészőjével a következő URL-címre:

```bash
http://<endpointname>.azureedge.net/img/03-enterprise.png
```

Most, hogy a statikus tartalom elérhető a CDN-en, frissítheti az alkalmazást, hogy az a CDN-végpont használatával biztosítsa a tartalmakat a végfelhasználók számára.

A webhely készítéséhez használt nyelvtől függően számos különféle keretrendszer lehet elérhető a CDN-tartalék támogatásához. Az ASP.NET például támogatja a [kötegelést és kicsinyítést](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/bundling-and-minification#using-a-cdn), amely elérhetővé teszi a CDN-tartalékhoz kapcsolódó képességeket is.

Ha a nyelv nem tartalmaz beépített támogatást vagy egy könyvtárat a CDN-tartalék számára, használhat egy JavaScript-keretrendszert, például a [FallbackJS](http://fallback.io/) keretrendszert, amely támogatja a [szkriptek](https://github.com/dolox/fallback/tree/master/examples/loading-scripts), [stíluslapok](https://github.com/dolox/fallback/tree/master/examples/loading-stylesheets) és [képek](https://github.com/dolox/fallback/tree/master/examples/loading-images) betöltését.

## <a name="step-5---purge-the-cdn"></a>5- lépés – A CDN végleges törlése

Néha szükség lehet a CDN végleges törlésére, ha azt szeretné, hogy a tartalom az élettartama (TTL) lejárta előtt elavuljon.

Az Azure CDN manuálisan végrehajtott végleges törlése a CDN-profil panelről vagy a CDN-végpont panelről lehetséges. Ha a Profil oldalon választja ki a végleges törlés lehetőséget, ki kell választania a véglegesen törölni kívánt végpontot.

Tartalom végleges törléséhez adja meg a törölni kívánt tartalmak elérési útjait. Megadhat egy teljes elérési útvonalat egy adott fájl törléséhez, vagy egy részleges útvonalat egy adott mappa tartalmának törléséhez és frissítéséhez.

Miután megadta az összes törölni kívánt tartalom elérési útját, kattintson a **Végleges törlés** gombra.

![app-service-web-purge-cdn](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

## <a name="step-6---map-a-custom-domain"></a>6. lépés – Egyéni tartomány leképezése

Egy egyéni tartomány CDN-végpontra történő leképezésével egy egységes tartományt hozhat létre a webalkalmazása számára.

Az egyéni tartomány a CDN-végpontra való leképezéséhez hozzon létre egy CNAME-rekordot a tartományregisztrálónál.

> [!NOTE]
> A CNAME-rekord egy DNS-szolgáltatás, amellyel egy forrástartomány (pl.: `www.contosocdn.com` vagy `static.contosocdn.com`) leképezhető egy céltartományra.

Ebben az esetben hozzáadunk egy `static.contosocdn.com` forrástartományt, és a céltartományra, azaz a CDN-végpontra irányítjuk.

| forrástartomány | céltartomány |
|---|---|
| static.contosocdn.com | &lt;végpontnév&gt;.azureedge.net |

A CDN-végpont áttekintő panelén kattintson a `+ Custom domain` gombra.

Az Egyéni tartomány hozzáadása panelen adja meg az egyéni tartományt az altartománnyal együtt a párbeszédpanelen. Adja meg például a contoso.com tartománynevet a következő formátumban: `static.contosocdn.com`.

Kattintson a **Hozzáadás** parancsra.

## <a name="step-7---version-content"></a>7. lépés – Tartalom verziózása

A CDN-végpont bal oldali navigációs menüjében válassza a **Gyorsítótár** elemet a Beállítások fejléc alatt.

A **Gyorsítótár** panelen konfigurálhatja, hogy a CDN hogyan kezelje a lekérdezési karakterláncokat a kérésben.

> [!NOTE]
> A lekérdezési karakterláncok gyorsítótárazási viselkedésének beállításaival kapcsolatban olvassa el [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal](../cdn/cdn-query-string.md) című témakört.

Válassza a **Minden egyedi URL-cím gyorsítótárazása** lehetőséget a legördülő menüből a Lekérdezési karakterlánc gyorsítótárazási viselkedése beállításnál.

Kattintson a **mentés** gombra.

## <a name="next-steps"></a>Következő lépések

* [Mi az Azure CDN?](../best-practices-cdn.md?toc=%2fazure%2fcdn%2ftoc.json)
* [HTTPS engedélyezése Azure CDN egyéni tartományon](../cdn/cdn-custom-ssl.md)
* [A teljesítmény javítása a fájlok tömörítésével az Azure CDN-ben](../cdn/cdn-improve-performance.md)
* [Eszközök előzetes betöltése Azure CDN-végponton](../cdn/cdn-preload-endpoint.md)


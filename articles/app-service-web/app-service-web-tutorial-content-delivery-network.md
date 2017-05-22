---
title: "Content Delivery Network (CDN) hozzáadása Azure App Service platformon | Microsoft Docs"
description: "A Content Delivery Network (CDN) hozzáadásával az Azure App Service platformon gyorsítótárazhatja és az ügyfelekhez közeli kiszolgálóiról továbbíthatja statikus fájljait világszerte."
services: app-service
author: syntaxc4
ms.author: cfowler
ms.date: 05/01/2017
ms.topic: hero-article
ms.service: app-service-web
manager: erikre
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7208abc0e6eaa9067c5bb36a09e1bfd276fe0b0c
ms.contentlocale: hu-hu
ms.lasthandoff: 05/08/2017

---
# <a name="add-a-content-delivery-network-cdn-to-an-azure-app-service"></a>Content Delivery Network (CDN) hozzáadása Azure App Service platformon

Az [Azure Content Delivery Network (CDN)](../cdn/cdn-overview.md) a statikus webtartalmakat stratégiailag kiválasztott helyeken gyorsítótárazza, így maximális átviteli sebességgel tudja kézbesíteni a tartalmakat a felhasználók számára. A CDN ezzel együtt csökkenti a kiszolgálók terhelését a webappban. Ez az oktatóanyag bemutatja, hogyan adható hozzá az Azure CDN [a webappokhoz az Azure App Service szolgáltatásban](app-service-web-overview.md). 

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * CDN-végpont létrehozása.
> * Gyorsítótárazott objektumok frissítése.
> * Gyorsítótárazott verziók felügyelete lekérdezési karakterláncok használatával.
> * Egyéni tartomány használata a CDN-végponthoz.

Íme a mintaként szolgáló statikus HTML-webhely kezdőlapja, amelyet használni fog:

![Mintaalkalmazás kezdőlapja](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

## <a name="create-the-web-app"></a>A webapp létrehozása

A használandó webapp létrehozásához kövesse a [statikus HTML gyorsútmutatót](app-service-web-get-started-html.md), de ne végezze el **Az erőforrások eltávolítása** lépést.

Ne zárja be a parancssort, miután az oktatóanyag végére ért, hogy további módosításokat hajthasson végre a webappon ennek az oktatóanyagnak a későbbi részeiben.

### <a name="have-a-custom-domain-ready"></a>Rendelkezésre álló egyéni tartománynév

Az oktatóanyag egyéni tartományra vonatkozó lépése során hozzá kell majd férnie tartományszolgáltatója (például a GoDaddy) DNS-jegyzékéhez. Például a `contoso.com` és a `www.contoso.com` DNS-bejegyzéseinek hozzáadásához hozzá kell férnie a `contoso.com` gyökértartomány DNS-beállításainak konfigurációjához.

Ha még nem rendelkezik tartománynévvel, az [App Service-tartományokkal foglalkozó oktatóanyagban](custom-dns-web-site-buydomains-web-app.md) foglaltak szerint vásárolhat egy tartományt az Azure Portal használatával. 

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Nyisson meg egy böngészőt, és keresse fel az [Azure Portalt](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>CDN-profil és -végpont létrehozása

A bal oldali navigációs felületen válassza az **App Services** lehetőséget, majd válassza ki a [statikus HTML gyorsútmutató](app-service-web-get-started-html.md) segítségével létrehozott alkalmazást.

![Az App Service alkalmazás kiválasztása a portálon](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

Az **App Service** lap **Beállítások** területén válassza a **Hálózatkezelés > Az Azure CDN konfigurálása az alkalmazáshoz** lehetőséget.

![A CDN kiválasztása a portálon](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

Az **Azure Content Delivery Network** lapon adja meg az **Új végpont** beállításait az alábbi táblának megfelelően.

![Profil és végpont létrehozása a portálon](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Beállítás | Ajánlott érték | Leírás |
| ------- | --------------- | ----------- |
| **CDN-profil** | myCDNProfile | Válassza az **Új létrehozása** lehetőséget egy új CDN-profil létrehozásához. A CDN-profil ugyanabba a tarifacsomagba tartozó CDN-végpontok gyűjteménye. |
| **Tarifacsomag** | Akamai Standard | A [tarifacsomag](../cdn/cdn-overview.md#azure-cdn-features) határozza meg a szolgáltatót és az elérhető szolgáltatásokat. Az oktatóanyagban az Akamai Standard csomagot használjuk. |
| **CDN-végpont neve** | Bármely egyedi név az azureedge.net tartományban | A gyorsítótárazott erőforrások a *\<végpont_neve>.azureedge.net* tartományban érhetőek el.

Kattintson a **Létrehozás** gombra.

Az Azure létrehozza a profilt és a végpontot. Az új végpont megjelenik ugyanezen a lapon a **Végpontok** listában, és a kiosztása után **Fut** állapotra vált.

![Új végpont a listában](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>A CDN-végpont tesztelése

A Verizon tarifacsomag kiválasztása esetén a végpontok terjesztése körülbelül 90 percet vesz igénybe. Az Akamai esetében a terjesztés csak néhány percig tart

Ugyanehhez az alkalmazáshoz tartozik egy `index.html` fájl, valamint *css*, *img* és *js* mappák is, amelyek egyéb statikus objektumokat tartalmaznak. Az összes fájl tartalmának elérési útjai megegyezik a CDN-végponton. Például a következő két URL egyaránt a *bootstrap.css* fájlra mutat a *css* mappában:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Nyissa meg egy böngészőben a következő URL-címet, és ugyanaz a lap jelenik meg, amelyet korábban egy Azure webappban futtatott, most azonban a CDN szolgáltatja.

```
http://<endpointname>.azureedge.net/index.html
```

![Mintaalkalmazás a CDN által szolgáltatott kezdőlapja](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

Az ábrán látható, hogy az Azure CDN lehívta a forrásként szolgáló webapp objektumait, és a CDN-végpontról szolgáltatja azokat. 

Annak érdekében, hogy a CDN gyorsítótárazza a lapot, frissítse azt. Néha két kérés is szükséges egyazon objektumra vonatkozóan, hogy a CDN gyorsítótárazza a kért tartalmat.

Az Azure CDN-profilok és -végpontok létrehozásával kapcsolatos további információkért lásd: [Az Azure CDN használatának első lépései](../cdn/cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>A CDN végleges törlése

A CDN rendszeres időközönként frissíti az erőforrásait a forrásként szolgáló webappból az élettartam (TTL) konfigurációja alapján. Az alapértelmezett élettartam 7 nap.

Esetenként az élettartam lejárta előtt is szükséges lehet a CDN frissítése – például amikor frissített tartalmat telepít a webappba. A frissítés indításához manuálisan törölheti a CDN-erőforrásokat. 

Az oktatóanyag jelen szakaszában egy módosítást fog telepíteni a webappba, és törli a CDN-t, hogy az frissítse a gyorsítótárát.

### <a name="deploy-a-change-to-the-web-app"></a>Módosítás telepítése a webappba

Nyissa meg az `index.html` fájlt, és adja hozzá a „- V2” utótagot a H1 fejléchez, ahogy az az alábbi példában látható: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Véglegesítse a módosítást, és telepítse a webappba.

```bash
git commit -am "version 2"
git push azure master
```

Miután befejeződött a telepítés, a böngészőben nyissa meg a webapp URL-címét, és láthatja a módosított tartalmat.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 a webapp címében](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Ha a böngészőben megnyitja a kezdőlap CDN-végponti URL-címét, láthatja, hogy a módosítás még nem jelent meg, mivel a CDN-ben gyorsítótárazott verzió még nem járt le. 

```
http://<endpointname>.azureedge.net/index.html
```

![A V2 nem jelenik meg a CDN-beli címben](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>A CDN törlése a portálon

Ahhoz, hogy a CDN frissítse a gyorsítótárazott verziót, törölje a CDN-t.

A portál bal oldali navigációs felületén válassza az **Erőforráscsoportok** elemet, majd válassza ki a webapphoz létrehozott erőforráscsoportot (myResourceGroup).

![Erőforráscsoport kiválasztása](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

Az erőforrások listájában válassza ki a CDN-végpontot.

![Végpont kiválasztása](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

A **Végpont** lap tetején kattintson a **Végleges törlés** gombra.

![Végleges törlés kiválasztva](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Adja meg a törölni kívánt tartalmak elérési útjait. Megadhat egy teljes elérési útvonalat egy adott fájl törléséhez, vagy egy részleges útvonalat egy adott mappa teljes tartalmának törléséhez és frissítéséhez. Mivel az `index.html` fájlt módosította, mindenképp ez legyen az egyik útvonal.

A lap alján kattintson a **Végleges törlés** gombra.

![Oldal végleges törlése](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>A CDN frissítésének ellenőrzése

Várjon, amíg végleges törlési kérés feldolgozása befejeződik. Ez általában néhány percet vesz igénybe. Az aktuális állapot megtekintéséhez válassza a harang ikont a lap tetején. 

![Törlési értesítés](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Böngészőben nyissa meg az `index.html` CDN-végponti URL-címét, és láthatja, hogy a kezdőlap címéhez hozzáadott V2 megjelent. Ez jelzi, hogy a CDN gyorsítótára frissítve lett.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 a CDN-beli címben](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

További információkért lásd: [CDN-végpont végleges törlése](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Tartalmak verziószámozása lekérdezési karakterláncok használatával

Az Azure CDN az alábbi gyorsítótárazási működésére vonatkozó lehetőségeket kínálja:

* Lekérdezési karakterláncok figyelmen kívül hagyása
* Lekérdezési karakterláncok gyorsítótárazásának megkerülése
* Minden egyedi URL gyorsítótárazása 

A legelső az alapértelmezett beállítás, amely azt jelenti, hogy minden objektumnak csak egy gyorsítótárazott verziója van, függetlenül az azt elérő URL-címben használt lekérdezési karakterlánctól. 

Az oktatóanyag ezen szakaszában a gyorsítótárazás működésének módosításával minden egyedi URL-címet gyorsítótárazni fog.

### <a name="change-the-cache-behavior"></a>A gyorsítótárazás működésének módosítása

Az Azure Portal **CDN-végpont** lapján válassza a **Gyorsítótár** lehetőséget.

Válassza a **Minden egyedi URL-cím gyorsítótárazása** lehetőséget a **Lekérdezési karakterláncok gyorsítótárazásának működése** legördülő menüben.

Kattintson a **Mentés** gombra.

![Lekérdezési karakterláncok gyorsítótárazási működésének kiválasztása](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Az egyedi URL-címek külön gyorsítótárazásának ellenőrzése

Egy böngészőben nyissa meg a kezdőlapot a CDN-végponton, de használjon egy lekérdezési karakterláncot is: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

A CDN a webapp aktuális tartalmát adja vissza, aminek a fejlécében szerepel a „V2” utótag. 

Annak érdekében, hogy a CDN gyorsítótárazza a lapot, frissítse azt. 

Nyissa meg az `index.html` fájlt, módosítsa a „V2”-t „V3”-ra, majd telepítse a módosítást. 

```bash
git commit -am "version 3"
git push azure master
```

Böngészőben nyissa meg a CDN-végponti URL-címet egy új lekérdezési karakterlánccal, például a következővel: `q=2`. A CDN lekéri az aktuális `index.html` fájlt, és megjelenik a „V3” utótag.  Ha azonban a `q=1` lekérdezési karakterlánccal nyitja meg a CDN-végpontot, a „V2” utótag látható.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 a CDN-beli címben, 2. lekérdezési karakterlánc](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 a CDN-beli címben, 1. lekérdezési karakterlánc](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

Ez a kimenet mutatja, hogy a rendszer minden lekérdezési karakterláncot máshogy kezel: korábban a q=1 volt használatban, amely a gyorsítótárazott tartalmakat adja vissza (V2), a q=2 viszont új, ezért a webapp legfrissebb tartalmait kéri le és adja vissza (V3).

További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Egyéni tartomány leképezése egy CDN-végpontra

Az egyéni tartományt a CDN-végpontra egy CNAME-rekord létrehozásával képezheti le. A CNAME-rekord egy DNS-szolgáltatás, amellyel egy forrástartomány leképezhető egy céltartományra. Például leképezheti a `cdn.contoso.com` vagy a `static.contoso.com` tartományt a `contoso.azureedge.net` tartományra.

Ha nem rendelkezik egyéni tartománnyal, az [App Service-tartományokkal foglalkozó oktatóanyagban](custom-dns-web-site-buydomains-web-app.md) foglaltak szerint vásárolhat egyet az Azure Portal használatával. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>A CNAME-rekordhoz használandó gazdagépnév keresése

Az Azure Portal **Végpont** lapján ellenőrizze, hogy a bal oldali navigációs felületen az **Áttekintés** lehetőség van kiválasztva, majd válassza a **+ Egyéni tartomány** gombot a lap tetején.

![Egyéni tartomány hozzáadása kiválasztva](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

Az **Egyéni tartomány hozzáadása** lapon látható a CNAME rekord létrehozásához használandó végpont gazdagépneve. A gazdagépnév CDN-végponti URL-címből van származtatva: **&lt;VégpontNeve>.azureedge.net**. 

![Tartomány hozzáadása lap](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>A CNAME konfigurálása a tartományregisztrálóval

Lépjen a tartományregisztráló webhelyére, és keresse meg a DNS-rekordok létrehozására szolgáló felületet. Ezt a **Tartománynév**, **DNS**, **Névkiszolgáló kezelése** vagy hasonló területen találja.

Keresse meg a CNAME-rekordok kezelésére szolgáló felületet. Ehhez esetleg a különleges beállítások lapjára kell lépnie, és ott a CNAME, Alias vagy Altartományok kifejezést kell keresnie.

Hozzon létre egy új CNAME-rekordot, amely leképezi a választott altartományt (például a **statikus** vagy **cdn** altartományt) a portálon korábban bemutatott **Végpont gazdagépnevére**. 

### <a name="enter-the-custom-domain-in-azure"></a>Az egyéni tartománynév megadása az Azure-ban

Lépjen vissza az **Egyéni tartomány hozzáadása** lapra, és adja meg az egyéni tartományt az altartománnyal együtt a párbeszédpanelen. Adja meg például a következőt: `cdn.contoso.com`.   
   
Az Azure ellenőrzi, hogy a megadott tartománynév esetében létezik-e a CNAME-rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz.

Időbe telhet, amíg megtörténik a CNAME-rekord névkiszolgálókon való propagálása az interneten. Ha úgy véli, hogy a CNAME-rekord helyes, ám a tartomány érvényesítése mégsem történik meg azonnal, várjon néhány percet, majd próbálja újra.

### <a name="test-the-custom-domain"></a>Az egyéni tartomány tesztelése

Egy böngészőben nyissa meg az `index.html` fájlt az egyéni tartomány (például a `cdn.contoso.com/index.html`) használatával annak ellenőrzéséhez, hogy az eredmény ugyanaz, mintha közvetlenül az `<endpointname>azureedge.net/index.html` helyre lépett volna.

![Mintaalkalmazás egyéni tartományi URL-címet használó kezdőlapja](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

További információkért lásd: [Azure CDN-tartalom leképezése egyéni tartományra](../cdn/cdn-map-content-to-custom-domain.md)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * CDN-végpont létrehozása.
> * Gyorsítótárazott objektumok frissítése.
> * Gyorsítótárazott verziók felügyelete lekérdezési karakterláncok használatával.
> * Egyéni tartomány használata a CDN-végponthoz.

A CDN teljesítményének optimalizálását a következő cikkekben sajátíthatja el.

> [!div class="nextstepaction"]
> [A teljesítmény javítása a fájlok tömörítésével az Azure CDN-ben](../cdn/cdn-improve-performance.md)

> [!div class="nextstepaction"]
> [Eszközök előzetes betöltése Azure CDN-végponton](../cdn/cdn-preload-endpoint.md)



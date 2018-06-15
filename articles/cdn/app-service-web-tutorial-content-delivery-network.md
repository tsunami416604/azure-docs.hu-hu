---
title: Oktatóanyag – Azure CDN hozzáadása egy Azure App Service-webalkalmazáshoz | Microsoft Docs
description: Ebben az oktatóanyagban hozzáadunk egy Azure tartalomkézbesítési hálózatot (CDN-t) egy Azure App Service-webalkalmazáshoz, mellyel gyorsítótárazhatja a statikus fájljait,majd kézbesítheti őket a világ különböző pontjain lévő ügyfeleinek a hozzájuk legközelebbi kiszolgálóról.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: d782f5bf30160d85d7d9ef7a00190551f9a9843a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161244"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Oktatóanyag: Azure CDN hozzáadása egy Azure App Service-webalkalmazáshoz

Ez az oktatóanyag bemutatja, hogyan adható hozzá az Azure CDN [a webappokhoz az Azure App Service szolgáltatásban](../app-service/app-service-web-overview.md). Az [Azure Content Delivery Network (CDN)](cdn-overview.md) a statikus webtartalmakat stratégiailag kiválasztott helyeken gyorsítótárazza, így maximális átviteli sebességgel tudja kézbesíteni a tartalmakat a felhasználók számára. Az Azure CDN emellett csökkenti a webalkalmazását futtató kiszolgáló terhelését. 

Íme a mintaként szolgáló statikus HTML-webhely kezdőlapja, amelyet használni fog:

![Mintaalkalmazás kezdőlapja](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

Ismertetett témák:

> [!div class="checklist"]
> * CDN-végpont létrehozása.
> * Gyorsítótárazott objektumok frissítése.
> * Gyorsítótárazott verziók felügyelete lekérdezési karakterláncok használatával.
> * Egyéni tartomány használata a CDN-végponthoz.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [A Git telepítése](https://git-scm.com/)
- [Telepítse az Azure CLI 2.0-t.](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>A webapp létrehozása

A használni kívánt webalkalmazás létrehozásához kövesse a [statikus HTML-es rövid útmutató](../app-service/app-service-web-get-started-html.md) **Az alkalmazás megkeresése tallózással** lépését.

### <a name="have-a-custom-domain-ready"></a>Rendelkezésre álló egyéni tartománynév

Az oktatóanyag egyéni tartományra vonatkozó lépése során rendelkeznie kell egy egyéni tartománnyal, és hozzá kell férnie a tartományszolgáltatója DNS-jegyzékéhez. Például a contoso.com és a www.contoso.com tartomány DNS-bejegyzéseinek hozzáadásához hozzá kell férnie a contoso.com gyökértartomány DNS-beállításainak konfigurációjához.

Ha még nem rendelkezik tartománynévvel, az [App Service-tartományokkal foglalkozó oktatóanyagban](../app-service/custom-dns-web-site-buydomains-web-app.md) foglaltak szerint vásárolhat egy tartományt az Azure Portal használatával. 

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Nyisson meg egy böngészőt, és keresse fel az [Azure Portalt](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>CDN-profil és -végpont létrehozása

A bal oldali navigációs felületen válassza az **App Services** lehetőséget, majd válassza ki a [statikus HTML gyorsútmutató](../app-service/app-service-web-get-started-html.md) segítségével létrehozott alkalmazást.

![Az App Service alkalmazás kiválasztása a portálon](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

Az **App Service** lap **Beállítások** területén válassza a **Hálózatkezelés > Az Azure CDN konfigurálása az alkalmazáshoz** lehetőséget.

![A CDN kiválasztása a portálon](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

Az **Azure Content Delivery Network** lapon adja meg az **Új végpont** beállításait az alábbi táblának megfelelően.

![Profil és végpont létrehozása a portálon](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Beállítás | Ajánlott érték | Leírás |
| ------- | --------------- | ----------- |
| **CDN-profil** | myCDNProfile | Válassza az **Új létrehozása** lehetőséget egy új CDN-profil létrehozásához. A CDN-profil ugyanabba a tarifacsomagba tartozó CDN-végpontok gyűjteménye. |
| **Tarifacsomag** | Akamai Standard | A [tarifacsomag](cdn-features.md) határozza meg a szolgáltatót és az elérhető szolgáltatásokat. Ez az oktatóanyag a *Standard Akamai* szolgáltatást használja. |
| **CDN-végpont neve** | Bármely egyedi név az azureedge.net tartományban | A gyorsítótárazott erőforrások a *\<végpont_neve>.azureedge.net* tartományban érhetőek el.

Kattintson a **Létrehozás** gombra.

Az Azure létrehozza a profilt és a végpontot. Az új végpont megjelenik a **Végpontok** listában, és a kiosztása után **Fut** állapotra vált.

![Új végpont a listában](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>A CDN-végpont tesztelése

A Verizon tarifacsomag kiválasztása esetén a végpontok propagálása körülbelül 90 percet vesz igénybe. Az Akamai esetében a terjesztés csak néhány percig tart.

Ugyanehhez az alkalmazáshoz tartozik egy *index.html* fájl, valamint *css*, *img* és *js* mappák is, amelyek egyéb statikus objektumokat tartalmaznak. Az összes fájl tartalmának elérési útjai megegyezik a CDN-végponton. Például a következő két URL egyaránt a *bootstrap.css* fájlra mutat a *css* mappában:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

A böngészőben nyissa meg a következő URL-címet:

```
http://<endpointname>.azureedge.net/index.html
```

![A mintaalkalmazás CDN által szolgáltatott kezdőlapja](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 Ugyanaz a lap jelenik meg, amelyet korábban egy Azure-webalkalmazásban futtatott. Az Azure CDN lekérte a forrás webalkalmazás objektumait, és a CDN-végpontról szolgálja ki azokat.

Annak érdekében, hogy a CDN gyorsítótárazza a lapot, frissítse azt. Néha két kérés is szükséges egyazon objektumra vonatkozóan, hogy a CDN gyorsítótárazza a kért tartalmat.

Az Azure CDN-profilok és -végpontok létrehozásával kapcsolatos további információkért lásd: [Az Azure CDN használatának első lépései](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>A CDN végleges törlése

A CDN rendszeres időközönként frissíti az erőforrásait a forrásként szolgáló webappból az élettartam (TTL) konfigurációja alapján. Az alapértelmezett élettartam hét nap.

Esetenként az élettartam lejárta előtt is szükséges lehet a CDN frissítése; például amikor frissített tartalmat továbbít a webalkalmazásba. A frissítés indításához törölje manuálisan a CDN-erőforrásokat. 

Az oktatóanyag jelen szakaszában egy módosítást fog telepíteni a webappba, és törli a CDN-t, hogy az frissítse a gyorsítótárát.

### <a name="deploy-a-change-to-the-web-app"></a>Módosítás telepítése a webappba

Nyissa meg az *index.html* fájlt, és adja hozzá a *- V2* utótagot a H1 fejléchez, ahogy az az alábbi példában látható: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Véglegesítse a módosítást, és telepítse a webappba.

```bash
git commit -am "version 2"
git push azure master
```

Miután befejeződött a telepítés, a böngészőben nyissa meg a webalkalmazás URL-címét, és láthatja a módosított tartalmat.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 a webapp címében](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Ha a böngészőben megnyitja a kezdőlap CDN-végponti URL-címét, nem fogja látni a módosítást, mert a CDN-ben gyorsítótárazott verzió még nem járt le. 

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

A **Végpont** lap tetején válassza a **Végleges törlés** gombot.

![Végleges törlés kiválasztva](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Adja meg a törölni kívánt tartalmak elérési útjait. Megadhat egy teljes elérési útvonalat egy adott fájl törléséhez, vagy egy részleges útvonalat egy adott mappa teljes tartalmának törléséhez és frissítéséhez. Mivel az *index.html* fájlt módosította, mindenképp ez legyen az egyik útvonal.

A lap alján kattintson a **Végleges törlés** gombra.

![Oldal végleges törlése](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>A CDN frissítésének ellenőrzése

Várjon, amíg a végleges törlési kérés feldolgozása befejeződik. Ez általában eltart néhány percig. Az aktuális állapot megtekintéséhez válassza a harang ikont a lap tetején. 

![Törlési értesítés](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Amikor a böngészőben megnyitja az *index.html* fájl CDN-végponti URL-címét, láthatja a kezdőlap címéhez hozzáadott *V2* utótagot, mely jelzi, hogy a CDN-gyorsítótár frissítve lett.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 a CDN-beli címben](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

További információkért lásd az [Azure CDN-végpontok végleges törléséről](../cdn/cdn-purge-endpoint.md) szóló cikket. 

## <a name="use-query-strings-to-version-content"></a>Tartalmak verziószámozása lekérdezési karakterláncok használatával

Az Azure CDN az alábbi gyorsítótárazási lehetőségeket kínálja:

* Lekérdezési karakterláncok figyelmen kívül hagyása
* Lekérdezési karakterláncok gyorsítótárazásának megkerülése
* Minden egyedi URL gyorsítótárazása 

Az első az alapértelmezett beállítás, amely azt jelenti, hogy minden objektumnak csak egy gyorsítótárazott verziója van, függetlenül az URL-címben lévő lekérdezési karakterlánctól. 

Az oktatóanyag ezen szakaszában a gyorsítótárazás működésének módosításával minden egyedi URL-címet gyorsítótárazni fog.

### <a name="change-the-cache-behavior"></a>A gyorsítótárazás működésének módosítása

Az Azure Portal **CDN-végpont** lapján válassza a **Gyorsítótár** lehetőséget.

Válassza a **Minden egyedi URL-cím gyorsítótárazása** lehetőséget a **Lekérdezési karakterláncok gyorsítótárazásának működése** legördülő menüben.

Kattintson a **Mentés** gombra.

![Lekérdezési karakterláncok gyorsítótárazási működésének kiválasztása](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Az egyedi URL-címek külön gyorsítótárazásának ellenőrzése

Nyissa meg a böngészőben a kezdőlapot a CDN-végpont címén úgy, hogy egy lekérdezési karakterláncot is hozzáad a címhez: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Az Azure CDN visszaadja a webalkalmazás aktuális tartalmát, melynek a fejlécében szerepel a *V2* utótag. 

Annak érdekében, hogy a CDN gyorsítótárazza a lapot, frissítse azt. 

Nyissa meg az *index.html* fájlt, módosítsa a *V2* értéket *V3* értékre, majd tegye közzé a módosítást. 

```bash
git commit -am "version 3"
git push azure master
```

Böngészőben nyissa meg a CDN-végponti URL-címet egy új lekérdezési karakterlánccal, például a következővel: `q=2`. Az Azure CDN ekkor lekéri az aktuális *index.html* fájlt, és megjelenik a *V3* utótag. Ha azonban a `q=1` lekérdezési karakterlánccal nyitja meg a CDN-végpontot, a *V2* utótag látható.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 a CDN-beli címben, 2. lekérdezési karakterlánc](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 a CDN-beli címben, 1. lekérdezési karakterlánc](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

Ez a kimenet mutatja, hogy a rendszer minden lekérdezési karakterláncot máshogy kezel:

* Korábban a q=1 volt használatban, amely a gyorsítótárazott tartalmakat adja vissza (V2).
* A q=2 viszont új, ezért a webalkalmazás legfrissebb tartalmait kéri le és adja vissza (V3).

További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Egyéni tartomány leképezése egy CDN-végpontra

Az egyéni tartományt a CDN-végpontra egy CNAME-rekord létrehozásával képezheti le. A CNAME-rekord egy DNS-szolgáltatás, amellyel egy forrástartomány leképezhető egy céltartományra. Leképezheti például a cdn.contoso.com vagy a static.contoso.com tartománynevet a contoso.azureedge.net tartománynévre.

Ha nem rendelkezik egyéni tartománnyal, az [App Service-tartományokkal foglalkozó oktatóanyagban](../app-service/custom-dns-web-site-buydomains-web-app.md) foglaltak szerint vásárolhat egyet az Azure Portal használatával. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>A CNAME-rekordhoz használandó gazdagépnév keresése

Az Azure Portal **Végpont** lapján győződjön meg róla, hogy a bal oldali navigációs felületen az **Áttekintés** lehetőség van kiválasztva, majd válassza a **+ Egyéni tartomány** gombot a lap tetején.

![Egyéni tartomány hozzáadása kiválasztva](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

Az **Egyéni tartomány hozzáadása** lapon látni fogja a CNAME rekord létrehozásához használandó végpont gazdagépnevét. A gazdagépnév CDN-végponti URL-címből van származtatva: **&lt;VégpontNeve>.azureedge.net**. 

![Tartomány hozzáadása lap](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>A CNAME konfigurálása a tartományregisztrálóval

Lépjen a tartományregisztráló webhelyére, és keresse meg a DNS-rekordok létrehozására szolgáló felületet. Ennek a szakasznak a neve lehet például **Tartománynév**, **DNS** vagy **Névkiszolgáló kezelése**.

Keresse meg a CNAME-rekordok kezelésére szolgáló felületet. Ehhez esetleg a különleges beállítások lapjára kell lépnie, és ott a CNAME, Alias vagy Altartományok kifejezést kell keresnie.

Hozzon létre egy CNAME-rekordot, amely leképezi a választott altartományt (például a **statikus** vagy **cdn** altartományt) a portálon korábban bemutatott **végpont gazdagépnevére**. 

### <a name="enter-the-custom-domain-in-azure"></a>Az egyéni tartománynév megadása az Azure-ban

Lépjen vissza az **Egyéni tartomány hozzáadása** lapra, és adja meg az egyéni tartományt az altartománnyal együtt a párbeszédpanelen. Írja be például a *cdn.contoso.com* tartománynevet.   
   
Az Azure ellenőrzi, hogy a megadott tartománynév esetében létezik-e a CNAME-rekord. Ha a CNAME helyes, az egyéni tartomány érvényesítve lesz.

Időbe telhet, amíg megtörténik a CNAME-rekord névkiszolgálókon való propagálása az interneten. Ha a tartomány érvényesítése nem történik meg azonnal, várjon néhány percet, majd próbálkozzon újra.

### <a name="test-the-custom-domain"></a>Az egyéni tartomány tesztelése

Egy böngészőben nyissa meg az *index.html* fájlt az egyéni tartománynév használatával (például cdn.contoso.com/index.html) annak ellenőrzéséhez, hogy az eredmény ugyanaz, mintha közvetlenül az &lt;endpointname&gt;azureedge.net/index.html helyre lépett volna.

![Mintaalkalmazás egyéni tartományi URL-címet használó kezdőlapja](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

További információkért lásd: [Azure CDN-tartalom leképezése egyéni tartományra](../cdn/cdn-map-content-to-custom-domain.md)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>További lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * CDN-végpont létrehozása.
> * Gyorsítótárazott objektumok frissítése.
> * Gyorsítótárazott verziók felügyelete lekérdezési karakterláncok használatával.
> * Egyéni tartomány használata a CDN-végponthoz.

A CDN teljesítményének optimalizálását a következő cikkekben sajátíthatja el:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md)



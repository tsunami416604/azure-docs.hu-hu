---
title: 'A következő cikket: személyre szabása - Azure kognitív szolgáltatások |} Microsoft Docs'
description: A cikk testre szabási célból Azure szolgáltatással egyéni döntési, egy felhőalapú API környezetfüggő döntéshozatali oktatóanyag.
services: cognitive-services
author: slivkins
manager: slivkins
ms.service: cognitive-services
ms.topic: article
ms.date: 05/08/2018
ms.author: slivkins;marcozo;alekh;marossi
ms.openlocfilehash: 35d0567f81a23d4726461059eb6fd31e04228697
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349010"
---
# <a name="article-personalization"></a>A cikk személyre szabása

Ez az oktatóanyag összpontosít személyre szabása cikkek az első oldalon egy webhely kiválasztása. Az egyéni döntési szolgáltatásra milyen hatással van *több* elején cikkek jegyzéke például lapon. Lehet, hogy a lap, amely lefedi csak politika és sport hírek webhely. Azt kellene megjelenítése cikkek három rangsorolt listáját: politika, sport, és a legutóbbi.

## <a name="applications-and-action-sets"></a>Alkalmazások és a művelet beállítása

Megtudhatja, hogyan illeszthető be a keretrendszer az adott esetben. Most tegyük fel három kérelmeket, amelyek minden optimalizált alatt álló listát: alkalmazás-politika, alkalmazás-sport, és alkalmazás-legutóbbi. Minden egyes alkalmazás esetében a jelölt cikkek megadásához nincsenek két művelet készlet: egy politika, egy, a sport. A művelet, állítsa be az alkalmazás-legutóbbi automatikusan, egy a többi két készlet unióját származnak.

> [!TIP]
> Művelet beállítja a különböző alkalmazások az egyéni döntési Service megosztható legyen.

## <a name="prepare-action-set-feeds"></a>A művelet set hírcsatornák előkészítése

Egyéni döntési szolgáltatás használ a művelet beállítja az ügyfél által megadott RSS- vagy Atom hírcsatornák keresztül. Megadja a két hírcsatornák: egy politika, egy, a sport. Tegyük fel, hogy azok a szolgáltatott `http://www.domain.com/feeds/<feed-name>`.

Minden egyes hírcsatorna a cikkek listáját tartalmazza. Az RSS, minden egyes megadott egy `<item>` elemet, az alábbiak szerint:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

A cikkek kérdések sorrendjét. Azt adja meg az alapértelmezett rangsorolási, amely a legjobb találat, hogyan kell rendelni a cikkek esetében. Az alapértelmezett rangsorolási használja teljesítmény összehasonlítása a a [irányítópult](#performance-dashboard).

Az adatcsatorna formátumú további információkért lásd: a [API-referencia](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Új alkalmazás regisztrálásához

1. Jelentkezzen be a [Microsoft-fiók](https://account.microsoft.com/account). Kattintson a menüszalagon **saját Portal**.

2. Új alkalmazás regisztrálásához kattintson a **új alkalmazás** gombra.

    ![Custom Decision Service-portál](./media/custom-decision-service-tutorial/portal.png)

3. Adjon meg egy egyedi nevet az alkalmazás a **Alkalmazásazonosító** szövegmezőben. Ha ezt a nevet már használja egy másik ügyfél, a rendszer arra kéri, hogy válasszon egy másik alkalmazás. Válassza ki a **speciális** jelölőnégyzetet, majd adja meg a [kapcsolati karakterlánc](../../storage/common/storage-configure-connection-string.md) az Azure-tárfiókot. Általában használhatja ugyanazt a tárfiókot az alkalmazások számára.

    ![Új alkalmazás párbeszédpanel](./media/custom-decision-service-tutorial/new-app-dialog.png)

    A fenti forgatókönyvben a mindhárom alkalmazás regisztrálása után szerepelnek:

    ![Alkalmazások listája](./media/custom-decision-service-tutorial/apps.png)

    Akkor is térjen vissza erre a listára kattintva a **alkalmazások** gombra.

4. Az a **új alkalmazás** párbeszédpanelen adja meg egy művelet adatcsatorna. Művelet hírcsatornák kattintva is megadható a **hírcsatornák** gombra kattint, és a **új adatcsatorna** gombra. Adjon meg egy **neve** az új adatcsatorna, adja meg a **URL-cím** ahonnan kiszolgált, és írja be a **frissítése idő**. A frissítési időt határozza meg, milyen gyakran egyéni döntési szolgáltatás frissíteni kell a csatornáról.

    ![Új adatcsatorna párbeszédpanel](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Művelet hírcsatornák használható, amelyet bármelyik olyan alkalmazás, függetlenül attól, hol van megadva. Mindkét művelet hírcsatornák esetén adja meg, miután szerepelnek:

    ![Hírcsatornák listája](./media/custom-decision-service-tutorial/feeds.png)

    Akkor is térjen vissza erre a listára kattintva a **hírcsatornák** gombra.

## <a name="use-the-apis"></a>Az API-k

Az egyéni döntési szolgáltatás rangsorolja cikkeket a prioritás API-n keresztül. Ez az API használatához a következő kód beszúrása az első oldal HTML vezetője:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

A prioritás API HTTP-válasz: JSONP-formátumú karakterlánc. Az alkalmazás-politika például a karakterlánc néz ki:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

A böngésző végrehajtja ezt a karakterláncot, hívása a `callback()` függvény. A `data` argumentumának a `callback()` függvény tartalmazza az alkalmazás Azonosítóját és az URL-címek rangsorolási állnak a megjelenítésre. Különösen `callback()` használandó `data.appId` segítségével különböztetheti meg egymástól a három alkalmazások között. `eventId` belsőleg egyéni döntési szolgáltatás kell egyeznie a megfelelő kattintással, a megadott prioritás, ha van ilyen.

> [!TIP]
> `callback()` Előfordulhat, hogy ellenőrizze az egyes műveletek során a frissesség a hírcsatorna a `lastRefresh` mező. Ha egy adott adatcsatorna nincs elég friss `callback()` előfordulhat, hogy figyelmen kívül hagyja a megadott prioritás, ez az adatcsatorna hívja közvetlenül és használja az alapértelmezett rangsorolási az adatcsatorna szolgálja ki.

A specifikációk és a prioritás API által biztosított további beállítások további információkért lásd: a [API-referencia](custom-decision-service-api-reference.md).

A legfelső cikk lehetőségek a felhasználó adta a ellenszolgáltatás API felület meghívásakor. Amikor egy legfelső cikk választott érkezik, a következő kódot akkor szabadna meghívni, az első oldalon:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Használatával `appId` és `eventId` a kattintson hibakezelési kód használatához szükséges néhány eljárni. Megvalósíthat például a `callback()` működéséhez az alábbiak szerint:

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

Ebben a példában valósítja meg a `render()` függvény leképezése egy adott cikk egy adott alkalmazáshoz. Ez a függvény a bemeneti alkalmazás Azonosítóját és a cikk (formátumban a prioritás API Felületről). A `onClick` paraméter kell meghívni, a függvény `render()` egy kattintással kezelésére. Ellenőrzi, hogy a kattintson a felső bővítőhely. Ezután meghívja a ellenszolgáltatás API-t a megfelelő alkalmazás azonosító és az esemény.

## <a name="next-steps"></a>További lépések

* Tekintse át a [API-referencia](custom-decision-service-api-reference.md) további információt a megadott funkciót.
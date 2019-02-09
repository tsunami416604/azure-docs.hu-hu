---
title: 'Oktatóanyag: A cikk személyre szabása – Custom Decision Service'
titlesuffix: Azure Cognitive Services
description: Oktatóanyag a cikk személyre szabásról környezetalapú döntéshozatalhoz.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: d8ddafe20ff93e7ae4d51e2180bbd40447729234
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983032"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Oktatóanyag: A cikk személyre szabása, a környezeti döntéshozatalhoz

Ez az oktatóanyag a webhely kezdőlapján megjelenő cikkek személyre szabásával foglalkozik. A Custom Decision Service például *több* cikk listát is befolyásol a kezdőlapon. Lehet szó például híroldalról, ami csak politikával és sporttal foglalkozik. A megjelenített cikkek rangsorolt listája ebben az esetben lehet: politika, sport és legújabb.

## <a name="applications-and-action-sets"></a>Alkalmazás és művelet halmazok

Íme, így lehet a saját forgatókönyvét a keretrendszerbe illeszteni. Képzeljünk el három alkalmazást, mindegyik optimalizált listához egyet: app-politika, app-sport és app-legujabb. Az egyes alkalmazásokhoz a jelölt cikkeket két műveleti halmazzal adjuk meg: egy a politikához és egy a sporthoz. Az app-legujabb műveleti halmaza a másik két halmaz uniójaként automatikusan adódik.

> [!TIP]
> A műveleti halmazok a Custom Decision szolgáltatásban lehetnek közösek alkalmazások között.

## <a name="prepare-action-set-feeds"></a>A műveleti halmaz hírcsatornák elkészítése

A Custom Decision szolgáltatás az ügyfél által megadott RSS és Atom hírcsatornákon keresztül fogadja a műveleti halmazokat. Két hírcsatornát ad meg: egyet a politikához és egyet a sporthoz. Tegyük fel, hogy a `http://www.domain.com/feeds/<feed-name>` szolgáltatja őket.

Mindegyik hírcsatorna cikkek listáját adja. Az RSS esetén mindegyiket egy `<item>` elem határozza meg a következőképpen:

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

A cikkek sorrendje számít. Megadja az alapértelmezett rangsort, ami a legjobb tipp arra, hogyan kellene a cikkeket rendezni. Alapértelmezés szerint a rangsorolás szolgál majd teljesítmény összehasonlítása az irányítópulton.

A hírcsatorna formátummal kapcsolatban bővebben lásd: [API-referencia](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Új alkalmazás regisztrálása

1. Bejelentkezés saját [Microsoft-fiókkal](https://portal.ds.microsoft.com/). Kattintson a menüszalagon a **Saját Portál** gombra.

2. Új alkalmazás regisztrálásához kattintson az **Új App** gombra.

    ![Custom Decision Service-portál](./media/custom-decision-service-tutorial/portal.png)

3. Adjon egyedi nevet az alkalmazásnak az **Alkalmazásazonosító** szövegmezőben. Ha ezt a nevet már használja egy másik ügyfél, a rendszer kérni fogja, hogy válasszon másik alkalmazásazonosítót. Válassza ki a **Speciális** jelölőnégyzetet, majd adja meg a [kapcsolati karakterláncot](../../storage/common/storage-configure-connection-string.md) az Azure tárfiókhoz. Normál esetben ugyanazt a tárfiókot használja az összes alkalmazáshoz.

    ![Új alkalmazás párbeszédpanel](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Miután regisztrálta mindhárom alkalmazást a fenti esetben, megjelenik a listájuk:

    ![Alkalmazások listája](./media/custom-decision-service-tutorial/apps.png)

    Az **Apps** gombra kattintva visszatérhet erre a listára.

4. Az **Új alkalmazás** párbeszédpanelen adja meg a műveleti hírcsatornát. A művelet hírcsatornák megadhatók a **Hírcsatornák** gombra, majd az **Új hírcsatorna** gombra kattintva is. Adjon **nevet** az új hírcsatornának, adja meg hozzá a szolgáltató **URL-címet**, majd adja meg a **frissítési időt**. A frissítési idő adja meg, milyen gyakran kell a Custom Decision szolgáltatásnak a hírcsatornát frissítenie.

    ![Új hírcsatorna párbeszédpanel](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    A műveleti hírcsatornákat bármely alkalmazás használhatja, függetlenül attól, hol lettek megadva. Miután megadta mindkét műveleti hírcsatornát az adott esetben, megjelenik a listájuk:

    ![Hírcsatornák listája](./media/custom-decision-service-tutorial/feeds.png)

    A **Hírcsatorna** gombra kattintva visszatérhet erre a listára.

## <a name="use-the-apis"></a>Az API-k használata

A Custom Decision Service a cikkeket a Ranking API-n keresztül rangsorolja. Ennek az API-nak a használatához illessze a következő kódot a Kezdőlap HTML fejrészébe:

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

A Ranking-API által adott HTTP-válasz JSONP-formátumú karakterlánc. Az app-politika esetén a karakterlánc például így néz ki:

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

A böngésző utána a `callback()` függvény meghívásaként értelmezve végrehajtja ezt a karakterláncot. Az `data` argumentumok tartalmazzák a `callback()` függvényben az alkalmazásazonosítót és a megjelenítendő URL-ek rangsorát. A három alkalmazás megkülönböztetéséhez különösen szükséges `callback()` által a `data.appId` használata. A `eventId` belső használatára szolgál, a Custom Decision Service vele párosítja a megadott rangsort a megfelelő kattintáshoz, ha van.

> [!TIP]
> `callback()` ellenőrizheti a műveleti hírcsatornák frissességét a `lastRefresh` mező használatával. Ha egy adott hírcsatorna nem elég friss, előfordulhat, hogy `callback()` figyelmen kívül hagyja a megadott rangsort, közvetlenül meghívja a hírcsatornát, és a hírcsatorna által megadott alapértelmezett rangsort használja.

A Ranking API specifikációkat és egyéb kiegészítő lehetőségeket lásd az [API-referenciában](custom-decision-service-api-reference.md).

A felhasználó legfőbb cikk választásai a Reward API meghívásával kaphatók meg. Legfelső cikk választás fogadásakor a következő kódot kell meghívni az első oldalon:

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

Az `appId` és `eventId` használata a kattintás-kezelő kódban némi figyelmet igényel. Megvalósíthatja a `callback()` függvényt például az alábbi módon:

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

Ebben a példában a `render()` függvény egy adott cikket jelenít meg egy adott alkalmazás számára. A függvény bemenete az alkalmazásazonosító és a cikk (a rangsorolás API-ból érkezett formátumban). A `onClick` paraméter az a függvény, amelyet a `render()`-ből a kattintás lekezelésére meg kell hívni. Azt ellenőrzi, hogy a kattintás a felső részen van-e. Ezután meghívja a Reward API-t a megfelelő alkalmazásazonosítóval és eseményazonosítóval.

## <a name="next-steps"></a>További lépések

* Az [API-referencia](custom-decision-service-api-reference.md) még további információkat tartalmaz a nyújtott funkciókról.

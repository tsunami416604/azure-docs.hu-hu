---
title: Webes API-k létrehozása & REST API-khoz az Azure Logic Apps alkalmazáshoz
description: Hozzon létre webes API-kat & REST API-kat, hogy meghívja API-kat, szolgáltatásokat vagy rendszereket az Azure Logic Apps rendszerintegrációihoz
services: logic-apps
ms.suite: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: bb6c99ea12e5b53631d42a04b36b7bfef2337e42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270536"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Egyéni API-k létrehozása, amelyeket az Azure Logic Apps alkalmazásból hívhat

Bár az Azure Logic Apps [több száz összekötőt](../connectors/apis-list.md) kínál, amelyeket a logikai alkalmazások munkafolyamataiban használhat, érdemes lehet meghívni a nem összekötőként elérhető API-kat, rendszereket és szolgáltatásokat. Létrehozhat saját API-kat, amelyek a logikai alkalmazásokban használható műveleteket és eseményindítókat biztosítanak. Az alábbiakban további okokat is találhat, amelyek miatt érdemes létrehozni a saját API-kat, amelyeket a logikai alkalmazás munkafolyamataiból hívhat meg:

* Bővítse ki jelenlegi rendszerintegrációs és adatintegrációs munkafolyamatait.
* Segítsen az ügyfeleknek a szolgáltatás használatával a szakmai vagy személyes feladatok kezeléséhez.
* Bővítse ki a szolgáltatás elérését, felderíthetőségét és használatát.

Alapvetően az összekötők olyan webes API-k, amelyek REST-et használnak a csatlakoztatható felületekhez, [a Swagger metaadat-formátumot](https://swagger.io/specification/) a dokumentációhoz, és a JSON-t adatcsere-formátumként. Mivel az összekötők REST API-k, amelyek HTTP-végpontokon keresztül kommunikálnak, bármilyen nyelvet használhat, például .NET, Java, Python vagy Node.js összekötők létrehozásához. Az API-kat az [Azure App Service](../app-service/overview.md)platformszolgáltatásként (PaaS) is üzemeltetheti, amely az API-üzemeltetés egyik legjobb, legegyszerűbb és leginkább méretezhető módját kínálja. 

Ahhoz, hogy az egyéni API-k együttműködjenek a logikai alkalmazásokkal, az API olyan [*műveleteket*](./logic-apps-overview.md#logic-app-concepts) biztosíthat, amelyek meghatározott feladatokat hajtanak végre a logikai alkalmazások munkafolyamataiban. Az API is működhet, mint egy [*eseményindító,*](./logic-apps-overview.md#logic-app-concepts) amely elindítja a logikai alkalmazás munkafolyamat, amikor új adatok vagy egy esemény megfelel egy adott feltételnek. Ez a témakör ismerteti a gyakori mintákat, amelyeket követhet az API-ban végrehajtott műveletek és eseményindítók létrehozásához, az API által biztosítani kívánt viselkedés alapján.

Api-kat az [Azure App Service](../app-service/overview.md)platformszolgáltatásként (PaaS) üzemeltethet, amely rendkívül méretezhető, egyszerű API-üzemeltetést biztosít.

> [!TIP] 
> Bár az API-kat webalkalmazásokként is üzembe helyezheti, fontolja meg az API-k API-alkalmazásokként való üzembe helyezését, amelyek megkönnyítik a munkát, amikor api-kat hoz létre, üzemeltet és használ fel a felhőben és a helyszínen. Nem kell módosítania az API-kban lévő kódot – csak telepítse a kódot egy API-alkalmazásba. Például ismerje meg, hogyan hozhat létre az alábbi nyelvekkel létrehozott API-alkalmazásokat: 
> 
> * [ASP.NET.](../app-service/app-service-web-get-started-dotnet.md) 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [Php](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> A logikai alkalmazásokhoz készült API-alkalmazásmintákért keresse fel az [Azure Logic Apps GitHub-tárházat](https://github.com/logicappsio) vagy [-blogot.](https://aka.ms/logicappsblog)

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Miben különböznek az egyéni API-k az egyéni összekötőktől?

Az egyéni API-k és [az egyéni összekötők](../logic-apps/custom-connector-overview.md) olyan webes API-k, amelyek REST-et használnak a csatlakoztatható felületekhez, [a Swagger metaadat-formátumot](https://swagger.io/specification/) a dokumentációhoz, és a JSON-t adatcsere-formátumként. És mivel ezek az API-k és összekötők REST API-k, amelyek HTTP-végpontokon keresztül kommunikálnak, bármilyen nyelvet használhat, például a .NET, java, Python vagy Node.js, egyéni API-k és összekötők létrehozásához.

Az egyéni API-k lehetővé teszik, hogy olyan API-kat hívjon meg, amelyek nem összekötők, és olyan végpontokat biztosíthat, amelyeket http + Swagger, Azure API Management vagy App Services használatával hívhat meg. Az egyéni összekötők egyéni API-ként működnek, de rendelkeznek az alábbi attribútumokkal is:

* Logic Apps Connector-erőforrásként regisztrálva az Azure-ban.
* A Logic Apps Designerben ikonok jelennek meg a Microsoft által felügyelt összekötők mellett.
* Csak az összekötők szerzői és a logikai alkalmazás felhasználói számára érhető el, akik azonos Azure Active Directory-bérlői és Azure-előfizetéssel rendelkeznek abban a régióban, ahol a logikai alkalmazások telepítve vannak.

A Microsoft minősítéshez regisztrált összekötőket is kijelölhet. Ez a folyamat ellenőrzi, hogy a regisztrált összekötők megfelelnek-e a nyilvános használatra vonatkozó feltételeknek, és elérhetővé teszi ezeket az összekötőket a Power Automate és a Microsoft Power Apps felhasználói számára.

Az egyéni összekötőkről további információt a 

* [Egyéni összekötők áttekintése](../logic-apps/custom-connector-overview.md)
* [Egyéni összekötők létrehozása webes API-kból](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Egyéni összekötők regisztrálása az Azure Logic Apps alkalmazásban](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Hasznos eszközök

Az egyéni API-k akkor működnek a legjobban a logikai alkalmazásokkal, ha az API-hoz egy [Swagger-dokumentum](https://swagger.io/specification/) is rendelkezik, amely leírja az API műveleteit és paramétereit.
Számos könyvtár, például [a Swashbuckle,](https://github.com/domaindrivendev/Swashbuckle)automatikusan generálhatja a Swagger fájlt. A Swagger-fájl felirattal a megjelenítendő nevek, tulajdonságtípusok, és így tovább, akkor is használhatja [a TRex,](https://github.com/nihaue/TRex) hogy a Swagger fájl jól működik a logikai alkalmazások.

<a name="actions"></a>

## <a name="action-patterns"></a>Akcióminták

A logikai alkalmazások feladatok végrehajtásához az egyéni API-nak műveleteket kell [*biztosítania.*](./logic-apps-overview.md#logic-app-concepts) Az API-ban minden művelet egy művelethez van rendelve. Az alapvető művelet egy olyan vezérlő, amely elfogadja a HTTP-kéréseket, és HTTP-válaszokat ad vissza. Így például egy logikai alkalmazás http-kérelmet küld a webalkalmazás vagy AZ API-alkalmazás. Az alkalmazás ezután egy HTTP-választ ad vissza, valamint a logikai alkalmazás által feldolgozható tartalmat.

Szabványos művelet esetén írhat egy HTTP-kérelem metódust az API-ban, és ezt a metódust egy Swagger-fájlban írhatja le. Ezután az API-t közvetlenül meghívhat egy [HTTP-művelettel](../connectors/connectors-native-http.md) vagy egy [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) művelettel. Alapértelmezés szerint a válaszokat a [kérelem időkorláton](./logic-apps-limits-and-config.md)belül kell visszaküldeni. 

![Szokásos műveletminta](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a>Ahhoz, hogy egy logikai alkalmazás várjon, amíg az API befejezi a hosszabb ideig futó feladatokat, az API követheti az [aszinkron lekérdezési mintát,](#async-pattern) vagy az ebben a témakörben leírt [aszinkron webhook-mintát.](#webhook-actions) Egy analógia, amely segít elképzelni ezeket a mintákat a különböző viselkedések, elképzelni a folyamat megrendelése egyéni torta egy pékségben. A lekérdezési minta tükrözi a viselkedést, ahol hívja a pékség 20 percenként, hogy ellenőrizze, hogy a torta készen áll. A webhook minta tükrözi a viselkedést, ahol a pékség kéri a telefonszámát, így lehet hívni, ha a torta készen áll.

A mintákért keresse fel a [Logic Apps GitHub-tárházat.](https://github.com/logicappsio) További információ a [műveletek használati méréséről](logic-apps-pricing.md)is.

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>Hosszan futó feladatok végrehajtása a lekérdezési műveletmintával

Ha azt szeretné, hogy az API olyan feladatokat hajtson végre, amelyek hosszabb ideig futhatnak, mint a [kérelem időkorlátja,](./logic-apps-limits-and-config.md)használhatja az aszinkron lekérdezési mintát. Ez a minta rendelkezik az API-t egy külön szál, de tartsa aktív kapcsolatot a Logic Apps motor. Így a logikai alkalmazás nem időtúllépés, vagy folytassa a következő lépés a munkafolyamatban, mielőtt az API befejezi a munkát.

Itt az általános minta:

1. Győződjön meg arról, hogy a motor tudja, hogy az API elfogadta a kérést, és megkezdte a munkát.
2. Amikor a motor további kérelmeket küld a feladat állapotára vonatkozóan, tudassa a motorral, ha az API befejezi a feladatot.
3. A megfelelő adatok visszaküldése a motorba, hogy a logikai alkalmazás munkafolyamata folytatódhasson.

<a name="bakery-polling-action"></a>Most alkalmazza az előző pékség analógia a szavazási mintát, és elképzelni, hogy hívja a pékség, és annak érdekében, egyéni torta szállítás. A folyamat, hogy a torta időt vesz igénybe, és nem akar várni a telefonon, míg a pékség működik a tortán. A pékség megerősíti a rendelést, és 20 percenként felhívja a tortát a torta állapotáért. 20 perc elteltével felhívod a pékséget, de azt mondják, hogy a tortád még nincs kész, és hogy még 20 perc múlva telefonálnod kell. Ez oda-vissza folyamat folytatódik, amíg nem hívja, és a pékség azt mondja, hogy a megrendelés készen áll, és szállítja a tortát. 

Szóval térképezzük fel ezt a szavazómintát. A pékség az egyéni API-t képviseli, míg Ön, a torta-ügyfél a Logic Apps motort képviseli. Amikor a motor meghívja az API-t egy kéréssel, az API megerősíti a kérést, és válaszol az időintervallummal, amikor a motor ellenőrizheti a feladat állapotát. A motor folytatja a feladat állapotának ellenőrzését, amíg az API válaszol, hogy a feladat befejeződött, és adatokat ad vissza a logikai alkalmazásnak, amely ezután folytatja a munkafolyamatot. 

![Lekérdezési művelet minta](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Az API által követendő konkrét lépéseket az API szemszögéből ismertetjük:

1. Amikor az API http-kérelmet kap a munka `202 ACCEPTED` megkezdéséhez, azonnal adjon vissza egy HTTP-választ a fejléc későbbi ebben a `location` lépésben leírt. Ez a válasz lehetővé teszi, hogy a Logic Apps motor tudja, hogy az API megkapta a kérelmet, elfogadta a kérelem hasznos adatát (adatbevitel), és most feldolgozása. 
   
   A `202 ACCEPTED` válasznak tartalmaznia kell a következő fejléceket:
   
   * *Kötelező:* `location` Olyan fejléc, amely megadja egy URL abszolút elérési útját, ahol a Logic Apps motor ellenőrizheti az API feladatállapotát

   * *Nem kötelező:* Olyan `retry-after` fejléc, amely megadja, hogy a `location` motornak hány másodpercet kell várnia, mielőtt ellenőrizné a feladat állapotát. 

     Alapértelmezés szerint a motor 20 másodpercenként ellenőrzi. Eltérő időköz megadásához `retry-after` adja meg a fejlécet és a következő lekérdezésig eltelt másodpercek számát.

2. A megadott idő eltelte után a Logic `location` Apps motor lekérdezi az URL-címet a feladat állapotának ellenőrzéséhez. Az API-nak végre kell hajtania ezeket az ellenőrzéseket, és vissza kell adnia a válaszokat:
   
   * Ha a feladat befejeződött, `200 OK` adjon vissza egy HTTP-választ, valamint a válasz hasznos adatát (a következő lépés bemenetét).

   * Ha a feladat feldolgozása még `202 ACCEPTED` mindig feldolgozás, adjon vissza egy másik HTTP-választ, de ugyanazokkal a fejlécekkel, mint az eredeti válasz.

Ha az API követi ezt a mintát, nem kell semmit tennie a logikai alkalmazás munkafolyamat-definíciójában a feladat állapotának ellenőrzéséhez. Amikor a motor `202 ACCEPTED` http-választ `location` és érvényes fejlécet kap, a motor tiszteletben `location` tartja az aszinkron mintát, és ellenőrzi a fejlécet, amíg az API nem 202-es választ ad vissza.

> [!TIP]
> Például aszinkron minta, tekintse át ezt az [aszinkron vezérlő válaszminta a GitHubon.](https://github.com/logicappsio/LogicAppsAsyncResponseSample)

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>Hosszú ideig futó feladatok végrehajtása a webhook műveletmintával

Alternatív megoldásként használhatja a webhook-mintát a hosszú ideig futó feladatokhoz és az aszinkron feldolgozáshoz. Ez a minta rendelkezik a logikai alkalmazás szüneteltetése, és várja meg a "visszahívás" az API-ból a feldolgozás befejezése a munkafolyamat folytatása előtt. Ez a visszahívás egy HTTP-posta, amely üzenetet küld egy URL-címre, amikor egy esemény bekövetkezik. 

<a name="bakery-webhook-action"></a>Most alkalmazza az előző pékség analógia a webhook mintát, és elképzelni, hogy hívja a pékség, és annak érdekében, hogy egy egyéni torta szállítás. A folyamat, hogy a torta időt vesz igénybe, és nem akar várni a telefonon, míg a pékség működik a tortán. A pékség megerősíti a rendelést, de ezúttal megadja nekik a telefonszámát, hogy felhívhassák, ha a torta kész. Ez alkalommal, a pékség megmondja, ha a megrendelés készen áll, és szállítja a tortát.

Amikor ezt a webhook mintát vissza, a pékség képviseli az egyéni API-t, míg a torta ügyfél, képviseli a Logic Apps motor. A motor meghívja az API-t egy kéréssel, és tartalmaz egy "visszahívási" URL-címet.
Amikor a feladat befejeződött, az API az URL-címet használja a motor értesítésére, és adatokat ad vissza a logikai alkalmazásnak, amely ezután folytatja a munkafolyamatot. 

Ehhez a mintához állítson be két `subscribe` végpontot a kontrolleren: és`unsubscribe`

*  `subscribe`végpont: Amikor a végrehajtás eléri az API-t a munkafolyamatban, `subscribe` a Logic Apps motor meghívja a végpontot. Ez a lépés hatására a logikai alkalmazás hozzon létre egy visszahívási URL-t, amely az API tárolja, és majd várja meg a visszahívást az API-ból, ha a munka befejeződött. Az API ezután http-bejegyzéssel visszahívja az URL-címet, és továbbítja a visszaadott tartalmakat és fejléceket a logikai alkalmazásba való bevitelként.

* `unsubscribe`végpont: Ha a logikai alkalmazás futtatása megszakad, `unsubscribe` a Logic Apps motor meghívja a végpontot. Az API ezután törölje a visszahívási URL-cím regisztrációját, és szükség szerint leállíthatja a folyamatokat.

![Webhook műveletminta](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Jelenleg a Logic App Designer nem támogatja a webhook-végpontok swaggeren keresztül történő felderítése. Így ehhez a mintához hozzá kell adnia egy [ **Webhook-műveletet,** ](../connectors/connectors-native-webhook.md) és meg kell adnia a kérés URL-címét, fejlécét és törzsét. Lásd [még: Munkafolyamat-műveletek és eseményindítók.](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action) A visszahívási URL-cím átadásához `@listCallbackUrl()` szükség szerint használhatja a munkafolyamat-függvényt az előző mezők bármelyikében.

> [!TIP]
> Például webhook-minta, tekintse át ezt a [webhook-trigger minta A GitHub.](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Eseményindító minták

Az egyéni [*API-t egy eseményindító,*](./logic-apps-overview.md#logic-app-concepts) amely elindítja a logikai alkalmazást, amikor új adatok vagy egy esemény megfelel egy adott feltételnek. Ez az eseményindító rendszeresen ellenőrizheti, vagy várjon és figyelje az új adatokat vagy eseményeket a szolgáltatás végpontján. Ha új adatok vagy esemény megfelel a megadott feltételnek, az eseményindító aktiválódik, és elindítja a logikai alkalmazást, amely figyeli az eseményindítót. Logikai alkalmazások így indításához az API követheti a [*lekérdezési eseményindító*](#polling-triggers) vagy a [*webhook-trigger*](#webhook-triggers) minta. Ezek a minták hasonlóak a társaik a [közvélemény-kutatás intézkedések](#async-pattern) és [webhook akciók](#webhook-actions). További információ az [eseményindítók használati méréséről](logic-apps-pricing.md)is.

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Új adatok vagy események rendszeres ellenőrzése a lekérdezési eseményindító mintával

A *lekérdezési eseményindító* hasonlóan működik a témakörben korábban ismertetett [lekérdezési művelethez.](#async-pattern) A Logic Apps motor rendszeresen felhívja, és ellenőrzi az eseményindító végpont új adatok vagy események. Ha a motor új adatokat vagy a megadott feltételnek megfelelő eseményt talál, az eseményindító aktiválódik. Ezután a motor létrehoz egy logikai alkalmazáspéldányt, amely az adatokat bemenetként dolgozza fel. 

![Lekérdezési eseményindító minta](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Minden lekérdezési kérelem műveletvégrehajtásnak számít, még akkor is, ha nem jön létre logikai alkalmazáspéldány. Ugyanazon adatok többszöri feldolgozásának megakadályozása érdekében az eseményindítónak meg kell tisztítania a már elolvasott és a logikai alkalmazásnak átadott adatokat.

Az API szemszögéből ismertetett lekérdezési eseményindító konkrét lépései:

| Új adatokat vagy eseményt talált?  | API-válasz | 
| ------------------------- | ------------ |
| Találat | Http-állapotot `200 OK` ad vissza a válasz hasznos adatával (a következő lépésbemenet). <br/>Ez a válasz létrehoz egy logikai alkalmazáspéldányt, és elindítja a munkafolyamatot. | 
| Nem található | HTTP-állapot `202 ACCEPTED` ot `location` ad `retry-after` vissza fejléccel és fejléccel. <br/>Az eseményindítók `location` esetében a `triggerState` fejlécnek tartalmaznia kell egy lekérdezési paramétert is, amely általában egy "időbélyeg". Az API használhatja ezt az azonosítót nyomon követheti az utolsó alkalommal, amikor a logikai alkalmazás aktiválódott. | 
||| 

Ha például rendszeresen ellenőrizni szeretné a szolgáltatásúj fájlokat, készíthet egy lekérdezési eseményindítót, amely az alábbi viselkedéseket tartalmaz:

| A `triggerState`kérelem tartalmazza a ? | API-válasz | 
| -------------------------------- | -------------| 
| Nem | Http-állapotot `202 ACCEPTED` és `location` fejlécet ad vissza `retry-after` az aktuális időre és az időköz15 másodpercre állítva. `triggerState` | 
| Igen | Ellenőrizze a szolgáltatásban a `DateTime` `triggerState`(- fájl) után hozzáadott fájlokat. | 
||| 

| Talált fájlok száma | API-válasz | 
| --------------------- | -------------| 
| Egyetlen fájl | `200 OK` Http-állapotot és a tartalom `triggerState` hasznos `DateTime` tartalmát adja vissza, `retry-after` frissítse a visszaadott fájlt, és állítsa az időközt 15 másodpercre. | 
| Több fájl | Egyszerre egy fájlt és egy `200 OK` HTTP-állapotot ad vissza, frissítse `triggerState`a fájlt, és állítsa az `retry-after` időközt 0 másodpercre. </br>Ezek a lépések tudatják a motorral, hogy további adatok állnak rendelkezésre, `location` és hogy a motornak azonnal kérnie kell az adatokat a fejlécben lévő URL-címről. | 
| Nincsenek fájlok | Http-állapotot `202 ACCEPTED` ad vissza, ne módosítsa `triggerState`a t, és állítsa az `retry-after` időközt 15 másodpercre. | 
||| 

> [!TIP]
> Egy példa lekérdezési eseményindító minta, tekintse át ezt a [lekérdezési eseményindító vezérlő minta A GitHub.](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs)

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Várjon és hallgassa az új adatokat vagy eseményeket a webhook-trigger mintával

A webhook-eseményindító egy *leküldéses eseményindító,* amely megvárja, és figyeli az új adatokat vagy eseményeket a szolgáltatás végpontján. Ha új adatok vagy esemény megfelel a megadott feltételnek, az eseményindító aktiválódik, és létrehoz egy logikai alkalmazáspéldányt, amely ezután feldolgozza az adatokat bemenetként.
A Webhook-eseményindítók ugyanúgy működnek, mint a témakörben `subscribe` `unsubscribe` korábban ismertetett [webhook-műveletek,](#webhook-actions) és végpontokkal vannak beállítva. 

* `subscribe`végpont: Amikor hozzáad és ment egy webhook-eseményindítót a logikai alkalmazásban, a Logic Apps motor meghívja a `subscribe` végpontot. Ez a lépés hatására a logikai alkalmazás hozzon létre egy visszahívási URL-t, amely az API tárolja. Ha új adatok vagy olyan esemény történik, amely megfelel a megadott feltételnek, az API http-bejegyzéssel hívja vissza az URL-címet. A tartalom hasznos tartalma és a fejlécek a logikai alkalmazás bemeneteként haladnak át.

* `unsubscribe`végpont: Ha a webhook-eseményindító vagy a teljes logikai `unsubscribe` alkalmazás törlődik, a Logic Apps motor meghívja a végpontot. Az API ezután törölje a visszahívási URL-cím regisztrációját, és szükség szerint leállíthatja a folyamatokat.

![Webhook-trigger minta](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Jelenleg a Logic App Designer nem támogatja a webhook-végpontok swaggeren keresztül történő felderítése. Így ehhez a mintához hozzá kell adnia egy [ **Webhook-eseményindítót,** ](../connectors/connectors-native-webhook.md) és meg kell adnia a kérés URL-címét, fejlécét és törzsét. Lásd még: [HTTPWebhook trigger](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). A visszahívási URL-cím átadásához `@listCallbackUrl()` szükség szerint használhatja a munkafolyamat-függvényt az előző mezők bármelyikében.
>
> Ugyanazon adatok többszöri feldolgozásának megakadályozása érdekében az eseményindítónak meg kell tisztítania a már elolvasott és a logikai alkalmazásnak átadott adatokat.

> [!TIP]
> Például webhook-minta, tekintse át ezt a [webhook-trigger vezérlő minta A GitHub.](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="improve-security-for-calls-to-your-apis-from-logic-apps"></a>Az API-k logikai alkalmazásokból érkező hívásainak biztonsága

Az egyéni API-k létrehozása után állítsa be az API-k hitelesítését, hogy biztonságosan hívhassa őket a logikai alkalmazásokból. [Ismerje meg, hogyan növelheti az egyéni API-k logikai alkalmazásokból érkező hívások biztonságát.](../logic-apps/logic-apps-custom-api-authentication.md)

## <a name="deploy-and-call-your-apis"></a>Az API-k üzembe helyezése és hívása

A hitelesítés beállítása után állítsa be az API-k üzembe helyezését. Ismerje [meg, hogyan telepítheti és hívhatja meg az egyéni API-kat a logikai alkalmazásokból.](../logic-apps/logic-apps-custom-api-host-deploy-call.md)

## <a name="publish-custom-apis-to-azure"></a>Egyéni API-k közzététele az Azure-ban

Az egyéni API-k elérhetővé az Azure-ban más Logic Apps-felhasználók számára, hozzá kell adnia a biztonságot, és regisztrálnia kell őket Logic App-összekötőkként. További információért lásd az [egyéni összekötők áttekintését](../logic-apps/custom-connector-overview.md). 

Ahhoz, hogy az egyéni API-k at elérhetővé tegye a Logic Apps, a Power Automate és a Microsoft Power Apps összes felhasználója számára, hozzá kell adnia a biztonságot, regisztrálnia kell az API-kat Logic App-összekötőkként, és ki kell jelölnie az összekötőket a [Microsoft Azure Certified programhoz.](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/) 

## <a name="get-support"></a>Támogatás kérése

* Az egyéni API-kkal kapcsolatban speciális segítségért forduljon a kapcsolathoz. [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com)

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Ha szeretne segíteni a Logic Apps fejlesztésében, szavazzon vagy küldje el javaslatait a [Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>További lépések

* [Hibák és kivételek kezelése](../logic-apps/logic-apps-exception-handling.md)
* [Http-végpontokkal rendelkező logikai alkalmazások hívása, aktiválása vagy beágyazott sága](../logic-apps/logic-apps-http-endpoint.md)
* [Műveletek és eseményindítók használati mérése](../logic-apps/logic-apps-pricing.md)

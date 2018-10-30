---
title: Webes API-k és REST API-k létrehozása az Azure Logic Apps |} A Microsoft Docs
description: Webes API-k és REST API-kat hívni az API-kat, szolgáltatásokat vagy rendszereket az Azure Logic Appsben rendszert Integrációk
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.assetid: bd229179-7199-4aab-bae0-1baf072c7659
ms.date: 05/26/2017
ms.openlocfilehash: a3f837b41ba6ec7ecadb3e34917a8088e4d1e2d9
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233514"
---
# <a name="create-custom-apis-you-can-call-from-azure-logic-apps"></a>Az Azure Logic Apps segítségével meghívhatja, egyéni API-k létrehozása

Bár az Azure Logic Apps biztosít [100 + beépített összekötők](../connectors/apis-list.md) , hogy a logikai alkalmazások munkafolyamataiba is használhatja, érdemes lehet hívni az API-k, rendszerek és szolgáltatások, amelyek nem érhetők el beépített összekötőkként. Létrehozhat saját API-k által biztosított műveleteket és eseményindítókat, a logic apps használatára. Az alábbiakban a valamilyen más okból, miért érdemes a saját API-k, amelyeket meghívhat a logikai alkalmazás munkafolyamatok létrehozása:

* Bővítheti a jelenlegi rendszer integráció és a data-integrációs munkafolyamatokat.
* Segíthet az ügyfeleknek használni a szolgáltatást, professional vagy személyes feladatait.
* Bontsa ki az elérhetőséget, felfedezhetősége és a szolgáltatás használatát.

Alapvetően az összekötők a webes API-kat használó REST csatlakoztatható adapterek [Swagger-metaadatok formátum](http://swagger.io/specification/) dokumentáció, és JSON-ban, az adatcsere-formátumot. Mivel az összekötők REST API-k HTTP-végpontokon keresztül kommunikáló, bármilyen nyelv, .NET, Java vagy node.js nyelven, például az összekötők készítéséhez is használhatja. Az API-k a is üzemeltethető [Azure App Service](../app-service/app-service-web-overview.md), a platform--szolgáltatásként (PaaS) kínál, amely biztosítja a legjobb, legegyszerűbb és leggyakrabban méretezhető módon API üzemeltetéséhez. 

Az egyéni API-k a logic apps szolgáltatással működik, az API-t biztosíthat [ *műveletek* ](./logic-apps-overview.md#logic-app-concepts) , amely adott feladatokat a logic app-munkafolyamatokba. Az API-t is működhet, egy [ *eseményindító* ](./logic-apps-overview.md#logic-app-concepts) , amely egy logikai alkalmazás munkafolyamatának elindul, amikor új adatokat és a egy esemény megfelel-e a megadott feltételnek. Ez a témakör ismerteti a gyakori minták, amelyekkel műveletek és eseményindítók az API-hoz, az API-t adja meg a kívánt viselkedés alapján.

Az API-k is üzemeltethet [Azure App Service](../app-service/app-service-web-overview.md), a platform--szolgáltatásként (PaaS) kínál, amely a nagy mértékben skálázható, egyszerű API-t üzemeltető biztosít.

> [!TIP] 
> Bár az API-k webes alkalmazásokat telepíteni, érdemes megfontolni az API-k, az API apps, amely megkönnyítheti a feladat készítése, üzemeltetése és felhasználása a felhőben és helyszíni API-k. Nem kell minden olyan kódot az API-k – csupán telepítse kódját egy API-alkalmazásba. Ha például megtudhatja, hogyan ezeken a nyelveken a létrehozott API-alkalmazásokat hozhat létre: 
> 
> * [ASP.NET](../app-service/app-service-web-get-started-dotnet.md). 
> * [Java](../app-service/app-service-web-get-started-java.md)
> * [Node.js](../app-service/app-service-web-get-started-nodejs.md)
> * [PHP](../app-service/app-service-web-get-started-php.md)
> * [Python](../app-service/containers/quickstart-python.md)
> * [Ruby](../app-service/containers/quickstart-ruby.md)
>
> A logic apps beépített API-alkalmazás minták, látogasson el a [Azure Logic Apps GitHub-adattár](http://github.com/logicappsio) vagy [blog](https://aka.ms/logicappsblog).

## <a name="how-do-custom-apis-differ-from-custom-connectors"></a>Hogyan térnek el egyéni API-k az egyéni összekötők?

Egyéni API-k és [egyéni összekötők](../logic-apps/custom-connector-overview.md) vannak webes API-kat használó REST csatlakoztatható adapterek [Swagger-metaadatok formátum](http://swagger.io/specification/) dokumentáció, és JSON-ban, az adatcsere-formátumot. És mivel ezen API-k és összekötők REST API-k HTTP-végpontokon keresztül kommunikáló, bármilyen nyelv, .NET, Java vagy node.js nyelven, például az egyéni API-k és összekötők használhatja.

Egyéni API-k lehetővé teszik az API-kat, amelyek nem összekötőket, és adja meg a végpontokat, amelyeket meghívhat HTTP + Swagger, Azure API Management vagy App Services. Egyéni összekötők működik, mint az egyéni API-kat, de ezek az attribútumok is rendelkezik:

* Az Azure Logic Apps-összekötő-erőforrások regisztrálva.
* Ikonok mellett a Microsoft által felügyelt összekötők a Logic Apps Designerben együtt jelennek meg.
* Csak az összekötők szerzők és a régióban, ahol a logic Apps-alkalmazások üzembe vannak helyezve az azonos Azure Active Directory-bérlővel és Azure-előfizetéssel rendelkező logikai alkalmazás felhasználói számára elérhető.

Nevezheti regisztrált összekötő Microsoft általi hitelesítésre. Ez a folyamat ellenőrzi, hogy regisztrált összekötők nyilvánosan a feltételeknek, és ezek az összekötők elérhetővé teszi a felhasználók számára a Microsoft Flow és a Microsoft PowerApps.

Egyéni összekötőkkel kapcsolatos további információkért lásd: 

* [Egyéni összekötők áttekintése](../logic-apps/custom-connector-overview.md)
* [Egyéni összekötők létrehozása webes API-kon keresztül.](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [Egyéni összekötők regisztrálása az Azure Logic Appsben](../logic-apps/logic-apps-custom-connector-register.md)

## <a name="helpful-tools"></a>Hasznos eszközök

Egyéni API-k optimális működéséhez a logic apps szolgáltatással is rendelkezik az API-t egy [Swagger-dokumentumok](http://swagger.io/specification/) , amely az API műveleteit és paramétereit írja le.
Számos kódtár, például [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle), automatikusan hozhat létre a Swagger-fájl az Ön számára. Jegyzettel láthatja el a Swagger-fájl a megjelenített neveket, tulajdonságtípus, és így tovább, akkor is használható [TRex](https://github.com/nihaue/TRex) úgy, hogy a Swagger-fájl is a logic apps szolgáltatással működik.

<a name="actions"></a>

## <a name="action-patterns"></a>A művelet minták

A logic apps feladatok végrehajtásához, biztosítania kell az egyéni API [ *műveletek*](./logic-apps-overview.md#logic-app-concepts). Egyes műveletek az API-művelet rendeli hozzá. Egy alapszintű művelet egy vezérlő, amely elfogadja a HTTP-kérelmek és HTTP-válaszok adja vissza. Így például a logikai alkalmazás HTTP-kérést küld a webalkalmazás vagy API-alkalmazás. Az alkalmazás ezután, együtt, amelyek a logikai alkalmazás tud feldolgozni egy HTTP-választ adja vissza.

Standard szintű művelet írja be az API-t egy HTTP-kérési metódust, és ismertetik a módszer egy Swagger-fájl. Ezt követően meghívhatja az API-t közvetlenül egy [HTTP-művelet](../connectors/connectors-native-http.md) vagy egy [HTTP + Swagger](../connectors/connectors-native-http-swagger.md) művelet. Alapértelmezés szerint válaszok belül vissza kell a [kérelem időkorlátja](./logic-apps-limits-and-config.md). 

![Standard szintű művelet minta](./media/logic-apps-create-api-app/standard-action.png)

<a name="pattern-overview"></a> Ahhoz, hogy egy logikai alkalmazást, várjon, amíg az API-t befejezése hosszabb ideig futó feladatok, az API-t is követheti a [lekérdezési aszinkron minta](#async-pattern) vagy a [webhook aszinkron minta](#webhook-actions) a jelen témakörben. Ezek a minták másként egy hasonlóan, amelyek segítségével vizualizálhatja a Képzelje el, a folyamat egy webmatrixos az egyéni torta rendezéshez. A lekérdezési minta a viselkedését, ahol hívja a webmatrixos 20 percenként ellenőrzi, hogy a torta készen áll a tükrözi. A webhook-minta a viselkedését, ahol a webmatrixos kéri a telefonszámát, is hívják meg, amikor készen áll a torta tükrözi.

Minták, látogasson el a [Logic Apps GitHub-adattár](https://github.com/logicappsio). Ezenfelül bővebben [műveletek esetében a használatmérés](logic-apps-pricing.md).

<a name="async-pattern"></a>

### <a name="perform-long-running-tasks-with-the-polling-action-pattern"></a>A lekérdezési művelet mintával hosszú ideig futó feladatok végrehajtása

Szeretné, hogy az API-t, amely hosszabb futtathat feladatokat a [kérelem időkorlátja](./logic-apps-limits-and-config.md), a lekérdezés aszinkron minta is használhatja. Ez a minta az API-t tegye egy külön szál, de tartsa a Logic Apps-motor aktív kapcsolat a munkahelyi rendelkezik. Ezzel a módszerrel a a logikai alkalmazás nem nem időtúllépés, és folytassa a következő lépés a munkafolyamatban, az API-t működő befejezése előtt.

Az általános mintája a következő:

1. Győződjön meg arról, hogy a motor tudja, hogy az API-t elfogadta a kérést, és a munka megkezdése.
2. A motor feladat állapotát a későbbi kérelmeket küld, amikor lehetővé teszik a motor értesíti, amikor az API-t a feladat befejeződik.
3. A vonatkozó adatokat térjen vissza a motor, hogy a logikai alkalmazás munkafolyamatának továbbra is.

<a name="bakery-polling-action"></a> Mostantól a lekérdezés a minta az előző webmatrixos hasonlóan a alkalmazni, és tegyük fel, hogy hívja a webmatrixos és a sorrend egyéni torta történő továbbítását. A folyamat, hogy a torta időt vesz igénybe, és nem kívánja a webmatrixos működik a torta türelmet a telefonon. A webmatrixos megerősíti, hogy a rendelés, és rendelkezik a torta állapot 20 percenként hívja meg. Után 20 perc, a webmatrixos hívja, de értesítést kap, és a, hogy meg kell hívnia a másik 20 percet, hogy a torta nem fejeződött. A vissza oda-folyamat folytatódik, amíg nem hívja, és a webmatrixos jelzi, hogy a rendelés készen áll, és továbbítja a torta. 

Úgyhogy képezze le a lekérdezési minta vissza. A webmatrixos jelöli az egyéni API, amíg Ön, a torta ügyfél kijelenti, hogy a Logic Apps-motor. A motor az API-kérelem hív meg, ha az API megerősíti, hogy a kérelem, és reagáljon az az időintervallum, amikor a motor ellenőrizheti a feladat állapota. A motor továbbra is fennáll, addig, amíg az API-t válaszol, hogy történt-e a feladat állapotának ellenőrzését, és visszaadja az adatokat a logikai alkalmazást, amely majd a munkafolyamat folytatódik. 

![Lekérdezési művelet minta](./media/logic-apps-create-api-app/custom-api-async-action-pattern.png)

Az API-hoz, kövesse az adott lépéseket ismerteti az API-k szempontjából:

1. Az API-t indítsa el a munkát a HTTP-kérést kap, ha azonnal vissza egy olyan HTTP `202 ACCEPTED` válaszban a `location` ebben a lépésben ismertetett fejléc. Ez a válasz lehetővé teszi, hogy a Logic Apps-motor tudja, hogy az API-t a kérés érkezett, fogadja a kérelem hasznos adatai (a bemeneti adatokat), és feldolgozása már folyamatban van. 
   
   A `202 ACCEPTED` válasz ezeket a fejléceket kell tartalmaznia:
   
   * *Szükséges*: A `location` fejlécet, amely abszolút elérési útját adja meg egy URL-címet, ahol a Logic Apps-motor ellenőrizheti az API-feladat állapota

   * *Nem kötelező*: A `retry-after` fejlécet, amely a motor várnia kell, mielőtt ellenőrizné másodpercek számát adja meg a `location` feladatállapot URL-CÍMÉT. 

     Alapértelmezés szerint a motor 20 másodpercenként ellenőrzi. Adja meg egy másik intervallumot, például a `retry-after` fejlécére, és amíg a következő lekérdezési másodpercek számát.

2. A megadott idő letelte után a Logic Apps összetevő, amely lekérdezi a `location` URL-címet a feladat állapotának ellenőrzése. Az API-t kell ezen ellenőrzések elvégzéséhez, és ezek a válaszok adja vissza:
   
   * Ha a feladat elkészült, lépjen vissza a HTTP `200 OK` választ, a válasz-adattartalomra (a következő lépésben a bemeneti) együtt.

   * Ha a feladat még dolgoz fel, adja vissza egy másik HTTP `202 ACCEPTED` válaszként, de ugyanazon a fejlécek, mint az eredeti válasz.

Ha ezt a mintát követi, az API-t, nem kell semmit a a logikai alkalmazás munkafolyamat-definíció folytatja a feladat állapotának ellenőrzése. Ha a motor beolvassa a HTTP `202 ACCEPTED` válasz és a egy érvényes `location` fejléc, a motor tiszteletben tartja az aszinkron minta, és ellenőrzi a `location` fejléc mindaddig, amíg az API-t nem 202 választ adja vissza.

> [!TIP]
> Egy példa aszinkron minta, tekintse át ezt [vezérlő aszinkron válasz minta a Githubon](https://github.com/logicappsio/LogicAppsAsyncResponseSample).

<a name="webhook-actions"></a>

### <a name="perform-long-running-tasks-with-the-webhook-action-pattern"></a>A webhook művelet mintával hosszú ideig futó feladatok végrehajtása

Alternatív megoldásként használhatja a webhook minta hosszú ideig futó feladatok és aszinkron feldolgozás céljából. Ehhez a mintához egy "visszahívást" Várjon a logikai alkalmazás munkafolyamat folytatása előtt feldolgozását az API-ból. A visszahívás egy HTTP POST, amely egy üzenetet küld egy esemény egy URL-címet. 

<a name="bakery-webhook-action"></a> Most a webhook minta az előző webmatrixos hasonlóan a alkalmazni, és tegyük fel, hogy hívja a webmatrixos és a sorrend egyéni torta történő továbbítását. A folyamat, hogy a torta időt vesz igénybe, és nem kívánja a webmatrixos működik a torta türelmet a telefonon. A webmatrixos megerősíti, hogy a rendelés, de ezúttal, tegye lehetővé számukra a telefonszámát, is hívják meg, amikor a torta történik. Ezúttal a webmatrixos kiderül, hogy amikor a rendelés készen áll, és a torta biztosít.

Vissza a webhook-minta azt leképezni, ha a webmatrixos jelöli az egyéni API, amíg Ön, a torta ügyfél kijelenti, hogy a Logic Apps-motor. A motor meghívja az API-kérés és a "visszahívási" URL-címet.
A feladat végeztével az API URL-CÍMÉT a motor értesítést, és térjen vissza a logikai alkalmazást, majd folytatja a munkafolyamatot, amely adatokat használ. 

Az ebben a mintában a vezérlő két végpontok beállítása: `subscribe` és `unsubscribe`

*  `subscribe` végpont: Amikor végrehajtási eléri az API-művelet a munkafolyamatban, a Logic Apps összetevő, amely meghívja a `subscribe` végpont. Ez a lépés a logikai alkalmazás létrehozása egy visszahívási URL-címet, amely az API-t tárolja, és ezután Várjon, amíg a visszahívás az API-t a munka befejeztével okoz. Az API-t, majd visszahívja az URL-címre egy HTTP POST-, és átadja a visszaadott tartalom és a fejlécek a logikai alkalmazás bemeneteként.

* `unsubscribe` végpont: Ha a logikai alkalmazás futtatásának meg lett szakítva, a Logic Apps összetevő, amely meghívja a `unsubscribe` végpont. Ezután regisztrációjának törlése a visszahívási URL-Címének és a folyamatokat, szükség szerint állítsa le az API-t.

![Webhook művelet minta](./media/logic-apps-create-api-app/custom-api-webhook-action-pattern.png)

> [!NOTE]
> Jelenleg a Logikaialkalmazás-tervező nem támogatja a Swagger felderítő webhook-végpontjait. Így az ebben a mintában fel kell vennie egy [ **Webhook** művelet](../connectors/connectors-native-webhook.md) , és adja meg az URL-címe, fejlécek és a kérés törzsében. Lásd még: [munkafolyamat-műveletek és eseményindítók](logic-apps-workflow-actions-triggers.md#apiconnection-webhook-action). A visszahívási URL-címet megadni, használhatja a `@listCallbackUrl()` szükség szerint az előző mezők munkafolyamat-funkció.

> [!TIP]
> Egy példa webhook-minta, tekintse át ezt [webhook eseményindító minta a Githubon](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

<a name="triggers"></a>

## <a name="trigger-patterns"></a>Eseményindító-minták

Az egyéni API működhet, egy [ *eseményindító* ](./logic-apps-overview.md#logic-app-concepts) egy logikai alkalmazást, amely elindul, amikor új adatokat és a egy esemény megfelel-e a megadott feltétel. Ez az eseményindító is vagy rendszeresen figyelni, vagy várjon és figyelésére, az új adatokat és eseményeket a szolgáltatási végpont. Ha új adatokat és a egy esemény megfelel a megadott feltételnek, akkor a akkor aktiválódik, majd elindítja a logikai alkalmazást, amely figyeli az adott eseményindító. A logic apps ezzel a módszerrel, az API-t is kövesse a [ *lekérdezési eseményindító* ](#polling-triggers) vagy a [ *webhooktrigger* ](#webhook-triggers) mintát. Ezek a minták hasonlóak a mint a [lekérdezési művelet](#async-pattern) és [webhook-műveletek](#webhook-actions). Ezenfelül bővebben [az eseményindítók a használatmérés](logic-apps-pricing.md).

<a name="polling-triggers"></a>

### <a name="check-for-new-data-or-events-regularly-with-the-polling-trigger-pattern"></a>Ellenőrizze, hogy az új adatok vagy események rendszeresen a lekérdezési eseményindító mintával

A *lekérdezési eseményindító* lényegében ugyanúgy működik a [lekérdezési művelet](#async-pattern) ebben a témakörben korábban leírt. A Logic Apps-motor rendszeres időközönként meghívja, és ellenőrzi a trigger végpont az új adatokat és eseményeket. Ha a motor az új adatokat és a egy eseményt, amely megfelel a megadott feltételnek talál, az eseményindító aktiválódik. Ezt követően a motor létrehoz egy logikaialkalmazás-példányt, amely a bemeneti adatokat feldolgozó. 

![Ciklikus lekérdezés eseményindító minta](./media/logic-apps-create-api-app/custom-api-polling-trigger-pattern.png)

> [!NOTE]
> Egyes lekérdezési kérelmek egy művelet végrehajtási számít, még akkor is, ha nincs logikaialkalmazás-példányt jön létre. Hogy a feldolgozási ugyanazokat az adatokat többször is feldolgozza, az eseményindító már elolvasta és a logikai alkalmazás átadott adatokat kell törölni.

Az alábbiakban egy lekérdezési eseményindító, az API-k szempontból leírt lépéseit:

| Új adatokat és az esemény található?  | API-válasz | 
| ------------------------- | ------------ |
| Található | Adja vissza egy olyan HTTP `200 OK` , ahol a válasz-adattartalomra (bemenet a következő lépésre) állapot. <br/>Ez a válasz létrehoz egy logikaialkalmazás-példányt, és elindítja a munkafolyamatot. | 
| Nincs találat | Adja vissza egy olyan HTTP `202 ACCEPTED` állapot egy `location` fejlécet és a egy `retry-after` fejléc. <br/>Az eseményindítók a `location` fejlécnek is tartalmaznia kell egy `triggerState` lekérdezési paraméter, amely általában a "Timestamp"mezőt. Az API-t a Ez az azonosító segítségével nyomon követheti, hogy a logikai alkalmazás aktiválva lett utoljára. | 
||| 

Például a szolgáltatás új fájlok rendszeres időnként ellenőrzik, felépíthet egy lekérdezési eseményindítói viselkedést:

| Tartalmazza a kérés `triggerState`? | API-válasz | 
| -------------------------------- | -------------| 
| Nem | Adja vissza egy olyan HTTP `202 ACCEPTED` állapota és a egy `location` fejléc `triggerState` állítsa be az aktuális idő és a `retry-after` időköz 15 másodperc. | 
| Igen | Ellenőrizze a szolgáltatást, miután hozzáadott fájlok a `DateTime` a `triggerState`. | 
||| 

| Található fájlok száma | API-válasz | 
| --------------------- | -------------| 
| Egyetlen fájl | Adja vissza egy olyan HTTP `200 OK` állapota és a tartalom tartalmat, a frissítés `triggerState` , a `DateTime` a visszaadott fájlt, és állítsa be a `retry-after` időköz 15 másodperc. | 
| Több fájl | Adja vissza egy fájl egy időben és a egy olyan HTTP `200 OK` állapot, a frissítés `triggerState`, és állítsa be a `retry-after` intervallum 0 másodperc. </br>Ezeket a lépéseket, hogy több adat érhető el, és, hogy a motor kell azonnal kérhet az adatok az URL-címet a motor lehetővé teszik a `location` fejléc. | 
| Nincsenek fájlok | Adja vissza egy olyan HTTP `202 ACCEPTED` állapota, nem módosítja a `triggerState`, és állítsa be a `retry-after` időköz 15 másodperc. | 
||| 

> [!TIP]
> Példa lekérdezési eseményindító minta, tekintse át ezt [lekérdezési eseményindító vezérlő minta a Githubon](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/PollTriggerController.cs).

<a name="webhook-triggers"></a>

### <a name="wait-and-listen-for-new-data-or-events-with-the-webhook-trigger-pattern"></a>Várjon, és az új adatokat és eseményeket a webhook-eseményindító mintával figyelésére

A webhook eseményindítóhoz van egy *leküldéses trigger* , amely megvárja, és figyeli az új adatokat és eseményeket a szolgáltatási végpont. Ha új adatokat és a egy esemény megfelel a megadott feltételnek, akkor a trigger akkor aktiválódik, majd létrehoz egy logikaialkalmazás-példányt, amely majd dolgozza fel a bemeneti adatok.
Webhook eseményindítók lényegében ugyanúgy működik a [webhook-műveletek](#webhook-actions) korábban ebben a témakörben leírt, és a beállítása `subscribe` és `unsubscribe` végpontok. 

* `subscribe` végpont: Ha ad hozzá, és mentse a logikai alkalmazás a webhook eseményindítóhoz, a Logic Apps összetevő, amely meghívja a `subscribe` végpont. Ebben a lépésben hatására a logikai alkalmazás létrehozása egy visszahívási URL-címet, amely az API-t tárolja. Ha új adatokat és a egy eseményt, amely megfelel a megadott feltételnek, az API-hívások vissza és a egy HTTP POST URL-címre. A tartalom hasznos és a fejléc adja át a logikai alkalmazás bemeneteként.

* `unsubscribe` végpont: Ha a webhook eseményindítóhoz vagy teljes logikai alkalmazás törlése a Logic Apps összetevő, amely meghívja a `unsubscribe` végpont. Ezután regisztrációjának törlése a visszahívási URL-Címének és a folyamatokat, szükség szerint állítsa le az API-t.

![Webhook-eseményindító minta](./media/logic-apps-create-api-app/custom-api-webhook-trigger-pattern.png)

> [!NOTE]
> Jelenleg a Logikaialkalmazás-tervező nem támogatja a Swagger felderítő webhook-végpontjait. Így az ebben a mintában fel kell vennie egy [ **Webhook** eseményindító](../connectors/connectors-native-webhook.md) , és adja meg az URL-címe, fejlécek és a kérés törzsében. Lásd még: [HTTPWebhook eseményindító](logic-apps-workflow-actions-triggers.md#httpwebhook-trigger). A visszahívási URL-címet megadni, használhatja a `@listCallbackUrl()` szükség szerint az előző mezők munkafolyamat-funkció.
>
> Hogy a feldolgozási ugyanazokat az adatokat többször is feldolgozza, az eseményindító már elolvasta és a logikai alkalmazás átadott adatokat kell törölni.

> [!TIP]
> Egy példa webhook-minta, tekintse át ezt [webhook eseményindító vezérlő minta a Githubon](https://github.com/logicappsio/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs).

## <a name="secure-calls-to-your-apis-from-logic-apps"></a>Biztonságos API-k hívása a logikai alkalmazásokból

Miután létrehozta az egyéni API-k, hitelesítés beállítása API-k, hogy hívása azokat biztonságosan a logikai alkalmazásokból. Ismerje meg, [biztonságossá tétele a logic apps egyéni API-hívások](../logic-apps/logic-apps-custom-api-authentication.md).

## <a name="deploy-and-call-your-apis"></a>Üzembe helyezése és hívása az API-k

Miután beállította a hitelesítést, API-k üzembe helyezés beállítása. Ismerje meg, [hogyan helyezheti üzembe és egyéni API-k hívása a logic apps](../logic-apps/logic-apps-custom-api-host-deploy-call.md).

## <a name="publish-custom-apis-to-azure"></a>Egyéni API-k közzététele az Azure-bA

Egyéni API-k elérhetővé tétele az Azure Logic Apps más felhasználói számára, adja hozzá a biztonsági, és regisztrálja őket a logikai alkalmazás-összekötők. További információért lásd az [egyéni összekötők áttekintését](../logic-apps/custom-connector-overview.md). 

Egyéni API-k elérhetővé tenni a Logic Apps, a Microsoft Flow és a Microsoft PowerApps összes felhasználójára, kell hozzáadni a biztonsági, az API-k regisztrálása a logikai alkalmazás-összekötők, és összekötők jelölje a [Microsoft Azure Certified program](https://azure.microsoft.com/marketplace/programs/certified/logic-apps/). 

## <a name="get-support"></a>Támogatás kérése

* Egyéni API-k az adott segítségért lépjen kapcsolatba [ customapishelp@microsoft.com ](mailto:customapishelp@microsoft.com).

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Ha szeretne segíteni a Logic Apps fejlesztésében, szavazzon vagy küldje el javaslatait a [Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish). 

## <a name="next-steps"></a>További lépések

* [Hibák és kivételek kezelése](../logic-apps/logic-apps-exception-handling.md)
* [Hívása, eseményindító, vagy a HTTP-végponttal rendelkező logikai alkalmazások beágyazása](../logic-apps/logic-apps-http-endpoint.md)
* [A használatmérés a műveletek és triggerek](../logic-apps/logic-apps-pricing.md)

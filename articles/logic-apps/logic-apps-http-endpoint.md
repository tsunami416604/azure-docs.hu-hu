---
title: Hívása, aktiválása vagy beágyazása a HTTP-végpontokat – Azure Logic Apps-munkafolyamatok |} A Microsoft Docs
description: Állítsa be a HTTP-végpontok hívása, eseményindító, vagy a munkafolyamatok beágyazása az Azure Logic Apps
services: logic-apps
keywords: a munkafolyamatok, HTTP-végpontokat
author: jeffhollan
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.author: klam; LADocs
ms.openlocfilehash: 7920fee1bacf569ac41c36142fc68080b4de5780
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230488"
---
# <a name="call-trigger-or-nest-workflows-with-http-endpoints-in-logic-apps"></a>Hívása, eseményindító, vagy HTTP-végpontokat, a logic apps-munkafolyamatok beágyazása

Natív módon, közzéteheti a szinkron HTTP-végpontokat eseményindítóként, a logic apps szolgáltatásról, hogy indíthat, vagy hívja a logic apps egy URL-címmel. Beágyazhatja munkafolyamatok is a logikai alkalmazások használata hívható végpontok egy minta használatával.

HTTP-végpontokat létrehozni, ezek az eseményindítók úgy, hogy a logikai alkalmazások fogadhatnak bejövő kérések is hozzáadhat:

* [Kérés](../connectors/connectors-native-reqres.md)

* [API-kapcsolat Webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Bár az ezekben a példákban a **kérelem** eseményindító, bármilyen a felsorolt HTTP-eseményindítók és a farmja minden elvek vonatkoznak a triggerek típusának.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>Állítsa be a logikai alkalmazás egy HTTP-végpontot

Hozzon létre egy HTTP-végpontot, adja hozzá egy eseményindítót, amely képes fogadni a bejövő kérelmeket.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com "Azure Portal") Nyissa meg a logikai alkalmazást, és nyissa meg a Logikaialkalmazás-Tervező.

2. Adjon hozzá egy eseményindítót, amely lehetővé teszi, hogy a logikai alkalmazás bejövő kérelmek fogadásához. Például adja hozzá a **kérelem** eseményindítót a logikai alkalmazáshoz.

3.  A **kérelem Kéréstörzs JSON-sémája**, igény szerint megadhatja egy JSON-sémájában fogadásához az eseményindító várt adattartalom (adatok).

    A Tervező ebben a sémában jogkivonatokat, amelyeket a logikai alkalmazás használatával felhasználását, elemzése és adatokat átadni a munkafolyamaton keresztül az eseményindító létrehozásához használja. 
    Többet is megtudhat [által létrehozott JSON-sémáinak jogkivonatok](#generated-tokens).

    Ebben a példában adja meg a sémában a tervezőben látható:

    ```json
    {
      "type": "object",
      "properties": {
        "address": {
          "type": "string"
        }
      },
      "required": [
        "address"
      ]
    }
    ```

    ![A kérelem művelet hozzáadása][1]

    > [!TIP]
    > 
    > Egy eszköz, például a sémát a JSON hasznosadat-minta is létrehozhat [jsonschema.net](http://jsonschema.net/), vagy a **kérelem** eseményindító kiválasztásával **Mintaadat használata séma létrehozásához**. 
    > Adja meg a hasznosadat-minta, és válassza a **kész**.

    Ha például a hasznosadat-minta:

    ```json
    {
       "address": "21 2nd Street, New York, New York"
    }
    ```

    állít elő, ha a sémát:

    ```json
    {
       "type": "object",
       "properties": {
          "address": {
             "type": "string" 
          }
       }
    }
    ```

4.  Mentse a logikai alkalmazást. A **HTTP POST ezen URL-címen**, most látnia kell egy generált visszahívási URL-CÍMÉT, például ebben a példában:

    ![Generált visszahívási URL-végpont](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

    Az URL-címet tartalmaz egy közös hozzáférésű Jogosultságkód (SAS) kulcsot a lekérdezési paraméterek, amelyek a hitelesítéshez használhatók. 
    A HTTP-végpont URL-címe is kérhet a logic app – áttekintés az Azure Portalon. A **eseményindítási előzményeinek**, az eseményindító kiválasztása:

    ![HTTP-végpont URL-cím lekérése az Azure Portalról][2]

    Vagy azáltal, hogy a hívás URL-CÍMÉT is kap:

    ```
    POST https://management.azure.com/{logic-app-resourceID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>A HTTP-metódus az eseményindító módosítása

Alapértelmezés szerint a **kérelem** eseményindító egy HTTP POST-kérést vár, de más HTTP módszert is használhat. 

> [!NOTE]
> Megadhatja, hogy csak egy metódus típusa.

1. Az a **kérelem** aktiválhat, válassza a **speciális beállítások megjelenítése**.

2. Nyissa meg a **metódus** listája. Ebben a példában válassza **első** úgy, hogy a HTTP-végpont URL-cím újabb teszteléséhez.

    > [!NOTE]
    > Bármilyen más HTTP-metódus kiválasztása, vagy adjon meg egy egyéni módszer a saját logikai alkalmazás.

    ![Módosítsa a HTTP-metódus](./media/logic-apps-http-endpoint/change-method.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Fogad el paramétereket a HTTP-végpont URL-CÍMEN keresztül

Ha azt szeretné, hogy a HTTP végpont URL-címe, paraméterek elfogadásához, testre szabhatja a trigger relatív elérési út.

1. Az a **kérelem** aktiválhat, válassza a **speciális beállítások megjelenítése**. 

2. A **metódus**, adja meg, amelyeket szeretne használni a kérelem HTTP-metódust. Ebben a példában válassza ki a **első** módszert, ha még nem tette, így ellenőrizheti, hogy a HTTP-végpont URL-címe.

      > [!NOTE]
      > A trigger relatív elérési utat ad meg, amikor az eseményindító közvetlenül is adjon meg egy HTTP-metódust.

3. A **relatív elérési út**, adja meg a paraméter, amely az URL-cím elfogadjon, például a következő relatív elérési `customers/{customerID}`.

    ![Adja meg a HTTP-metódust és a relatív elérési út paraméter](./media/logic-apps-http-endpoint/relativeurl.png)

4. Használja ezt a paramétert, adjon hozzá egy **válasz** művelet a logikai alkalmazáshoz. (Az eseményindító területén válassza a **új lépés** > **művelet hozzáadása** > **válasz**) 

5. A válaszban **törzs**, tartalmazza a tokent a paraméterhez, a trigger relatív elérési utat adott meg.

    Például adja vissza `Hello {customerID}`, frissítse a válasz **törzs** a `Hello {customerID token}`. 
    A dinamikus tartalmak listája jelenik meg kell, és megjelenítése a `customerID` token választható.

    ![Paraméter hozzáadása a válasz törzse](./media/logic-apps-http-endpoint/relativeurlresponse.png)

    A **törzs** példához hasonlóan kell kinéznie:

    ![Választörzs paraméterrel](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Mentse a logikai alkalmazást. 

    A HTTP-végpont URL-CÍMÉT most már tartalmazza a relatív elérési út, például: 

    https&#58;//prod-00.southcentralus.logic.azure.com/workflows/f90cb66c52ea4e9cabe0abf4e197deff/triggers/manual/paths/invoke/customers/{customerID}...

7. A HTTP-végpont tesztelése, másolja és illessze be a frissített URL-cím egy másik böngészőablakban, de cserélje le `{customerID}` a `123456`, és nyomja le az Enter billentyűt.

    A böngésző ezt a szöveget kell megjelennie: 

    `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>JSON-sémáinak a logikai alkalmazás által létrehozott jogkivonatok

Ha megadta a JSON-séma a **kérelem** , a Logic App Designerben az eseményindító tulajdonságok jogkivonatokat hoz létre, hogy a séma. A logikai alkalmazás munkafolyamatának keresztül adatok átadására ezután használhatja ezeket a jogkivonatokat.

Ebben a példában, ha a `title` és `name` tulajdonságokat a JSON-sémájában, tokenjeiket immár újabb munkafolyamat-lépéseit az elérhető legyen. 

A következő teljes JSON-sémája:

```json
{
   "type": "object",
   "properties": {
      "address": {
         "type": "string"
      },
      "title": {
         "type": "string"
      },
      "name": {
         "type": "string"
      }
   },
   "required": [
      "address",
      "title",
      "name"
   ]
}
```

## <a name="create-nested-workflows-for-logic-apps"></a>A logic apps beágyazott munkafolyamatok létrehozása

A logikai alkalmazás munkafolyamatok ágyazhatja más logikai alkalmazások fogadhassanak kérelmek hozzáadásával. Ezek a logic apps történő felvételéhez adja hozzá a **Azure Logic Apps -, válasszon ki egy Logic Apps-munkafolyamatot** az eseményindító műveletet. Ezután kiválaszthatja a megfelelő logikai alkalmazásokból.

![Adjon hozzá egy másik logikai alkalmazás](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Hívás vagy eseményindítót, a logic apps HTTP-végpontokon keresztül

Miután létrehozta a HTTP-végpontot, a logikai alkalmazás használatával aktiválhat egy `POST` módszer a teljes URL-címre. A Logic apps beépített támogatást nyújt a közvetlen hozzáférési végpontok rendelkezik.

> [!NOTE] 
> Egy logikai alkalmazást futtathatja manuálisan tetszőleges időpontban, vagy a logikai alkalmazás Kódnézete Logikaialkalmazás-Tervező eszköztárán válassza a **futtatása**.

## <a name="reference-content-from-an-incoming-request"></a>Segédanyagok a bejövő kérések

Ha a tartalom típusa `application/json`, a bejövő kérelem tulajdonságai hivatkozhat. Ellenkező esetben tartalom, amely más API-kkal adhat át egy bináris egységet számít. Ezt a tartalmat, a munkafolyamat belül, hivatkozni kell konvertálnia a tartalom. Például, ha a felhasználó `application/xml` tartalom, használhat `@xpath()` egy XPath kivonására vagy `@json()` for XML átalakítása JSON. Ismerje meg [tartalomtípusok használata](../logic-apps/logic-apps-content-type.md).

A kimenet kérhet egy bejövő kérésnek, használhatja a `@triggerOutputs()` függvény. Ebben a példában a kimeneti hasonló lehet:

```json
{
    "headers": {
        "content-type" : "application/json"
    },
    "body": {
        "myProperty" : "property value"
    }
}
```

Hozzáférés a `body` tulajdonság, használhatja a `@triggerBody()` parancsikon. 

## <a name="respond-to-requests"></a>Válaszol a kérelmekre

Érdemes bizonyos kérelmei, amelyek elindítják a logikai alkalmazás tartalom visszaküldésével a hívónak. Az állapotkód, fejléc és a válasz törzse hozhatnak létre, használhatja a **válasz** művelet. Ez a művelet a logikai alkalmazást, nem csak a munkafolyamat végén bárhol megjelenhet.

> [!NOTE] 
> Ha nem adja meg a logikai alkalmazás egy **válasz**, válaszol a HTTP-végpontot *azonnal* együtt egy **202-es elfogadva** állapotát. Ezenkívül lekérni a választ az eredeti kérés, a válasz lépéseit Befejezés belül a [kérelem időkorlátja](./logic-apps-limits-and-config.md) , kivéve, ha a munkafolyamat számára egy beágyazott logikai alkalmazást hívja. Nincs válasz a határidőn belül nem történik, ha a bejövő kérelem túllépi az időkorlátot, és a HTTP-válasz fogadása **408 ügyfél időkorlátját**. A beágyazott logic apps a szülő logikai alkalmazás továbbra is várniuk a válaszra, amíg befejeződik, függetlenül attól, hogy mennyi idő szükség.

### <a name="construct-the-response"></a>A válasz szerkezet

A válasz törzsében tartalmazhatnak egynél több fejlécet és a tartalom bármilyen típusú. A példa válaszban a fejléc Megadja, hogy rendelkezik-e a válasz tartalomtípusa `application/json`. a törzse tartalmazza, és `title` és `name`korábban frissítve a JSON-sémája alapján a **kérelem** eseményindító.

![HTTP-válasz művelet][3]

Válaszok rendelkezik, ezeket a tulajdonságokat:

| Tulajdonság | Leírás |
| --- | --- |
| Állapotkód |Itt adhatja meg a bejövő kérelem válaszol a HTTP-állapotkódot. Ez a kód bármilyen érvényes állapotkód: 2xx, 4xx vagy 5xx kezdetű is lehet. Azonban 3xx állapotkódok használata nem engedélyezett. |
| A fejlécek |A válasz tartalmazza a fejlécek tetszőleges számú határozza meg. |
| törzs |Megadja egy szervezet objektum, amely egy karakterlánc lehet, egy JSON-objektum, vagy akár bináris tartalom hivatkozni az előző lépésben. |

Itt látható a JSON-sémája néz ki most a **válasz** művelet:

``` json
"Response": {
   "inputs": {
      "body": {
         "title": "@{triggerBody()?['title']}",
         "name": "@{triggerBody()?['name']}"
      },
      "headers": {
           "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {},
   "type": "Response"
}
```

> [!TIP]
> A Logic App Designerben a teljes JSON-definíciót a logikai alkalmazás megtekintéséhez válassza **Kódnézet**.

## <a name="q--a"></a>Kérdések és válaszok

#### <a name="q-what-about-url-security"></a>K: Mi a helyzet biztonsági URL-cím?

V: az azure biztonságos készítésére szolgál a logikai alkalmazás visszahívási URL egy közös hozzáférésű Jogosultságkód (SAS) használatával. Az aláírás halad keresztül, mint egy lekérdezési paraméter, és ellenőrizni kell, mielőtt a logikai alkalmazás aktiválódik is. Az Azure az aláírást, valamint a logikai alkalmazást, az eseményindító neve és a végrehajtandó műveletet egy titkos kulcs egyedi együttes használatával hoz létre. Ezért, ha valaki hozzáfér a titkos logic app-kulcs, azok nem hoznak létre érvényes aláírással.

   > [!IMPORTANT]
   > Az éles és biztonságos rendszerek javasoljuk, hogy ellen a logikai alkalmazás meghívása közvetlenül a böngészőből, mivel:
   > 
   > * A közös hozzáférési kulcs az URL-cím jelenik meg.
   > * Biztonságos tartalomszabályzat megosztott tartományok miatt nem kezelheti a logikai alkalmazás ügyfelek között.

#### <a name="q-can-i-configure-http-endpoints-further"></a>K: további HTTP-végpontok konfigurálása?

V: Igen, támogatja a HTTP-végpontokat a Speciális konfiguráció keresztül [ **az API Management**](../api-management/api-management-key-concepts.md). Ez a szolgáltatás is lehetőséget nyújt a, hogy egységesen kezelheti minden API-k, beleértve a logic apps, egyéni tartománynevek beállítása, használja a további hitelesítési módszereket, és egyéb, például:

* [A kérelem módjának módosítása](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#SetRequestMethod)
* [Módosítsa az URL-szegmenseknek a kérelem](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#RewriteURL)
* Az API Management tartományok beállítása a [az Azure portal](https://portal.azure.com/ "Azure Portalon")
* Állítsa be a szabályzatot, az egyszerű hitelesítés ellenőrzése

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>K: Mi változott, a 2014. December 1-én előzetes való migrálásakor a séma?

V: Itt található egy összefoglaló változásokkal kapcsolatban:

| 2014. december 1-előzetes verzió | 2016. június 1-én |
| --- | --- |
| Kattintson a **HTTP-figyelő** API-alkalmazás |Kattintson a **manuális eseményindító** (API-alkalmazás nem kötelező) |
| HTTP-figyelő beállítása "*automatikusan választ küld*" |Tartalmaznak egy **válasz** művelet vagy a munkafolyamat-definíció nem található |
| Basic vagy OAuth-hitelesítés konfigurálása |API Management szolgáltatáson keresztül |
| Konfigurálja a HTTP-metódus |A **speciális beállítások megjelenítése**, válassza a HTTP-metódus |
| Relatív elérési út konfigurálása |A **speciális beállítások megjelenítése**, egy relatív elérési út hozzáadása |
| A bejövő törzsében keresztül hivatkozhat. `@triggerOutputs().body.Content` |– Referencia `@triggerOutputs().body` |
| **HTTP-válasz küldése** műveletet a HTTP-figyelő |Kattintson a **HTTP-kérelem megválaszolásához** (API-alkalmazás nem kötelező) |

## <a name="get-help"></a>Segítségkérés

Látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps), ahol kérdéseket tehet fel és válaszolhat meg, valamint megtudhatja, mivel foglalkoznak az Azure Logic Apps más felhasználói.

Ha szeretné segíteni az Azure Logic Apps és összekötők fejlesztését, szavazzon az ötletekre az [Azure Logic Apps felhasználói visszajelzések oldalán](https://aka.ms/logicapps-wish), vagy küldje be saját javaslatait.

## <a name="next-steps"></a>További lépések

* [Logikaialkalmazás-definíciók készítése](./logic-apps-author-definitions.md)
* [Hibák és kivételek kezelése](./logic-apps-exception-handling.md)

[1]: ./media/logic-apps-http-endpoint/manualtrigger.png
[2]: ./media/logic-apps-http-endpoint/manualtriggerurl.png
[3]: ./media/logic-apps-http-endpoint/response.png
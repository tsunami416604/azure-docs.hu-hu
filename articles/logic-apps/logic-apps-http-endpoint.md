---
title: Logikai alkalmazások hívása, triggere vagy beágyazása – Azure Logic Apps
description: HTTP-végpontok beállítása a logikai alkalmazások munkafolyamatainak meghívásához, triggeréhez vagy beágyazásához Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: jehollan, klam, LADocs
manager: carmonm
ms.assetid: 73ba2a70-03e9-4982-bfc8-ebfaad798bc2
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 03/31/2017
ms.openlocfilehash: 4fc20c4b1314d953ea979192c81b2c264292d3af
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73041937"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Logikai alkalmazások hívása, elindítása vagy beágyazása HTTP-végpontok használatával Azure Logic Apps

A Logic apps szolgáltatásokban natív módon teheti elérhetővé a szinkron HTTP-végpontokat, így a logikai alkalmazásokat elindíthatja vagy meghívhatja egy URL-cím használatával. A munkafolyamatokat a logikai alkalmazásokban a hívható végpontok mintájának használatával is beágyazhatja.

HTTP-végpontok létrehozásához hozzáadhatja ezeket az eseményindítókat, hogy a logikai alkalmazások fogadni tudják a bejövő kérelmeket:

* [Kérés](../connectors/connectors-native-reqres.md)

* [API-kapcsolat webhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnection-trigger)

* [HTTP Webhook](../connectors/connectors-native-webhook.md)

   > [!NOTE]
   > Bár ezek a példák a **kérelem** eseményindítót használják, a felsorolt kérelmeken alapuló eseményindítók bármelyikét használhatja, a többi eseményindító-típussal azonos elveket is alkalmazva.

## <a name="set-up-an-http-endpoint-for-your-logic-app"></a>HTTP-végpont beállítása a logikai alkalmazáshoz

HTTP-végpont létrehozásához adjon hozzá egy triggert, amely fogadja a bejövő kéréseket.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com "Azure Portal"). Lépjen a logikai alkalmazáshoz, és nyissa meg a Logic app designert.

1. Adjon hozzá egy triggert, amely lehetővé teszi, hogy a logikai alkalmazás fogadja a bejövő kérelmeket. Adja meg például a **kérelem** triggert a logikai alkalmazáshoz.

1. A **kérelem törzse JSON-séma**alatt megadhat egy JSON-sémát a hasznos adattartalomhoz (adatok).

   A tervező ezt a sémát használja azon jogkivonatok generálásához, amelyeket a logikai alkalmazás használhat a triggerből származó adatok felhasználásához, elemzéséhez és átadásához a munkafolyamaton keresztül. További információ a [JSON-sémák által generált tokenekről](#generated-tokens).

   Ebben a példában adja meg ezt a sémát a tervezőben látható módon:

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

   ![JSON-séma megadása a kérelem műveletéhez](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   > [!TIP]
   >
   > Létrehozhat egy sémát egy minta JSON-adattartalomhoz egy olyan eszközről, mint például a [jsonschema.net](https://jsonschema.net/) vagy a **kérelem** -trigger, ha a minta hasznos adatok használata lehetőséget választja a **séma létrehozásához**. Adja meg a minta hasznos adatait, majd válassza a **kész**lehetőséget.

   Például ez a minta hasznos adat:

   ```json
   {
      "address": "21 2nd Street, New York, New York"
   }
   ```

   a következő séma generálása:

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

1. Mentse a logikai alkalmazást. Az **URL-címre való http-közzététel**alatt a generált visszahívási URL-címet kell megkeresnie, például a következő példához hasonló módon:

   ![A végponthoz generált visszahívási URL-cím](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   Ez az URL-cím a hitelesítéshez használt lekérdezési paraméterek közös hozzáférésű aláírási (SAS) kulcsát tartalmazza. A HTTP-végpont URL-címét a logikai alkalmazás áttekintésében is lekérheti a Azure Portal. Az **aktiválási előzmények**területen válassza ki az triggert:

   ![HTTP-végpont URL-címének lekérése Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   Az URL-címet a következő hívással kérheti le:

    ```http
    POST https://management.azure.com/{logic-app-resource-ID}/triggers/{myendpointtrigger}/listCallbackURL?api-version=2016-06-01
    ```

## <a name="change-the-http-method-for-your-trigger"></a>Az trigger HTTP-metódusának módosítása

Alapértelmezés szerint a **kérelem** TRIGGERE http post-kérést vár, de más http-metódust is használhat.

> [!NOTE]
> Csak egy metódus típust adhat meg.

1. A **kérelem** triggerén válassza a **Speciális beállítások megjelenítése**lehetőséget.

2. Nyissa meg a **metódusok** listáját. Ebben a példában válassza a **beolvasás** lehetőséget, hogy később tesztelje a http-végpont URL-címét.

   > [!NOTE]
   > Kiválaszthat bármilyen más HTTP-metódust, vagy egyéni módszert is megadhat saját logikai alkalmazásához.

   ![Válassza ki a kérelemhez használni kívánt HTTP-metódust](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-through-your-http-endpoint-url"></a>Paraméterek elfogadása a HTTP-végpont URL-címén keresztül

Ha azt szeretné, hogy a HTTP-végpont URL-címe fogadja a paramétereket, szabja testre az trigger relatív elérési útját.

1. A **kérelem** triggerén válassza a **Speciális beállítások megjelenítése**lehetőséget. 

2. A **metódus**alatt adja meg azt a http-metódust, amelyet a kérelmének használnia kell. Ebben a példában válassza a **Get** metódust, ha még nem tette meg, hogy tesztelni tudja a http-végpont URL-címét.

   > [!NOTE]
   > Ha az trigger relatív elérési útját adja meg, explicit módon meg kell adnia egy HTTP-metódust az triggerhez.

3. A **relatív elérési út**alatt adja meg a paraméter relatív elérési útját, amelyet az URL-címnek el kell fogadnia, például `customers/{customerID}`.

   ![A HTTP-metódus és a relatív elérési út megadása a paraméterhez](./media/logic-apps-http-endpoint/relative-path-url-value.png)

4. A paraméter használatához adjon hozzá egy **Response** műveletet a logikai alkalmazáshoz. (Az trigger alatt válassza az **új lépés**  > **művelet hozzáadása**  > **Válasz**) lehetőséget. 

5. A válasz **törzsében**adja meg az trigger relatív elérési útján megadott paraméter tokenjét.

   Például `Hello {customerID}` visszaküldéséhez frissítse a válasz **törzsét** a `Hello {customerID token}`. A dinamikus tartalom lista ekkor megjelenik, és megjeleníti a kiválasztható `customerID` tokent.

   ![Paraméter hozzáadása a válasz törzséhez](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   A **törzsnek** a következő példához hasonlóan kell kinéznie:

   ![Példa válasz törzsére paraméterrel](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

6. Mentse a logikai alkalmazást. 

    A HTTP-végpont URL-címe mostantól tartalmazza a relatív elérési utat, például: 

    ```http
    https://prod-00.southcentralus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/customers/{customerID}...
    ```

7. A HTTP-végpont teszteléséhez másolja és illessze be a frissített URL-címet egy másik böngészőablakba, de cserélje le a `{customerID}`t a `123456`ra, majd nyomja le az ENTER billentyűt.

   A böngészőben a következő szövegnek kell megjelennie: `Hello 123456`

<a name="generated-tokens"></a>

### <a name="tokens-generated-from-json-schemas-for-your-logic-app"></a>A logikai alkalmazás JSON-sémájából generált tokenek

Ha JSON-sémát ad meg a **kérelem** -triggerben, a Logic app Designer jogkivonatokat hoz létre a séma tulajdonságaihoz. Ezeket a jogkivonatokat használhatja a logikai alkalmazás munkafolyamatán keresztüli adattovábbításhoz.

Ebben a példában, ha hozzáadja a `title` és `name` tulajdonságokat a JSON-sémához, a tokenek mostantól elérhetők a későbbi munkafolyamat-lépésekben való használatra. 

Itt látható a teljes JSON-séma:

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

## <a name="create-nested-workflows-for-logic-apps"></a>Beágyazott munkafolyamatok létrehozása logikai alkalmazásokhoz

A munkafolyamatokat a logikai alkalmazásban úgy ágyazhatja be, hogy más, a kérelmeket fogadó logikai alkalmazásokat is felvesz. A logikai alkalmazások belefoglalásához adja hozzá a **Azure Logic apps – válasszon egy Logic apps munkafolyamat-** műveletet az triggerhez. Ezután választhat a jogosult logikai alkalmazások közül.

![Logikai alkalmazás beágyazása az aktuális logikai alkalmazásba](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

## <a name="call-or-trigger-logic-apps-through-http-endpoints"></a>Logikai alkalmazások hívása vagy elindítása HTTP-végpontokon keresztül

A HTTP-végpont létrehozása után a logikai alkalmazást egy `POST` metódussal aktiválhatja a teljes URL-címre. A Logic apps beépített támogatást nyújt a közvetlen elérésű végpontokhoz.

> [!NOTE] 
> Ha bármikor manuálisan szeretne futtatni egy logikai alkalmazást, a Logic app Designer vagy a Logic app Code nézet eszköztárán válassza a **Futtatás**lehetőséget.

## <a name="reference-content-from-an-incoming-request"></a>Hivatkozás egy bejövő kérelem tartalmára

Ha a tartalom típusa `application/json`, akkor a bejövő kérelem tulajdonságaira hivatkozhat. Ellenkező esetben a tartalmat egyetlen bináris egységként kezeli a rendszer, amelyet átadhat más API-khoz. A tartalom munkafolyamaton belüli hivatkozásához konvertálnia kell a tartalmat. Ha például átadja `application/xml` tartalmat, a `@xpath()` XPath-kinyeréshez, vagy az XML-fájl JSON-re való átalakításához `@json()` is használhatja. Tudnivalók a [tartalomtípusok használatáról](../logic-apps/logic-apps-content-type.md).

A bejövő kérelem kimenetének lekéréséhez használhatja a `@triggerOutputs()` függvényt. A kimenet a következő példához hasonló lehet:

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

A `body` tulajdonság kimondottan való eléréséhez használhatja a `@triggerBody()` parancsikont.

## <a name="respond-to-requests"></a>Válaszadás a kérelmekre

Előfordulhat, hogy válaszolni szeretne bizonyos, a logikai alkalmazást elindító kérelmekre, ha tartalmat ad vissza a hívónak. A válaszhoz tartozó állapotkód, fejléc és törzs létrehozásához használhatja a **Válasz** műveletet. Ez a művelet bárhol megjelenhet a logikai alkalmazásban, nem csak a munkafolyamat végén.

> [!NOTE] 
> Ha a logikai alkalmazás nem tartalmaz **választ**, a http-végpont *azonnal* válaszol a **202 elfogadott** állapottal. Emellett ahhoz, hogy az eredeti kérelem megkapja a választ, a válaszhoz szükséges összes lépésnek a [kérés időkorlátján](./logic-apps-limits-and-config.md) belül kell futnia, kivéve, ha a munkafolyamatot beágyazott logikai alkalmazásként hívja meg. Ha nem érkezik válasz ezen a korláton belül, a bejövő kérelem időtúllépést okoz, és megkapja a HTTP-válasz 408-es **ügyfél időkorlátját**. A beágyazott logikai alkalmazások esetében a szülő logikai alkalmazás továbbra is vár a válasz befejeződéséig, függetlenül attól, hogy mennyi időre van szükség.

### <a name="construct-the-response"></a>A válasz felépítése

A válasz törzsében több fejlécet és bármilyen típusú tartalmat is felvehet. A példában szereplő válaszban a fejléc azt adja meg, hogy a válasz tartalomtípusa `application/json`. a törzs pedig `title` és `name` tartalmaz a **kérelem** -triggerhez korábban frissített JSON-séma alapján.

![Adja meg a válasz tartalmát a HTTP-válasz művelethez](./media/logic-apps-http-endpoint/content-for-response-action.png)

A válaszok a következő tulajdonságokkal rendelkeznek:

| Tulajdonság | Leírás |
| --- | --- |
| statusCode |Megadja a bejövő kérelem megválaszolásához szükséges HTTP-állapotkódot. Ez a kód bármely érvényes állapotkód lehet, amely 2xx, 4xx vagy 5xx kezdődik. A 3xx-állapotkódok azonban nem engedélyezettek. |
| fejlécek |Meghatározza a válaszban szerepeltetni kívánt fejlécek számát. |
| törzse |Egy olyan szövegtörzset határoz meg, amely lehet egy karakterlánc, egy JSON-objektum, vagy akár egy előző lépésben hivatkozott bináris tartalom is. |

A JSON-séma mostantól a **Válasz** művelethez hasonlít:

``` json
"Response": {
   "type": "Response",
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
   "runAfter": {}
}
```

> [!TIP]
> A logikai alkalmazás teljes JSON-definíciójának megtekintéséhez a Logic app Designerben válassza a **kód nézet**lehetőséget.

## <a name="q--a"></a>Kérdések és válaszok

#### <a name="q-what-about-url-security"></a>K: Mi az URL-biztonság?

A: az Azure biztonságos módon generálja a logikai alkalmazás visszahívási URL-címeit egy közös hozzáférési aláírás (SAS) használatával. Ez az aláírás lekérdezési paraméterként halad át, és ellenőrizni kell, hogy a logikai alkalmazás képes-e tüzet elérni. Az Azure egy titkos kulcs egyedi kombinációjával, a trigger nevével és a végrehajtott művelettel hozza létre az aláírást. Tehát ha valaki hozzáfér a titkos logikai alkalmazás kulcsához, nem tud érvényes aláírást előállítani.

   > [!IMPORTANT]
   > Éles és biztonságos rendszerek esetében javasoljuk, hogy közvetlenül a böngészőből hívja meg a logikai alkalmazást, mert:
   > 
   > * A megosztott elérési kulcs megjelenik az URL-címben.
   > * A logikai alkalmazás ügyfelei között megosztott tartományok miatt nem kezelheti a biztonságos tartalmi házirendeket.

#### <a name="q-can-i-configure-http-endpoints-further"></a>K: tud további HTTP-végpontokat konfigurálni?

Válasz: igen, a HTTP-végpontok az [Azure API Management](../api-management/api-management-key-concepts.md)segítségével fejlettebb konfigurációt támogatnak. Ezzel a szolgáltatással következetesen kezelheti az összes API-t, beleértve a Logic apps-t, egyéni tartományneveket állíthat be, több hitelesítési módszert használhat, többek között például:

* [A kérelem módszerének módosítása](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [A kérelem URL-szegmensének módosítása](../api-management/api-management-transformation-policies.md#RewriteURL)
* A API Management-tartományok beállítása a [Azure Portal](https://portal.azure.com/)
* Házirend beállítása az egyszerű hitelesítés kereséséhez

#### <a name="q-what-changed-when-the-schema-migrated-from-the-december-1-2014-preview"></a>K: mi változott, amikor a séma áttelepítése a 2014-es december 1-től megtekinthető?

A: Íme egy összefoglaló a következő változásokról:

| 2014. december 1. előzetes verzió | 2016. június 1. |
| --- | --- |
| Kattintson a **http-figyelő** API-alkalmazás lehetőségre. |Kattintson a **manuális triggerre** (nincs szükség API-alkalmazásra) |
| HTTP-figyelő beállítása "*Válasz küldése automatikusan*" |Adjon meg egy **választ** vagy nem a munkafolyamat-definícióban. |
| Alapszintű vagy OAuth hitelesítés konfigurálása |API Management keresztül |
| HTTP-metódus konfigurálása |A **Speciális beállítások megjelenítése**területen válasszon egy http-metódust. |
| Relatív elérési út konfigurálása |A **Speciális beállítások megjelenítése**területen adjon hozzá egy relatív elérési utat |
| Hivatkozás a beérkező törzsre `@triggerOutputs().body.Content` |Hivatkozás `@triggerOutputs().body` |
| **Http-válasz küldése** művelet a http-figyelőn |Kattintson **a válasz http-kérelemre** (nincs szükség API-alkalmazásra) |

## <a name="next-steps"></a>Következő lépések

* [Logikaialkalmazás-definíciók készítése](logic-apps-author-definitions.md)
* [Hibák és kivételek kezelése](logic-apps-exception-handling.md)
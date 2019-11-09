---
title: Logikai alkalmazások hívása, triggere vagy beágyazása – Azure Logic Apps
description: HTTP-végpontok beállítása a logikai alkalmazások munkafolyamatainak meghívásához, triggeréhez vagy beágyazásához Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.workload: integration
author: ecfan
ms.author: klam
ms.reviewer: klam, jehollan, LADocs
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: 27a3bf036cce27a5f215068ff71928cb7e181452
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73833916"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>Logikai alkalmazások hívása, elindítása vagy beágyazása HTTP-végpontok használatával Azure Logic Apps

Annak érdekében, hogy a logikai alkalmazás egy URL-címen legyen meghívható, hogy a logikai alkalmazás fogadni tudja a bejövő kéréseket más szolgáltatásokból, natív módon elérhetővé teheti a szinkron HTTP-végpontot triggerként a logikai alkalmazásban. Ha beállítja ezt a képességet, a logikai alkalmazás más logikai alkalmazásokban is beágyazható, ami lehetővé teszi a hívható végpontok mintázatának létrehozását.

Egy HTTP-végpont beállításához használhatja az alábbi típusú triggereket, amelyek lehetővé teszik a logikai alkalmazások számára a bejövő kérések fogadását:

* [Kérés](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Felügyelt összekötő-eseményindítók, amelyek rendelkeznek a [ApiConnectionWebhook típussal](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) , és fogadhatnak bejövő http-kéréseket

> [!NOTE]
> Ezek a példák a kérelem triggert használják, de az előző listában szereplő HTTP-kérelmeken alapuló triggereket is használhat. Az összes elv ugyanezt a más típusú triggerekre is alkalmazza.

Ha most ismerkedik a Logic apps szolgáltatással, tekintse meg a [Mi az a Azure Logic apps](../logic-apps/logic-apps-overview.md) és a gyors útmutató [: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az a logikai alkalmazás, amelyben a HTTP-végpontot triggerként szeretné beállítani. Elindíthat egy üres logikai alkalmazást vagy egy meglévő logikai alkalmazást, ahol az aktuális triggert le szeretné cserélni. Ez a példa egy üres logikai alkalmazással kezdődik.

## <a name="create-a-callable-endpoint"></a>Hívható végpont létrehozása

1. Bejelentkezés az [Azure Portalra](https://portal.azure.com). Hozzon létre és nyisson meg egy üres logikai alkalmazást a Logic app Designerben.

   Ez a példa a kérelem triggert használja, de bármilyen olyan triggert használhat, amely képes fogadni a bejövő HTTP-kérelmeket. Ezek az eseményindítók mindegyike azonos elveket alkalmaz. További információ a kérések triggeréről: [fogadás és válaszadás a bejövő HTTPS-hívásokra Azure Logic Apps használatával](../connectors/connectors-native-reqres.md).

1. A keresőmezőbe válassza a **beépített**lehetőséget. A keresőmezőbe írja be a `request` szűrőt. Az eseményindítók listából válassza ki, **hogy mikor érkezik HTTP-kérelem**.

   ![A kérelem triggerének megkeresése és kiválasztása](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. Ha szükséges, a **kérelem törzse JSON-sémája** mezőben megadhat egy JSON-sémát, amely leírja azt a hasznos adattartalmat vagy adatot, amelyre az indítás várható.

   A tervező ezt a sémát használja az aktiválási kimeneteket jelképező tokenek létrehozásához. Ezután egyszerűen hivatkozhat ezekre a kimenetekre a logikai alkalmazás munkafolyamata során. További információ a [JSON-sémák által generált tokenekről](#generated-tokens).

   Ebben a példában adja meg a következő sémát:

   ```json
      {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "streetNumber": {
                  "type": "string"
               },
               "streetName": {
                  "type": "string"
               },
               "town": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "string"
               }
            }
         }
      }
   }
    ```

   ![JSON-séma megadása a kérelem műveletéhez](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Vagy létrehozhat egy JSON-sémát egy minta hasznos adat biztosításával:

   1. A **kérelem** triggerben válassza a **minta hasznos adatok használata a séma létrehozásához**lehetőséget.

   1. Az **írja be vagy illessze be a minta JSON-adattartalmat** mezőbe írja be a minta hasznos adatait, például:

      ```json
      {
         "address": {
            "streetNumber": "00000",
            "streetName": "AnyStreet",
            "town": "AnyTown",
            "postalCode": "11111-1111"
        }
      }
      ```

   1. Ha elkészült, válassza a **kész**lehetőséget.

      A **kérelem törzse JSON-sémája** most már megjeleníti a generált sémát.

1. Mentse a logikai alkalmazást.

   A **http-bejegyzés erre az URL-címre** mező most megjeleníti a generált visszahívási URL-címet, amelyet más szolgáltatások használhatnak a logikai alkalmazás meghívásához és elindításához. Ez az URL-cím tartalmaz egy közös hozzáférésű aláírás (SAS) kulcsot, amelyet a rendszer a hitelesítéshez használ a lekérdezési paraméterekben, például:

   ![A végponthoz generált visszahívási URL-cím](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   A HTTP-végpont URL-címét a logikai alkalmazás **Áttekintés** paneljéről is lekérheti.

   1. A logikai alkalmazás menüjében válassza az **Áttekintés**lehetőséget.

   1. Az **Összefoglalás** szakaszban válassza az **aktiválási előzmények megtekintése**lehetőséget.

      ![HTTP-végpont URL-címének lekérése Azure Portal](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. A **visszahívási URL [post]** alatt másolja ki az URL-címet:

      ![HTTP-végpont URL-címének másolása Azure Portal](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Az URL-címet a következő hívással kérheti le:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>A várt HTTP-metódus beállítása

Alapértelmezés szerint a kérelem triggere HTTP POST-kérelmet vár. Azonban eltérő módszert is megadhat, de csak egy metódust.

1. A kérelem triggerben nyissa meg az **új paraméter hozzáadása** listát, és válassza a **metódus**lehetőséget, amely hozzáadja ezt a tulajdonságot az triggerhez.

   ![A "Method" tulajdonság hozzáadása az aktiváláshoz](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. A **metódus** listából válassza ki azt a másik metódust, amelyet az trigger elvár. Vagy egyéni metódust is megadhat.

   Válassza például a **Get** metódust, hogy később tesztelje a http-végpont URL-címét.

   ![Válassza ki az triggerhez használni kívánt HTTP-metódust.](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Paraméterek elfogadása a végpont URL-címében

Ha azt szeretné, hogy a végpont URL-címe fogadja a paramétereket, adja meg a relatív elérési utat az triggerben. Emellett explicit módon [meg kell adnia a](#set-method) HTTP-kérelem várható metódusát is.

1. A kérelem triggerben nyissa meg az **új paraméter hozzáadása** listát, és válassza a **relatív elérési út**lehetőséget, amely hozzáadja ezt a tulajdonságot az triggerhez.

   ![A "relatív elérési út" tulajdonság hozzáadása az aktiváláshoz](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. A **relatív elérési út** tulajdonságnál adja meg a JSON-séma paraméterének relatív elérési útját, amelyet az URL-cím el szeretne fogadni (például `address/{postalCode}`).

   ![A paraméter relatív elérési útjának megadása](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. A paraméter használatához keresse meg és adjon hozzá egy **Response** műveletet a logikai alkalmazáshoz.

   1. A kérelem trigger alatt válassza az **új lépés** > **művelet hozzáadása**lehetőséget.

   1. A **válasszon műveletet**területen a keresőmezőbe írja be a `response` szűrőt.

   1. A műveletek listából válassza ki a **Válasz** műveletet.

1. A válasz tevékenység **törzs** tulajdonságában adja meg az trigger relatív elérési útján megadott paramétert jelölő tokent.

   Tegyük fel például, hogy azt szeretné, hogy a válasz művelet visszaállítsa a `Postal Code: {postalCode}`.

   A **Body (törzs** ) tulajdonságban adja meg a `Postal Code: ` záró szóközzel. A megjelenő dinamikus tartalom listából válassza ki az **Irányítószám** -tokent.

   ![A megadott paraméter hozzáadása a válasz törzséhez](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   A **Body (törzs** ) tulajdonság mostantól a kiválasztott paramétert is tartalmazza:

   ![Példa válasz törzsére paraméterrel](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Mentse a logikai alkalmazást.

    A HTTP-végpont URL-címe mostantól tartalmazza a relatív elérési utat, például:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. A HTTP-végpont teszteléséhez másolja és illessze be a frissített URL-címet egy másik böngészőablakba, de cserélje le a `{postalCode}`t a `123456`ra, majd nyomja le az ENTER billentyűt.

   A böngészőben a következő szöveg jelenik meg: `Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Logikai alkalmazás hívása HTTP-végponton keresztül

Miután létrehozta a HTTP-végpontot, aktiválhatja a logikai alkalmazást egy HTTP `POST` kérelem küldésével a végpont teljes URL-címére. A Logic apps beépített támogatást nyújt a közvetlen elérésű végpontokhoz.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Séma alapján generált tokenek

Ha JSON-sémát ad meg a kérelem-triggerben, a Logic app Designer jogkivonatokat hoz létre a séma tulajdonságaihoz. Ezeket a jogkivonatokat használhatja a logikai alkalmazás munkafolyamatán keresztüli adattovábbításhoz.

Ha például további tulajdonságokat (például `"suite"`) ad hozzá a JSON-sémához, akkor ezekhez a tulajdonságokhoz tartozó jogkivonatok használhatók a logikai alkalmazás későbbi lépéseiben. Itt látható a teljes JSON-séma:

```json
   {
   "type": "object",
   "properties": {
      "address": {
         "type": "object",
         "properties": {
            "streetNumber": {
               "type": "string"
            },
            "streetName": {
               "type": "string"
            },
            "suite": {
               "type": "string"
            },
            "town": {
               "type": "string"
            },
            "postalCode": {
               "type": "string"
            }
         }
      }
   }
}
```

## <a name="create-nested-logic-apps"></a>Beágyazott logikai alkalmazások létrehozása

A munkafolyamatokat a logikai alkalmazásban úgy ágyazhatja be, hogy más, a kérelmeket fogadó logikai alkalmazásokat is felvesz. A logikai alkalmazások belefoglalásához kövesse az alábbi lépéseket:

1. A lépés alatt, ahol másik logikai alkalmazást szeretne meghívni, válassza az **új lépés** > **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen válassza a **beépített**lehetőséget. A keresőmezőbe írja be a `logic apps` szűrőt. A műveletek listából válassza ki **a Logic apps munkafolyamatot**.

   ![Logikai alkalmazás beágyazása az aktuális logikai alkalmazásba](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   A tervező megjeleníti a jogosult logikai alkalmazásokat a kiválasztáshoz.

1. Válassza ki az aktuális logikai alkalmazásból hívni kívánt logikai alkalmazást.

   ![Válassza ki az aktuális logikai alkalmazásból hívni kívánt logikai alkalmazást](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Hivatkozás egy bejövő kérelem tartalmára

Ha a bejövő kérelem tartalomtípusa `application/json`, akkor hivatkozhat a bejövő kérelemben szereplő tulajdonságokra. Ellenkező esetben ezt a tartalmat egyetlen bináris egységként kezeli a rendszer, amelyet átadhat más API-khoz. Ha ezt a tartalmat a logikai alkalmazás munkafolyamatán belül szeretné hivatkozni, először konvertálnia kell a tartalmat.

Ha például átadja a `application/xml` típusú tartalmat, a [`@xpath()` kifejezéssel](../logic-apps/workflow-definition-language-functions-reference.md#xpath) elvégezheti az XPath kibontását, vagy használhatja az [`@json()` kifejezést](../logic-apps/workflow-definition-language-functions-reference.md#json) az XML-fájl JSON formátumra való konvertálásához. További információ a támogatott [tartalomtípusok](../logic-apps/logic-apps-content-type.md)használatáról.

A bejövő kérelem kimenetének lekéréséhez használhatja a [`@triggerOutputs` kifejezést](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs). Tegyük fel például, hogy olyan kimenettel rendelkezik, amely a következő példához hasonlít:

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

A `body` tulajdonság eléréséhez használhatja a [`@triggerBody()` kifejezést](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) parancsikonként.

## <a name="respond-to-requests"></a>Válaszadás a kérelmekre

Időnként a logikai alkalmazást kiváltó bizonyos kérelmekre szeretne válaszolni, ha tartalmat ad vissza a hívónak. A válaszhoz tartozó állapotkód, fejléc és törzs létrehozásához használja a válasz műveletet. Ez a művelet bárhol megjelenhet a logikai alkalmazásban, nem csak a munkafolyamat végén. Ha a logikai alkalmazás nem tartalmaz válasz műveletet, a HTTP-végpont *azonnal* válaszol az **202 elfogadott** állapottal.

Ahhoz, hogy az eredeti hívó sikeresen lekérje a választ, a válaszhoz szükséges összes lépésnek a [kérés időkorlátján](./logic-apps-limits-and-config.md) belül kell lennie, kivéve, ha az aktivált logikai alkalmazást beágyazott logikai alkalmazásnak nevezzük. Ha ezen a korláton belül nem ad vissza választ, a bejövő kérelem időtúllépést okoz, és megkapja az **408-ügyfél időkorlát** -válaszát.

A beágyazott logikai alkalmazások esetében a szülő logikai alkalmazás továbbra is vár a válaszra, amíg az összes lépést be nem fejeződik, függetlenül attól, hogy mennyi idő szükséges.

### <a name="construct-the-response"></a>A válasz felépítése

A válasz törzsében több fejlécet és bármilyen típusú tartalmat is megadhat. A válasz fejléce például azt adja meg, hogy a válasz tartalomtípusa `application/json`, és hogy a törzs a `town` és a `postalCode` tulajdonságok értékeit tartalmazza, a kérelem triggeréhez a témakörben korábban ismertetett JSON-séma alapján.

![Adja meg a válasz tartalmát a HTTP-válasz művelethez](./media/logic-apps-http-endpoint/content-for-response-action.png)

A válaszok a következő tulajdonságokkal rendelkeznek:

| Tulajdonság (megjelenítés) | Tulajdonság (JSON) | Leírás |
|--------------------|-----------------|-------------|
| **Állapotkód** | `statusCode` | A bejövő kérelemre adott válaszban használandó HTTP-állapotkód. Ez a kód bármely érvényes állapotkód lehet, amely 2xx, 4xx vagy 5xx kezdődik. A 3xx-állapotkódok azonban nem engedélyezettek. |
| **Fejlécek** | `headers` | Egy vagy több, a válaszban szerepeltetni kívánt fejléc |
| **Törzs** | `body` | Olyan szövegtörzs, amely lehet egy sztring, egy JSON-objektum, vagy akár egy előző lépésben hivatkozott bináris tartalom is. |
||||

A válasz művelet JSON-definíciójának és a logikai alkalmazás teljes JSON-definíciójának megtekintéséhez a Logic app Designer eszköztárán válassza a **kód nézet**lehetőséget.

``` json
"Response": {
   "type": "Response",
   "kind": "http",
   "inputs": {
      "body": {
         "postalCode": "@triggerBody()?['address']?['postalCode']",
         "town": "@triggerBody()?['address']?['town']"
      },
      "headers": {
         "content-type": "application/json"
      },
      "statusCode": 200
   },
   "runAfter": {}
}
```

## <a name="q--a"></a>Kérdések és válaszok

#### <a name="q-what-about-url-security"></a>K: Mi az URL-biztonság?

**A**: az Azure biztonságos módon generálja a logikai alkalmazás visszahívási URL-címeit [közös hozzáférésű aláírás (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)használatával. Ez az aláírás lekérdezési paraméterként halad át, és a logikai alkalmazás futtatása előtt érvényesíteni kell. Az Azure egy titkos kulcs egyedi kombinációjával, a trigger nevével és a végrehajtott művelettel hozza létre az aláírást. Tehát ha valaki hozzáfér a titkos logikai alkalmazás kulcsához, nem tud érvényes aláírást előállítani.

> [!IMPORTANT]
> Éles és biztonságos rendszerek esetében erősen ajánljuk, hogy a logikai alkalmazást közvetlenül a böngészőből hívja meg a következő okok miatt:
>
> * A megosztott elérési kulcs megjelenik az URL-címben.
> * Azure Logic Apps ügyfelek megosztott tartományai miatt nem kezelhetők biztonságos tartalmi házirendek.

#### <a name="q-can-i-configure-http-endpoints-further"></a>K: tud további HTTP-végpontokat konfigurálni?

Válasz **: igen**, a http-végpontok az [Azure API Management](../api-management/api-management-key-concepts.md)segítségével fejlettebb konfigurációt támogatnak. Ezzel a szolgáltatással következetesen kezelheti az összes API-t, beleértve a Logic apps-t, egyéni tartományneveket állíthat be, több hitelesítési módszert használhat, többek között például:

* [A kérelem módszerének módosítása](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [A kérelem URL-szegmensének módosítása](../api-management/api-management-transformation-policies.md#RewriteURL)
* A API Management-tartományok beállítása a [Azure Portal](https://portal.azure.com/)
* Házirend beállítása az egyszerű hitelesítés kereséséhez

## <a name="next-steps"></a>További lépések

* [Bejövő HTTPS-hívások fogadása és válaszadás a Azure Logic Apps használatával](../connectors/connectors-native-reqres.md)
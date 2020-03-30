---
title: Logikai alkalmazások hívása, aktiválása vagy beágyazása
description: HTTP-végpontok beállítása az Azure Logic Apps-munkafolyamatok hívására, aktiválására vagy beágyazására
services: logic-apps
ms.workload: integration
ms.reviewer: klam, jehollan, logicappspm
ms.topic: article
ms.date: 11/04/2019
ms.openlocfilehash: d5b5a69c7927d07c0ae6b3b56ec97b6551e5d46b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191334"
---
# <a name="call-trigger-or-nest-logic-apps-by-using-http-endpoints-in-azure-logic-apps"></a>A logikai alkalmazások hívása, aktiválása vagy beágyazása HTTP-végpontok használatával az Azure Logic Apps alkalmazásban

Annak érdekében, hogy a logikai alkalmazás hívékon keresztül egy URL-t, hogy a logikai alkalmazás képes fogadni a bejövő kérelmeket más szolgáltatásokból, natív módon elérhetővé teheti a szinkron HTTP-végpont, mint egy eseményindító az adott logikai alkalmazás. Ha beállítja ezt a funkciót, a logikai alkalmazást más logikai alkalmazásokba is beágyazhatja, így létrehozhat egy hívható végpontok mintáját.

HTTP-végpont beállításához az alábbi eseményindító-típusok bármelyikét használhatja, amelyek lehetővé teszik a logikai alkalmazások számára a bejövő kérések fogadását:

* [Kérés](../connectors/connectors-native-reqres.md)
* [HTTP Webhook](../connectors/connectors-native-webhook.md)
* Felügyelt összekötő eseményindítók, amelyek [apiConnectionWebhook típusú,](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) és fogadhat bejövő HTTP-kérelmek

> [!NOTE]
> Ezek a példák a kérelem eseményindító, de használhatja az előző listában szereplő HTTP-kérelem-alapú eseményindító. Minden alapelv azonos vonatkozik ezekre a többi eseményindító típusra.

Ha most kezdi meg a logikai alkalmazásokat, olvassa el [a Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a rövid [útmutató: Az első logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazás, ahol be szeretné állítani a HTTP-végpont, mint az eseményindító. Kezdheti egy üres logikai alkalmazással vagy egy meglévő logikai alkalmazással, ahol le szeretné cserélni az aktuális eseményindítót. Ez a példa egy üres logikai alkalmazással kezdődik.

## <a name="create-a-callable-endpoint"></a>Hívható végpont létrehozása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Hozzon létre és nyisson meg egy üres logikai alkalmazást a Logic App Designerben.

   Ez a példa a Kérelem eseményindítót használja, de bármilyen eseményindítót használhat, amely fogadhat bejövő HTTP-kéréseket. Minden alapelv azonos vonatkozik ezekre a kiváltó eseményre. A kérelemeseményindítóról további információt a [bejövő HTTPS-hívások fogadása és megválaszolása az Azure Logic Apps használatával című témakörben talál.](../connectors/connectors-native-reqres.md)

1. A keresőmező alatt válassza a **Beépített**lehetőséget. A keresőmezőbe írja `request` be szűrőként. Az eseményindítók listájában válassza a **HTTP-kérelem fogadásának kori lehetőséget.**

   ![A Kérelem eseményindító megkeresése és kiválasztása](./media/logic-apps-http-endpoint/find-and-select-request-trigger.png)

1. A **Kérelem törzse JSON-séma** mezőben megadhat egy JSON-sémát, amely leírja a hasznos értéket vagy az okat az adatokat, amelyekvárhatóan az eseményindítót fogadja.

   A tervező ezt a sémát használja az eseményindító kimeneteket képviselő jogkivonatok létrehozásához. Ezután könnyedén hivatkozhat ezekre a kimenetekre a logikai alkalmazás munkafolyamatában. További információ a [JSON-sémákból generált jogkivonatokról.](#generated-tokens)

   Ebben a példában adja meg ezt a sémát:

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

   ![JSON-séma biztosítása a kérésművelethez](./media/logic-apps-http-endpoint/manual-request-trigger-schema.png)

   Vagy létrehozhat egy JSON-sémát egy minta hasznos adat megadásával:

   1. A **kérelem** eseményindító, válassza **a Minta hasznos adat létrehozása séma.**

   1. Az **Enter or paste a minta JSON hasznos adat** mezőben adja meg a minta hasznos adatait, például:

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

   1. Ha készen áll, válassza a **Kész gombot.**

      A **Kérelem törzsJSON-séma** mező most a létrehozott sémát mutatja.

1. Mentse a logikai alkalmazást.

   Az **URL-címHEZ írt HTTP-bejegyzés** most már a létrehozott visszahívási URL-címet mutatja, amelyet más szolgáltatások a logikai alkalmazás hívásához és aktiválásához használhatnak. Ez az URL-cím tartalmaz egy SAS-kulcsot, amely hitelesítésre szolgál a lekérdezési paraméterekben, például:

   ![Létrehozva a végpont visszahívási URL-címe](./media/logic-apps-http-endpoint/generated-endpoint-url.png)

   A HTTP-végpont URL-címét a logikai alkalmazás **áttekintő** ablaktáblájáról is leolvashatja.

   1. A logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget.

   1. Az **Összegzés** csoportban válassza az **Eseményindító előzményeinek megtekintése**lehetőséget.

      ![HTTP-végpont URL-címének beszereznie az Azure Portalról](./media/logic-apps-http-endpoint/find-manual-trigger-url.png)

   1. A **Visszahívás url[POST] csoportban**másolja az URL-t:

      ![HTTP-végpont URL-címének másolása az Azure Portalról](./media/logic-apps-http-endpoint/copy-manual-trigger-callback-url.png)

      Vagy lehet kapni az URL-t, hogy ezt a hívást:

      ```http
      POST https://management.azure.com/{logic-app-resource-ID}/triggers/{endpoint-trigger-name}/listCallbackURL?api-version=2016-06-01
      ```

<a name="set-method"></a>

## <a name="set-expected-http-method"></a>A várt HTTP-módszer beállítása

Alapértelmezés szerint a kérelem eseményindító http-posta kérést vár. Azonban megadhat egy másik módszert, de csak egy metódust.

1. A Kérelem eseményindítóban nyissa meg az **Új paraméter hozzáadása** listát, és válassza a Metódus **lehetőséget,** amely hozzáadja ezt a tulajdonságot az eseményindítóhoz.

   !["Metódus" tulajdonság hozzáadása az eseményindítóhoz](./media/logic-apps-http-endpoint/select-add-new-parameter-for-method.png)

1. A **Metódus listából** válasszon egy másik módszert, amelyet az eseményindító elvár. Egyéni metódust is megadhat.

   Válassza ki például a **GET** metódust, hogy később tesztelhesse a HTTP-végpont URL-címét.

   ![Válassza az eseményindítóhoz használandó HTTP-metódust](./media/logic-apps-http-endpoint/select-method-request-trigger.png)

## <a name="accept-parameters-in-endpoint-url"></a>Paraméterek elfogadása a végpont URL-címében

Ha azt szeretné, hogy a végpont URL-címe elfogadja a paramétereket, adja meg a relatív elérési utat az eseményindítóban. Azt is explicit módon kell [beállítani a módszert,](#set-method) amely a HTTP-kérelem vár.

1. A Kérelem eseményindítóban nyissa meg az **Új paraméter hozzáadása** listát, és válassza a Relatív elérési **út**lehetőséget, amely hozzáadja ezt a tulajdonságot az eseményindítóhoz.

   !["Relatív elérési út" tulajdonság hozzáadása az aktiváláshoz](./media/logic-apps-http-endpoint/select-add-new-parameter-for-relative-path.png)

1. A **Relatív elérési út** tulajdonságban adja meg a JSON-séma azon paraméterének relatív `address/{postalCode}`elérési útját, amelyet például az URL-címnek el kell fogadnia.

   ![A paraméter relatív elérési útjának megadása](./media/logic-apps-http-endpoint/relative-path-url-value.png)

1. A paraméter használatához keressen meg egy **válaszműveletet** a logikai alkalmazáshoz, és adjon hozzá egy válaszműveletet.

   1. A Kérelem eseményindító csoportban válassza az **Új lépés** > **Művelet hozzáadása**lehetőséget.

   1. A **Művelet kiválasztása csoport**keresőmezőjében `response` írja be szűrőként jelölőnégyzetet.

   1. A műveletek listájában válassza a **Válasz** műveletet.

1. A Válasz művelet **Törzs** tulajdonságában adja meg azt a jogkivonatot, amely az eseményindító relatív elérési útján megadott paramétert jelöli.

   Tegyük fel például, hogy azt `Postal Code: {postalCode}`szeretné, hogy a Válasz művelet visszatérjen.

   A **Body** tulajdonságba `Postal Code: ` lépjen be egy záró térrel. A megjelenő dinamikus tartalomlistából válassza ki a **postalCode** jogkivonatot.

   ![Adja hozzá a megadott paramétert a választörzshöz](./media/logic-apps-http-endpoint/relative-url-with-parameter-token.png)

   A **Body** tulajdonság mostantól tartalmazza a kiválasztott paramétert:

   ![Példa választörzs e paraméterrel](./media/logic-apps-http-endpoint/relative-url-with-parameter.png)

1. Mentse a logikai alkalmazást.

    A HTTP-végpont URL-címe most már tartalmazza a relatív elérési utat, például:

    ```http
    https://prod-25.westus.logic.azure.com/workflows/{logic-app-resource-ID}/triggers/manual/paths/invoke/address/{postalCode}?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig={shared-access-signature}
    ```

1. A HTTP-végpont teszteléséhez másolja és illessze be a `{postalCode}` frissített `123456`URL-címet egy másik böngészőablakba, de cserélje le a lehetőségre, és nyomja le az Enter billentyűt.

   A böngésző jegeli ezt a szöveget:`Postal Code: 123456`

## <a name="call-logic-app-through-http-endpoint"></a>Logikai alkalmazás hívása HTTP-végponton keresztül

A HTTP-végpont létrehozása után a logikai alkalmazást egy `POST` HTTP-kérelem elküldésével aktiválhatja a végpont teljes URL-címére. A logikai alkalmazások beépített támogatással rendelkeznek a közvetlen hozzáférésű végpontokhoz.

<a name="generated-tokens"></a>

## <a name="tokens-generated-from-schema"></a>Sémából létrehozott tokenek

Amikor egy JSON-sémát a kérelem eseményindító, a Logic App Designer jogkivonatokat hoz létre a tulajdonságok az adott séma. Ezután használhatja ezeket a jogkivonatokat a logikai alkalmazás munkafolyamatán keresztül történő adatátadáshoz.

Ha például további tulajdonságokat ad hozzá, például `"suite"`a JSON-sémához, akkor ezekhez a tulajdonságokhoz jogkivonatok használhatók a logikai alkalmazás későbbi lépéseiben. Itt van a teljes JSON-séma:

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

A logikai alkalmazásban beágyazhatja a munkafolyamatokat más logikai alkalmazások hozzáadásával, amelyek kéréseket fogadhatnak. A logikai alkalmazások felvételéhez hajtsa végre az alábbi lépéseket:

1. A lépés alatt, ahol egy másik logikai alkalmazást szeretne hívni, válassza az **Új lépés** > **Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja `logic apps` be szűrőként. A műveletek listájában válassza a **Logic Apps munkafolyamat kiválasztása**lehetőséget.

   ![Logikai alkalmazás beágyazása az aktuális logikai alkalmazásba](./media/logic-apps-http-endpoint/choose-logic-apps-workflow.png)

   A tervező megjeleníti a kijelölt kijelölt logikai alkalmazásokat.

1. Válassza ki a jelenlegi logikai alkalmazásból hívandó logikai alkalmazást.

   ![A hívandó logikai alkalmazás kiválasztása az aktuális logikai alkalmazásból](./media/logic-apps-http-endpoint/select-logic-app-to-nest.png)

## <a name="reference-content-from-an-incoming-request"></a>Hivatkozási tartalom bejövő kérelemből

Ha a bejövő kérelem tartalomtípusa `application/json`a , hivatkozhat a tulajdonságokra a bejövő kérelemben. Ellenkező esetben ez a tartalom egyetlen bináris egységként lesz kezelve, amelyet más API-knak is átadhat. A logikai alkalmazás munkafolyamatának tartalmára való hivatkozáshoz először konvertálnia kell a tartalmat.

Ha például olyan tartalmat ad át, amely nek típusa van, `application/xml` a [ `@xpath()` kifejezéssel](../logic-apps/workflow-definition-language-functions-reference.md#xpath) XPath-kibontást hajthat végre, vagy használhatja az [ `@json()` ](../logic-apps/workflow-definition-language-functions-reference.md#json) XML JSON-ra történő konvertálására szolgáló kifejezést. További információ a támogatott [tartalomtípusok](../logic-apps/logic-apps-content-type.md)ról.

A kimenet bekérése egy bejövő kérelemből, [ `@triggerOutputs` használhatja](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs)a kifejezést. Tegyük fel például, hogy a kimenet e példához hasonlóan néz ki:

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

Ha kifejezetten `body` a tulajdonságot szeretné elérni, a [ `@triggerBody()` kifejezést](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) parancsikonként használhatja.

## <a name="respond-to-requests"></a>Kérések megválaszolása

Néha szeretne válaszolni bizonyos kérésekre, amelyek a logikai alkalmazást a tartalom visszaadásával indítják el. Az állapotkód, a fejléc és a törzs létrehozásához használja a Válasz műveletet. Ez a művelet bárhol megjelenhet a logikai alkalmazásban, nem csak a munkafolyamat végén. Ha a logikai alkalmazás nem tartalmaz válaszműveletet, a HTTP-végpont *azonnal* válaszol a **202 Elfogadott** állapottal.

Ahhoz, hogy az eredeti hívó sikeresen megkapja a választ, a válaszhoz szükséges összes lépésnek be kell fejeződnie a [kérelem időkorláton](./logic-apps-limits-and-config.md) belül, kivéve, ha az aktivált logikai alkalmazást beágyazott logikai alkalmazásként hívják meg. Ha ezen a korláton belül nem érkezik válasz, a bejövő kérelem túllépi a számat, és megkapja a **408-as ügyfél időtúllépési** válaszát.

Beágyazott logikai alkalmazások esetén a szülő logikai alkalmazás továbbra is várja a választ, amíg az összes lépést el nem végezték, függetlenül attól, hogy mennyi időre van szükség.

### <a name="construct-the-response"></a>A válasz összeállítása

A választörzsben több fejlécet és bármilyen típusú tartalmat is felvehet. A válasz fejlécének fejléce például azt adja meg, hogy a válasz `application/json` `town` tartalomtípusa, és hogy a törzs a témakörben ismertetett JSON-séma alapján tartalmazza-e a és `postalCode` a tulajdonságok értékeit.

![Választartalom biztosítása a HTTP-válasz művelethez](./media/logic-apps-http-endpoint/content-for-response-action.png)

A válaszok a következő tulajdonságokkal rendelkeznek:

| Tulajdonság (megjelenítés) | Ingatlan (JSON) | Leírás |
|--------------------|-----------------|-------------|
| **Állapotkód** | `statusCode` | A bejövő kérelemre adott válaszban használandó HTTP-állapotkód. Ez a kód lehet bármilyen érvényes állapotkód, amely 2xx, 4xx vagy 5xx kezdetű. A 3xx állapotkódok azonban nem engedélyezettek. |
| **Fejlécek** | `headers` | Egy vagy több fejléc, amely szerepel a válaszban |
| **Törzs** | `body` | Olyan törzsobjektum, amely lehet karakterlánc, JSON-objektum vagy akár egy előző lépésből hivatkozott bináris tartalom is |
||||

A Válasz művelet JSON-definíciójának és a logikai alkalmazás teljes JSON-definíciójának megtekintéséhez a Logic App Designer eszköztáron válassza a **Kód nézet lehetőséget.**

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

#### <a name="q-what-about-url-security"></a>K: Mi a helyzet az URL-biztonsággal?

**A**: Az Azure biztonságosan generál logikai alkalmazás visszahívási URL-címeket a [Megosztott hozzáférésű aláírás (SAS)](https://docs.microsoft.com/rest/api/storageservices/delegate-access-with-shared-access-signature)használatával. Ez az aláírás lekérdezési paraméterként halad át, és a logikai alkalmazás futtatása előtt ellenőrizni kell. Az Azure egy titkos kulcs egyedi kombinációjával hozza létre az aláírást logikai alkalmazásonként, az eseményindító nevét és a végrehajtott műveletet. Így ha valaki nem fér hozzá a titkos logikai alkalmazás kulcsához, nem tud érvényes aláírást létrehozni.

> [!IMPORTANT]
> Éles és magasabb szintű biztonsági rendszerek esetén a következő okok miatt javasoljuk, hogy ne hívja fel a logikai alkalmazást közvetlenül a böngészőből:
>
> * A megosztott hozzáférési kulcs megjelenik az URL-címben.
> * Az Azure Logic Apps-ügyfelek megosztott tartományai miatt nem kezelheti a biztonsági tartalomra vonatkozó szabályzatokat.

#### <a name="q-can-i-configure-http-endpoints-further"></a>K: Konfigurálhatom tovább a HTTP-végpontokat?

**A**: Igen, a HTTP-végpontok támogatják a fejlettebb konfigurációt az [Azure API Management](../api-management/api-management-key-concepts.md)en keresztül. Ez a szolgáltatás lehetővé teszi az összes API következetes kezelését is, beleértve a logikai alkalmazásokat, egyéni tartománynevek beállítását, több hitelesítési módszer használatát és egyebeket, például:

* [A kérelem metódusának módosítása](../api-management/api-management-advanced-policies.md#SetRequestMethod)
* [A kérelem URL-szegmenseinek módosítása](../api-management/api-management-transformation-policies.md#RewriteURL)
* Az API-felügyeleti tartományok beállítása az [Azure Portalon](https://portal.azure.com/)
* Az alapfokú hitelesítés ellenőrzésére szolgáló házirend beállítása

## <a name="next-steps"></a>További lépések

* [Bejövő HTTPS-hívások fogadása és megválaszolása az Azure Logic Apps használatával](../connectors/connectors-native-reqres.md)
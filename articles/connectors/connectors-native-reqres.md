---
title: Hívások fogadása és válaszadás a HTTPS használatával
description: Külső szolgáltatásokból érkező bejövő HTTPS-kérések kezelése Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewers: jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/29/2020
tags: connectors
ms.openlocfilehash: 9f3f361b3e9fafdb350f943c0a8adcd87fa06c78
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84325133"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Bejövő HTTPS-kérések fogadása és válasza Azure Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített kérelem-trigger és válasz művelettel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek fogadják és válaszolnak a bejövő HTTPS-kérelmekre. Használhatja például a logikai alkalmazást:

* Egy HTTPS-kérelem fogadása és megválaszolása egy helyszíni adatbázisban lévő adatszolgáltatáshoz.

* Munkafolyamat elindítása külső webhook-esemény bekövetkezésekor.

* Egy másik logikai alkalmazástól érkező HTTPS-hívás fogadása és megválaszolása.

A kérelem-trigger támogatja [Azure Active Directory nyílt hitelesítés](../active-directory/develop/about-microsoft-identity-platform.md) (Azure ad OAuth) használatát a logikai alkalmazás bejövő hívásainak engedélyezéséhez. A hitelesítés engedélyezésével kapcsolatos további információkért tekintse [meg a biztonságos hozzáférés és adatok Azure Logic apps – Azure ad OAuth-hitelesítés engedélyezése](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth)című témakört.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

* A [Logic apps](../logic-apps/logic-apps-overview.md)alapszintű ismerete. Ha most ismerkedik a Logic apps szolgáltatással, Ismerje meg, [hogyan hozhatja létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

* A bejövő hívások *csak* TRANSPORT Layer Security (TLS) 1,2-es támogatást támogatják. Ha TLS-kézfogási hibákat kap, győződjön meg arról, hogy a TLS 1,2-et használja. További információ: [a TLS 1,0-probléma megoldása](https://docs.microsoft.com/security/solving-tls1-problem). A kimenő hívások a TLS 1,0, 1,1 és 1,2 protokollt támogatják a célként megadott végpont képességei alapján.

* A bejövő hívások a következő titkosítási csomagokat támogatják:

  * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256

  * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256

  * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

  * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Kérelem-trigger hozzáadása

Ez a beépített trigger egy manuálisan megadható HTTPS-végpontot hoz létre, amely *csak* a bejövő HTTPS-kérelmek fogadására képes. Ha ez az esemény történik, az eseményindító elindít és futtatja a logikai alkalmazást. Az trigger alapjául szolgáló JSON-definícióval és az trigger meghívásával kapcsolatos további információkért tekintse meg a [kérelem trigger típusának](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) és [hívásának, triggerének vagy beágyazásának munkafolyamatait HTTPS-végpontokkal Azure Logic Appsban](../logic-apps/logic-apps-http-endpoint.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Üres logikai alkalmazás létrehozása.

1. A Logic app Designer megnyitása után a keresőmezőbe írja be `http request` szűrőként a kifejezést. Az eseményindítók listából válassza ki a **http-kérelem fogadása** eseményindítót, amely a logikai alkalmazás munkafolyamatának első lépése.

   ![Kérelem triggerének kiválasztása](./media/connectors-native-reqres/select-request-trigger.png)

   A kérelem-trigger a következő tulajdonságokat jeleníti meg:

   ![Kérelem triggere](./media/connectors-native-reqres/request-trigger.png)

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL-CÍM** | nEz egy | Yes | A logikai alkalmazás mentése után generált végponti URL-cím, amely a logikai alkalmazás meghívására szolgál |
   | **Kérelem törzsének JSON-sémája** | `schema` | No | A bejövő kérelem törzsében található tulajdonságokat és értékeket leíró JSON-séma |
   |||||

1. A **kérelem törzse JSON-sémája** mezőben opcionálisan megadhat egy JSON-sémát, amely leírja a beérkező kérelem törzsét, például:

   ![Példa JSON-sémára](./media/connectors-native-reqres/provide-json-schema.png)

   A tervező ezt a sémát használja a kérelemben szereplő tulajdonságokhoz tartozó jogkivonatok létrehozásához. Ily módon a logikai alkalmazás elemezheti, felhasználhatja és átadhatja a kérelemből származó adatokkal a munkafolyamaton keresztüli triggert.

   Itt látható a minta séma:

   ```json
   {
      "type": "object",
      "properties": {
         "account": {
            "type": "object",
            "properties": {
               "name": {
                  "type": "string"
               },
               "ID": {
                  "type": "string"
               },
               "address": {
                  "type": "object",
                  "properties": {
                     "number": {
                        "type": "string"
                     },
                     "street": {
                        "type": "string"
                     },
                     "city": {
                        "type": "string"
                     },
                     "state": {
                        "type": "string"
                     },
                     "country": {
                        "type": "string"
                     },
                     "postalCode": {
                        "type": "string"
                     }
                  }
               }
            }
         }
      }
   }
   ```

   Ha JSON-sémát ad meg, a tervező egy emlékeztetőt jelenít meg, amely tartalmazza a `Content-Type` fejlécet a kérelemben, és beállítja a fejléc értékét a következőre: `application/json` . További információ: [tartalomtípusok kezelése](../logic-apps/logic-apps-content-type.md).

   ![Emlékeztető a "Content-Type" fejléc belefoglalásához](./media/connectors-native-reqres/include-content-type.png)

   A fejléc a következőképpen néz ki, mint JSON formátumban:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   A várt adattartalom (adatok) alapján létrehozott JSON-séma létrehozásához használhat olyan eszközt, mint például a [JSONSchema.net](https://jsonschema.net), vagy a következő lépéseket teheti:

   1. A kérelem triggerben válassza a **minta hasznos adatok használata a séma létrehozásához**lehetőséget.

      ![Séma előállítása a hasznos adatokból](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Adja meg a minta hasznos adatait, majd válassza a **kész**lehetőséget.

      ![Séma előállítása a hasznos adatokból](./media/connectors-native-reqres/enter-payload.png)

      Itt látható a minta hasznos adat:

      ```json
      {
         "account": {
            "name": "Contoso",
            "ID": "12345",
            "address": { 
               "number": "1234",
               "street": "Anywhere Street",
               "city": "AnyTown",
               "state": "AnyState",
               "country": "USA",
               "postalCode": "11111"
            }
         }
      }
      ```

1. Az alábbi lépéseket követve ellenőrizheti, hogy a bejövő hívás rendelkezik-e a megadott sémának megfelelő kérelem törzsével:

   1. A kérelem triggerének címsorában válassza az ellipszisek (**..**.) gombot.
   
   1. Az trigger beállításainak bekapcsolásához kapcsolja be a **séma érvényesítése**elemet, majd kattintson a **kész**gombra.
   
      Ha a bejövő hívás kérelmének törzse nem felel meg a sémának, az trigger hibát ad vissza `HTTP 400 Bad Request` .

1. További tulajdonságok megadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a hozzáadni kívánt paramétereket.

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **Metódus** | `method` | No | Az a módszer, amelyet a bejövő kérelemnek használnia kell a logikai alkalmazás meghívásához. |
   | **Relatív elérési út** | `relativePath` | No | Annak a paraméternek a relatív elérési útja, amelyet a logikai alkalmazás végpontjának URL-címe el tud fogadni |
   |||||

   Ez a példa hozzáadja a **Method** tulajdonságot:

   ![Metódus hozzáadása paraméter](./media/connectors-native-reqres/add-parameters.png)

   A **Method** tulajdonság megjelenik a triggerben, hogy kiválasszon egy metódust a listából.

   ![Módszer kiválasztása](./media/connectors-native-reqres/select-method.png)

1. Most adjon hozzá egy újabb műveletet a munkafolyamat következő lépéseként. Az trigger alatt válassza a **következő lépés** lehetőséget, hogy megtalálja a hozzáadni kívánt műveletet.

   Választhatja például a kérést [egy válasz művelet hozzáadásával](#add-response), amelyet egy testreszabott válasz visszaadására használhat, és a jelen témakör későbbi részében is ismertetjük.

   A logikai alkalmazás csak [korlátozott ideig](../logic-apps/logic-apps-limits-and-config.md#request-limits)tart nyitva a bejövő kérelemben. Feltételezve, hogy a logikai alkalmazás munkafolyamata tartalmaz egy választ, ha a logikai alkalmazás nem ad vissza választ az adott idő elteltével, a logikai alkalmazás a `504 GATEWAY TIMEOUT` hívót adja vissza. Ellenkező esetben, ha a logikai alkalmazás nem tartalmaz válasz műveletet, a logikai alkalmazás azonnal visszaadja a `202 ACCEPTED` hívónak küldött választ.

1. Ha elkészült, mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

   Ez a lépés a logikai alkalmazást indító kérelem küldéséhez használandó URL-címet hozza létre. Az URL-cím másolásához válassza a másolás ikont az URL mellett.

   ![A logikai alkalmazás aktiválását használó URL-cím](./media/connectors-native-reqres/generated-url.png)

   > [!NOTE]
   > Ha a kivonatot vagy a font szimbólumot () az URI-ban szeretné felvenni a **#** kérelem-trigger hívása során, használja inkább a következő kódolt verziót:`%25%23`

1. A logikai alkalmazás elindításához küldjön egy HTTP-BEJEGYZÉST a generált URL-címre.

   Használhat például egy olyan eszközt, mint például a [Poster](https://www.getpostman.com/) a http post küldését. Ha [engedélyezte Azure Active Directory nyílt hitelesítés](../logic-apps/logic-apps-securing-a-logic-app.md#enable-oauth) (Azure ad OAuth) számára a bejövő hívások engedélyezését a kérelem-trigger számára, hívja meg az triggert egy [megosztott hozzáférés-ALÁÍRÁSi (SAS-) URL-cím](../logic-apps/logic-apps-securing-a-logic-app.md#sas) vagy egy hitelesítési jogkivonat használatával, de nem használhatja mindkettőt. A hitelesítési jogkivonatnak meg kell adnia a `Bearer` típust az engedélyezési fejlécben. További információ: a [biztonságos hozzáférés és az adatok Azure Logic apps – hozzáférés a kérelmeken alapuló triggerekhez](../logic-apps/logic-apps-securing-a-logic-app.md#secure-triggers).

Az trigger alapjául szolgáló JSON-definícióval és az trigger meghívásával kapcsolatos további információkért tekintse meg a következő témaköröket: [trigger típusának](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) és [hívásának, triggerének vagy beágyazásának munkafolyamatai http-végpontokkal Azure Logic apps](../logic-apps/logic-apps-http-endpoint.md).

### <a name="trigger-outputs"></a>Trigger kimenetek

További információ a kérelmek trigger kimenetéről:

| JSON-tulajdonság neve | Adattípus | Description |
|--------------------|-----------|-------------|
| `headers` | Objektum | Egy JSON-objektum, amely leírja a kérelem fejléceit. |
| `body` | Objektum | Egy JSON-objektum, amely leírja a kérelem törzsének tartalmát |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Response művelet hozzáadása

A válasz művelettel válaszolhat egy adattartalomra (adatok) egy bejövő HTTPS-kérelemre, de csak egy HTTPS-kérelem által aktivált logikai alkalmazásban. A válasz műveletet a munkafolyamat bármely pontjára felveheti. További információ az adott trigger alapjául szolgáló JSON-definícióról: [Válasz művelet típusa](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

A logikai alkalmazás csak [korlátozott ideig](../logic-apps/logic-apps-limits-and-config.md#request-limits)tart nyitva a bejövő kérelemben. Feltételezve, hogy a logikai alkalmazás munkafolyamata tartalmaz egy választ, ha a logikai alkalmazás nem ad vissza választ az adott idő elteltével, a logikai alkalmazás a `504 GATEWAY TIMEOUT` hívót adja vissza. Ellenkező esetben, ha a logikai alkalmazás nem tartalmaz válasz műveletet, a logikai alkalmazás azonnal visszaadja a `202 ACCEPTED` hívónak küldött választ.

> [!IMPORTANT]
> Ha a Response művelet tartalmazza ezeket a fejléceket, Logic Apps eltávolítja ezeket a fejléceket a generált válaszüzenetből anélkül, hogy a rendszer figyelmeztetést vagy hibát mutat:
>
> * `Allow`
> * `Content-*`a következő kivételekkel: `Content-Disposition` , `Content-Encoding` és`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Bár a Logic Apps nem fogja leállítani a logikai alkalmazások mentését, amelyekben a válasz művelettel rendelkezik, a Logic Apps figyelmen kívül hagyja ezeket a fejléceket.

1. A Logic app Designerben abban a lépésben, amelyhez hozzá szeretne adni egy válasz műveletet, válassza az **új lépés**lehetőséget.

   Például a korábban megjelenő kérelem triggerének használatával:

   ![Új lépés hozzáadása](./media/connectors-native-reqres/add-response.png)

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**a keresőmezőbe írja be szűrőként a "válasz" kifejezést, majd válassza ki a **Válasz** műveletet.

   ![Válassza ki a válasz műveletet](./media/connectors-native-reqres/select-response-action.png)

   Ebben a példában az egyszerűség kedvéért a kérelem-trigger össze van csukva.

1. Adja meg a válaszüzenethez szükséges értékeket. 

   Egyes mezőkben a szövegdobozokra kattintva megnyílik a dinamikus tartalmak listája. Ezután kiválaszthatja azokat a jogkivonatokat, amelyek a munkafolyamat előző lépéseiből származó elérhető kimeneteket jelölik. A korábbi példában megadott sémából származó tulajdonságok most megjelennek a dinamikus tartalmak listájában.

   Például a **fejlécek** mezőben adja `Content-Type` meg a kulcs nevét, és állítsa a kulcs értékét a `application/json` témakörben korábban említettek szerint. A **törzs** mezőben kiválaszthatja a dinamikus tartalom lista trigger törzsének kimenetét.

   ![Válasz művelet részletei](./media/connectors-native-reqres/response-details.png)

   Ha JSON formátumban szeretné megtekinteni a fejléceket, válassza a **váltás szöveges nézetre**lehetőséget.

   ![Fejlécek – váltás szöveges nézetre](./media/connectors-native-reqres/switch-to-text-view.png)

   A válasz műveletben megadható tulajdonságokkal kapcsolatos további információk. 

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **Állapotkód** | `statusCode` | Yes | A válaszban visszaadni kívánt állapotkód |
   | **Fejlécek** | `headers` | No | Egy JSON-objektum, amely egy vagy több, a válaszban szerepeltetni kívánt fejlécet ismertet. |
   | **Törzs** | `body` | No | A válasz törzse |
   |||||

1. Ha további tulajdonságokat szeretne megadni, például egy JSON-sémát a válasz törzséhez, nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a hozzáadni kívánt paramétereket.

1. Ha elkészült, mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget. 

## <a name="next-steps"></a>További lépések

* [Összekötők a Logic Apps számára](../connectors/apis-list.md)

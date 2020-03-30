---
title: Hívások fogadása és megválaszolása HTTPS használatával
description: Külső szolgáltatások bejövő HTTPS-kéréseinek kezelése az Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewers: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: d65b81f18d4dcb0ee97a21a7edec885e308bd8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297295"
---
# <a name="receive-and-respond-to-inbound-https-requests-in-azure-logic-apps"></a>Bejövő HTTPS-kérelmek fogadása és megválaszolása az Azure Logic Apps alkalmazásban

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a beépített kérelem eseményindító vagy válasz művelet, hozhat létre automatizált feladatok és munkafolyamatok, amelyek fogadására és a bejövő HTTPS-kérelmek fogadására és válaszol. A logikai alkalmazás például a következő:

* A helyszíni adatbázisban lévő HTTPS-adatkérés fogadása és megválaszolása.
* Munkafolyamatot kezdeményezni, amikor egy külső webhook-esemény történik.
* Https-hívás fogadása és megválaszolása egy másik logikai alkalmazásból.

> [!NOTE]
> A kérelem eseményindító *csak* a Transport Layer Security (TLS) 1.2-t támogatja a bejövő hívások esetén. A kimenő hívások továbbra is támogatják a TLS 1.0, 1.1 és 1.2-es hívásokat. További információ: [A TLS 1.0 probléma megoldása.](https://docs.microsoft.com/security/solving-tls1-problem)
>
> Ha SSL kézfogási hibákat lát, győződjön meg arról, hogy a TLS 1.2-t használja. Bejövő hívások esetén az alábbiakban a támogatott titkosítási csomagok találhatók:
>
> * TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
> * TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
> * TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
> * TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra.](https://azure.microsoft.com/free/)

* Alapvető ismeretek a [logikai alkalmazásokról.](../logic-apps/logic-apps-overview.md) Ha most ismerkedik a logikai alkalmazásokkal, ismerje meg, [hogyan hozhat létre az első logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Kérelem eseményindítóhozzáadása

Ez a beépített eseményindító egy manuálisan hívható HTTPS-végpontot hoz létre, amely *csak* bejövő HTTPS-kérelmeket fogadhat. Ha ez az esemény történik, az eseményindító aktiválódik, és futtatja a logikai alkalmazást. Az eseményindító alapjául szolgáló JSON-definíciójáról és az eseményindító hívásáról az [Azure Logic Apps rendszerben http-végpontokkal rendelkező hívás, eseményindító és munkafolyamatok kérése, aktiválása és beágyazása](../logic-apps/logic-apps-http-endpoint.md)című témakörben talál további információt. [Request trigger type](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) Üres logikai alkalmazás létrehozása.

1. A Logic App Designer megnyitása után a keresőmezőbe írja be a "http request" szűrőt. Az eseményindítók listájában válassza a **HTTP-kérelem fogadásának időpontját,** amely a logikai alkalmazás munkafolyamatának első lépése.

   ![Válassza a Kérelem eseményindítójának kiválasztása](./media/connectors-native-reqres/select-request-trigger.png)

   A Kérelem eseményindító a következő tulajdonságokat jeleníti meg:

   ![Kérelem eseményindító](./media/connectors-native-reqres/request-trigger.png)

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **HTTP BEJEGYZÉS URL-CÍME** | {nincs} | Igen | A logikai alkalmazás mentése után létrehozott végpont URL-címe, amely a logikai alkalmazás hívására szolgál |
   | **Kérelem törzse JSON-séma** | `schema` | Nem | A JSON-séma, amely leírja a tulajdonságokat és értékeket a bejövő kérelem törzsében |
   |||||

1. A **Kérelem törzse JSON-séma** mezőben adja meg a JSON-sémát, amely leírja a törzset a bejövő kérelemben, például:

   ![Példa JSON-sémára](./media/connectors-native-reqres/provide-json-schema.png)

   A tervező ezt a sémát használja a kérelemben szereplő tulajdonságok jogkivonatjainak létrehozásához. Így a logikai alkalmazás elemezheti, felhasználhatja és továbbíthatja a kérelemből származó adatokat az eseményindítón keresztül a munkafolyamatba.

   Itt van a minta séma:

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

   JSON-séma beírásakor a tervező egy `Content-Type` emlékeztetőt jelenít meg, amely `application/json`tartalmazza a fejlécet a kérelemben, és a fejléc értékét a értékre állítja. További információt a [Tartalomtípusok kezelése](../logic-apps/logic-apps-content-type.md)című témakörben talál.

   ![Emlékeztető a "Tartalomtípus" fejlécre](./media/connectors-native-reqres/include-content-type.png)

   Így néz ki ez a fejléc JSON formátumban:

   ```json
   {
      "Content-Type": "application/json"
   }
   ```

   A várt hasznos adaton (adatokon) alapuló JSON-séma létrehozásához használhat egy eszközt, például [a JSONSchema.net,](https://jsonschema.net)vagy kövesse az alábbi lépéseket:

   1. A kérelem eseményindító, válassza **a Minta hasznos adat létrehozása séma.**

      ![Séma létrehozása a hasznos adatból](./media/connectors-native-reqres/generate-from-sample-payload.png)

   1. Adja meg a mintahasznos t, és válassza **a Kész**lehetőséget.

      ![Séma létrehozása a hasznos adatból](./media/connectors-native-reqres/enter-payload.png)

      Itt van a minta hasznos teher:

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

1. További tulajdonságok megadásához nyissa meg az **Új paraméter hozzáadása** listát, és jelölje ki a hozzáadni kívánt paramétereket.

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **Módszer** | `method` | Nem | Az a metódus, amelyet a bejövő kérelemnek a logikai alkalmazás hívásához kell használnia |
   | **Relatív elérési út** | `relativePath` | Nem | Annak a paraméternek a relatív elérési útja, amelyet a logikai alkalmazás végpontjának URL-címe el tud fogadni |
   |||||

   Ez a példa hozzáadja a **Metódus** tulajdonságot:

   ![Metódus hozzáadása paraméter](./media/connectors-native-reqres/add-parameters.png)

   A **Metódus** tulajdonság megjelenik az eseményindítóban, így kiválaszthat egy metódust a listából.

   ![Módszer kiválasztása](./media/connectors-native-reqres/select-method.png)

1. Most adjon hozzá egy másik műveletet a munkafolyamat következő lépéseként. Az eseményindító alatt válassza a **Következő lépés** lehetőséget, hogy megtalálja a hozzáadni kívánt műveletet.

   A kérésre például [válaszművelet hozzáadásával](#add-response)válaszolhat, amelynek segítségével testreszabott választ adhat vissza, és a témakör későbbi részében ismertet.

   A logikai alkalmazás a bejövő kérelmet csak egy percig tartja nyitva. Feltételezve, hogy a logikai alkalmazás munkafolyamat a válasz művelet, ha a logikai alkalmazás nem `504 GATEWAY TIMEOUT` ad vissza választ ezen idő elteltével halad, a logikai alkalmazás a d. Ellenkező esetben, ha a logikai alkalmazás nem tartalmaz válaszműveletet, a logikai alkalmazás azonnal `202 ACCEPTED` választ ad vissza a hívónak.

1. Ha elkészült, mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.** 

   Ez a lépés a logikai alkalmazást indító kérelem küldéséhez használandó URL-címet hozza létre. Az URL másolásához jelölje ki az URL-cím melletti másolásikont.

   ![A logikai alkalmazás aktiválásához használandó URL-cím](./media/connectors-native-reqres/generated-url.png)

1. A logikai alkalmazás aktiválásához küldjön egy HTTP-bejegyzést a létrehozott URL-címre. Használhat például egy eszközt, például [a Postman t.](https://www.getpostman.com/)

### <a name="trigger-outputs"></a>Eseményindító kimenetek

További információ a kérelem eseményindító kimeneteiről:

| JSON-tulajdonság neve | Adattípus | Leírás |
|--------------------|-----------|-------------|
| `headers` | Objektum | JSON-objektum, amely a kérelem fejléceit írja le |
| `body` | Objektum | Egy JSON-objektum, amely leírja a kérelem törzstartalmát |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Válaszművelet hozzáadása

A Válasz művelet segítségével válaszolhat egy hasznos adattal egy bejövő HTTPS-kérelemre, de csak egy logikai alkalmazásban, amelyet egy HTTPS-kérelem vált ki. A válaszműveletet a munkafolyamat bármely pontján hozzáadhatja. Az eseményindító alapjául szolgáló JSON-definícióról a [Válasz művelettípusa](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action)című témakörben talál további információt.

A logikai alkalmazás a bejövő kérelmet csak egy percig tartja nyitva. Feltételezve, hogy a logikai alkalmazás munkafolyamat a válasz művelet, ha a logikai alkalmazás nem `504 GATEWAY TIMEOUT` ad vissza választ ezen idő elteltével halad, a logikai alkalmazás a d. Ellenkező esetben, ha a logikai alkalmazás nem tartalmaz válaszműveletet, a logikai alkalmazás azonnal `202 ACCEPTED` választ ad vissza a hívónak.

> [!IMPORTANT]
> Ha egy válaszművelet tartalmazza ezeket a fejléceket, a Logic Apps figyelmeztetés vagy hiba nélkül eltávolítja ezeket a fejléceket a létrehozott válaszüzenetből:
>
> * `Allow`
> * `Content-*`az alábbi kivételekkel: `Content-Disposition`, , `Content-Encoding`és`Content-Type`
> * `Cookie`
> * `Expires`
> * `Last-Modified`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Bár a Logic Apps nem akadályozza meg a logikai alkalmazások mentését, amelyek ezekkel a fejlécekkel rendelkeznek válaszművelettel, a Logic Apps figyelmen kívül hagyja ezeket a fejléceket.

1. A Logic App Designer ben, abban a lépésben, ahol válaszműveletet szeretne hozzáadni, válassza az **Új lépés lehetőséget.**

   Például a kérelem eseményindító korábbi használatával:

   ![Új lépés hozzáadása](./media/connectors-native-reqres/add-response.png)

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. A **Művelet kiválasztása csoportkereső**mezőjében írja be szűrőként a "válasz" kifejezést, és jelölje ki a **Válasz** műveletet.

   ![A Válasz művelet kijelölése](./media/connectors-native-reqres/select-response-action.png)

   A kérelem eseményindító van összecsukva ebben a példában az egyszerűség kedvéért.

1. Adja meg a válaszüzenethez szükséges értékeket. 

   Egyes mezőkben a mezőiken belülre kattintva megnyílik a dinamikus tartalomlista. Ezután kiválaszthatja azokat a tokeneket, amelyek a munkafolyamat előző lépéseiben elérhető kimeneteket jelölik. A korábbi példában megadott séma tulajdonságai most antól megjelennek a dinamikus tartalomlistában.

   A **Fejlécek** mezőben például `Content-Type` adja meg a kulcs nevét, `application/json` és állítsa a kulcs értékét a témakör korábbi része szerint. A **Törzs** mezőben kiválaszthatja az eseményindító törzs kimenetét a dinamikus tartalomlistából.

   ![Válaszművelet részletei](./media/connectors-native-reqres/response-details.png)

   Ha JSON formátumban szeretné megtekinteni a fejléceket, válassza **a Váltás szöveges nézetre lehetőséget.**

   ![Fejlécek – Váltás szöveges nézetre](./media/connectors-native-reqres/switch-to-text-view.png)

   Az alábbiakban további információkat talál a Válasz műveletben beállítható tulajdonságokról. 

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **Állapotkód** | `statusCode` | Igen | A válaszban visszaadandó állapotkód |
   | **Fejlécek** | `headers` | Nem | JSON-objektum, amely egy vagy több fejlécet ír le a válaszba |
   | **Törzs** | `body` | Nem | A válaszszerv |
   |||||

1. További tulajdonságok, például json-séma megadásához nyissa meg az **Új paraméter hozzáadása** listát, és válassza ki a hozzáadni kívánt paramétereket.

1. Ha elkészült, mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.** 

## <a name="next-steps"></a>További lépések

* [Összekötők a Logic Apps számára](../connectors/apis-list.md)

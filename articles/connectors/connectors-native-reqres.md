---
title: HTTPS-hívások fogadása és megválaszolása – Azure Logic Apps
description: HTTPS-kérelmek és-események kezelése valós időben a Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewers: klam, LADocs
manager: carmonm
ms.assetid: 566924a4-0988-4d86-9ecd-ad22507858c0
ms.topic: article
ms.date: 09/06/2019
tags: connectors
ms.openlocfilehash: 668e815f1dc1ead0ad38264bdc71fc3c315b751c
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122713"
---
# <a name="receive-and-respond-to-incoming-https-calls-by-using-azure-logic-apps"></a>Bejövő HTTPS-hívások fogadása és válaszadás a Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített kérelem-trigger vagy Response művelettel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek fogadják és válaszolnak a bejövő HTTPS-kérelmekre. Használhatja például a logikai alkalmazást:

* Egy HTTPS-kérelem fogadása és megválaszolása egy helyszíni adatbázisban lévő adatszolgáltatáshoz.
* Munkafolyamat elindítása külső webhook-esemény bekövetkezésekor.
* Egy másik logikai alkalmazástól érkező HTTPS-hívás fogadása és megválaszolása.

A kérelem-trigger *csak* HTTPS protokollt támogat. A kimenő HTTP-vagy HTTPS-hívások létrehozásához használja a beépített [http-triggert vagy műveletet](../connectors/connectors-native-http.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

* A [Logic apps](../logic-apps/logic-apps-overview.md)alapszintű ismerete. Ha most ismerkedik a Logic apps szolgáltatással, Ismerje meg, [hogyan hozhatja létre az első logikai alkalmazását](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-request"></a>

## <a name="add-request-trigger"></a>Kérelem-trigger hozzáadása

Ez a beépített trigger egy manuálisan megadható HTTPS-végpontot hoz létre, amely *csak* a bejövő HTTPS-kérelmek fogadására képes. Ha ez az esemény történik, az eseményindító elindít és futtatja a logikai alkalmazást. Az trigger alapjául szolgáló JSON-definícióval és az trigger meghívásával kapcsolatos további információkért tekintse meg a [kérelem trigger típusának](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger) és [hívásának, triggerének vagy beágyazásának munkafolyamatait http-végpontokkal Azure Logic apps](../logic-apps/logic-apps-http-endpoint.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Üres logikai alkalmazás létrehozása.

1. A Logic app Designer megnyitása után a keresőmezőbe írja be szűrőként a "http-kérelem" kifejezést. Az eseményindítók listából válassza ki a **http-kérelem fogadása** eseményindítót, amely a logikai alkalmazás munkafolyamatának első lépése.

   ![Kérelem triggerének kiválasztása](./media/connectors-native-reqres/select-request-trigger.png)

   A kérelem-trigger a következő tulajdonságokat jeleníti meg:

   ![Kérelem triggere](./media/connectors-native-reqres/request-trigger.png)

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **HTTP POST URL-CÍM** | nEz egy | Igen | A logikai alkalmazás mentése után generált végponti URL-cím, amely a logikai alkalmazás meghívására szolgál |
   | **Kérelem törzsének JSON-sémája** | `schema` | Nem | A bejövő kérelem törzsében található tulajdonságokat és értékeket leíró JSON-séma |
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

   Ha JSON-sémát ad meg, a tervező egy emlékeztetőt jelenít meg `Content-Type` , amely tartalmazza a fejlécet a kérelemben, `application/json`és beállítja a fejléc értékét a következőre:. További információ: [tartalomtípusok kezelése](../logic-apps/logic-apps-content-type.md).

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

1. További tulajdonságok megadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a hozzáadni kívánt paramétereket.

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **Metódus** | `method` | Nem | Az a módszer, amelyet a bejövő kérelemnek használnia kell a logikai alkalmazás meghívásához. |
   | **Relatív elérési út** | `relativePath` | Nem | Annak a paraméternek a relatív elérési útja, amelyet a logikai alkalmazás végpontjának URL-címe el tud fogadni |
   |||||

   Ez a példa hozzáadja a **Method** tulajdonságot:

   ![Metódus hozzáadása paraméter](./media/connectors-native-reqres/add-parameters.png)

   A **Method** tulajdonság megjelenik a triggerben, hogy kiválasszon egy metódust a listából.

   ![Módszer kiválasztása](./media/connectors-native-reqres/select-method.png)

1. Most adjon hozzá egy újabb műveletet a munkafolyamat következő lépéseként. Az trigger alatt válassza a **következő lépés** lehetőséget, hogy megtalálja a hozzáadni kívánt műveletet.

   Választhatja például a kérést [egy válasz művelet hozzáadásával](#add-response), amelyet egy testreszabott válasz visszaadására használhat, és a jelen témakör későbbi részében is ismertetjük.

   A logikai alkalmazás csak egy percig tart nyitva a bejövő kérelemben. Feltételezve, hogy a logikai alkalmazás munkafolyamata tartalmaz egy választ, ha a logikai alkalmazás nem ad vissza választ az adott idő elteltével, a logikai `504 GATEWAY TIMEOUT` alkalmazás a hívót adja vissza. Ellenkező esetben, ha a logikai alkalmazás nem tartalmaz válasz műveletet, a logikai alkalmazás azonnal visszaadja `202 ACCEPTED` a hívónak küldött választ.

1. Ha elkészült, mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget. 

   Ez a lépés a logikai alkalmazást indító kérelem küldéséhez használandó URL-címet hozza létre. Az URL-cím másolásához válassza a másolás ikont az URL mellett.

   ![A logikai alkalmazás aktiválását használó URL-cím](./media/connectors-native-reqres/generated-url.png)

1. A logikai alkalmazás elindításához küldjön egy HTTP-BEJEGYZÉST a generált URL-címre. Használhat például egy olyan eszközt, mint például a [Poster](https://www.getpostman.com/).

### <a name="trigger-outputs"></a>Trigger kimenetek

További információ a kérelmek trigger kimenetéről:

| JSON-tulajdonság neve | Adattípus | Leírás |
|--------------------|-----------|-------------|
| `headers` | Object | Egy JSON-objektum, amely leírja a kérelem fejléceit. |
| `body` | Object | Egy JSON-objektum, amely leírja a kérelem törzsének tartalmát |
||||

<a name="add-response"></a>

## <a name="add-a-response-action"></a>Response művelet hozzáadása

A válasz művelettel válaszolhat egy adattartalomra (adatok) egy bejövő HTTPS-kérelemre, de csak egy HTTPS-kérelem által aktivált logikai alkalmazásban. A válasz műveletet a munkafolyamat bármely pontjára felveheti. További információ az adott trigger alapjául szolgáló JSON-definícióról: [Válasz művelet típusa](../logic-apps/logic-apps-workflow-actions-triggers.md#response-action).

A logikai alkalmazás csak egy percig tart nyitva a bejövő kérelemben. Feltételezve, hogy a logikai alkalmazás munkafolyamata tartalmaz egy választ, ha a logikai alkalmazás nem ad vissza választ az adott idő elteltével, a logikai `504 GATEWAY TIMEOUT` alkalmazás a hívót adja vissza. Ellenkező esetben, ha a logikai alkalmazás nem tartalmaz válasz műveletet, a logikai alkalmazás azonnal visszaadja `202 ACCEPTED` a hívónak küldött választ.

1. A Logic app Designerben abban a lépésben, amelyhez hozzá szeretne adni egy válasz műveletet, válassza az **új lépés**lehetőséget.

   Például a korábban megjelenő kérelem triggerének használatával:

   ![Új lépés hozzáadása](./media/connectors-native-reqres/add-response.png)

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**a keresőmezőbe írja be szűrőként a "válasz" kifejezést, majd válassza ki a **Válasz** műveletet.

   ![Válassza ki a válasz műveletet](./media/connectors-native-reqres/select-response-action.png)

   Ebben a példában az egyszerűség kedvéért a kérelem-trigger össze van csukva.

1. Adja meg a válaszüzenethez szükséges értékeket. 

   Egyes mezőkben a szövegdobozokra kattintva megnyílik a dinamikus tartalmak listája. Ezután kiválaszthatja azokat a jogkivonatokat, amelyek a munkafolyamat előző lépéseiből származó elérhető kimeneteket jelölik. A korábbi példában megadott sémából származó tulajdonságok most megjelennek a dinamikus tartalmak listájában.

   Például a **fejlécek** mezőben adja `Content-Type` meg a kulcs nevét, és állítsa `application/json` a kulcs értékét a témakörben korábban említettek szerint. A **törzs** mezőben kiválaszthatja a dinamikus tartalom lista trigger törzsének kimenetét.

   ![Válasz művelet részletei](./media/connectors-native-reqres/response-details.png)

   Ha JSON formátumban szeretné megtekinteni a fejléceket, válassza a **váltás szöveges nézetre**lehetőséget.

   ![Fejlécek – váltás szöveges nézetre](./media/connectors-native-reqres/switch-to-text-view.png)

   A válasz műveletben megadható tulajdonságokkal kapcsolatos további információk. 

   | Tulajdonság neve | JSON-tulajdonság neve | Kötelező | Leírás |
   |---------------|--------------------|----------|-------------|
   | **Állapotkód** | `statusCode` | Igen | A válaszban visszaadni kívánt állapotkód |
   | **Fejlécek** | `headers` | Nem | Egy JSON-objektum, amely egy vagy több, a válaszban szerepeltetni kívánt fejlécet ismertet. |
   | **Törzs** | `body` | Nem | A válasz törzse |
   |||||

1. Ha további tulajdonságokat szeretne megadni, például egy JSON-sémát a válasz törzséhez, nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a hozzáadni kívánt paramétereket.

1. Ha elkészült, mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget. 

## <a name="next-steps"></a>További lépések

* [Logic Apps-összekötők](../connectors/apis-list.md)

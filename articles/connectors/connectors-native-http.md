---
title: Szolgáltatási végpontok hívása HTTP vagy HTTPS használatával
description: Kimenő HTTP-vagy HTTPS-kérelmek küldése a szolgáltatási végpontoknak Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/09/2020
tags: connectors
ms.openlocfilehash: 8c7a0ddb80ba28548fc1821cc2063e500af0fa66
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286631"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Szolgáltatásvégpontok HTTP-n vagy HTTPS-en keresztül történő meghívása az Azure Logic Appsből

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített http-trigger vagy művelet használatával olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek http-vagy https-kapcsolaton keresztül küldenek kéréseket a szolgáltatási végpontoknak. Megfigyelheti például a webhely szolgáltatási végpontját, ha ellenőrzi, hogy a végpont egy adott időpontban van-e. Ha a megadott esemény a végponton történik, például a webhely leállásakor, az esemény elindítja a logikai alkalmazás munkafolyamatát, és futtatja az adott munkafolyamat műveleteit. Ha ehelyett a bejövő HTTPS-hívásokat szeretné fogadni és válaszolni, használja a beépített [kérelem-triggert vagy a válasz műveletet](../connectors/connectors-native-reqres.md).

* Ha ismétlődő ütemterv alapján szeretné megtekinteni vagy *lekérdezni* egy végpontot, [adja hozzá a http-triggert](#http-trigger) a munkafolyamat első lépéseként. Minden alkalommal, amikor a trigger ellenőrzi a végpontot, az trigger meghívja vagy elküld egy *kérést* a végpontnak. A végpont válasza határozza meg, hogy a logikai alkalmazás munkafolyamata fut-e. Az trigger bármilyen tartalmat továbbít a végpont válaszában a logikai alkalmazás műveleteire.

* Ha egy végpontot szeretne meghívni a munkafolyamatban bárhol máshol, [adja hozzá a http-műveletet](#http-action). A végpont válasza határozza meg, hogyan futnak a munkafolyamat hátralévő műveletei.

Ez a cikk bemutatja, hogyan adhat hozzá HTTP-triggert vagy műveletet a logikai alkalmazás munkafolyamataihoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A hívni kívánt cél végpont URL-címe

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md)?

* Az a logikai alkalmazás, amelyről meg szeretné hívni a célként megadott végpontot. A HTTP-trigger elindításához [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP-művelet használatához indítsa el a logikai alkalmazást a kívánt triggerrel. Ez a példa a HTTP-triggert használja első lépésként.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>HTTP-trigger hozzáadása

Ez a beépített trigger egy HTTP-hívást kezdeményez a végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A tervező keresési mezőjében válassza a **beépített**lehetőséget. A keresőmezőbe írja be `http` szűrőként a kifejezést. Az **Eseményindítók** listából válassza ki a **http** -eseményindítót.

   ![Válassza a HTTP-trigger lehetőséget.](./media/connectors-native-http/select-http-trigger.png)

   Ez a példa átnevezi a triggert a "HTTP trigger" névre, hogy a lépésnek legyen egy leíró neve. A példa később egy HTTP-műveletet is felvesz, és mindkét névnek egyedinek kell lennie.

1. Adja meg a http- [trigger azon paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) értékeit, amelyeket a célként megadott végpont felé irányuló hívásba kíván foglalni. Állítsa be az ismétlődést, hogy milyen gyakran szeretné, hogy az eseményindító ellenőrizzék a célként megadott végpontot.

   ![HTTP-trigger paramétereinek megadása](./media/connectors-native-http/http-trigger-parameters.png)

   Ha a **none**értéktől eltérő hitelesítési típust választ, a hitelesítési beállítások a kijelöléstől függően eltérőek. A HTTP-n elérhető hitelesítési típusokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

   * [Hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Az erőforrásokhoz való hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Folytassa a logikai alkalmazás munkafolyamatának kialakítását olyan műveletekkel, amelyek az eseményindító indításakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

<a name="http-action"></a>

## <a name="add-an-http-action"></a>HTTP-művelet hozzáadása

Ez a beépített művelet HTTP-hívást kezdeményez egy végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a HTTP-triggert használja első lépésként.

1. Válassza ki azt a lépést, ahol a HTTP-műveletet hozzá szeretné adni, majd kattintson az **új lépés**gombra.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen válassza a **beépített**lehetőséget. A keresőmezőbe írja be `http` szűrőként a kifejezést. A **műveletek** listából válassza ki a **http** -műveletet.

   ![HTTP-művelet kiválasztása](./media/connectors-native-http/select-http-action.png)

   Ez a példa átnevezi a műveletet "HTTP-művelet" névre, hogy a lépésnek legyen egy leíró neve.

1. Adja meg azoknak a [http-műveleti paramétereknek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) az értékeit, amelyeket fel szeretne venni a célként megadott végpontba.

   ![HTTP-műveleti paraméterek megadása](./media/connectors-native-http/http-action-parameters.png)

   Ha a **none**értéktől eltérő hitelesítési típust választ, a hitelesítési beállítások a kijelöléstől függően eltérőek. A HTTP-n elérhető hitelesítési típusokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

   * [Hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Az erőforrásokhoz való hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

<a name="tls-support"></a>

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A célként megadott végponti képesség alapján a kimenő hívások támogatják Transport Layer Security (TLS), amely korábban SSL (SSL), 1,0, 1,1 és 1,2 verziójú. Logic Apps a lehető legmagasabb támogatott verzió használatával egyeztet a végponttal.

Ha például a végpont támogatja a 1,2-et, a HTTP-összekötő először a 1,2-et használja. Ellenkező esetben az összekötő a következő legmagasabb támogatott verziót használja.

<a name="self-signed"></a>

## <a name="self-signed-certificates"></a>Önaláírt tanúsítványok

* A globális, több-bérlős Azure-környezetben található Logic apps esetében a HTTP-összekötő nem engedélyezi az önaláírt TLS/SSL-tanúsítványokat. Ha a logikai alkalmazás HTTP-hívást kezdeményez egy kiszolgálónak, és egy TLS/SSL önaláírt tanúsítványt jelenít meg, akkor a HTTP-hívás `TrustFailure` hibával meghiúsul.

* Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében a http-összekötő engedélyezi az önaláírt tanúsítványokat a TLS/SSL-kézfogásokhoz. Először is engedélyeznie kell az [önaláírt tanúsítvány-támogatást](../logic-apps/create-integration-service-environment-rest-api.md#request-body) egy meglévő ISE vagy új ISE számára a Logic apps REST API használatával, és a nyilvános tanúsítványt a helyen kell telepíteni `TrustedRoot` .

## <a name="content-with-multipartform-data-type"></a>Tartalom multipart/form-adattípus

A `multipart/form-data` http-kérelmekben szereplő tartalom kezeléséhez hozzáadhat egy JSON-objektumot, amely tartalmazza a `$content-type` http- `$multipart` kérelem törzsének és attribútumainak a formátum használatával történő kezelését.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Tegyük fel például, hogy rendelkezik egy olyan logikai alkalmazással, amely egy, az adott hely API-ját támogató HTTP POST-kérelmet küld egy webhelyre, amely támogatja a `multipart/form-data` típust. A művelet a következő módon jelenhet meg:

![Többrészes űrlapadatok](./media/connectors-native-http/http-action-multipart.png)

Az alábbi példa a HTTP-művelet JSON-definícióját mutatja be az alapul szolgáló munkafolyamat-definícióban:

```json
"HTTP_action": {
   "inputs": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

<a name="asynchronous-pattern"></a>

## <a name="asynchronous-request-response-behavior"></a>Aszinkron kérelem – válasz viselkedése

Alapértelmezés szerint a Azure Logic Apps összes HTTP-alapú művelete követi a normál [aszinkron műveleti mintát](/azure/architecture/patterns/async-request-reply). Ez a minta azt adja meg, hogy egy HTTP-művelet vagy egy, a végpontra, szolgáltatásra, rendszerre vagy API-ra irányuló kérés küldése után a fogadó azonnal ["202 elfogadott"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.3) választ ad vissza. Ez a kód megerősíti, hogy a fogadó elfogadta a kérést, de nem fejezte be a feldolgozást. A válasz tartalmazhat egy `location` fejlécet, amely meghatározza az URL-címet és a frissítési azonosítót, amelyet a hívó használhat az aszinkron kérelem állapotának lekéréséhez vagy a fogadó leállításához, illetve az ["200 OK"](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10.2.1) sikerességi válasz vagy más nem 202 válasz visszaadását. A hívónak azonban nem kell megvárnia, amíg a kérés befejezte a feldolgozást, és továbbra is futtathatja a következő műveletet. További információ: az [aszinkron szolgáltatások integrációja a szolgáltatásbeli önállóságot kényszeríti](/azure/architecture/microservices/design/interservice-communication#synchronous-versus-asynchronous-messaging).

* A Logic app Designerben a HTTP-művelet, de az trigger nem, **aszinkron mintázatú** beállítással rendelkezik, amely alapértelmezés szerint engedélyezve van. Ezzel a beállítással megadhatja, hogy a hívó ne várjon a feldolgozás befejezésére, és továbblép a következő műveletre, de továbbra is ellenőrzi az állapotot, amíg a feldolgozás leáll. Ha le van tiltva, ez a beállítás azt határozza meg, hogy a hívó a következő műveletre való áttérés előtt megvárja a feldolgozás befejeződését.

  A beállítás megkereséséhez kövesse az alábbi lépéseket:

  1. A HTTP-művelet címsorán válassza az ellipszisek (**..**.) gombot, amely megnyitja a művelet beállításait.

  1. Az **aszinkron minta** beállításának megkeresése.

     !["Aszinkron minta" beállítás](./media/connectors-native-http/asynchronous-pattern-setting.png)

* A HTTP-művelet alapjául szolgáló JavaScript Object Notation (JSON) definíciója implicit módon követi az aszinkron műveleti mintát.

<a name="disable-asynchronous-operations"></a>

## <a name="disable-asynchronous-operations"></a>Aszinkron műveletek letiltása

Időnként előfordulhat, hogy bizonyos helyzetekben a HTTP-művelet aszinkron viselkedését szeretné használni, például a következőket:

* [A hosszú ideig futó feladatok HTTP-időtúllépésének elkerülése](#avoid-http-timeouts)
* [Helyadatok ellenőrzésének letiltása](#disable-location-header-check)

<a name="turn-off-asynchronous-pattern-setting"></a>

### <a name="turn-off-asynchronous-pattern-setting"></a>**Aszinkron mintázat** beállításának kikapcsolása

1. A Logic app Designerben a HTTP-művelet címsorán válassza az ellipszisek (**..**.) gombot, amely megnyitja a művelet beállításait.

1. Keresse meg az **aszinkron minta** beállítást, kapcsolja **ki** a beállítást, ha engedélyezve van, és válassza a **kész**lehetőséget.

   ![Az "aszinkron minta" beállítás letiltása](./media/connectors-native-http/disable-asynchronous-pattern-setting.png)

<a name="add-disable-async-pattern-option"></a>

### <a name="disable-asynchronous-pattern-in-actions-json-definition"></a>Aszinkron minta letiltása a művelet JSON-definíciójában

A HTTP-művelet alapjául szolgáló JSON-definícióban [adja hozzá a `"DisableAsyncPattern"` művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options) definícióját a művelethez, hogy a művelet a szinkron műveleti mintát követi. További információ: [műveletek futtatása szinkron műveleti mintában](../logic-apps/logic-apps-workflow-actions-triggers.md#disable-asynchronous-pattern).

<a name="avoid-http-timeouts"></a>

## <a name="avoid-http-timeouts-for-long-running-tasks"></a>A hosszú ideig futó feladatok HTTP-időtúllépésének elkerülése

A HTTP-kérelmek [időtúllépési korláttal](../logic-apps/logic-apps-limits-and-config.md#http-limits)rendelkeznek. Ha olyan hosszan futó HTTP-művelettel rendelkezik, amely túllépi a korlátot, a következő lehetőségek közül választhat:

* [Tiltsa le a http-művelet aszinkron műveleti mintáját](#disable-asynchronous-operations) , így a művelet nem kérdezi le folyamatosan a lekérdezést, vagy nem vizsgálja meg a kérelem állapotát. Ehelyett a művelet megvárja, amíg a fogadó válaszol az állapotra és az eredményekre, miután a kérés befejezte a feldolgozást.

* Cserélje le a HTTP-műveletet a [http webhook műveletre](../connectors/connectors-native-webhook.md), amely arra vár, hogy a fogadó válaszoljon az állapotra és az eredményekre, miután a kérés befejezte a feldolgozást.

<a name="disable-location-header-check"></a>

## <a name="disable-checking-location-headers"></a>Helyadatok ellenőrzésének letiltása

Egyes végpontok, szolgáltatások, rendszerek vagy API-k "202 elfogadott" választ adnak vissza, amely nem tartalmaz `location` fejlécet. Ha el szeretné kerülni, hogy a HTTP-műveletek folyamatosan ellenőrizzék a kérés állapotát, ha a `location` fejléc nem létezik, a következő lehetőségek közül választhat:

* [Tiltsa le a http-művelet aszinkron műveleti mintáját](#disable-asynchronous-operations) , így a művelet nem kérdezi le folyamatosan a lekérdezést, vagy nem vizsgálja meg a kérelem állapotát. Ehelyett a művelet megvárja, amíg a fogadó válaszol az állapotra és az eredményekre, miután a kérés befejezte a feldolgozást.

* Cserélje le a HTTP-műveletet a [http webhook műveletre](../connectors/connectors-native-webhook.md), amely arra vár, hogy a fogadó válaszoljon az állapotra és az eredményekre, miután a kérés befejezte a feldolgozást.

## <a name="known-issues"></a>Ismert problémák

<a name="omitted-headers"></a>

### <a name="omitted-http-headers"></a>Kihagyott HTTP-fejlécek

Ha egy HTTP-trigger vagy-művelet tartalmazza ezeket a fejléceket, Logic Apps eltávolítja ezeket a fejléceket a generált kérési üzenetből a figyelmeztetés vagy a hiba megjelenítése nélkül:

* `Accept-*`
* `Allow`
* `Content-*`a következő kivételekkel: `Content-Disposition` , `Content-Encoding` és`Content-Type`
* `Cookie`
* `Expires`
* `Host`
* `Last-Modified`
* `Origin`
* `Set-Cookie`
* `Transfer-Encoding`

Bár a Logic Apps nem állítja le a HTTP-triggert vagy műveletet használó logikai alkalmazások mentését ezekkel a fejlécekkel, Logic Apps figyelmen kívül hagyja ezeket a fejléceket.

## <a name="connector-reference"></a>Összekötő-referencia

Az trigger-és műveleti paraméterekkel kapcsolatos további információkért tekintse meg a következő részeket:

* [HTTP-trigger paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP-műveleti paraméterek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Kimenet részletei

Itt talál további információt a HTTP-triggerből vagy-műveletből származó kimenetekről, ami visszaadja ezt az információt:

| Tulajdonság | Típus | Leírás |
|----------|------|-------------|
| `headers` | JSON-objektum | A kérelemben szereplő fejlécek |
| `body` | JSON-objektum | A kérelem szövegtörzsét tartalmazó objektum |
| `status code` | Egész szám | A kérelemben szereplő állapotkód |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Hibás kérelem |
| 401 | Nem engedélyezett |
| 403 | Forbidden |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése


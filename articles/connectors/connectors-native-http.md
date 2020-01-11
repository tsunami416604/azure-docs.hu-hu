---
title: HTTP-és HTTPS-végpontok hívása
description: Kimenő kérelmek küldése HTTP-és HTTPS-végpontoknak Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 232b17852e89ebdfa6f81b5aadcdbcd9c83d4055
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888141"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Kimenő hívások küldése HTTP-vagy HTTPS-végpontoknak Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített http-trigger vagy művelet használatával olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek rendszeresen küldenek kéréseket bármely http-vagy https-végpontnak. Ha ehelyett a bejövő HTTP-vagy HTTPS-hívásokat szeretné fogadni és válaszolni, használja a beépített [kérelem-triggert vagy a válasz műveletet](../connectors/connectors-native-reqres.md).

A webhely szolgáltatási végpontját figyelheti például úgy, hogy ellenőrzi, hogy a végpont egy adott időpontban van-e megadva. Ha egy adott esemény történik a végponton, például a webhelyén, az esemény elindítja a logikai alkalmazás munkafolyamatát, és futtatja a megadott műveleteket.

Ha rendszeres időközönként szeretné megtekinteni vagy *lekérdezni* egy végpontot, használhatja a http-triggert a munkafolyamat első lépéseként. Az trigger minden egyes vizsgálatkor hívást vagy *kérést* küld a végpontnak. A végpont válasza határozza meg, hogy a logikai alkalmazás munkafolyamata fut-e. Az trigger a logikai alkalmazás műveleteire adott válasz tartalmával együtt halad át.

A HTTP-műveletet használhatja a munkafolyamat bármely más lépéseként a végpont meghívásához. A végpont válasza határozza meg, hogyan futnak a munkafolyamat hátralévő műveletei.

A cél végpont képességei alapján a HTTP-összekötő támogatja a 1,0, 1,1 és 1,2 Transport Layer Security (TLS) verziókat. Logic Apps a lehető legmagasabb támogatott verzió használatával egyeztet a végponttal. Tehát ha például a végpont támogatja a 1,2-et, az összekötő először a 1,2-et használja. Ellenkező esetben az összekötő a következő legmagasabb támogatott verziót használja.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A hívni kívánt cél végpont URL-címe

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md)?

* Az a logikai alkalmazás, amelyről meg szeretné hívni a célként megadott végpontot. A HTTP-trigger elindításához [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP-művelet használatához indítsa el a logikai alkalmazást a kívánt triggerrel. Ez a példa a HTTP-triggert használja első lépésként.

## <a name="add-an-http-trigger"></a>HTTP-trigger hozzáadása

Ez a beépített trigger egy HTTP-hívást kezdeményez a végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A **válasszon műveletet**területen a keresőmezőbe írja be a "http" kifejezést a szűrőként. Az **Eseményindítók** listából válassza ki a **http** -eseményindítót.

   ![HTTP-trigger kiválasztása](./media/connectors-native-http/select-http-trigger.png)

   Ez a példa átnevezi a triggert a "HTTP trigger" névre, hogy a lépésnek legyen egy leíró neve. A példa később egy HTTP-műveletet is felvesz, és mindkét névnek egyedinek kell lennie.

1. Adja meg a http- [trigger azon paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) értékeit, amelyeket a célként megadott végpont felé irányuló hívásba kíván foglalni. Állítsa be az ismétlődést, hogy milyen gyakran szeretné, hogy az eseményindító ellenőrizzék a célként megadott végpontot.

   Ha a **none**értéktől eltérő hitelesítési típust választ, a hitelesítési beállítások a kijelöléstől függően eltérőek. További információ: [hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![HTTP-trigger paramétereinek megadása](./media/connectors-native-http/http-trigger-parameters.png)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Folytassa a logikai alkalmazás munkafolyamatának kialakítását olyan műveletekkel, amelyek az eseményindító indításakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

## <a name="add-an-http-action"></a>HTTP-művelet hozzáadása

Ez a beépített művelet HTTP-hívást kezdeményez egy végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a HTTP-triggert használja első lépésként.

1. Válassza ki azt a lépést, ahol a HTTP-műveletet hozzá szeretné adni, majd kattintson az **új lépés**gombra.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen a keresőmezőbe írja be a "http" kifejezést a szűrőként. A **műveletek** listából válassza ki a **http** -műveletet.

   ![HTTP-művelet kiválasztása](./media/connectors-native-http/select-http-action.png)

   Ez a példa átnevezi a műveletet "HTTP-művelet" névre, hogy a lépésnek legyen egy leíró neve.

1. Adja meg azoknak a [http-műveleti paramétereknek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) az értékeit, amelyeket fel szeretne venni a célként megadott végpontba.

   Ha a **none**értéktől eltérő hitelesítési típust választ, a hitelesítési beállítások a kijelöléstől függően eltérőek. További információ: [hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![HTTP-műveleti paraméterek megadása](./media/connectors-native-http/http-action-parameters.png)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

## <a name="content-with-multipartform-data-type"></a>Tartalom multipart/form-adattípus

A HTTP-kérésekben `multipart/form-data` típusú tartalom kezeléséhez hozzáadhat egy JSON-objektumot, amely tartalmazza a HTTP-kérelem törzsének `$content-type` és `$multipart` attribútumait.

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

Tegyük fel például, hogy van egy logikai alkalmazás, amely egy, az adott hely API-ját támogató HTTP POST-kérelmet küld egy webhelynek, amely támogatja a `multipart/form-data` típusát. A művelet a következő módon jelenhet meg:

![Többrészes űrlapadatok](./media/connectors-native-http/http-action-multipart.png)

Az alábbi példa a HTTP-művelet JSON-definícióját mutatja be az alapul szolgáló munkafolyamat-definícióban:

```json
{
   "HTTP_action": {
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

## <a name="connector-reference"></a>Összekötő-referencia

Az trigger-és műveleti paraméterekkel kapcsolatos további információkért tekintse meg a következő részeket:

* [HTTP-trigger paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP-műveleti paraméterek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Kimenet részletei

Itt talál további információt a HTTP-triggerből vagy-műveletből származó kimenetekről, ami visszaadja ezt az információt:

| Tulajdonság neve | Type (Típus) | Leírás |
|---------------|------|-------------|
| fejlécek | objektum | A kérelemben szereplő fejlécek |
| törzs | objektum | JSON-objektum | A kérelem szövegtörzsét tartalmazó objektum |
| állapotkód | int | A kérelemben szereplő állapotkód |
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

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

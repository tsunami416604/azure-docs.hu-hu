---
title: Kapcsolódás HTTP-vagy HTTPS-végpontokhoz Azure Logic Apps
description: HTTP-vagy HTTPS-végpontok figyelése automatizált feladatokban, folyamatokban és munkafolyamatokban Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 04d9beaef29e76d40c0bb3f9dcf0bb6f4fe3152d
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234384"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>HTTP-vagy HTTPS-végpontok hívása Azure Logic Apps használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített http-összekötő segítségével automatizálhatja a logikai alkalmazások létrehozásával a http-vagy https-végpontokat rendszeresen meghívó munkafolyamatokat. A webhely szolgáltatási végpontját figyelheti például úgy, hogy ellenőrzi, hogy a végpont egy adott időpontban van-e megadva. Ha egy adott esemény történik a végponton, például a webhelyén, az esemény elindítja a logikai alkalmazás munkafolyamatát, és futtatja a megadott műveleteket.

Ha rendszeres időközönként  szeretné megtekinteni vagy lekérdezni egy végpontot, használhatja a http-triggert a munkafolyamat első lépéseként. Az trigger minden egyes vizsgálatkor hívást vagy kérést  küld a végpontnak. A végpont válasza határozza meg, hogy a logikai alkalmazás munkafolyamata fut-e. Az trigger a logikai alkalmazás műveleteire adott válasz tartalmával együtt halad át.

A HTTP-műveletet használhatja a munkafolyamat bármely más lépéseként a végpont meghívásához. A végpont válasza határozza meg, hogyan futnak a munkafolyamat hátralévő műveletei.

A cél végpont képességei alapján a HTTP-összekötő támogatja a 1,0, 1,1 és 1,2 Transport Layer Security (TLS) verziókat. Logic Apps a lehető legmagasabb támogatott verzió használatával egyeztet a végponttal. Tehát ha például a végpont támogatja a 1,2-et, az összekötő először a 1,2-et használja. Ellenkező esetben az összekötő a következő legmagasabb támogatott verziót használja.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A hívni kívánt cél végpont URL-címe

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

* Az a logikai alkalmazás, amelyről meg szeretné hívni a célként megadott végpontot. A HTTP-trigger elindításához [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP-művelet használatához indítsa el a logikai alkalmazást a kívánt triggerrel. Ez a példa a HTTP-triggert használja első lépésként.

## <a name="add-an-http-trigger"></a>HTTP-trigger hozzáadása

Ez a beépített trigger egy HTTP-hívást kezdeményez a végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A tervezőben a keresőmezőbe írja be szűrőként a "http" kifejezést. Az **Eseményindítók** listából válassza ki a **http** -eseményindítót.

   ![HTTP-trigger kiválasztása](./media/connectors-native-http/select-http-trigger.png)

   Ez a példa átnevezi a triggert a "HTTP trigger" névre, hogy a lépésnek legyen egy leíró neve. A példa később egy HTTP-műveletet is felvesz, és mindkét névnek egyedinek kell lennie.

1. Adja meg a http- [trigger azon paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) értékeit, amelyeket a célként megadott végpont felé irányuló hívásba kíván foglalni. Állítsa be az ismétlődést, hogy milyen gyakran szeretné, hogy az eseményindító ellenőrizzék a célként megadott végpontot.

   ![HTTP-trigger paramétereinek megadása](./media/connectors-native-http/http-trigger-parameters.png)

   A HTTP-n elérhető hitelesítési típusokkal kapcsolatos további információkért lásd: [http-eseményindítók és-műveletek hitelesítése](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Folytassa a logikai alkalmazás munkafolyamatának kialakítását olyan műveletekkel, amelyek az eseményindító indításakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

## <a name="add-an-http-action"></a>HTTP-művelet hozzáadása

Ez a beépített művelet HTTP-hívást kezdeményez egy végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a HTTP-triggert használja első lépésként.

1. Válassza ki azt a lépést, ahol a HTTP-műveletet hozzá szeretné adni, majd kattintson az **új lépés**gombra.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A tervezőben a keresőmezőbe írja be szűrőként a "http" kifejezést. A **műveletek** listából válassza ki a **http** -műveletet.

   ![HTTP-művelet kiválasztása](./media/connectors-native-http/select-http-action.png)

   Ez a példa átnevezi a műveletet "HTTP-művelet" névre, hogy a lépésnek legyen egy leíró neve.

1. Adja meg azoknak a [http-műveleti paramétereknek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) az értékeit, amelyeket fel szeretne venni a célként megadott végpontba.

   ![HTTP-műveleti paraméterek megadása](./media/connectors-native-http/http-action-parameters.png)

   A HTTP-n elérhető hitelesítési típusokkal kapcsolatos további információkért lásd: [http-eseményindítók és-műveletek hitelesítése](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

## <a name="content-with-multipartform-data-type"></a>Tartalom multipart/form-adattípus

A http-kérelmekben `multipart/form-data` szereplő tartalom kezeléséhez hozzáadhat egy JSON-objektumot, amely tartalmazza a `$content-type` http- `$multipart` kérelem törzsének és attribútumainak a formátum használatával történő kezelését.

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

Tegyük fel például, hogy rendelkezik egy olyan logikai alkalmazással, amely egy, az adott hely API-ját támogató http post-kérelmet küld egy webhelyre `multipart/form-data` , amely támogatja a típust. A művelet a következő módon jelenhet meg:

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

* [HTTP-trigger paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [HTTP-műveleti paraméterek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Kimenet részletei

Itt talál további információt a HTTP-triggerből vagy-műveletből származó kimenetekről, ami visszaadja ezt az információt:

| Tulajdonság neve | Type | Leírás |
|---------------|------|-------------|
| fejlécek | object | A kérelemben szereplő fejlécek |
| törzse | object | JSON-objektum | A kérelem szövegtörzsét tartalmazó objektum |
| Állapotkód | int | A kérelemben szereplő állapotkód |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadott |
| 400 | Hibás kérés |
| 401 | Nem engedélyezett |
| 403 | Tiltott |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

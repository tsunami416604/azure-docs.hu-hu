---
title: Szolgáltatási végpontok hívása HTTP vagy HTTPS használatával
description: Kimenő HTTP-vagy HTTPS-kérelmek küldése a szolgáltatási végpontoknak Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 8aefe851708c0b8d8780d03e4364e034e783bf4a
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79297200"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Szolgáltatási végpontok hívása HTTP vagy HTTPS protokollon keresztül Azure Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített http-trigger vagy művelet használatával olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek http-vagy https-kapcsolaton keresztül küldenek kéréseket a szolgáltatási végpontoknak. Megfigyelheti például a webhely szolgáltatási végpontját, ha ellenőrzi, hogy a végpont egy adott időpontban van-e. Ha a megadott esemény a végponton történik, például a webhely leállásakor, az esemény elindítja a logikai alkalmazás munkafolyamatát, és futtatja az adott munkafolyamat műveleteit. Ha ehelyett a bejövő HTTPS-hívásokat szeretné fogadni és válaszolni, használja a beépített [kérelem-triggert vagy a válasz műveletet](../connectors/connectors-native-reqres.md).

> [!NOTE]
> A cél végpont képességei alapján a HTTP-összekötő támogatja a 1,0, 1,1 és 1,2 Transport Layer Security (TLS) verziókat. Logic Apps a lehető legmagasabb támogatott verzió használatával egyeztet a végponttal. Tehát ha például a végpont támogatja a 1,2-et, az összekötő először a 1,2-et használja. Ellenkező esetben az összekötő a következő legmagasabb támogatott verziót használja.

Ha ismétlődő ütemterv alapján szeretné megtekinteni vagy *lekérdezni* egy végpontot, [adja hozzá a http-triggert](#http-trigger) a munkafolyamat első lépéseként. Minden alkalommal, amikor a trigger ellenőrzi a végpontot, az trigger meghívja vagy elküld egy *kérést* a végpontnak. A végpont válasza határozza meg, hogy a logikai alkalmazás munkafolyamata fut-e. Az trigger bármilyen tartalmat továbbít a végpont válaszában a logikai alkalmazás műveleteire.

Ha egy végpontot szeretne meghívni a munkafolyamatban bárhol máshol, [adja hozzá a http-műveletet](#http-action). A végpont válasza határozza meg, hogyan futnak a munkafolyamat hátralévő műveletei.

> [!IMPORTANT]
> Ha egy HTTP-trigger vagy-művelet tartalmazza ezeket a fejléceket, Logic Apps eltávolítja ezeket a fejléceket a generált kérési üzenetből a figyelmeztetés vagy a hiba megjelenítése nélkül:
>
> * `Accept-*`
> * `Allow`
> * `Content-*` a következő kivételekkel: `Content-Disposition`, `Content-Encoding`és `Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Bár a Logic Apps nem állítja le a HTTP-triggert vagy műveletet használó logikai alkalmazások mentését ezekkel a fejlécekkel, Logic Apps figyelmen kívül hagyja ezeket a fejléceket.

Ez a cikk bemutatja, hogyan adhat hozzá HTTP-triggert vagy műveletet a logikai alkalmazás munkafolyamataihoz.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A hívni kívánt cél végpont URL-címe

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps](../logic-apps/logic-apps-overview.md)?

* Az a logikai alkalmazás, amelyről meg szeretné hívni a célként megadott végpontot. A HTTP-trigger elindításához [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP-művelet használatához indítsa el a logikai alkalmazást a kívánt triggerrel. Ez a példa a HTTP-triggert használja első lépésként.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>HTTP-trigger hozzáadása

Ez a beépített trigger egy HTTP-hívást kezdeményez a végpont megadott URL-címére, és választ ad vissza.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A tervező keresési mezőjében válassza a **beépített**lehetőséget. A keresőmezőbe írja be a `http` szűrőt. Az **Eseményindítók** listából válassza ki a **http** -eseményindítót.

   ![HTTP-trigger kiválasztása](./media/connectors-native-http/select-http-trigger.png)

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

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a HTTP-triggert használja első lépésként.

1. Válassza ki azt a lépést, ahol a HTTP-műveletet hozzá szeretné adni, majd kattintson az **új lépés**gombra.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A **válasszon műveletet**területen válassza a **beépített**lehetőséget. A keresőmezőbe írja be a `http` szűrőt. A **műveletek** listából válassza ki a **http** -műveletet.

   ![HTTP-művelet kiválasztása](./media/connectors-native-http/select-http-action.png)

   Ez a példa átnevezi a műveletet "HTTP-művelet" névre, hogy a lépésnek legyen egy leíró neve.

1. Adja meg azoknak a [http-műveleti paramétereknek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) az értékeit, amelyeket fel szeretne venni a célként megadott végpontba.

   ![HTTP-műveleti paraméterek megadása](./media/connectors-native-http/http-action-parameters.png)

   Ha a **none**értéktől eltérő hitelesítési típust választ, a hitelesítési beállítások a kijelöléstől függően eltérőek. A HTTP-n elérhető hitelesítési típusokkal kapcsolatos további információkért tekintse meg a következő témaköröket:

   * [Hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Az erőforrásokhoz való hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)

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

| Tulajdonság neve | Típus | Leírás |
|---------------|------|-------------|
| fejlécek | object | A kérelemben szereplő fejlécek |
| törzse | object | JSON-objektum | A kérelem szövegtörzsét tartalmazó objektum |
| állapotkód | int | A kérelemben szereplő állapotkód |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Hibás kérés |
| 401 | Jogosulatlan |
| 403 | Tiltott |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>További lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

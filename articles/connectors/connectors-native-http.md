---
title: Szolgáltatásvégpontok hívása HTTP vagy HTTPS használatával
description: Kimenő HTTP- vagy HTTPS-kérelmek küldése szolgáltatásvégpontokba az Azure Logic Apps alkalmazásból
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
tags: connectors
ms.openlocfilehash: 6c52f2df34faf441ab70b48b11bbc393ebcecb65
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617615"
---
# <a name="call-service-endpoints-over-http-or-https-from-azure-logic-apps"></a>Szolgáltatásvégpontok hívása HTTP-n vagy HTTPS-en keresztül az Azure Logic Apps alkalmazásból

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a beépített HTTP-eseményindító vagy -művelet segítségével automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek http-n vagy HTTPS-en keresztül küldnek kéréseket a szolgáltatásvégpontok számára. Például figyelheti a szolgáltatás végpontját a webhely ellenőrzése, hogy a végpont egy adott ütemezés. Amikor a megadott esemény történik, hogy a végpont, például a webhely megy le, az esemény elindítja a logikai alkalmazás munkafolyamatés futtatja a műveleteket, hogy a munkafolyamatban. Ha ehelyett bejövő HTTPS-hívásokat szeretne fogadni és válaszolni, használja a beépített [kérelemeseményindítót vagy a válaszműveletet.](../connectors/connectors-native-reqres.md)

> [!NOTE]
> A célvégpont képessége alapján a HTTP-összekötő támogatja a Transport Layer Security (TLS) 1.0, 1.1 és 1.2 verzióit. A Logic Apps a végpontdal a lehető legmagasabb támogatott verzió használatával egyeztet. Így például ha a végpont támogatja az 1.2-t, az összekötő először az 1.2-t használja. Ellenkező esetben az összekötő a következő legmagasabb támogatott verziót használja.

Ha egy ismétlődő ütemezésen lévő végpontot szeretne ellenőrizni vagy *lehallgatni,* [adja hozzá a HTTP-eseményindítót](#http-trigger) a munkafolyamat első lépéseként. Minden alkalommal, amikor az eseményindító ellenőrzi a végpontot, az eseményindító hívásokat, vagy küld egy *kérést* a végpontra. A végpont válasza határozza meg, hogy a logikai alkalmazás munkafolyamata fut-e. Az eseményindító a végpont nak a logikai alkalmazásban végrehajtott műveletekre adott válaszából származó bármely tartalmat átad.

Ha a munkafolyamat bármely pontjáról szeretne végpontot hívni, [adja hozzá a HTTP műveletet.](#http-action) A végpont válasza határozza meg, hogyan futnak a munkafolyamat hátralévő műveletei.

> [!IMPORTANT]
> Ha egy HTTP-eseményindító vagy -művelet tartalmazza ezeket a fejléceket, a Logic Apps figyelmeztetés vagy hiba nélkül eltávolítja ezeket a fejléceket a létrehozott kérésüzenetből:
>
> * `Accept-*`
> * `Allow`
> * `Content-*`az alábbi kivételekkel: `Content-Disposition`, , `Content-Encoding`és`Content-Type`
> * `Cookie`
> * `Expires`
> * `Host`
> * `Last-Modified`
> * `Origin`
> * `Set-Cookie`
> * `Transfer-Encoding`
>
> Bár a Logic Apps nem akadályozza meg a HTTP-eseményindítót vagy műveletet használó logikai alkalmazások mentését, a Logic Apps figyelmen kívül hagyja ezeket a fejléceket.

Ez a cikk bemutatja, hogyan adhat hozzá egy HTTP-eseményindítót vagy műveletet a logikai alkalmazás munkafolyamatához.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A hívni kívánt célvégpont URL-címe

* Alapvető ismeretek [a logikai alkalmazások létrehozásához.](../logic-apps/quickstart-create-first-logic-app-workflow.md) Ha most kezdi a logikai alkalmazásokat, tekintse át [a Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A logikai alkalmazás, ahonnan meg szeretné hívni a célvégpontot. A HTTP-eseményindítóval való kezdéshez [hozzon létre egy üres logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md) A HTTP-művelet használatához indítsa el a logikai alkalmazást a kívánt eseményindítóval. Ez a példa a HTTP-eseményindítót használja első lépésként.

<a name="http-trigger"></a>

## <a name="add-an-http-trigger"></a>HTTP-eseményindító hozzáadása

Ez a beépített eseményindító http-hívást hoz egy végpont megadott URL-címéhez, és választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic App Designerben.

1. A tervező keresőmezőjében válassza **a Beépített**lehetőséget. A keresőmezőbe írja `http` be szűrőként. Az **Eseményindítók** listában válassza ki a **HTTP-eseményindítót.**

   ![HTTP-eseményindító kiválasztása](./media/connectors-native-http/select-http-trigger.png)

   Ez a példa átnevezi az eseményindítót "HTTP-eseményindítóra", hogy a lépés nek leíróbb neve is van. A példa később http-műveletet is hozzáad, és mindkét névnek egyedinek kell lennie.

1. Adja meg a [HTTP-eseményindító-paraméterek értékeit, amelyeket](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger) a célvégpont hívásában fel szeretne venni. Állítsa be az ismétlődést, hogy milyen gyakran ellenőrizze az eseményindító a célvégpontot.

   ![HTTP-eseményindító paramétereinek megadása](./media/connectors-native-http/http-trigger-parameters.png)

   Ha a **Nincs**hitelesítési típustól eltérő hitelesítéstípust választ, a hitelesítési beállítások a választástól függően eltérnek. A HTTP-hez elérhető hitelesítési típusokról az alábbi témakörökben olvashat bővebben:

   * [Hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Erőforrásokhoz való hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)

1. További elérhető paraméterek hozzáadásához nyissa meg az **Új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Folytassa a logikai alkalmazás munkafolyamatának felépítését olyan műveletekkel, amelyek az eseményindító aktiválódásakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

<a name="http-action"></a>

## <a name="add-an-http-action"></a>HTTP-művelet hozzáadása

Ez a beépített művelet http-hívást hoz egy végpont megadott URL-címéhez, és választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic App Designerben.

   Ez a példa a HTTP-eseményindítót használja első lépésként.

1. A HTTP-művelet hozzáadásának lépése alatt válassza az **Új lépés lehetőséget.**

   Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. Jelölje ki a**+** megjelenő pluszjelet ( ), majd kattintson **a Művelet hozzáadása gombra.**

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja `http` be szűrőként. A **Műveletek** listában válassza a **HTTP-műveletet.**

   ![HTTP-művelet kijelölése](./media/connectors-native-http/select-http-action.png)

   Ez a példa átnevezi a műveletet "HTTP művelet"-re, hogy a lépés nek leíróbb neve van.

1. Adja meg a [HTTP-műveletparaméterek értékeit, amelyeket](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) a célvégpont hívásában fel szeretne venni.

   ![HTTP-műveletparamétereinek megadása](./media/connectors-native-http/http-action-parameters.png)

   Ha a **Nincs**hitelesítési típustól eltérő hitelesítéstípust választ, a hitelesítési beállítások a választástól függően eltérnek. A HTTP-hez elérhető hitelesítési típusokról az alábbi témakörökben olvashat bővebben:

   * [Hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
   * [Erőforrásokhoz való hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)

1. További elérhető paraméterek hozzáadásához nyissa meg az **Új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

## <a name="content-with-multipartform-data-type"></a>Többrészes/űrlapadat-típusú tartalom

A HTTP-kérelmekben `multipart/form-data` beírt tartalom kezeléséhez hozzáadhat egy JSON-objektumot, amely ezzel a formátummal tartalmazza a `$content-type` HTTP-kérelem törzsének és `$multipart` attribútumainak és attribútumainak.

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

Tegyük fel például, hogy van egy logikai alkalmazás, amely http posta kérelmet küld egy `multipart/form-data` Excel-fájlhoz egy webhelyre a webhely API-jának használatával, amely támogatja a típust. A művelet a következőképpen nézhet ki:

![Többrészes űrlapadatok](./media/connectors-native-http/http-action-multipart.png)

Íme ugyanaz a példa, amely a HTTP-művelet JSON-definícióját mutatja az alapul szolgáló munkafolyamat-definícióban:

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

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítók és a műveletparamétereiről az alábbi szakaszokban talál további információt:

* [HTTP-eseményindító paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger)
* [HTTP-művelet paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action)

### <a name="output-details"></a>Kimenet részletei

Az alábbiakban további információkat talál a HTTP-eseményindító vagy -művelet kimeneteiről, amelyek ezt az információt adják vissza:

| Tulajdonság neve | Típus | Leírás |
|---------------|------|-------------|
| Fejlécek | objektum | A kérelem fejlécei |
| body (Törzs) | objektum | JSON-objektum | A kérelemből származó törzstartalommal rendelkező objektum |
| állapotkód | int | A kérelem állapotkódja |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Rossz kérés |
| 401 | Nem engedélyezett |
| 403 | Forbidden |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>További lépések

* További információ a [Logic Apps-összekötőkről](../connectors/apis-list.md)

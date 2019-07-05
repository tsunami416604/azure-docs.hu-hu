---
title: Csatlakozás az Azure Logic Apps a HTTP vagy HTTPS-végpontok
description: Automatizált feladatokat, folyamatok és munkafolyamatok HTTP vagy HTTPS végpontok monitorozása az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: fa5fd3ef8b144826468f56ea2a14be592cef5dc1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541248"
---
# <a name="call-http-or-https-endpoints-by-using-azure-logic-apps"></a>Azure Logic Apps használatával HTTP vagy HTTPS-végpontok hívása

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a beépített HTTP-összekötőt, akkor automatizálhatja, amelyek rendszeresen meg a logikai alkalmazások létrehozásával bármely olyan HTTP- vagy HTTPS-végponthoz. Például figyelemmel kísérheti a szolgáltatásvégpont-webhely ellenőrzése, hogy a végpont a megadott ütemezés szerint. Ha egy adott esemény történik, ha a webhely leáll, például, hogy a végpont az esemény elindítja a logikai alkalmazás munkafolyamat, és futtatja a megadott műveleteket.

Ellenőrizze vagy *lekérdezési* végpont rendszeres ütemezés szerint, használhatja a HTTP-eseményindítóval első lépéseként a munkafolyamatban. Az eseményindító minden ellenőrzés hívást küld vagy *kérelem* a végponthoz. A végpont válasz határozza meg, hogy fut-e a logikai alkalmazás munkafolyamat. Az eseményindító a műveleteknek a logikai alkalmazásban a válaszból továbbítja a tartalmat.

Használható a HTTP-művelet további lépésként a munkafolyamatban a végpontot hív, ha szeretne. Koncového bodu válasz határozza meg, hogyan fennmaradó a munkafolyamat-műveletek futtatása.

A céloldali végpont funkció alapján a HTTP-összekötő támogatja a Transport Layer Security (TLS) 1.0, 1.1 és 1.2-es. A Logic Apps egyezteti a végponttal keresztül lehetséges legmagasabb támogatott verzióját használja. Így például, ha a végpont támogatja az 1.2-es, az összekötő az 1.2 verziót használja először. Ellenkező esetben az összekötő használja a következő legújabb támogatott verziót.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A meghívni kívánt cél végpont URL-címe

* Alapvető ismeretek szerezhetők [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A logikai alkalmazás, ahonnan csak szeretné, a céloldali végpont meghívására. A HTTP-eseményindítóval kezdődik [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP-művelet használatához indítsa el a logikai alkalmazás minden olyan eseményindítóval, amelyeket szeretne. A példa első lépéseként a HTTP-eseményindítóval.

## <a name="add-an-http-trigger"></a>HTTP-trigger hozzáadása

A beépített trigger lehetővé teszi a megadott URL-cím a végpont HTTP-hívás, és választ küld.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. A tervezőben a keresőmezőbe írja be szűrőként "http". Az a **eseményindítók** listáról válassza ki a **HTTP** eseményindító.

   ![Válassza ki a HTTP-eseményindító](./media/connectors-native-http/select-http-trigger.png)

   Ebben a példában a "HTTP-eseményindító" eseményindító átnevezése, úgy, hogy a lépés egy leíró nevet. Ezenkívül a példa későbbi ad hozzá egy HTTP-művelet, és mindkét egyedieknek kell lenniük.

1. Adja meg az értékeket a [HTTP-eseményindító paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) , amelyeket szeretne szerepeltetni a cél-végpont meghívása. Állítsa be számára, hogy milyen gyakran kívánja az eseményindító az ismétlődést a céloldali végpont ellenőrzése.

   ![Adja meg a HTTP-eseményindító paraméterei](./media/connectors-native-http/http-trigger-parameters.png)

   HTTP-hez elérhető hitelesítési típusok kapcsolatos további információkért lásd: [hitelesítéshez a HTTP-eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Egyéb elérhető paraméterek hozzáadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt paramétereket.

1. Folytassa a logikai alkalmazás munkafolyamat-műveletek, amelyek futtatását, amikor az eseményindító aktiválódik.

1. Ha elkészült, kész, ne felejtse el menteni a logikai alkalmazást. A Tervező eszköztárán válassza **mentése**.

## <a name="add-an-http-action"></a>Egy HTTP-művelet hozzáadása

A beépített művelet lehetővé teszi a megadott URL-cím a végpont HTTP-hívás, és a egy választ ad vissza.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

   A példa első lépéseként a HTTP-eseményindítóval.

1. Válassza ki a lépés, ha szeretné a HTTP-művelet hozzáadása, alatt **új lépés**.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. Válassza a pluszjelet ( **+** ), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A tervezőben a keresőmezőbe írja be szűrőként "http". Az a **műveletek** listáról válassza ki a **HTTP** művelet.

   ![Válassza ki a HTTP-művelet](./media/connectors-native-http/select-http-action.png)

   Ebben a példában a "HTTP-művelet" művelet átnevezi, úgy, hogy a lépés egy leíró nevet.

1. Adja meg az értékeket a [HTTP műveleti paraméterek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) , amelyeket szeretne szerepeltetni a cél-végpont meghívása.

   ![Adja meg a HTTP műveleti paraméterek](./media/connectors-native-http/http-action-parameters.png)

   HTTP-hez elérhető hitelesítési típusok kapcsolatos további információkért lásd: [hitelesítéshez a HTTP-eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Egyéb elérhető paraméterek hozzáadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt paramétereket.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A Tervező eszköztárán válassza **mentése**.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindító és művelet paraméterekkel kapcsolatos további információkért tekintse meg az ezekben a szakaszokban:

* [HTTP eseményindító paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [A HTTP műveleti paraméterek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Kimenet részletei

További információ a kimenetek egy HTTP-trigger vagy művelet, amely ezeket az információkat adja vissza a következő:

| Tulajdonság neve | Típus | Leírás |
|---------------|------|-------------|
| A fejlécek | object | A kérelemből a fejlécek |
| Törzs | object | JSON-objektum | Az objektum a kérelem törzsében tartalommal |
| Állapotkód | int | A kérelem az állapotkód: |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadva |
| 400 | Hibás kérés |
| 401 | Nem engedélyezett |
| 403 | Tiltott |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)

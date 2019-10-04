---
title: Esemény-alapú feladatok és a munkafolyamatok létrehozása az Azure Logic Appsben
description: Aktiválja, szüneteltetése és folytatása automatizált feladatokat, folyamatok és munkafolyamatok, amelyek egy végponton fordulhat elő, az Azure Logic Apps-események alapján
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: c2658df185d4836210c496d2c46a00a3541257a2
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541335"
---
# <a name="automate-event-based-tasks-and-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Az Azure Logic Appsben a HTTP-webhookok használatával eseményalapú feladatok és a munkafolyamatok automatizálása

A [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és a beépített HTTP-Webhook-összekötőt, akkor automatizálhatja, várjon, és fognak futni az adott eseményeket egy HTTP vagy HTTPS-végpontján logikai alkalmazások létrehozásával. Például létrehozhat egy logikai alkalmazást, amely figyeli egy szolgáltatásvégpont várakozással előtt a munkafolyamatot kiváltó, és a megadott műveletek futtatása ahelyett, hogy rendszeresen ellenőrzi egy adott esemény vagy *lekérdezési* , hogy a végpont.

Az alábbiakban néhány példa eseményalapú munkafolyamat:

* Várjon, amíg az elemek érkeznek a egy [Azure Event Hub](https://github.com/logicappsio/EventHubAPI) egy logikai alkalmazás futtatásának indítása előtt.
* Várjon, amíg egy jóváhagyási munkafolyamat folytatása előtt.

## <a name="how-do-webhooks-work"></a>Hogyan működik a webhookok?

Egy HTTP-webhook eseményindító eseményalapú, amely nem függ az ellenőrzés vagy lekérdezési rendszeresen új elemeket. Egy logikai alkalmazást, amely egy webhook eseményindítóval indul, vagy ha módosítja a logikai alkalmazás le van tiltva, hogy engedélyezve van, a webhook eseményindítóhoz mentésekor *feliratkozik* egy adott szolgáltatás vagy a végpont regisztrálásával egy *visszahívási URL-Címének* szolgáltatás vagy a végpont. Az eseményindító majd megvárja, amíg a szolgáltatás vagy a végpont hívja meg az URL-cím, amely a logikai alkalmazás futtatásának megkezdése. Hasonló a [kérelem típusú trigger](connectors-native-reqres.md), a logikai alkalmazás akkor következik be, azonnal, ha a megadott esemény történik. Az eseményindító *lemondja az előfizetést* a szolgáltatás vagy a végpont Ha eltávolítja az eseményindítót, a logikai alkalmazás mentéséhez, vagy ha módosítja a logikai alkalmazást engedélyezett.

Egy HTTP-webhook művelettel is, eseményalapú és *feliratkozik* egy adott szolgáltatás vagy a végpont regisztrálja a *visszahívási URL-Címének* szolgáltatás vagy a végpont. A webhook művelettel megszakítja a logic app-munkafolyamatot, és megvárja, amíg a szolgáltatás vagy az endpoint hívásokat az URL-cím a logikai alkalmazás folytatja futtatása előtt. A művelet logikai alkalmazás *lemondja az előfizetést* a szolgáltatás vagy a végpont ezekben az esetekben:

* Ha a webhook művelet sikeres befejezését követően
* Ha a logikai alkalmazás futtatásának megszakítása válaszra való várakozás közben
* Mielőtt a logikai alkalmazás túllépi az időkorlátot

Ha például az Office 365 Outlook-összekötő a [ **jóváhagyó e-mail küldése** ](connectors-create-api-office365-outlook.md) beavatkozásra egy webhook művelettel ezt a mintát a következő példát. Ez a minta a webhook művelet használatával kiterjesztheti az bármely szolgáltatás.

További információkért lásd az alábbi témakörök:

* [HTTP-Webhook eseményindító paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhookok és -előfizetések](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Egyéni API-k, amelyek támogatják a webhook létrehozása](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy már üzembe helyezett végpont URL-CÍMÉT vagy az API, amely támogatja a webhook előfizetés, és mondja mintája [webhook eseményindítók a logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) vagy [webhook-műveletek a logic apps](../logic-apps/logic-apps-create-api-app.md#webhook-actions) szükség szerint

* Alapvető ismeretek szerezhetők [létrehozása a logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

* A logikai alkalmazás, ahol szeretné várja meg, a céloldali végpont adott esemény. A HTTP-Webhook eseményindító használatbavételéhez [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP-Webhook művelettel, indítsa el a logikai alkalmazás minden olyan eseményindítóval, amelyeket szeretne. A példa első lépéseként a HTTP-eseményindítóval.

## <a name="add-an-http-webhook-trigger"></a>Egy HTTP-Webhook eseményindító hozzáadása

A beépített trigger regisztrálja a megadott szolgáltatás egy visszahívási URL-címet, és megvárja, amíg a szolgáltatás URL-CÍMRE egy HTTP POST-kérelmet küldeni. Ez az esemény akkor fordul elő, amikor a trigger akkor aktiválódik, majd azonnal futtatja a logikai alkalmazás.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. A tervezőben a keresőmezőbe írja be szűrőként "http-webhook". Az a **eseményindítók** listáról válassza ki a **HTTP-Webhook** eseményindító.

   ![Válassza ki a HTTP-Webhook eseményindító](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Ebben a példában a "HTTP-Webhook eseményindító" eseményindító átnevezése, úgy, hogy a lépés egy leíró nevet. Ezenkívül a példa későbbi ad hozzá a HTTP-Webhook művelet, és mindkét egyedieknek kell lenniük.

1. Adja meg az értékeket a [HTTP-Webhook eseményindító paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) , hogy szeretné-e az előfizetés használata és előfizetés lemondása hívásokat, például:

   ![Adja meg a HTTP-Webhook eseményindító paraméterei](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Egyéb elérhető paraméterek hozzáadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt paramétereket.

   HTTP-webhook elérhető hitelesítési típusok kapcsolatos további információkért lásd: [hitelesítéshez a HTTP-eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Folytassa a logikai alkalmazás munkafolyamat-műveletek, amelyek futtatását, amikor az eseményindító aktiválódik.

1. Ha elkészült, kész, ne felejtse el menteni a logikai alkalmazást. A Tervező eszköztárán válassza **mentése**.

   A logikai alkalmazás mentése meghívja az előfizetési végpont, és regisztrálja a visszahívási URL-cím a logikai alkalmazás indítására.

1. Most már minden alkalommal, amikor a célként megadott szolgáltatás elküldi egy `HTTP POST` kérése a visszahívási URL-címre, a logikai alkalmazás akkor aktiválódik, és a kérés átadott adatokat tartalmazza.

## <a name="add-an-http-webhook-action"></a>HTTP-Webhook művelet hozzáadása

A beépített művelet regisztrálja a megadott szolgáltatás egy visszahívási URL-címet, a logikai alkalmazás munkafolyamat felfüggesztése és megvárja, amíg a szolgáltatás URL-CÍMRE egy HTTP POST-kérelmet küldeni. Ha ez az esemény történik, a művelet folytatja, a logikai alkalmazás futtatása.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

   Ez a példa első lépéseként a HTTP-Webhook eseményindító használja.

1. Válassza ki a lépés, ha szeretné a HTTP-Webhook művelet hozzáadása, alatt **új lépés**.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. Válassza a pluszjelet ( **+** ), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. A tervezőben a keresőmezőbe írja be szűrőként "http-webhook". Az a **műveletek** listáról válassza ki a **HTTP-Webhook** művelet.

   ![HTTP-Webhook művelet kiválasztása](./media/connectors-native-webhook/select-http-webhook-action.png)

   Ebben a példában a "HTTP-Webhook művelettel" művelet átnevezi, úgy, hogy a lépés egy leíró nevet.

1. Adja meg a HTTP Webhook műveleti paraméterek, amelyek hasonlóak a [HTTP-Webhook eseményindító paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) , hogy szeretné-e az előfizetés használata és előfizetés lemondása hívásokat, például:

   ![Adja meg a HTTP-Webhook műveleti paraméterek](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   Futásidőben a logikai alkalmazás az előfizetési végpont hívja, ez a művelet futtatásakor. A logikai alkalmazás ezután felfüggeszti a munkafolyamatot, és megvárja, amíg a célszolgáltatás küldése egy `HTTP POST` kérelem a visszahívási URL-CÍMRE. Ha a művelet sikeresen befejeződik, a művelet lemondja az előfizetést a végpontról és a logikai alkalmazás folytatja a munkafolyamatot futtató.

1. Egyéb elérhető paraméterek hozzáadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt paramétereket.

   HTTP-webhook elérhető hitelesítési típusok kapcsolatos további információkért lásd: [hitelesítéshez a HTTP-eseményindítók és műveletek](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A Tervező eszköztárán válassza **mentése**.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindító és művelet paramétereket, amelyek hasonlít egymáshoz, további információ: [HTTP-Webhook paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Kimenet részletei

További információ a kimenetek HTTP-Webhook eseményindító vagy egy műveletet, amely ezeket az információkat adja vissza a következő:

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

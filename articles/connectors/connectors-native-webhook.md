---
title: Várjon, és válaszoljon az eseményekre – Azure Logic Apps
description: Automatizálhatja, szüneteltetheti és folytathatja a munkafolyamatokat egy szolgáltatási végpont eseményei alapján a Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 10/10/2019
tags: connectors
ms.openlocfilehash: ee86f53795b1b3e7bd61480a490d4e18c844d4c2
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804265"
---
# <a name="create-and-run-automated-event-based-workflows-by-using-http-webhooks-in-azure-logic-apps"></a>Automatizált eseményvezérelt munkafolyamatok létrehozása és futtatása HTTP-webhookok használatával Azure Logic Apps

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a beépített http webhook-összekötő segítségével automatizálhatja a várakozási és a futtatási munkafolyamatokat a logikai alkalmazások létrehozásával egy http-vagy https-végponton megjelenő események alapján. Létrehozhat például egy olyan logikai alkalmazást, amely figyeli a szolgáltatási végpontot egy adott eseményre való várakozással, mielőtt elindítja a munkafolyamatot, és futtatja a megadott műveleteket, nem pedig rendszeresen ellenőrzi vagy *lekérdezi* a végpontot.

Íme néhány példa az eseményvezérelt munkafolyamatokra:

* Várjon, amíg egy elem egy [Azure Event hub](https://github.com/logicappsio/EventHubAPI) -ból érkezik a logikai alkalmazás futtatásának elindítása előtt.
* Várjon egy jóváhagyást, mielőtt folytatná a munkafolyamatot.

## <a name="how-do-webhooks-work"></a>Hogyan működnek a webhookok?

A HTTP webhook eseményindító esemény-alapú, amely nem függ az új elemek ellenőrzésével vagy lekérdezésével. Ha olyan logikai alkalmazást ment, amely egy webhook-triggerrel kezdődik, vagy ha a logikai alkalmazást Letiltottról Engedélyezettre módosítja, akkor a webhook-trigger egy adott szolgáltatásra vagy végpontra *fizet* , ha a *visszahívási URL-címet* regisztrálja az adott szolgáltatással vagy végponttal. Az trigger ezután megvárja, amíg az adott szolgáltatás vagy végpont meghívja az URL-címet, amely elindítja a logikai alkalmazás futtatását. A [kérelem eseményindítóhoz](connectors-native-reqres.md)hasonlóan a logikai alkalmazás azonnal aktiválódik, ha a megadott esemény történik. Ha eltávolítja az triggert, és menti a logikai alkalmazást, vagy ha a logikai alkalmazást letiltottra módosítja, a trigger *lemond* a szolgáltatásról vagy a végpontról.

Egy HTTP-webhook művelet is esemény-alapú, és egy adott szolgáltatásra vagy végpontra is *előfizet* egy *visszahívási URL-cím* az adott szolgáltatással vagy végponttal való regisztrálásával. A webhook művelet szünetelteti a logikai alkalmazás munkafolyamatát, és megvárja, amíg a szolgáltatás vagy végpont meghívja az URL-címet, mielőtt a logikai alkalmazás folytatja a futtatást. A műveleti logikai alkalmazás *lemond* a szolgáltatásból vagy végpontból a következő esetekben:

* A webhook művelet sikeres befejeződése után
* Ha a logikai alkalmazás futtatása megszakad a válaszra való várakozás közben.
* A logikai alkalmazás időtúllépése előtt

Például az Office 365 Outlook Connector [**Küldés-jóváhagyási e-mail-** ](connectors-create-api-office365-outlook.md) művelete egy példa a webhook műveletre, amely ezt a mintát követi. Ezt a mintát bármely szolgáltatásra kiterjesztheti a webhook művelet használatával.

> [!NOTE]
> A Logic Apps a Transport Layer Security (TLS) 1,2 kényszeríti, ha a hívást a HTTP webhook-triggerre vagy műveletre fogadja. Ha az SSL-kézfogás hibáit látja, ügyeljen arra, hogy a TLS 1,2-et használja.

További információkért tekintse meg a következő témaköröket:

* [HTTP webhook-trigger paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Webhookok és előfizetések](../logic-apps/logic-apps-workflow-actions-triggers.md#webhooks-and-subscriptions)
* [Webhookot támogató egyéni API-k létrehozása](../logic-apps/logic-apps-create-api-app.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy már üzembe helyezett végpont vagy API URL-címe, amely támogatja a [webhook](../logic-apps/logic-apps-create-api-app.md#webhook-triggers) előfizetését és a leiratkozási mintát a Logic apps vagy [webhook műveletekben a logikai alkalmazásokban](../logic-apps/logic-apps-create-api-app.md#webhook-actions) a megfelelő módon.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

* Az a logikai alkalmazás, amelyben meg szeretné várni a konkrét eseményeket a célként megadott végponton. Ha a HTTP webhook-triggerrel szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). A HTTP webhook művelet használatához indítsa el a logikai alkalmazást a kívánt triggerrel. Ez a példa a HTTP-triggert használja első lépésként.

## <a name="add-an-http-webhook-trigger"></a>HTTP webhook-trigger hozzáadása

Ez a beépített trigger egy visszahívási URL-címet regisztrál a megadott szolgáltatással, és arra vár, hogy a szolgáltatás küldjön egy HTTP POST-kérelmet az URL-címre. Ha ez az esemény történik, az eseményindító elindít és azonnal futtatja a logikai alkalmazást.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Nyissa meg az üres logikai alkalmazást a Logic app Designerben.

1. A tervezőben a keresőmezőbe írja be a "http webhook" kifejezést a szűrőként. Az **Eseményindítók** listából válassza ki a **http webhook** eseményindítót.

   ![HTTP webhook-trigger kiválasztása](./media/connectors-native-webhook/select-http-webhook-trigger.png)

   Ez a példa átnevezi a triggert a "HTTP webhook trigger" névre, hogy a lépésnek legyen egy leíró neve. A példa később egy HTTP webhook-műveletet is felvesz, és mindkét névnek egyedinek kell lennie.

1. Adja meg az előfizetési és leiratkozási hívásokhoz használni kívánt [http webhook-trigger paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger) értékeit, például:

   ![HTTP webhook-trigger paramétereinek megadása](./media/connectors-native-webhook/http-webhook-trigger-parameters.png)

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

   További információ a HTTP webhookhoz elérhető hitelesítési típusokról: [hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Folytassa a logikai alkalmazás munkafolyamatának kialakítását olyan műveletekkel, amelyek az eseményindító indításakor futnak.

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

   A logikai alkalmazás mentése meghívja az előfizetés végpontot, és regisztrálja a visszahívási URL-címet a logikai alkalmazás aktiválásához.

1. Most, amikor a TARGET szolgáltatás egy `HTTP POST` kérést küld a visszahívási URL-címre, a logikai alkalmazás tüzeket tartalmaz, és tartalmazza a kérelemben átadott adatokat.

## <a name="add-an-http-webhook-action"></a>HTTP-webhook művelet hozzáadása

Ez a beépített művelet regisztrálja a visszahívási URL-címet a megadott szolgáltatással, szünetelteti a logikai alkalmazás munkafolyamatát, és megvárja, amíg a szolgáltatás HTTP POST-kérelmet küld az adott URL-címre. Ha ez az esemény történik, a művelet folytatja a logikai alkalmazás futtatását.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). Nyissa meg a logikai alkalmazást a Logic app Designerben.

   Ez a példa a HTTP webhook triggert használja első lépésként.

1. Válassza ki azt a lépést, amelyhez hozzá szeretné adni a HTTP webhook műveletet, majd kattintson az **új lépés**gombra.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a megjelenő pluszjelet ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

1. A tervezőben a keresőmezőbe írja be a "http webhook" kifejezést a szűrőként. A **műveletek** listából válassza ki a **http webhook** műveletet.

   ![HTTP-webhook művelet kiválasztása](./media/connectors-native-webhook/select-http-webhook-action.png)

   Ez a példa átnevezi a műveletet "HTTP webhook művelet" névre, hogy a lépésnek legyen egy leíró neve.

1. Adja meg a HTTP webhook műveleti paramétereinek értékeit, amelyek az előfizetési és leiratkozási hívásokhoz használni kívánt [http-webhook-trigger paramétereinek](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger) hasonlók, például:

   ![Adja meg a HTTP-webhook műveleti paramétereit](./media/connectors-native-webhook/http-webhook-action-parameters.png)

   A Futtatás során a logikai alkalmazás a művelet futtatásakor meghívja az előfizetés-végpontot. A logikai alkalmazás ezután szünetelteti a munkafolyamatot, és megvárja, amíg a TARGET szolgáltatás elküld egy `HTTP POST` kérelmet a visszahívási URL-címre. Ha a művelet sikeresen befejeződik, a művelet lemond a végpontról, és a logikai alkalmazás folytatja a munkafolyamat futtatását.

1. Más elérhető paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket.

   További információ a HTTP webhookhoz elérhető hitelesítési típusokról: [hitelesítés hozzáadása a kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. Ha elkészült, ne felejtse el menteni a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés**lehetőséget.

## <a name="connector-reference"></a>Összekötő-referencia

További információ az aktiválási és műveleti paraméterekről, amelyek hasonlóak egymáshoz: [http webhook paraméterei](../logic-apps/logic-apps-workflow-actions-triggers.md##http-webhook-trigger).

### <a name="output-details"></a>Kimenet részletei

Itt talál további információt egy HTTP webhook-triggerből vagy-műveletből származó kimenetről, amely a következő információkat adja vissza:

| Tulajdonság neve | Type (Típus) | Leírás |
|---------------|------|-------------|
| fejlécek | objektum | A kérelemben szereplő fejlécek |
| törzse | objektum | JSON-objektum | A kérelem szövegtörzsét tartalmazó objektum |
| állapotkód | int | A kérelemben szereplő állapotkód |
|||

| Állapotkód | Leírás |
|-------------|-------------|
| 200 | OK |
| 202 | Elfogadott |
| 400 | Hibás kérelem |
| 401 | Nem engedélyezett |
| 403 | Forbidden |
| 404 | Nem található |
| 500 | Belső kiszolgálóhiba. Ismeretlen hiba történt. |
|||

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése

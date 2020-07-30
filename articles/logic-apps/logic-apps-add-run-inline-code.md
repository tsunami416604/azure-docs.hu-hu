---
title: Kódrészletek hozzáadása és futtatása beágyazott kóddal
description: Útmutató kódrészletek létrehozásához és futtatásához a Azure Logic Apps használatával létrehozott automatizált feladatokhoz és munkafolyamatokhoz.
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.custom: devx-track-javascript
ms.openlocfilehash: a1c3828a4b1899ff4b22c0a9520f676add21fe02
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420124"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Kódrészletek hozzáadása és futtatása beágyazott kód használatával Azure Logic Apps

Ha egy kódrészletet szeretne futtatni a logikai alkalmazásban, akkor a logikai alkalmazás munkafolyamatának lépéseként hozzáadhatja a beépített **beágyazott kód** műveletet. Ez a művelet akkor működik a legjobban, ha olyan kódot szeretne futtatni, amely megfelel ennek a forgatókönyvnek:

* A JavaScriptben fut. Hamarosan további nyelvek érkeznek.
* A futás öt másodperc vagy kevesebb idő alatt fejeződik be.
* Legfeljebb 50 MB méretű adatkezelést tesz elérhetővé.
* Nem szükséges a [ **változók** műveletekkel](../logic-apps/logic-apps-create-variables-store-values.md)dolgozni, amelyek még nem támogatottak.
* A Node.js 8.11.1 verzióját használja. További információ: [standard beépített objektumok](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > `require()`Ezt a függvényt nem támogatja a **beágyazott kód** művelet a JavaScript futtatásához.

Ez a művelet futtatja a kódrészletet, és visszaadja az adott kódrészlet kimenetét az **eredmény**nevű tokennek, amelyet a logikai alkalmazás későbbi műveleteiben használhat. Olyan esetekben, amikor egy függvényt szeretne létrehozni a kódhoz, próbálkozzon [egy Azure-függvény létrehozásával és meghívásával](../logic-apps/logic-apps-azure-functions.md) a logikai alkalmazásban.

Ebben a cikkben a logikai alkalmazás akkor aktiválódik, amikor új e-mail érkezik egy Office 365 Outlook-fiókba. A kódrészlet kibontja és visszaadja az e-mail törzsében megjelenő e-mail-címeket.

![Példa – áttekintés](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az a logikai alkalmazás, amelyhez hozzá szeretné adni a kódrészletet, beleértve egy triggert is. Ha nem rendelkezik logikai alkalmazással, tekintse [meg az első logikai alkalmazás létrehozását](../logic-apps/quickstart-create-first-logic-app-workflow.md)ismertető útmutatót.

   A jelen témakörben található példa logikai alkalmazás ezt az Office 365 Outlook triggert használja: **új e-mail érkezésekor**

* A logikai alkalmazáshoz társított [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

  > [!NOTE]
  > Győződjön meg arról, hogy olyan integrációs fiókot használ, amely megfelelő a használati esethez vagy forgatókönyvhöz. A [szabad szintű](../logic-apps/logic-apps-pricing.md#integration-accounts) integrációs fiókok esetében például csak a feltáró forgatókönyvek és a számítási feladatok, a nem éles környezetek, a használat és az átviteli sebesség korlátozott, és nem támogatott a szolgáltatói szerződés (SLA). Más szinteken költségek is merülnek fel, azonban SLA-támogatással, nagyobb átviteli sebességgel és magasabb korlátokkal rendelkeznek. További információ az integrációs fiók [szintjeiről](../logic-apps/logic-apps-pricing.md#integration-accounts), [díjszabásáról](https://azure.microsoft.com/pricing/details/logic-apps/)és [korlátairól](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="add-inline-code"></a>Beágyazott kód hozzáadása

1. Ha még nem tette meg, a [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A tervezőben adja hozzá a **beágyazott kód** műveletet a logikai alkalmazás munkafolyamatában a kívánt helyen.

   * A munkafolyamat végén található művelet hozzáadásához válassza az **új lépés**lehetőséget.

   * A meglévő lépések közötti művelet hozzáadásához vigye az egérmutatót a lépéseket összekötő nyíl fölé. Válassza a pluszjelet ( **+** ), és válassza a **művelet hozzáadása**lehetőséget.

   Ez a példa hozzáadja a **beágyazott kód** műveletet az Office 365 Outlook trigger alatt.

   ![Új lépés hozzáadása](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. A **válasszon műveletet**területen a keresőmezőbe írja be a "beágyazott kód" kifejezést a szűrőként. A műveletek listából válassza a következő műveletet: **JavaScript-kód végrehajtása**

   ![Válassza a "JavaScript-kód végrehajtása" lehetőséget](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A művelet megjelenik a tervezőben, és tartalmaz néhány alapértelmezett mintakód, beleértve a visszatérési utasítást is.

   ![Beágyazott kód művelet alapértelmezett mintakód](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. A **Code (kód** ) mezőben törölje a mintakódt, és adja meg a futtatni kívánt kódot. Olyan kódot írhat, amelyet egy metódusba szeretne helyezni, de nem definiálja a metódus aláírását. 

   Egy felismert kulcsszó beírásakor megjelenik az automatikus kiegészítés lista, amely az elérhető kulcsszavak közül választhat, például:

   ![Kulcsszavak automatikus kiegészítési listája](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Ez a példa a kódrészletet először egy olyan változót hoz létre, amely egy *reguláris kifejezést*tárol, amely meghatározza a bemeneti szövegben egyeztetendő mintát. A kód Ezután létrehoz egy változót, amely az e-mail-törzs adatait az triggerből tárolja.

   ![Változók létrehozása](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Ahhoz, hogy a trigger és a korábbi műveletek eredményei könnyebben használhatók legyenek, a dinamikus tartalmak listája jelenik meg, miközben a kurzor a **kód** mezőben található. Ebben a példában a lista az triggerből származó elérhető eredményeket jeleníti meg, beleértve a **szövegtörzs** tokenjét is, amelyet most kiválaszthat.

   Miután kiválasztotta a **törzs** jogkivonatát, a beágyazott kód művelet feloldja a tokent egy olyan `workflowContext` objektumra, amely hivatkozik az e-mailek `Body` tulajdonságának értékére:

   ![Eredmény kiválasztása](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   A **kód** mezőben a kódrészlet a írásvédett `workflowContext` objektumot bemenetként használhatja. Ez az objektum olyan altulajdonságokkal rendelkezik, amelyek lehetővé tennék a kód elérését a munkafolyamatban lévő trigger és előző műveletek eredményeihez.
   További információ: a jelen témakör későbbi részében található, az [trigger és a művelet eredményének ismertetése a kódban](#workflowcontext).

   > [!NOTE]
   >
   > Ha a kódrészlet a pont (.) operátort használó műveleti nevekre hivatkozik, ezeket a műveleti neveket hozzá kell adnia a [ **Actions** paraméterhez](#add-parameters). Ezeknek a hivatkozásoknak a szögletes zárójelek ([]) és az idézőjelek közé kell foglalniuk a műveleti neveket is, például:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A beágyazott kód művelethez nem szükséges `return` utasítás, de az eredmények egy `return` utasításból az **eredmény** -tokenen keresztül is elérhetők a későbbi műveletekben. 
   Például a kódrészlet visszaadja az eredményt a függvény meghívásával `match()` , amely megkeresi az e-mail-szövegtörzsben szereplő egyezéseket a reguláris kifejezéssel. Az **összeállítás** művelet az **eredmény** -token használatával hivatkozik a beágyazott kód művelet eredményeire, és egyetlen eredményt hoz létre.

   ![Befejezett logikai alkalmazás](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Ha elkészült, mentse a logikai alkalmazást.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Az trigger és a művelet eredményei a kódban

Az `workflowContext` objektum rendelkezik ezzel a szerkezettel, amely tartalmazza a `actions` , a `trigger` és az `workflow` altulajdonságot:

```json
{
   "workflowContext": {
      "actions": {
         "<action-name-1>": @actions('<action-name-1>'),
         "<action-name-2>": @actions('<action-name-2>')
      },
      "trigger": {
         @trigger()
      },
      "workflow": {
         @workflow()
      }
   }
}
```

Ez a tábla további információkat tartalmaz ezekről az altulajdonságokról:

| Tulajdonság | Típus | Description |
|----------|------|-------|
| `actions` | Objektum gyűjtése | A kódrészlet futtatása előtt futtatott műveletek eredményének objektumai. Minden objektumhoz tartozik egy *kulcs-érték* pár, amelyben a kulcs egy művelet neve, és az érték megegyezik a [műveletek () függvénynek](../logic-apps/workflow-definition-language-functions-reference.md#actions) a használatával történő meghívásával `@actions('<action-name>')` . A művelet neve ugyanazt a műveleti nevet használja, mint amelyet a rendszer az alapul szolgáló munkafolyamat-definícióban használ, amely a művelet nevében a szóközöket ("") váltja fel aláhúzással (_). Ez az objektum hozzáférést biztosít a műveleti tulajdonságok értékeihez az aktuális munkafolyamat-példány futtatásával. |
| `trigger` | Objektum | A trigger [() függvény](../logic-apps/workflow-definition-language-functions-reference.md#trigger)meghívására szolgáló eredmény objektum az triggerből és azzal egyenértékű. Ez az objektum hozzáférést biztosít a tulajdonságértékek az aktuális munkafolyamat-példányból való futtatásához. |
| `workflow` | Objektum | A munkafolyamat-objektum és az azzal egyenértékű, hogy meghívja a [workflow () függvényt](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Ez az objektum hozzáférést biztosít a munkafolyamat-tulajdonságértékek (például a munkafolyamat neve, a futtatási azonosító stb.) számára az aktuális munkafolyamat-példány futtatásához. |
|||

Ebben a témakörben az objektum a `workflowContext` következő tulajdonságokkal rendelkezik, amelyekhez a kód hozzáférhet:

```json
{
   "workflowContext": {
      "trigger": {
         "name": "When_a_new_email_arrives",
         "inputs": {
            "host": {
               "connection": {
                  "name": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "includeAttachments": "False"
            }
         },
         "outputs": {
            "headers": {
               "Pragma": "no-cache",
               "Content-Type": "application/json; charset=utf-8",
               "Expires": "-1",
               "Content-Length": "962095"
            },
            "body": {
               "Id": "AAMkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgBGAAAAAABmZwxUQtCGTqSPpjjMQeD",
               "DateTimeReceived": "2019-03-28T19:42:16+00:00",
               "HasAttachment": false,
               "Subject": "Hello World",
               "BodyPreview": "Hello World",
               "Importance": 1,
               "ConversationId": "AAQkADY0NGZhNjdhLTRmZTQtNGFhOC1iYjFlLTk0MjZlZjczMWRhNgAQ",
               "IsRead": false,
               "IsHtml": true,
               "Body": "Hello World",
               "From": "<sender>@<domain>.com",
               "To": "<recipient-2>@<domain>.com;<recipient-2>@<domain>.com",
               "Cc": null,
               "Bcc": null,
               "Attachments": []
            }
         },
         "startTime": "2019-05-03T14:30:45.971564Z",
         "endTime": "2019-05-03T14:30:50.1746874Z",
         "scheduledTime": "2019-05-03T14:30:45.8778117Z",
         "trackingId": "1cd5ffbd-f989-4df5-a96a-6e9ce31d03c5",
         "clientTrackingId": "08586447130394969981639729333CU06",
         "originHistoryName": "08586447130394969981639729333CU06",
         "code": "OK",
         "status": "Succeeded"
      },
      "workflow": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>",
         "name": "<logic-app-workflow-name>",
         "type": "Microsoft.Logic/workflows",
         "location": "<Azure-region>",
         "run": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<logic-app-workflow-name>/runs/08586453954668694173655267965CU00",
            "name": "08586453954668694173655267965CU00",
            "type": "Microsoft.Logic/workflows/runs"
         }
      }
   }
}
```

<a name="add-parameters"></a>

## <a name="add-parameters"></a>Paraméterek hozzáadása

Bizonyos esetekben előfordulhat, hogy explicit módon meg kell követelnie, hogy a **beágyazott kód** **művelet az trigger vagy a** **műveletek** paramétereinek hozzáadásával a kód által a függőségként megadott műveletekből származó eredményeket tartalmazzon. Ez a beállítás olyan esetekben hasznos, amikor a hivatkozott eredmények nem találhatók a futási időben.

> [!TIP]
> Ha azt tervezi, hogy felhasználja a kódot, adja hozzá a tulajdonságokra mutató hivatkozásokat a **kód** mező használatával, hogy a kód tartalmazza a megoldott jogkivonat-hivatkozásokat, nem pedig explicit függőségként adja hozzá az triggert vagy a műveleteket.

Tegyük fel például, hogy rendelkezik olyan kóddal, amely hivatkozik a **SelectedOption** eredményre az Office 365 Outlook-összekötőhöz tartozó **jóváhagyó e-mail küldése** műveletből. A létrehozás ideje alatt a Logic Apps motor elemzi a kódot annak megállapításához, hogy hivatkozott-e valamilyen triggerre vagy műveletre, és hogy az eredményeket automatikusan tartalmazza-e. A futási időben hibaüzenet jelenik meg, ha a hivatkozott trigger vagy művelet eredménye nem érhető el a megadott `workflowContext` objektumban, a triggert vagy műveletet explicit függőségként adhatja hozzá. Ebben a példában a **műveletek** paramétert adja hozzá, és megadja, hogy a **beágyazott kód** művelet explicit módon tartalmazza az eredményt a **jóváhagyó e-mail küldése** műveletből.

A paraméterek hozzáadásához nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket:

   ![Paraméterek hozzáadása](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Paraméter | Leírás |
   |-----------|-------------|
   | **Műveletek** | A korábbi műveletek eredményeinek belefoglalása. Lásd: [művelet eredményeinek belefoglalása](#action-results). |
   | **Eseményindító** | Az trigger eredményeinek belefoglalása. Lásd: [trigger eredményeinek belefoglalása](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Trigger eredményeinek belefoglalása

Ha az **Eseményindítók**lehetőséget választja, a rendszer megkérdezi, hogy az eseményindító eredményeit is tartalmazza-e.

* Az **trigger** listából válassza az **Igen**lehetőséget.

<a name="action-results"></a>

### <a name="include-action-results"></a>Művelet eredményeinek belefoglalása

Ha a **műveletek**lehetőséget választja, a rendszer kéri, hogy adja meg a hozzáadni kívánt műveleteket. A műveletek hozzáadásának megkezdése előtt azonban szüksége lesz a logikai alkalmazás alapjául szolgáló munkafolyamat-definícióban megjelenő művelet nevének verziójára.

* Ez a funkció nem támogatja a változókat, a hurkokat és az iterációs indexeket.

* A logikai alkalmazás munkafolyamat-definíciójában szereplő nevek aláhúzást (_) használnak, nem szóközzel.

* A pont operátort (.) használó műveleti nevek közé tartoznak például a következő operátorok:

  `My.Action.Name`

1. A tervező eszköztárán kattintson a **kód nézet**elemre, és keressen rá a `actions` művelet nevére a (z) attribútumon belül.

   Például `Send_approval_email_` a **jóváhagyás küldése e-mailben** művelet JSON-neve.

   ![Művelet nevének keresése a JSON-ban](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. A Tervező nézetre való visszatéréshez a kód nézet eszköztárán válassza a **tervező**lehetőséget.

1. Az első művelet hozzáadásához a **műveletek elem – 1** mezőben adja meg a művelet JSON-nevét.

   ![Első művelet megadása](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Egy másik művelet hozzáadásához válassza az **új elem hozzáadása**lehetőséget.

## <a name="reference"></a>Referencia

További információ a JavaScript- **kód végrehajtása** művelet szerkezetéről és szintaxisáról a logikai alkalmazás mögöttes munkafolyamat-definíciójában a munkafolyamat-definíciós nyelv használatával: Ez a művelet [hivatkozási szakasza](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>Következő lépések

További információ az [Azure Logic apps-összekötők számára](../connectors/apis-list.md)

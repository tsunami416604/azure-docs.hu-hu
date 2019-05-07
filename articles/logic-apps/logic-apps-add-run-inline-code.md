---
title: Adja hozzá, és futtassa a kódtöredékek – Azure Logic Apps
description: Adja hozzá, és a kódrészleteket futtat az Azure Logic Apps beágyazott kóddal
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: derek1ee, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 9ef11eb2099ff617fb4da4b9a924dc3f0550f226
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160545"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Adja hozzá, és a kódrészleteket futtat az Azure Logic Apps használatával beágyazott kódot

A logikai alkalmazás belül kódrészleteket futtatni szeretne, ha a beépített hozzáadhat **beágyazott kódot** művelet, a logikai alkalmazás munkafolyamat-lépés. Ez a művelet akkor működik a legjobban, ha szeretné futtatni a kódot, amely megfelel az ebben a forgatókönyvben:

* A JavaScript fut. Több nyelv hamarosan elérhető lesz.
* Öt másodperc vagy kevesebb befejeződik.
* Kezeli az adatokat legfeljebb 50 MB-nál.
* Node.js verzió 8.11.1 használja. További információkért lásd: [Standard beépített objektummal](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects).

Ez a művelet a kódtöredék fut, és a kimenetet visszaadja, hogy a kódrészlet a nevű jogkivonatként **eredmény**, amely a logikai alkalmazás a következő műveleteket használhatja. Más esetekben, ahol szeretné a kódot a függvény létrehozása, próbálja meg [egy Azure-függvény meghívásával](../logic-apps/logic-apps-azure-functions.md) a logikai alkalmazásban.

Ebben a cikkben a példa a logikai alkalmazások eseményindítói, amikor új e-mail érkezik az Office 365 Outlook-fiókot. A kódtöredék ad eredményül, és adja vissza minden olyan e-mail-címek jelennek meg az e-mail törzsének.

![Példa – áttekintés](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Hol szeretné a kódrészletet, beleértve a trigger hozzáadása a logikai alkalmazás. Ha a logikai alkalmazás nem rendelkezik, tekintse meg [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Ebben a témakörben a példa logikai alkalmazás használja az Office 365 Outlook-eseményindító: **Új e-mail érkezésekor**

* Egy [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , amely kapcsolódik a logikai alkalmazás

## <a name="add-inline-code"></a>Adja hozzá a beágyazott kódot

1. Ha még nem tette, az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logic App Designerben.

1. A tervezőben, adja hozzá a **beágyazott kódot** a helyen, a logikai alkalmazás munkafolyamat kívánt műveletet.

   * A művelet hozzáadása a munkafolyamat végén, válassza ki a **új lépés**.

   * A meglévő lépések közötti művelet hozzáadása, vigye az egérmutatót a nyílra, amely kapcsolódik a ezeket a lépéseket. Válassza a plusz jelre (**+**), és válassza ki **művelet hozzáadása**.

   Ebben a példában a **beágyazott kódot** az Office 365 Outlook-eseményindító műveletet.

   ![Új lépés hozzáadása](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. A **válasszon ki egy műveletet**, a Keresés mezőbe írja be az "beágyazott kódot" szűrőként. A műveletek listából válassza a következő műveletet: **Hajtsa végre a JavaScript-kódot**

   ![Válassza a "Végrehajtása JavaScript-kód"](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A művelet megjelenik a tervezőben, és tartalmaz néhány alapértelmezett kódpéldákat, beleértve a return utasítás.

   ![Beágyazott kód művelet alapértelmezett kódmintával](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. Az a **kód** mezőbe törlése a mintakódot, és írja be a kódot, amelyet futtatni szeretne. Akkor célszerű helyezni metódus belül, de a podpis metody definiálása nélkül programkódokat írhat. 

   Írja be egy felismerhető kulcsszót, amikor az automatikus kiegészítési lista jelenik meg, így kiválaszthatja a rendelkezésre álló kulcsszavakat, például:

   ![Kulcsszó automatikus kiegészítés listája](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Ez a példa a kódrészlet először létrehoz egy változót, amely tárolja a *reguláris kifejezés*, amely a bemeneti szövegben megfelelő mintát adja meg. A kód ezután létrehoz egy változót, a trigger által az e-mail törzsének adatokat tároló.

   ![Változók létrehozása](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Hogy könnyebben referencia az eseményindító és az előző művelet eredményét, a dinamikus tartalmak listája jelenik meg, amíg a kurzor belül van a **kód** mezőbe. Ebben a példában a listát a trigger által elérhető eredményeit jeleníti meg többek között a **törzs** jogkivonatot, amely most kiválaszthatja.

   Kiválasztása után a **törzs** token, a beágyazott kódot művelet oldja fel a tokent egy `workflowContext` objektum, amely az e-mail hivatkozik `Body` tulajdonság értéke:

   ![Válassza ki az eredmény](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   Az a **kód** mezőbe a kódrészlet használhatja a csak olvasható `workflowContext` bemenetként objektum. Ez az objektum altulajdonságok hozzáférést a kódot az eredményeket az eseményindítót és a munkafolyamat korábbi műveletek.
   További információkért lásd: Ez a rész a jelen témakör későbbi részében: [A kódban eseményindító és művelet eredménye hivatkozik](#workflowcontext).

   > [!NOTE]
   >
   > Ha a kódrészlet a művelet nevét, amely a pont (.) operátor használata hivatkozik, hozzá kell adnia ezeket a művelet nevét a [ **műveletek** paraméter](#add-parameters). Például ezeket a hivatkozásokat is kell tenni a szögletes zárójelek ([]) és idézőjelek között, a művelet nevét:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A beágyazott kódot a művelet nem igényel egy `return` utasítás, de származó eredmények egy `return` utasítás referenciaként a későbbi műveletekben keresztül érhetők el a **eredmény** token. 
   Például a kódtöredék eredményét adja vissza. ehhez hívja a `match()` függvény, mely talál megegyezik az e-mail törzsének ellen a reguláris kifejezés. A **összeállítás** műveletet használja a **eredmény** jogkivonat-referenciáját a beágyazott eredményeinek művelet code és hoz létre egyetlen eredmény.

   ![Befejezett logikai alkalmazás](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Ha elkészült, mentse a logikai alkalmazást.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Hivatkozás eseményindító és művelet eredménye a kódban

A `workflowContext` objektumnak van ez a struktúra, amely tartalmazza a `actions`, `trigger`, és `workflow` altulajdonságok:

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

Ez a táblázat tartalmazza ezen altulajdonságok további információ:

| Tulajdonság | Típus | Leírás |
|----------|------|-------|
| `actions` | Objektum-gyűjtemény | Eredményobjektumok a műveleteket, amelyeket a kódrészlet futtatása előtt futtatni. Minden objektum rendelkezik egy *kulcs-érték* pár, ahol a kulcs művelet neve, az értéke pedig hívása egyenértékű a [actions() függvény](../logic-apps/workflow-definition-language-functions-reference.md#actions) a `@actions('<action-name>')`. A művelet neve, amely lecseréli a tárolóhelyek alapjául szolgáló munkafolyamat-definíció van használatban ugyanazt a művelet a nevet használja ("") és aláhúzásjeleket (_) a művelet neve. Ez az objektum hozzáférést biztosít a műveleti tulajdonság-érték az aktuális a munkafolyamat-példány futtatása. |
| `trigger` | Object | Eredményobjektum az eseményindító és az azzal egyenértékű a hívása a [trigger() függvény](../logic-apps/workflow-definition-language-functions-reference.md#trigger). Ez az objektum hozzáférést biztosít az eseményindító tulajdonságértékeket az aktuális a munkafolyamat-példány futtatása. |
| `workflow` | Object | A munkafolyamat-objektum és a megfelelő megoldások hívása a [workflow() függvény](../logic-apps/workflow-definition-language-functions-reference.md#workflow). Ez az objektum hozzáférést biztosít a munkafolyamat tulajdonságértékek, például a munkafolyamat nevét, futtatási azonosító és így tovább, az aktuális a munkafolyamat-példány futtatása. |
|||

Ez a témakör a példában a `workflowContext` objektumnak ezeket a tulajdonságokat, amely a programkód miként férhet hozzá:

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

Bizonyos esetekben előfordulhat, hogy rendelkezik, amely kifejezetten a **beágyazott kódot** művelet eredményét, amely a kód hozzáadásával függőségekként hivatkozik. az eseményindító vagy műveleteket tartalmaz a **eseményindító** vagy **Műveletek** paramétereket. Ez a beállítás akkor hasznos, ha a hivatkozott eredmények nem találhatók futási időben forgatókönyvek.

> [!TIP]
> Ha azt tervezi, újból felhasználja a kódot, adja hozzá hivatkozásokat tulajdonságok használatával a **kód** mezőre, hogy a kód a feloldott token hivatkozásokat tartalmaz, nem pedig a trigger és műveletek hozzáadása explicit függőségek.

Tegyük fel például, a kód által hivatkozott rendelkezik a **SelectedOption** eredménye a **jóváhagyó e-mail küldése** művelet az Office 365 Outlook-összekötő. A létrehozás ideje, a Logic Apps-motor elemzi a kódot úgy, hogy hivatkozott bármely trigger vagy művelet eredményeket, és ezeket az eredményeket automatikusan tartalmazza. Futási időben kell hibaüzenetet kap, hogy a hivatkozott eseményindítót vagy műveletet eredménye nem érhető el a megadott `workflowContext` objektumot, egy explicit függőségként hozzáadhat adott eseményindítót vagy műveletet. Ebben a példában adja hozzá a **műveletek** paramétert, és adja meg, amely a **beágyazott kódot** művelet explicit módon eredmény belefoglalása a **jóváhagyó e-mail küldése** művelet.

Ezek a Paraméterek hozzáadásához nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a kívánt paramétereket:

   ![Paraméterek hozzáadása](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Paraméter | Leírás |
   |-----------|-------------|
   | **Műveletek** | A korábbi műveletek eredményeit tartalmazza. Lásd: [műveleti eredmények belefoglalása](#action-results). |
   | **Eseményindító** | Az eseményindító származó eredmények jelenjenek meg. Lásd: [Belefoglalás eseményindító eredmények](#trigger-results). |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Az eseményindító eredmények belefoglalása

Ha **eseményindítók**, hogy eredmények belefoglalása a trigger kéri.

* Az a **eseményindító** listáról válassza ki **Igen**.

<a name="action-results"></a>

### <a name="include-action-results"></a>Műveleti eredmények belefoglalása

Ha **műveletek**, kéri a hozzáadni kívánt műveleteket. Azonban műveletek hozzáadása előtt verziójára van szüksége az alapul szolgáló a logikai alkalmazás munkafolyamat-definíció jelenik meg a művelet neve.

* Ez a funkció nem támogatja a változók, a hurkokat és a iteráció indexeket.

* A logikai alkalmazás munkafolyamat-definíció neveiben aláhúzásjelet (_), nem adható meg.

* A művelet nevét, amely a pont (.) operátort használja a következők ezen operátorok, például:

  `My.Action.Name`

1. A Tervező eszköztárán válassza **Kódnézet**, belül keresőmezőjébe a `actions` attribútuma a művelet neve.

   Ha például `Send_approval_email_` JSON neve a **jóváhagyó e-mail küldése** művelet.

   ![A JSON-ban keresse meg a művelet neve](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. Tervező nézetben, a kód nézet eszköztár való visszatéréshez válassza **Designer**.

1. Az első művelet hozzáadása a **műveletek elem – 1** adja meg a művelet JSON nevét.

   ![Adja meg az első művelet](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Válasszon egy másik művelet hozzáadása, **új elem hozzáadása**.

## <a name="reference"></a>Leírások

További információ a **végrehajtása JavaScript-kódot** művelet szerkezetének és szintaxisának a logikai alkalmazás alapjául szolgáló munkafolyamat-definíció a munkafolyamat-definíciós nyelv használatával, lásd: Ez a művelet [hivatkozhat szakasz ](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code).

## <a name="next-steps"></a>További lépések

Tudjon meg többet [Azure Logic Apps összekötői](../connectors/apis-list.md)

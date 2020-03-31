---
title: Kódrészletek hozzáadása és futtatása szövegközi kód használatával
description: Megtudhatja, hogy miként hozhat létre és futtathat kódrészleteket az Azure Logic Apps alkalmazásokkal létrehozott automatizált feladatokhoz és munkafolyamatokhoz való szövegközi kódműveletek használatával
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: article
ms.date: 05/14/2019
ms.openlocfilehash: f7a134fd026b42d1666b8310b3fb0c10642c7bb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453496"
---
# <a name="add-and-run-code-snippets-by-using-inline-code-in-azure-logic-apps"></a>Kódrészletek hozzáadása és futtatása szövegközi kód használatával az Azure Logic Apps alkalmazásban

Ha egy kóddarabot szeretne futtatni a logikai alkalmazáson belül, hozzáadhatja a beépített **inline kód** műveletet a logikai alkalmazás munkafolyamatának lépéseként. Ez a művelet akkor működik a legjobban, ha az erre a forgatókönyvre megfelelő kódot szeretne futtatni:

* JavaScript-ben fut. Hamarosan több nyelv is elérhető.
* Öt másodperc vagy kevesebb alatt fejeződik be.
* Akár 50 MB méretű adatokat is kezel.
* Nem igényel olyan [ **változókat,** ](../logic-apps/logic-apps-create-variables-store-values.md)amelyek még nem támogatottak.
* A Node.js 8.11.1-es verzióját használja. További információ: [Standard beépített objektumok](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects). 

  > [!NOTE]
  > A `require()` függvényt a **Szövegközi kód** művelet nem támogatja a JavaScript futtatásához.

Ez a művelet futtatja a kódrészletet, és a kódrészlet kimenetét adja vissza **eredmény**nevű tokenként, amelyet a logikai alkalmazásban későbbi műveletekben használhat. Más forgatókönyvek, ahol szeretne létrehozni egy függvényt a kódot, próbálja [meg létrehozni és meghívni egy Azure-függvényt](../logic-apps/logic-apps-azure-functions.md) a logikai alkalmazásban.

Ebben a cikkben a példa logikai alkalmazás akkor aktiválódik, ha egy új e-mail érkezik egy Office 365 Outlook-fiókba. A kódrészlet kibontja és visszaadja az e-mail törzsében megjelenő e-mail címeket.

![Példa – áttekintés](./media/logic-apps-add-run-inline-code/inline-code-example-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazás, ahol szeretné hozzáadni a kódrészletet, beleértve az eseményindítót is. Ha nem rendelkezik logikai alkalmazással, olvassa [el a Rövid útmutató: Az első logikai alkalmazás létrehozása című témakört.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

   A témakörben szereplő példa logikai alkalmazás ezt az Office 365 Outlook-eseményindítót használja: **Amikor új e-mail érkezik**

* A logikai alkalmazáshoz kapcsolódó [integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

  > [!NOTE]
  > Győződjön meg arról, hogy a használati esetnek vagy forgatókönyvnek megfelelő integrációs fiókot használ. Például [a free-tier](../logic-apps/logic-apps-pricing.md#integration-accounts) integrációs fiókok célja például csak a feltáró forgatókönyvek és a számítási feladatok, nem éles forgatókönyvek, korlátozott a használat és az átviteli, és nem támogatja a szolgáltatásiszint-szerződés (SLA). Más szintek költségei merülnek fel, de tartalmazzák az SLA-támogatást, nagyobb átviteli- és magasabb korlátokat kínálnak. További információ az [integrációs fiókszintekről](../logic-apps/logic-apps-pricing.md#integration-accounts), [az árképzésről](https://azure.microsoft.com/pricing/details/logic-apps/)és [a korlátokról.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="add-inline-code"></a>Szövegközi kód hozzáadása

1. Ha még nem, az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designer.

1. A tervezőben adja hozzá a **szövegközi kód** műveletet a logikai alkalmazás munkafolyamatában a kívánt helyen.

   * Ha a műveletet a munkafolyamat végén szeretné hozzáadni, válassza az **Új lépés lehetőséget.**

   * Ha a műveletet a meglévő lépések közé szeretné felvenni, vigye az egérmutatót a lépéseket összekötő nyíl fölé. Válassza ki a**+** pluszjelet ( ), majd a **Művelet hozzáadása**lehetőséget.

   Ez a példa hozzáadja a **Szövegközi kód** műveletet az Office 365 Outlook eseményindítóalatt.

   ![Új lépés hozzáadása](./media/logic-apps-add-run-inline-code/add-new-step.png)

1. A **Művelet kiválasztása csoportkeresőmezőjében**írja be szűrőként a "szövegközi kód" kifejezést. A műveletlistából válassza a következő műveletet: **JavaScript-kód végrehajtása**

   ![Válassza a "JavaScript-kód végrehajtása" lehetőséget](./media/logic-apps-add-run-inline-code/select-inline-code-action.png)

   A művelet megjelenik a tervezőben, és tartalmaz néhány alapértelmezett példakódot, beleértve a visszatérési utasítást.

   ![Szövegközi kód művelet alapértelmezett mintakóddal](./media/logic-apps-add-run-inline-code/inline-code-action-default.png)

1. A **Kód** mezőben törölje a mintakódot, és írja be a futtatni kívánt kódot. Írjon olyan kódot, amelyet egy metódusba helyezne, de a metódus aláírásának megadása nélkül. 

   Ha felismert kulcsszót ír be, megjelenik az automatikus kiegészítési lista, így választhat az elérhető kulcsszavak közül, például:

   ![Kulcsszó automatikus kiegészítési listája](./media/logic-apps-add-run-inline-code/auto-complete.png)

   Ez a példa kódrészlet először létrehoz egy változót, amely egy *reguláris kifejezést*tárol, amely megadja a bemeneti szövegben egyező mintát. A kód ezután létrehoz egy változót, amely tárolja az e-mail törzs adatait az eseményindítóból.

   ![Változók létrehozása](./media/logic-apps-add-run-inline-code/save-email-body-variable.png)

   Az eseményindító és a korábbi műveletek eredményeinek könnyebb hivatkozása érdekében a dinamikus tartalomlista akkor jelenik meg, amikor a kurzor a **Kód** mezőben van. Ebben a példában a lista az eseményindító elérhető eredményeit jeleníti meg, beleértve a **Törzs** jogkivonatot is, amelyet most már kiválaszthat.

   Miután kiválasztotta a Törzsjogkivonatot, a szövegközi `workflowContext` kódművelet feloldja `Body` a jogkivonatot egy olyan objektumhoz, amely az e-mail tulajdonságértékére hivatkozik: **Body**

   ![Eredmény kiválasztása](./media/logic-apps-add-run-inline-code/inline-code-example-select-outputs.png)

   A **Kód** mezőben a kódrészlet használhatja `workflowContext` az írásvédett objektumot bemenetként. Ez az objektum olyan altulajdonságokkal rendelkezik, amelyek hozzáférést biztosítanak a kódnak az eseményindító és a munkafolyamat korábbi műveletei eredményeihez.
   További információt a témakör későbbi részében talál: [Hivatkozási eseményindító és műveleteredmények a kódban](#workflowcontext).

   > [!NOTE]
   >
   > Ha a kódrészlet a pont (.) operátort használó műveletnevekre hivatkozik, akkor ezeket a műveletneveket hozzá kell [ **adnia** ](#add-parameters)a Műveletek paraméterhez. Ezeknek a hivatkozásoknak a műveletneveket szögletes zárójelekkel ([]) és idézőjelekkel kell elfoglalniuk, például:
   >
   > `// Correct`</br> 
   > `workflowContext.actions["my.action.name"].body`</br>
   >
   > `// Incorrect`</br>
   > `workflowContext.actions.my.action.name.body`

   A szövegközi kód művelet nem `return` igényel utasítást, `return` de az eredmények egy utasítás érhető el a későbbi műveletek az **eredmény** jogkivonaton keresztül. 
   Például a kódrészlet az eredményt adja `match()` vissza a függvény hívásával, amely az e-mail törzsében a reguláris kifejezéssel megegyezik. A **Compose** művelet az **Eredmény** jogkivonat segítségével hivatkozik a szövegközi kód művelet eredményeire, és egyetlen eredményt hoz létre.

   ![Befejezett logikai alkalmazás](./media/logic-apps-add-run-inline-code/inline-code-complete-example.png)

1. Ha elkészült, mentse a logikai alkalmazást.

<a name="workflowcontext"></a>

### <a name="reference-trigger-and-action-results-in-your-code"></a>Hivatkozási eseményindító és művelet eredmények a kódban

Az `workflowContext` objektum nak ez a `actions` `trigger`szerkezete `workflow` van, amely magában foglalja a , és altulajdonságokat:

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

Ez a táblázat további információkat tartalmaz az alábbi altulajdonságokról:

| Tulajdonság | Típus | Leírás |
|----------|------|-------|
| `actions` | Objektumgyűjtemény | A kódkódrészlet futtatása előtt futó műveletek ből származó objektumok eredményobjektumai. Minden objektumnak van egy *kulcs-érték* párja, ahol a kulcs egy művelet neve, és `@actions('<action-name>')`az érték egyenértékű a [actions() függvény](../logic-apps/workflow-definition-language-functions-reference.md#actions) hívásával. A művelet neve ugyanazt a műveletnevet használja, mint az alapul szolgáló munkafolyamat-definícióban, amely a művelet nevében lévő szóközöket (" ") aláhúzásjelekre (_) helyettesíti. Ez az objektum hozzáférést biztosít a művelettulajdonság-értékekhez az aktuális munkafolyamat-példány futtatásakor. |
| `trigger` | Objektum | Eredményobjektum az eseményindítóból, és egyenértékű az [eseményindító() függvény hívásával.](../logic-apps/workflow-definition-language-functions-reference.md#trigger) Ez az objektum hozzáférést biztosít az aktuális munkafolyamat-példány futtatásából származó eseményindító tulajdonságértékekhez. |
| `workflow` | Objektum | A munkafolyamat-objektum és a [workflow() függvény](../logic-apps/workflow-definition-language-functions-reference.md#workflow)hívásával egyenértékű. Ez az objektum hozzáférést biztosít a munkafolyamat-tulajdonságértékekhez, például a munkafolyamat nevéhez, a futtatási azonosítóhoz és így tovább az aktuális munkafolyamat-példány futtatása kortól. |
|||

Ebben a témakörben az `workflowContext` objektum a következő tulajdonságokkal rendelkezik, amelyekhez a kód hozzáférhet:

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

Bizonyos esetekben előfordulhat, hogy explicit módon meg kell követelnie, hogy a **szövegközi kód** művelet tartalmazza az eseményindító vagy a kód által függőségként hivatkozott konkrét műveletek eredményeit az **Eseményindító** vagy a **Műveletek** paraméterek hozzáadásával. Ez a beállítás olyan esetekben hasznos, ahol a hivatkozott eredmények nem találhatók futási időben.

> [!TIP]
> Ha azt tervezi, hogy újra felhasználja a kódot, adjon hozzá hivatkozásokat a tulajdonságokra a **Kód** mező használatával, hogy a kód tartalmazza a feloldott jogkivonat-hivatkozásokat, ahelyett, hogy az eseményindítót vagy a műveleteket explicit függőségként adná hozzá.

Tegyük fel például, hogy olyan kóddal rendelkezik, amely a **SelectedOption** eredményre hivatkozik az Office 365 Outlook-összekötő **jóváhagyási e-mail küldése** műveletéből. Létrehozáskor a Logic Apps motor elemzi a kódot annak megállapítására, hogy hivatkozott-e eseményindítók vagy műveleteredményekre, és automatikusan tartalmazza ezeket az eredményeket. Futási időben, ha olyan hibaüzenetet kap, hogy a hivatkozott eseményindító `workflowContext` vagy művelet eredménye nem érhető el a megadott objektumban, hozzáadhatja az eseményindítót vagy műveletet explicit függőségként. Ebben a példában adja hozzá a **Műveletek paramétert,** és adja meg, hogy a **szövegközi kód** művelet kifejezetten tartalmazza a **jóváhagyási e-mail küldése** művelet eredményét.

A paraméterek hozzáadásához nyissa meg az **Új paraméter hozzáadása** listát, és válassza ki a kívánt paramétereket:

   ![Paraméterek hozzáadása](./media/logic-apps-add-run-inline-code/inline-code-action-add-parameters.png)

   | Paraméter | Leírás |
   |-----------|-------------|
   | **Műveletek** | A korábbi műveletek eredményeinek felvétele. Lásd: [Műveleteredmények belefoglalása.](#action-results) |
   | **Eseményindító** | Az eseményindító ból származó eredmények belefoglalása. Lásd: [Eseményindítók eredményeinek belefoglalása.](#trigger-results) |
   |||

<a name="trigger-results"></a>

### <a name="include-trigger-results"></a>Eseményindító-eredmények belefoglalása

Ha az **Eseményindítók lehetőséget választja,** a program megkérdezi, hogy szerepeljen-e az eseményindítók eredményei.

* Az **Eseményindító** listában válassza az **Igen**lehetőséget.

<a name="action-results"></a>

### <a name="include-action-results"></a>Műveleteredmények belefoglalása

Ha a Műveletek lehetőséget **választja,** a program kéri a hozzáadni kívánt műveleteket. A műveletek hozzáadása előtt azonban szüksége van a műveletnevének a logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójában megjelenő verziójára.

* Ez a funkció nem támogatja a változókat, hurkokat és iterációs indexeket.

* A logikai alkalmazás munkafolyamat-definíciójában szereplő nevek aláhúzást (_) használnak, nem szóközt.

* A pontoperátort (.) használó műveletnevekhez az operátorok szerepelnek, például:

  `My.Action.Name`

1. A tervező eszköztárán válassza a **Kód** `actions` nézet lehetőséget, és keressen az attribútumon belül a művelet nevére.

   Például `Send_approval_email_` a JSON neve a **Send jóváhagyási e-mail** művelet.

   ![Műveletnév keresése a JSON-ban](./media/logic-apps-add-run-inline-code/find-action-name-json.png)

1. A tervezőnézethez való visszatéréshez kattintson a kódnézet eszköztárán a **Tervező gombra.**

1. Az első művelet hozzáadásához írja be a művelet JSON-nevét a **Műveletek elem – 1** mezőbe.

   ![Első művelet megadása](./media/logic-apps-add-run-inline-code/add-action-parameter.png)

1. Másik művelet hozzáadásához válassza **az Új elem hozzáadása**lehetőséget.

## <a name="reference"></a>Referencia

A **JavaScript-kód végrehajtása** művelet szerkezetéről és szintaxisáról a logikai alkalmazás alapjául szolgáló munkafolyamat-definícióban a Munkafolyamat-definíció nyelvhasználatával további információt ebben a [műveletben talál.](../logic-apps/logic-apps-workflow-actions-triggers.md#run-javascript-code)

## <a name="next-steps"></a>További lépések

További információ [az Azure Logic Apps összekötőiről](../connectors/apis-list.md)

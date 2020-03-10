---
title: Hurkok hozzáadása az Ismétlési műveletekhez
description: A munkafolyamat-műveleteket ismétlődő vagy a Azure Logic Appsban feldolgozó hurkok létrehozása
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358983"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>A munkafolyamat-műveleteket ismétlődő vagy a Azure Logic Appsban feldolgozó hurkok létrehozása

Egy tömb a logikai alkalmazásban való feldolgozásához létrehozhat egy ["foreach" ciklust](#foreach-loop). Ez a hurok megismétli a tömb egyes elemeinek egy vagy több műveletét. A "foreach" hurkok által feldolgozható tömbök számának korlátozásai: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md). 

Ha meg szeretné ismételni a műveleteket, amíg egy feltétel nem teljesül, vagy egy állapot változik, létrehozhat egy ["ig" ciklust](#until-loop). A logikai alkalmazás először a hurokon belüli összes műveletet futtatja, majd ellenőrzi a feltételt vagy az állapotot. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok megismétlődik. A logikai alkalmazások futtatásához tartozó "ig" ciklusok számának korlátozásai: [korlátok és konfiguráció](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Ha olyan triggerrel rendelkezik, amely egy tömböt kap, és minden tömb elemhez szeretne futtatni egy munkafolyamatot, akkor a tömböt a [ **SplitOn** trigger tulajdonsággal](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) *végezheti el.* 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" hurok

A "foreach hurok" megismétli egy vagy több műveletet minden tömb elemnél, és csak tömbökön működik. A "foreach" ciklusban lévő iterációk párhuzamosan futnak. Egy [szekvenciális "foreach" hurok](#sequential-foreach-loop)beállításával azonban egyszerre is futtathat iterációkat. 

Az "foreach" hurkok használatakor a következő szempontokat érdemes figyelembe venni:

* Beágyazott hurkok esetén az ismétlések mindig szekvenciálisan futnak, nem párhuzamosan. Ha párhuzamosan szeretné futtatni a műveleteket egy beágyazott hurok elemein, hozzon létre és [hívjon egy alárendelt logikai alkalmazást](../logic-apps/logic-apps-http-endpoint.md).

* Ha az egyes hurok-iterációk esetében kiszámítható eredményt szeretne kapni a változók műveleteitől, futtassa ezeket a hurkokat egymás után. Ha például egy párhuzamosan futó hurok véget ér, a növekmény, a csökkentés és a Hozzáfűzés változó művelet előre jelezhető eredményeket ad vissza. Ugyanakkor a párhuzamosan futó hurokban az egyes ismétlések során ezek a műveletek kiszámíthatatlan eredményeket adhatnak vissza. 

* A "foreach" ciklusban végrehajtott műveletek a [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) használják 
a tömb egyes elemeinek hivatkozására és feldolgozására szolgáló kifejezés. Ha olyan adathalmazt ad meg, amely nem egy tömbben található, akkor a logikai alkalmazás munkafolyamata sikertelen lesz. 

Ez a példa a logikai alkalmazás egy webhely RSS-hírcsatornájának napi összefoglalóját küldi el. Az alkalmazás egy "foreach" hurkot használ, amely minden új elemről e-mailt küld.

1. [Hozza létre ezt a minta logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy Outlook.com vagy Office 365 Outlook-fiókkal.

2. Az RSS-trigger és az e-mail küldése művelet között adjon hozzá egy "foreach" ciklust. 

   1. Ha a lépések között hurkot szeretne hozzáadni, vigye a mutatót a lépések közötti nyíl fölé. 
   Válassza ki a megjelenő **pluszjelet** ( **+** ), majd válassza a **művelet hozzáadása**lehetőséget.

      ![Válassza a "művelet hozzáadása" lehetőséget.](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. A keresőmező alatt válassza az **összes**lehetőséget. A keresőmezőbe írja be a "for each" kifejezést a szűrőként. A műveletek listából válassza ki ezt a műveletet: **az egyes vezérlőknél**

      !["Az egyes ciklusok" hozzáadása](media/logic-apps-control-flow-loops/select-for-each.png)

3. Most hozza létre a hurkot. A **dinamikus tartalom hozzáadása** **lehetőségre kattintva válassza ki a kimenet az előző lépésekből** lehetőséget, majd válassza ki az RSS-triggerből kimenetet a **hírcsatorna-hivatkozások** tömbben. 

   ![Kiválasztás a dinamikus tartalmak listájából](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Az előző lépésben *csak* a tömb kimenetei választhatók ki.

   A kiválasztott tömb most itt jelenik meg:

   ![Tömb kiválasztása](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Ha műveletet szeretne futtatni minden egyes tömb elemen, húzza az **E-mail küldése** műveletet a hurokba. 

   A logikai alkalmazás a következő példához hasonlóan néz ki:

   ![Lépések hozzáadása a "foreach" hurokhoz](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Mentse a logikai alkalmazást. A logikai alkalmazás manuális teszteléséhez a tervező eszköztárán válassza a **Futtatás**lehetőséget.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" hurok definíciója (JSON)

Ha a logikai alkalmazáshoz kód nézetben dolgozik, a logikai alkalmazás JSON-definíciójában megadhatja a `Foreach` hurkot, például:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" hurok: szekvenciális

Alapértelmezés szerint a "foreach" hurokban lévő ciklusok párhuzamosan futnak. Az egyes ciklusok egymás utáni futtatásához állítsa be a hurok **szekvenciális** beállítását. A "foreach" hurkoknak szekvenciálisan kell futniuk, ha beágyazott hurkok vagy változók vannak a hurokban, ahol előre jelezhető eredmények várhatók. 

1. A hurok jobb felső sarkában válassza az **ellipszisek** ( **...** ) > a **Beállítások**lehetőséget.

   ![A "foreach" ciklusban válassza a "..." lehetőséget. > "Beállítások"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. A **Egyidejűség vezérlőelem**alatt kapcsolja be a **Egyidejűség vezérlőelem** beállítást a **be**értékre. Helyezze át a **párhuzamossági** csúszka **1**értékre, majd válassza a **kész**lehetőséget.

   ![Egyidejűség-vezérlés bekapcsolása](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Ha a logikai alkalmazás JSON-definícióját használja, a `Sequential` lehetőséget használhatja a `operationOptions` paraméter hozzáadásával, például:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Ig" ciklus
  
A műveletek futtatásához és ismétléséhez, amíg egy feltétel nem teljesül, vagy nem változik az állapot, a műveleteket "ig" ciklusban kell elhelyezni. A logikai alkalmazás először a hurokban lévő összes műveletet futtatja, majd ellenőrzi a feltételt vagy az állapotot. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok megismétlődik.

Íme néhány gyakori forgatókönyv, ahol "ig" ciklust használhat:

* Hívja meg a végpontot, amíg meg nem kapja a kívánt választ.

* Hozzon létre egy rekordot egy adatbázisban. Várjon, amíg a rekordban egy adott mező jóváhagyva lesz. Folytassa a feldolgozást. 

Ez a példa minden nap 8:00-kor kezdődik, így a logikai alkalmazás egy változót ér el, amíg a változó értéke 10. A logikai alkalmazás ezt követően egy e-mailt küld, amely megerősíti az aktuális értéket. 

> [!NOTE]
> Ezek a lépések az Office 365 Outlookot használják, de a Logic Apps által támogatott bármely e-mail-szolgáltatót használhat. 
> [Itt tekintse meg az összekötők listáját](https://docs.microsoft.com/connectors/). Ha más e-mail-fiókot használ, az általános lépések ugyanazok maradnak, de a felhasználói felület némileg eltérő lehet. 

1. Üres logikai alkalmazás létrehozása. A Logic app Designerben a keresőmező alatt válassza az **összes**lehetőséget. Keressen rá az "Ismétlődés" kifejezésre. 
   Az eseményindítók listából válassza ki a következő eseményindítót: **Ismétlődés – ütemezése**

   !["Ismétlődés ütemezése" eseményindító hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Itt adhatja meg, hogy mikor induljon el az eseményindító a nap intervallumának, gyakoriságának és órájának beállításával. Az óra beállításához válassza a **Speciális beállítások megjelenítése lehetőséget**.

   ![Ismétlődési ütemterv beállítása](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Tulajdonság | Érték |
   | -------- | ----- |
   | **Intervallum** | 1 | 
   | **Gyakoriság** | Day |
   | **Ezekben az órákban** | 8 |
   ||| 

1. Az trigger alatt válassza az **új lépés**lehetőséget. 
   Keressen rá a "változók" kifejezésre, és válassza a következő műveletet: **változó inicializálása – változók**

   !["Változó inicializálása – változók" művelet hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Állítsa be a változót a következő értékekkel:

   ![Változó tulajdonságainak beállítása](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Tulajdonság | Érték | Leírás |
   | -------- | ----- | ----------- |
   | **Name (Név)** | Korlát | A változó neve | 
   | **Típus** | Egész szám | Változó adattípusa | 
   | **Érték** | 0 | A változó kezdő értéke | 
   |||| 

1. A **változó inicializálása** művelet alatt válassza az **új lépés**lehetőséget. 

1. A keresőmező alatt válassza az **összes**lehetőséget. Keressen rá a "ig" kifejezésre, és válassza ki a következő műveletet: **ig-Control**

   !["Ig" hurok hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. A hurok kilépési feltételének kiépítéséhez válassza a **limit** változót, az **pedig egyenlő** operátort. 
   Összehasonlítási értékként adja meg a **10-es** értéket.

   ![Build kilépési feltétele a hurok leállításához](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. A hurokon belül válassza a **művelet hozzáadása**lehetőséget. 

1. A keresőmező alatt válassza az **összes**lehetőséget. Keressen rá a "változók" kifejezésre, és válassza a következő műveletet: **változó növekménye – változók**

   ![Művelet hozzáadása a változó növeléséhez](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. A **név**mezőben válassza a **határérték** változót. Az **érték**mezőben adja meg az "1" értéket. 

     ![Növelje a "Limit" értéket 1-re](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Válassza az **új lépés**lehetőséget a hurokon kívül és alatt. 

1. A keresőmező alatt válassza az **összes**lehetőséget. 
     Keressen és adjon hozzá egy olyan műveletet, amely e-mailt küld, például: 

     ![E-mail küldésére szolgáló művelet hozzáadása](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába.

1. Adja meg az e-mail művelet tulajdonságait. Adja hozzá a **limit** változót a tárgyhoz. Így ellenőrizheti, hogy a változó aktuális értéke megfelel-e a megadott feltételnek, például:

      ![E-mail-tulajdonságok beállítása](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Tulajdonság | Érték | Leírás |
      | -------- | ----- | ----------- | 
      | **Címzett** | *\<e-mail-cím\@tartomány >* | A címzett e-mail-címe. Teszteléshez használja a saját e-mail-címét. | 
      | **Tárgy** | A "Limit" jelenlegi értéke **limit** | Határozza meg az e-mail tárgyát. Ebben a példában ellenőrizze, hogy tartalmazza-e a **határérték** változót. | 
      | **Törzs** | <*e-mail-tartalom*> | Itt adhatja meg a küldeni kívánt e-mail üzenet tartalmát. Ebben a példában írjon be egy tetszőleges szöveget. | 
      |||| 

1. Mentse a logikai alkalmazást. A logikai alkalmazás manuális teszteléséhez a tervező eszköztárán válassza a **Futtatás**lehetőséget.

      A logikájának futtatása után egy e-mailt kap a megadott tartalommal:

      ![Fogadott e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Végtelen hurkok megakadályozása

A "ig" ciklusban az alapértelmezett korlátok a végrehajtás leállítása, ha bármelyik feltétel teljesül:

| Tulajdonság | Alapértelmezett érték | Leírás | 
| -------- | ------------- | ----------- | 
| **Száma** | 60 | A hurok bezárása előtt futó hurkok maximális száma. Az alapértelmezett érték 60 ciklus. | 
| **Időtúllépés** | PT1H | A huroknak a hurokból való kilépése előtti futtatásának legnagyobb ideje. Az alapértelmezett érték egy óra, és ISO 8601 formátumban van megadva. <p>Az időtúllépési érték kiértékelése minden hurok ciklusakor megtörténik. Ha a hurok bármelyik művelete hosszabb időt vesz igénybe, mint az időkorlát, az aktuális ciklus nem áll le. A következő ciklus azonban nem indul el, mert a korlátozási feltétel nem teljesül. | 
|||| 

Az alapértelmezett korlátok módosításához a hurok művelet alakzatban válassza a **Speciális beállítások megjelenítése lehetőséget** .

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Amíg" definíció (JSON)

Ha a logikai alkalmazáshoz kód nézetben dolgozik, a logikai alkalmazás JSON-definíciójában megadhat egy `Until` hurkot, például:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

Ebben a példában a "ig" ciklus egy olyan HTTP-végpontot hív meg, amely létrehoz egy erőforrást. A hurok leáll, amikor a HTTP-válasz törzse `Completed` állapottal tér vissza. A végtelen hurkok megelőzése érdekében a hurok akkor is leáll, ha a következő feltételek bármelyike megtörténik:

* A hurok a `count` attribútum által megadott 10 alkalommal futott. Az alapértelmezett érték 60 alkalommal. 

* A hurok a `timeout` attribútum által az ISO 8601 formátumban megadott két órán keresztül futott. Az alapértelmezett érték egy óra.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A szolgáltatásokról és javaslatokról [Azure Logic apps felhasználói visszajelzési webhelyről](https://aka.ms/logicapps-wish)küldhet vagy szavazhat.

## <a name="next-steps"></a>Következő lépések

* [Lépések futtatása feltételek alapján (feltételes utasítások)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (switch utasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveleti állapot alapján (hatókörök)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

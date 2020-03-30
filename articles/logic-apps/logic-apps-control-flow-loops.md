---
title: Hurkok hozzáadása a műveletek megismétléséhez
description: Munkafolyamatok tevékenységeit ismétlő vagy tömböket feldolgozó hurkok létrehozása az Azure Logic Appsben
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: 5f6c04c9a57dc8c250d99f2fa944203d2d73c404
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270575"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Munkafolyamatok tevékenységeit ismétlő vagy tömböket feldolgozó hurkok létrehozása az Azure Logic Appsben

Tömb feldolgozásához a logikai alkalmazásban létrehozhat egy ["Foreach" hurkot.](#foreach-loop) Ez a ciklus a tömb minden elemén egy vagy több műveletet ismétel. A "Foreach" hurkok által feldolgozható tömbelemek számának korlátozásáról a Korlátok és konfiguráció című témakörben [van.](../logic-apps/logic-apps-limits-and-config.md) 

Ha addig szeretné ismételni a műveleteket, amíg egy feltétel nem teljesül, vagy az állapot nem változik, létrehozhat egy ["Amíg" ciklust.](#until-loop) A logikai alkalmazás először futtatja a cikluson belüli összes műveletet, majd ellenőrzi a feltételt vagy állapotot. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok ismétlődik. A logikai alkalmazások futtatásához használt "Until" hurkok számának korlátozását a Korlátok és konfiguráció című témakörben [tésszet.](../logic-apps/logic-apps-limits-and-config.md) 

> [!TIP]
> Ha olyan eseményindítóval rendelkezik, amely tömböt fogad, és munkafolyamatot szeretne futtatni minden tömbelemhez, akkor a [ **SplitOn** eseményindító tulajdonsággal](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) *felbonthatja* a tömböt. 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" hurok

A "Foreach loop" minden tömbelemen egy vagy több műveletet ismétel meg, és csak tömbökön működik. A "Foreach" ciklus ismétlései párhuzamosan futnak. Az ismétléseket azonban egyenként is futtathatja egy [egymást követő "Foreach" hurok](#sequential-foreach-loop)beállításával. 

A "Foreach" hurkok használatakor az alábbiakat kell figyelembe venni:

* Egymásba ágyazott hurkokban az ismétlések mindig egymás után futnak, nem párhuzamosan. Ha egymás után futtatja a műveleteket a beágyazott cikluselemeihez, hozzon létre és [hívjon meg egy gyermeklogikai alkalmazást.](../logic-apps/logic-apps-http-endpoint.md)

* A változókon végzett műveletek ből származó kiszámítható eredmények lekérése az egyes ciklusismétlések során, futtassa ezeket a hurkokat egymás után. Ha például egy egyidejűleg futó ciklus véget ér, a növekmény, a decrement és a hozzáfűzés változó műveletek et ad vissza kiszámítható eredményeket. Azonban az egyidejűleg futó ciklus minden egyes ismétlése során ezek a műveletek előre nem látható eredményeket adhatnak vissza. 

* A "Foreach" ciklusban végrehajtott műveletek a[`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
kifejezéssel hivatkozunk és feldolgozza a tömb minden elemét. Ha olyan adatokat ad meg, amelyek nem tömbben vannak, a logikai alkalmazás munkafolyamata sikertelen lesz. 

Ez a példa logikai alkalmazás napi összefoglalót küld egy webhely RSS-hírcsatornájához. Az alkalmazás egy "Foreach" hurkot használ, amely minden új elemhez küld egy e-mailt.

1. [Hozza létre ezt a mintalogikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md) egy Outlook.com vagy Office 365 Outlook-fiókkal.

2. Között az RSS eseményindító és küldjön e-mail művelet, adjunk hozzá egy "Foreach" hurok. 

   1. Ha hurkot szeretne hozzáadni a lépések közé, vigye az egérmutatót a lépések közötti nyíl fölé. 
   Válassza ki a**+** megjelenő **pluszjelet** ( ), majd a **Művelet hozzáadása**lehetőséget.

      ![Válassza a "Művelet hozzáadása" lehetőséget.](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. A keresőmező alatt válassza az **Összes**lehetőséget. A keresőmezőbe írja be szűrőként a "mindenkihez" kifejezést. A műveletek listájában válassza ki ezt a műveletet: **Minden - Control**

      ![Add hozzá a "Minden" ciklus](media/logic-apps-control-flow-loops/select-for-each.png)

3. Most építsd meg a hurkot. A Dinamikus **tartalom hozzáadása** lista megjelenítése után válassza ki a **Hírcsatorna-csatolási csatolások** tömböt, amely az RSS-eseményindítóból kimenete, válassza ki a Kimenet lehetőséget az **előző lépésekből.** 

   ![Kijelölés a dinamikus tartalomlistából](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Az előző lépésből *csak* tömbkimeneteket választhat ki.

   A kijelölt tömb most itt jelenik meg:

   ![Tömb kijelölése](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Ha minden tömbelemen szeretne műveletet futtatni, húzza az **E-mail küldése** műveletet a hurokba. 

   A logikai alkalmazás a következő példához hasonló lehet:

   ![Lépések hozzáadása a "Foreach" ciklushoz](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Mentse a logikai alkalmazást. A logikai alkalmazás manuális teszteléséhez a tervező eszköztárán válassza a **Futtatás lehetőséget.**

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" hurok definíció (JSON)

Ha a logikai alkalmazás kódnézetében dolgozik, `Foreach` a ciklust a logikai alkalmazás JSON-definíciójában adhatja meg, például:

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

## <a name="foreach-loop-sequential"></a>"Foreach" hurok: Szekvenciális

Alapértelmezés szerint a ciklusok egy "Foreach" cikluspárhuzamosan futnak. Az egyes ciklusok egymás utáni futtatásához állítsa be a ciklus **szekvenciális** beállítását. A "Foreach" hurkoknak egymás után kell futniuk, ha egymásba ágyazott hurkok vagy változók vannak a hurkokba, ahol kiszámítható eredményeket vár. 

1. A hurok jobb felső sarkában válassza a **három pontot** (**...**) > Beállítások ( Beállítások ) **lehetőséget.**

   ![A "Foreach" ciklusban válassza a "..." > a "Beállítások" lehetőséget.](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Az **Egyidejűség-vezérlés csoportban**kapcsolja be az **Egyidejűség-vezérlés** beállítást Be ( Egyeztetésvezérlés ) **beállításra.** Mozgassa a **párhuzamosság mértéke** csúszkát **1-re,** és válassza a Kész **gombot.**

   ![Egyidejűség-vezérlés bekapcsolása](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Ha a logikai alkalmazás JSON-definíciójával dolgozik, `Sequential` a paraméter `operationOptions` hozzáadásával használhatja a lehetőséget, például:

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

## <a name="until-loop"></a>"Amíg" hurok
  
Ha addig szeretné futtatni és ismételni a műveleteket, amíg egy feltétel nem teljesül, vagy egy állapot nem változik, tegye ezeket a műveleteket egy "Amíg" ciklusba. A logikai alkalmazás először futtatja a cikluson belüli összes műveletet, majd ellenőrzi a feltételt vagy állapotot. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok ismétlődik.

Íme néhány gyakori forgatókönyv, ahol használhatja a "Until" ciklust:

* Hívjon egy végpontot, amíg meg nem kapja a kívánt választ.

* Rekord létrehozása adatbázisban. Várjon, amíg a rekord egy adott mezőjét jóvá nem hagyják. Folytassa a feldolgozást. 

Kezdve 8:00 minden nap, ez a példa logikai alkalmazás növekszik a változó, amíg a változó értéke 10. A logikai alkalmazás ezután küld egy e-mailt, amely megerősíti az aktuális értéket. 

> [!NOTE]
> Ezek a lépések az Office 365 Outlook ot használják, de a Logic Apps által támogatott bármely e-mail szolgáltatót használhatja. 
> [Ellenőrizze az összekötők listáját itt](https://docs.microsoft.com/connectors/). Ha másik e-mail fiókot használ, az általános lépések változatlanok maradnak, de a felhasználói felület kissé eltérő lehet. 

1. Üres logikai alkalmazás létrehozása. A Logic App Designer keresőmezőjében válassza az **Összes**lehetőséget. Keressen rá az "ismétlődés" kifejezésre. 
   Az eseményindítók listájából válassza ezt az eseményindítót: **Ismétlődés - Ütemezés**

   !["Ismétlődés - Ütemezés" eseményindító hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Adja meg, hogy az eseményindító mikor aktiválódik a nap intervallumának, gyakoriságának és órájának beállításával. Az óra beállításához válassza a **Speciális beállítások megjelenítése lehetőséget.**

   ![Ismétlődési ütemezés beállítása](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Tulajdonság | Érték |
   | -------- | ----- |
   | **Intervallum** | 1 | 
   | **Frekvencia** | Day |
   | **Ezekben az órákban** | 8 |
   ||| 

1. Az eseményindító alatt válassza az **Új lépés lehetőséget.** 
   Keressen rá a "változók" kifejezésre, és válassza a következő műveletet: **Változóink inicializálása - Változók**

   !["Változóinicionica - Változók inicializálása" művelet hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Állítsa be a változót a következő értékekkel:

   ![Változó tulajdonságainak beállítása](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Tulajdonság | Érték | Leírás |
   | -------- | ----- | ----------- |
   | **Név** | Korlát | A változó neve | 
   | **Típus** | Egész szám | A változó adattípusa | 
   | **Érték** | 0 | A változó kezdő értéke | 
   |||| 

1. Az **Inicializálás változó** művelet csoportban válassza az **Új lépés lehetőséget.** 

1. A keresőmező alatt válassza az **Összes**lehetőséget. Keressen rá a "until" kifejezésre, és válassza ezt a műveletet: **Amíg - Control**

   ![Add hozzá a "Until" ciklust](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. A ciklus kilépési feltételének létrehozása a **Limit** változó kiválasztásával, és **az egyenlő** operátor. 
   Adja meg a **10-et** összehasonlítási értékként.

   ![Kilépési feltétel összeállítása a leállítási hurokhoz](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. A cikluson belül válassza a **Művelet hozzáadása**lehetőséget. 

1. A keresőmező alatt válassza az **Összes**lehetőséget. Keressen rá a "változók" kifejezésre, és válassza a következő műveletet: **Növekmény változó - Változók**

   ![Művelet hozzáadása a növekményváltozóhoz](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. A **Név csoportban**válassza a **Korlát** változót. Az **Érték**mezőbe írja be az "1" értéket. 

     ![A "Limit" növekménye 1-el](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Kívül és a hurok alatt válassza az **Új lépés lehetőséget.** 

1. A keresőmező alatt válassza az **Összes**lehetőséget. 
     Az e-maileket küldő művelet megkeresése és hozzáadása, például: 

     ![E-mailt küldő művelet hozzáadása](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába.

1. Állítsa be az e-mail művelet tulajdonságait. Adja hozzá a **Korlát** változót a tárgyhoz. Így ellenőrizheti, hogy a változó aktuális értéke megfelel-e a megadott feltételnek, például:

      ![E-mail tulajdonságainak beállítása](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Tulajdonság | Érték | Leírás |
      | -------- | ----- | ----------- | 
      | **Címzett** | *\<e-mail-cím\@tartomány>* | A címzett e-mail címe. A teszteléshez használja a saját e-mail címét. | 
      | **Tárgy** | A "Limit" aktuális **értéke: Limit** | Adja meg az e-mail tárgyát. Ebben a példában győződjön meg arról, hogy a **Korlát változót** is tartalmazza. | 
      | **Törzs** | <*e-mail-tartalom*> | Adja meg az elküldeni kívánt e-mail-tartalmat. Ebben a példában adja meg a kívánt szöveget. | 
      |||| 

1. Mentse a logikai alkalmazást. A logikai alkalmazás manuális teszteléséhez a tervező eszköztárán válassza a **Futtatás lehetőséget.**

      Miután a logika futásba kezdett, e-mailt kap a megadott tartalommal:

      ![Fogadott e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Végtelen hurkok megakadályozása

A "Until" ciklus alapértelmezett korlátokkal rendelkezik, amelyek leállítják a végrehajtást, ha a feltételek bármelyike megtörténik:

| Tulajdonság | Alapértelmezett érték | Leírás | 
| -------- | ------------- | ----------- | 
| **Darabszám** | 60 | A ciklus kilépése előtt futó hurkok legnagyobb száma. Az alapértelmezett érték 60 ciklus. | 
| **Időtúllépés** | PT1H | A ciklus kilépése előtt a legtöbb ideig futtasson egy hurkot. Az alapértelmezett érték egy óra, és ISO 8601 formátumban van megadva. <p>Az időtúlérték kiértékelése minden ciklusban. Ha a ciklus bármely művelete hosszabb időt vesz igénybe, mint az időkorlát, az aktuális ciklus nem áll le. Azonban a következő ciklus nem indul el, mert a korlát feltétel nem teljesül. | 
|||| 

Az alapértelmezett korlátok módosításához válassza a **Speciális beállítások megjelenítése** a ciklusművelet alakzatban lehetőséget.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Ig" meghatározás (JSON)

Ha a logikai alkalmazás kódnézetében dolgozik, `Until` definiálhat egy hurkot a logikai alkalmazás JSON-definíciójában, például:

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

Ez a példa "Until" hurok http-végpontot hív meg, amely létrehoz egy erőforrást. A ciklus leáll, amikor `Completed` a HTTP-választörzs állapottal tér vissza. A végtelen hurkok megelőzése érdekében a hurok is leáll, ha a feltételek bármelyike megtörténik:

* A hurok az `count` attribútum által megadott 10-szer futott. Az alapértelmezett érték 60-szor. 

* A hurok két órán át futott `timeout` az ISO 8601 formátumban megadott attribútum szerint. Az alapértelmezett érték egy óra.
  
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
* A funkciók és javaslatok elküldéséhez vagy szavazásához az [Azure Logic Apps felhasználói visszajelzési webhelyén.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>További lépések

* [Feltételek (feltételes utasítások) alapján lépések futtatása](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (kapcsolóutasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveletállapot (hatókörök) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

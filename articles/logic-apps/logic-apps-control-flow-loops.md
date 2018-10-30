---
title: Adja hozzá a hurkok, ismételje meg a műveletet, vagy feldolgozni tömbök – Azure Logic Apps |} A Microsoft Docs
description: Hurkok, ismételje meg a munkafolyamat-műveletek, vagy az Azure Logic Appsben tömbök feldolgozni létrehozása
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 5ba5e5abef4ebdc58c44cbe7f5ba584efe8abfc7
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233106"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Hurkok, ismételje meg a munkafolyamat-műveletek, vagy az Azure Logic Appsben tömbök feldolgozni létrehozása

A logikai alkalmazás a tömb végigléptetni, használhatja a ["Foreach" ciklus](#foreach-loop) vagy egy ["Foreach" szekvenciális hurok](#sequential-foreach-loop). Az ismétlések egy standard szintű "Foreach" ciklus párhuzamosan is futtatni, a "Foreach" szekvenciális hurok ismétlések egyenként futtatása közben. A "Foreach" hurkok feldolgozására képes futtatni egyetlen logikai alkalmazás a tömb elemek maximális számát, lásd: [korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Ha rendelkezik egy eseményindítót, amely egy tömböt kap, és szeretne futtatni egy munkafolyamatot a tömb mindegyik elemén, *debatch* a tömböt a [ **SplitOn** tulajdonság-trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Ismételje meg a műveleteket, amíg egy feltétel teljesül, vagy bizonyos állapota megváltozott, használja az ["Until" ciklus](#until-loop). A logikai alkalmazás először hajt végre a hurok belüli összes műveletet, és azután ellenőrzi a feltételt, utolsó lépésként. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok ismétlődik. Hurkok egyetlen logikai alkalmazás futtatásának, lásd: "Until" maximális száma [korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" ciklus

Ismételje meg a műveletet egy tömb minden egyes elemén, használja a logikai alkalmazás munkafolyamatának a "Foreach" hurkot. A "Foreach" ciklus több művelet is felvehet, és beágyazhatja a "Foreach" hurkok belül egymással. Alapértelmezés szerint a standard "Foreach" hurkot a könyvtárfában párhuzamosan futnak. A párhuzamos maximális számát a "Foreach" hurkok futtathatja ciklusok, lásd: [határértékek és konfiguráció](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> A "Foreach" ciklus csak egy tömb dolgozik, és a hurok műveletek használata a `@item()` referencia feldolgozni a tömb egyes elemeiről. Ha nem a tömbben adatok adja meg, a logikai alkalmazás munkafolyamata sikertelen lesz. 

Például a logikai alkalmazás küldi el, a napi összesítés egy webhely RSS-hírcsatornában. Az alkalmazás használja a "Foreach" hurkot, amely minden egyes új elem található e-mailt küld.

1. [Ez a minta logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) Outlook.com-os vagy Office 365 Outlook-fiókkal.

2. Között az RSS-trigger és e-mail küldése műveletet, adja hozzá a "Foreach" hurkot. 

   Lépések közötti hurkot felvételéhez vigye az egérmutatót a nyíl felett kívánja az iteráció hozzáadása. 
   Válassza ki a **plusz jelre** (**+**), amely akkor jelenik meg, majd válassza a **hozzáadása egy minden**.

   ![Adjon hozzá egy "Foreach" iterációt lépések közötti](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. A kör most felépítése. A **kimenet választása az előző lépésekből** után a **dinamikus tartalom hozzáadása** lista jelenik meg, válassza ki a **hírcsatorna-hivatkozások** tömb, amely az RSS-trigger kimenete. 

   ![Válassza ki a dinamikus tartalmú listából](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Választhat *csak* az előző lépésben kimenete tömb.

   A kiválasztott tömbben most itt jelenik meg:

   ![Válassza ki a tömböt](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Egy műveletet a tömb mindegyik elemén, húzza a **e-mail küldése** művelet be a **minden** ciklus. 

   A logikai alkalmazás következőhöz hasonlít:

   ![Lépések hozzáadása a "Foreach" hurok](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Mentse a logikai alkalmazást. Manuális tesztelése a logikai alkalmazást, a Tervező eszköztárán válassza a **futtatása**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" hurkot definition (JSON)

Ha a logikai alkalmazás kódnézete a dolgozik, meghatározhatja a `Foreach` hurok a JSON-definíciójában a logikai alkalmazás, például:

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
        "runAfter": {},
    }
},
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" ciklus: szekvenciális

Alapértelmezés szerint minden ciklusban, a "Foreach" hurkot a tömb mindegyik elemén párhuzamosan futtatja. Az egyes ciklusok egymás után futnak, állítsa be a **szekvenciális** hurokba, és a "Foreach" lehetőséget.

1. A hurok jobb felső sarokban, válassza ki a **három pont** (**...** ) > **Beállítások**.

   ![A "Foreach" ciklus, válassza a "..." > "Beállítások"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Kapcsolja be a **szekvenciális** beállítást, majd válassza a **kész**.

   ![Kapcsolja be a "Foreach" hurkot a szekvenciális beállítás](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Is beállíthat a **operationOptions** paramétert `Sequential` a logikai alkalmazás JSON-definíciójában. Példa:

``` json
"actions": {
    "myForEachLoopName": {
        "type": "Foreach",
        "actions": {
            "Send_an_email": {               
            }
        },
        "foreach": "@triggerBody()?['links']",
        "runAfter": {},
        "operationOptions": "Sequential"
    }
},
```

<a name="until-loop"></a>

## <a name="until-loop"></a>"Until" ciklus
  
Ismételje meg a műveleteket, amíg egy feltétel teljesül, vagy bizonyos állapota megváltozott, használja a logikai alkalmazás munkafolyamata egy "Csak" hurkot. Az alábbiakban néhány gyakori alkalmazási helyzetek, használhat egy "Csak" hurok:

* A végpont hívja, amíg a kívánt választ kap.
* Hozzon létre egy rekordot egy adatbázisban, várja meg, amíg egy adott mezőben abban, hogy a rekord jóváhagyását, és folytatja a feldolgozást. 

Ha például 8:00 órakor minden nap, a logikai alkalmazás egy változó értékének növelése mindaddig, amíg a változó értéke 10. Ezután a logikai alkalmazás küld egy e-mailt, amely megerősíti, hogy a jelenlegi érték. Bár ebben a példában az Office 365 Outlook, a Logic Apps által támogatott bármely e-mail-szolgáltatónál is használhatja ([tekintse át az itt felsorolt összekötőket](https://docs.microsoft.com/connectors/)). Ha más e-mail-fiókot használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet. 

1. Üres logikai alkalmazás létrehozása. A Logic App Designerben keresse meg a "recurrence", és válassza ki a következő eseményindítót: **ütemezés – ismétlődés** 

   !["Ütemezés – ismétlődés" eseményindító hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Adja meg, amikor az eseményindító aktiválódik az időköz, gyakoriságát és a nap órájának beállításával. Válassza ki, hogy az óra, **speciális beállítások megjelenítése**.

   !["Ütemezés – ismétlődés" eseményindító hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Tulajdonság | Érték |
   | -------- | ----- |
   | **Intervallum** | 1 | 
   | **Gyakoriság** | Nap |
   | **Ezekben az órákban** | 8 |
   ||| 

3. Az eseményindító területén válassza a **új lépés** > **művelet hozzáadása**. Keresse meg a "változók", és válassza a művelet: **változók – változó inicializálása**

   ![Adja hozzá a "Változók – változó inicializálása" művelet](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Állítsa be ezeket az értékeket a változót:

   ![Változó tulajdonságainak megadása](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Tulajdonság | Érték | Leírás |
   | -------- | ----- | ----------- |
   | **Name (Név)** | Korlát | A változó neve | 
   | **Típus** | Egész szám | A változó adattípusa | 
   | **Érték** | 0 | A változó értéke a indítása | 
   |||| 

5. Alatt a **változó inicializálása** műveletet, válassza a **új lépés** > **további**. Válassza ki a hurok: **egy do until hozzáadása**

   !["Do-until" ciklus hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. Válassza a hurok kilépési feltétele készítését a **korlátot** változót és a **egyenlő** operátor. Adja meg **10** összehasonlító értéket.

   ![Build leállítása folyamatban van a hurok kilépési feltétele](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. A hurok belül válassza **művelet hozzáadása**. Keresse meg a "változók", és adja hozzá az ezzel a művelettel: **változók – változó értékének növelése**

   ![Művelet hozzáadása a változó növekszik](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. A **neve**, jelölje be a **korlát** változó. A **érték**, adja meg az "1". 

   ![1 "Limit" növekmény](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. Alatt, de a tömbön kívül adjon hozzá egy műveletet, amely e-mailt küld. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába.

   ![E-mailt elküldő művelet hozzáadása](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Állítsa be az e-mail-tulajdonságok. Adja hozzá a **korlát** változó a tulajdonosnak. Így ellenőrizheti, a változó aktuális értéke megfelel a megadott feltétel, például:

    ![E-mail-tulajdonságok beállítása](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Tulajdonság | Érték | Leírás |
    | -------- | ----- | ----------- | 
    | **Címzett** | *<email-address@domain>* | a címzett e-mail címét. Tesztelésre a saját e-mail cím használható. | 
    | **Tárgy** | Jelenlegi "Limit" értéke **korlát** | Adja meg az e-mail tárgyát. Ebben a példában győződjön meg arról, hogy tartalmazzák a **korlát** változó. | 
    | **Törzs** | <*email-content*> | Adja meg az e-mail üzenet tartalma is szeretne küldeni. Ebben a példában adja meg bármilyen kívánt szöveg. | 
    |||| 

11. Mentse a logikai alkalmazást. Manuális tesztelése a logikai alkalmazást, a Tervező eszköztárán válassza a **futtatása**.

    Miután a logikai futni kezd, kap egy e-mailt a megadott tartalommal:

    ![Fogadott e-mailben](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Végtelen hurkok elkerülése

Egy "Csak" hurkot rendelkezik alapértelmezett korlátai, állítsa le a végrehajtást, ha bármely feltétel:

| Tulajdonság | Alapértelmezett érték | Leírás | 
| -------- | ------------- | ----------- | 
| **Száma** | 60 | Hurkok, amely előtt a hurok kilépjen maximális számát. Az alapértelmezett érték 60 ciklusokat. | 
| **Időtúllépés** | PT1H | Kilépés a legnagyobb egy hurkot, mielőtt a hurok futási idejét. Az alapértelmezett érték egy óra és az ISO 8601 formátumban van megadva. <p>Az időtúllépés értéke abban az esetben minden ciklus ciklus. Ha a hurok bármely művelet, mint a megadott időkorláton hosszabb időt vesz igénybe, a jelenlegi időszakra leállítása nem, de a következő ciklusig az újbóli nem indul el, mert a korlát feltétel nem teljesül. | 
|||| 

Ezek alapértelmezett korlátok módosításához válassza **speciális beállítások megjelenítése** a hurok művelet minősége.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Until" definíciójának (JSON)

Ha a logikai alkalmazás kódnézete a dolgozik, megadhat egy `Until` hurok a JSON-definíciójában a logikai alkalmazás, például:

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
        },
    }
},
```

Egy másik példa a "Csak" hurkot meghívja egy HTTP-végpontot, amely egy erőforrást hoz létre, és leáll, amikor a HTTP-válasz törzsében "Kész" állapot adja vissza. Végtelen hurkok elkerülése érdekében a hurok azt is leáll, ha bármely feltétel:

* A hurok 10 alkalommal által megadott futott a `count` attribútum. Az alapértelmezett érték 60 alkalommal. 
* A hurok próbálta meg két órán keresztül által megadott futtassa a `timeout` attribútum ISO 8601 formátumban. Az alapértelmezett érték egy óra.
  
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
},
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Az elküldés és a funkciók és javaslatok, szavazzon [Azure Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Hajtsa végre a lépéseket, egy feltételt (feltételes kifejezések) alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Hajtsa végre a lépéseket, a különböző értékek (switch-utasítások) alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ág)](../logic-apps/logic-apps-control-flow-branches.md)
* [Hajtsa végre a lépéseket, a csoportosított műveleti állapota (hatóköröket) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

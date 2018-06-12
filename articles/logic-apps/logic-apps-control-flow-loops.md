---
title: Adja hozzá a hurkokra, ismételje meg a műveleteket, illetve feldolgozni tömbök - Azure Logic Apps |} Microsoft Docs
description: Ismételje meg a munkafolyamat-műveleteket, vagy az Azure Logic Apps tömbök feldolgozni hurkok létrehozása
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 87595eeb0330a2d8210258c097c29b205b628cf4
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298185"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Ismételje meg a munkafolyamat-műveleteket, vagy az Azure Logic Apps tömbök feldolgozni hurkok létrehozása

A Logic Apps alkalmazást tömbjei iterációt, használhatja a ["Foreach" hurok](#foreach-loop) vagy egy [szekvenciális "Foreach" loop](#sequential-foreach-loop). A szekvenciális "Foreach" hurok ismétlésének egyenként futása közben, párhuzamos futtatása egy szabványos "Foreach" hurok ismétlésének. A maximális elemszáma tömb, amely "Foreach" hurkok képes futtatni egy logikai alkalmazásban, lásd: [korlátozásai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP] 
> Ha rendelkezik egy eseményindító fogadó egy tömb, és szeretné futtatni a munkafolyamat minden tömb elemhez, *debatch* , hogy a tömb a [ **SplitOn** tulajdonság indítás](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 
  
Ismételje meg a műveletet, amíg a feltétel teljesül, vagy bizonyos állapota megváltozott, használja az ["Csak" hurok](#until-loop). A Logic Apps alkalmazást először végrehajtja a műveleteket a hurkon belül, és ellenőrzi a feltétel utolsó lépéseként. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok ismétlődik. Hurkok futtatja, egyetlen logikai alkalmazás tekintse meg a maximális száma "Csak" [korlátozásai és konfigurációja](../logic-apps/logic-apps-limits-and-config.md). 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" hurok

Ismételje meg a műveletek minden elemhez a tömbben, használja a logic app munkafolyamat "Foreach" hurkot. Egy "Foreach" ciklus is felvehet több művelet, és ágyazhatja "Foreach" hurkok belül egymással. Alapértelmezés szerint a szabványos "Foreach" ismétlődő ciklust párhuzamosan futnak. A maximális száma párhuzamos ciklusok, hogy "Foreach" hurkokat is futtatható, a következő témakörben: [korlátozásai és konfigurációs](../logic-apps/logic-apps-limits-and-config.md).

> [!NOTE] 
> A "Foreach" hurkot csak egy tömb dolgozik, és a hurok a műveletek a `@item()` hivatkozás feldolgozni a tömb minden elemet. Nem tömb adatokat ad meg, ha a logic app munkafolyamat sikertelen lesz. 

Például a logikai alkalmazás küld Önnek napi összegzését az RSS-hírcsatorna egy webhelyet. Az alkalmazás használ, amelyet az e-mailt küld, minden egyes új cikk található "Foreach" hurkot.

1. [Ez a minta logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) Outlook.com vagy az Office 365 Outlook-fiókkal.

2. Az RSS közötti indul el, és küldjön e-mailek művelet, vegye fel a "Foreach" hurkot. 

   Lépések között hurkot hozzáadásához húzza az egérmutatót a nyíl ahol hozzáadandó a hurok. 
   Válassza ki a **pluszjel** (**+**), amely akkor jelenik meg, majd válassza a **hozzáadása egy minden**.

   ![Adja hozzá a "Foreach" hurkot lépések között](media/logic-apps-control-flow-loops/add-for-each-loop.png)

3. Most hozhat létre. a hurok. A **kimenetnek válassza az előző lépéseket** után a **dinamikus tartalom hozzáadása az** megjelenik a listán, válassza ki a **hivatkozások hírcsatorna** tömb, amely az RSS-eseményindítóval kimeneti. 

   ![Válassza ki a dinamikus tartalom listából](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Kiválaszthatja *csak* tömb kiírja az előző lépésben.

   A kijelölt tömb most itt jelenik meg:

   ![Válassza ki a tömb](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Egy műveletet minden egyes tömb elemen, húzza a **egy e-mailek küldése** a művelet a **minden** hurok. 

   A Logic Apps alkalmazást ebben a példában hasonlót nézhet ki:

   ![Vegyen fel olyan lépéseket "Foreach" hurok](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Mentse a logikai alkalmazást. Manuálisan tesztelje a Logic Apps alkalmazást, a designer eszköztáron kattintson a **futtatása**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>"Foreach" hurok definition (JSON)

Ha a logikai alkalmazásnak kódnézetben dolgozik, megadhatja a `Foreach` hurok a Logic Apps alkalmazást JSON-definícióban, például:

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

## <a name="foreach-loop-sequential"></a>"Foreach" loop: szekvenciális

Alapértelmezés szerint az egyes ciklusok "Foreach" ismétlődő minden tömb elemhez párhuzamosan fut. Az egyes ciklusok egymás után futnak, állítsa be a **Sorostevékenység** beállítás a "Foreach" ciklusban.

1. Válassza ki a hurok jobb felső sarokban, **folytatást jelző pontokra** (**...** ) > **Beállítások**.

   ![A "Foreach" hurok, válassza az "..." > "Beállítások"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

2. Kapcsolja be a **Sorostevékenység** beállítást, majd válassza a **végzett**.

   ![Kapcsolja be a "Foreach" hurok szekvenciális beállítás](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Azt is beállíthatja a **operationOptions** paramétert `Sequential` a Logic Apps alkalmazást JSON-definícióban. Példa:

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

## <a name="until-loop"></a>"Csak" hurok
  
Ismételje meg a műveletet, amíg a feltétel teljesül, vagy bizonyos állapota megváltozott, használja a logic app munkafolyamat egy "Csak" hurok. Az alábbiakban néhány gyakori alkalmazási esetekben, ahol ugyanúgy használhatók egy "Csak" loop:

* A végpont hívható, amíg elér a választ, amely a kívánt.
* Hozzon létre egy rekordot egy adatbázisban, várjon, amíg egy adott mező abban, hogy a rekord jóváhagyását és feldolgozás folytatásához. 

Például 8:00 órakor naponta, a logikai alkalmazás növeli a változó mindaddig, amíg a változó értéke 10. Ezt követően a logic app küld egy e-mailt, amely megerősíti, hogy a jelenlegi érték. Bár ez a példa az Office 365 Outlook, használhatja a Logic Apps által támogatott e-mail szolgáltató ([tekintse át az összekötők listán Itt](https://docs.microsoft.com/connectors/)). Ha más e-mail-fiókot használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet. 

1. Üres logikai alkalmazás létrehozása. Logic App Designer "ismétlődési", és adja meg az eseményindító: **ütemezés - ismétlődési** 

   ![Adja hozzá a "Ütemezés – ismétlődési" eseményindító](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

2. Adja meg, ha az eseményindító következik be, úgy, hogy az intervallum, gyakoriságát és a nap órája. Állítsa be az órát, válassza a **speciális beállítások megjelenítése**.

   ![Adja hozzá a "Ütemezés – ismétlődési" eseményindító](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Tulajdonság | Érték |
   | -------- | ----- |
   | **Intervallum** | 1 | 
   | **Gyakoriság** | Nap |
   | **Ezekben az órákban** | 8 |
   ||| 

3. Válassza ki az eseményindító **új lépés** > **művelet hozzáadása**. Keresse meg a "változók", és válassza ki ezt a műveletet: **változók - inicializálási változó**

   ![Adja hozzá a "Változók - inicializálási változó" művelet](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

4. Ezekkel az értékekkel a változó beállítása:

   ![Változó tulajdonságainak beállítása](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Tulajdonság | Érték | Leírás |
   | -------- | ----- | ----------- |
   | **Name (Név)** | Korlát | A változó neve | 
   | **Típus** | Egész szám | A változó adattípusa | 
   | **Érték** | 0 | A változó a kezdőérték | 
   |||| 

5. Az a **inicializálása változó** művelet, válassza a **új lépés** > **további**. Válassza ki a hurok: **csak egy tegye hozzáadása**

   !["Do csak" loop hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

6. A hurok kilépési feltétele build kiválasztásával a **korlát** változó és a **egyenlő** operátor. Adja meg **10** összehasonlító értékeként.

   ![Build leállítása a hurok kilépési feltétele](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

7. Válassza ki a hurok belül **művelet hozzáadása**. "Változó" keressen, és adja hozzá az ezzel a művelettel: **változók - növekmény változó**

   ![A változó növekvő művelet hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

8. A **neve**, jelölje be a **korlát** változó. A **érték**, adja meg az "1". 

   ![1 "Korlátot" növekmény](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

9. Alatt, de a hurok kívül az e-mailt küld művelet hozzáadása. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába.

   ![Műveletet, amely e-mailt küld](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

10. Az e-mailt tulajdonságainak beállítása. Adja hozzá a **korlát** változó a tulajdonosnak. Ezzel a módszerrel úgy ellenőrizheti a változó aktuális értéke megfelel a megadott feltétel, például:

    ![E-mailek tulajdonságainak beállítása](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

    | Tulajdonság | Érték | Leírás |
    | -------- | ----- | ----------- | 
    | **Címzett** | *<email-address@domain>* | a címzett e-mail címét. Tesztelési, e-mail címét használja. | 
    | **Tárgy** | Aktuális érték "Korlát" **korlátot** | Adja meg az e-mail tárgyát. Például győződjön meg arról, hogy a **korlát** változó. | 
    | **Törzs** | <*email-content*> | Adja meg az elküldeni kívánt e-mail üzenet tartalma. Ehhez a példához írja be a függetlenül zárult szöveges. | 
    |||| 

11. Mentse a logikai alkalmazást. Manuálisan tesztelje a Logic Apps alkalmazást, a designer eszköztáron kattintson a **futtatása**.

    Fut a logika elindulása után a tartalomhoz, amelyet e-mailt kap:

    ![Beérkezett e-mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Végtelen hurkok megakadályozása

Egy "Csak" hurok van alapértelmezett korlát végrehajtási le, ha az alábbi feltételek valamelyikének fordulhat elő:

| Tulajdonság | Alapértelmezett érték | Leírás | 
| -------- | ------------- | ----------- | 
| **Száma** | 60 | A maximális száma, amelyek végrehajtása a hurok kilépjen-e hurok. Az alapértelmezett érték 60 ciklusokat. | 
| **Időtúllépés** | PT1H | A maximális időt a hurok előtt hurkot futtatásához kilép. Alapértelmezés szerint egy óra, és az ISO 8601 formátum van megadva. <p>Az időtúllépés értéke minden hurok ciklus állapított meg. A ciklus minden művelet tovább tart, mint az időkorlátot, ha az aktuális ciklus nem leállítása, de a következő ciklusban nem indul el, mert a korlát feltétel nem teljesül. | 
|||| 

Alapértelmezett működés felső korlátjának módosításához válassza **speciális beállítások megjelenítése** hurok művelet alakú.

<a name="until-json"></a>

## <a name="until-definition-json"></a>"Csak" definition (JSON)

Ha a logikai alkalmazásnak kódnézetben dolgozik, megadhat egy `Until` hurok a Logic Apps alkalmazást JSON-definícióban, például:

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

Egy másik példa a "Csak" hurok meghívja a HTTP-végponttal, amely létrehoz egy erőforrást, és leáll, amikor a HTTP-válasz törzsében "Befejezve" állapotú adja vissza. Végtelen hurkok megelőzése érdekében a hurok azt is leáll, ha fordulhat elő, az alábbi feltételek valamelyikének:

* A hurok 10-szer által megadott futott a `count` attribútum. Az alapértelmezett érték 60 alkalommal. 
* A hurok megpróbálta két órán keresztül leírt módon futtassa a `timeout` attribútum ISO 8601 formátumban. Az alapértelmezett érték 1 óra.
  
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
* Az elküldés és a szolgáltatásokat és javaslataikat, szavazhatnak [Azure Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [A lépéseket (feltételes utasítások) feltétel alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Futtatás (kapcsoló utasítások) eltérő értékek alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Futtassa a csoportosított (hatókör) állapota alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

---
title: Adja hozzá a hurkok, ismételje meg a műveletet, vagy feldolgozni tömbök – Azure Logic Apps |} A Microsoft Docs
description: Hurkok, ismételje meg a munkafolyamat-műveletek, vagy az Azure Logic Appsben tömbök feldolgozni létrehozása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: jeconnoc
ms.date: 01/05/2019
ms.topic: article
ms.openlocfilehash: c37e41bce481fff5e172687907cce527c10ae006
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225008"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Hurkok, ismételje meg a munkafolyamat-műveletek, vagy az Azure Logic Appsben tömbök feldolgozni létrehozása

A logikai alkalmazás a tömb feldolgozásához, létrehozhat egy ["Foreach" ciklus](#foreach-loop). Ez a ciklus egy vagy több műveletet a a tömb mindegyik elemén ismétlődik. Korlátok, amely a "Foreach" hurkokat tömbelemek száma képes feldolgozni, lásd: [korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md). 

Ismételje meg a műveletek, amíg egy feltétel teljesül beolvasása, vagy egy állapota megváltozik, létrehozhat egy ["Until" ciklus](#until-loop). A logikai alkalmazás a hurok belüli összes műveletet fut, és ellenőrzi a feltétel, vagy az állapota. Ha a feltétel teljesül, a hurok leáll. Ellenkező esetben a hurok ismétlődik. A logikai alkalmazás futtatását, a hurkokat lásd az "Until" vonatkozó korlátok [korlátozások és konfiguráció](../logic-apps/logic-apps-limits-and-config.md). 

> [!TIP]
> Ha rendelkezik egy eseményindítót, amely egy tömböt kap, és szeretne futtatni egy munkafolyamatot a tömb mindegyik elemén, *debatch* a tömböt a [ **SplitOn** tulajdonság-trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch). 

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>"Foreach" ciklus

A "Foreach ciklus" megismétli a tömb mindegyik elemén egy vagy több műveletet, és csak a tömbök működik. Az ismétlések egy "Foreach" hurokba, és párhuzamosan futtathatók. Azonban, futtathatja az ismétlések egy egyszerre beállításával egy ["Foreach" szekvenciális hurok](#sequential-foreach-loop). 

Ha a "Foreach" ciklusokkal az alábbiakban néhány szempontot:

* Beágyazott hurkok, az ismétlések mindig egymás után futnak, nem párhuzamosan. Párhuzamos beágyazott ismétlődő elemek műveletek futtatásához, hozzon létre és [gyermek logikai alkalmazások hívása](../logic-apps/logic-apps-http-endpoint.md).

* Műveletek változói az egyes hurok iteráció során kiszámíthatóság lekéréséhez futtassa egymás után ezeket a hurkok. Például egyidejűleg zajló véget ér, a növekményes, a csökkentési ikonjához, és változó műveletek hozzáfűzése kiszámíthatóság adja vissza. Azonban minden egyes ismétléskor az egyidejűleg zajló hurokba került, során ezeket a műveleteket előfordulhat, hogy vissza kiszámíthatatlan következményekkel járhat. 

* Műveletek a "Foreach" ciklus használata a [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
kifejezés hivatkozik, és a tömbben található minden elem feldolgozására. Ha nem a tömbben adatok adja meg, a logikai alkalmazás munkafolyamata sikertelen lesz. 

Ez a példa a logikai alkalmazás küld egy webhely RSS-hírcsatorna napi összegzését. Az alkalmazás használja a "Foreach" hurkot, amely minden új elemről e-mailt küld.

1. [Ez a minta logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) Outlook.com-os vagy Office 365 Outlook-fiókkal.

2. Között az RSS-trigger és e-mail küldése műveletet, adja hozzá a "Foreach" hurkot. 

   1. Lépések közötti hurkot hozzáadásához helyezze az egérmutatót a nyíl ezeket a lépéseket között. 
   Válassza ki a **plusz jelre** (**+**), amely akkor jelenik meg, majd válassza ki **művelet hozzáadása**.

      ![Válassza a "Művelet hozzáadása"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. A keresőmező alatt válassza ki a **összes**. A Keresés mezőbe írja be a "for each" szűrőként. A műveletek listából válassza a következő műveletet: **Minden egyes - vezérlő**

      ![Adja hozzá a "mindegyikre" hurkot](media/logic-apps-control-flow-loops/select-for-each.png)

3. A kör most felépítése. A **kimenet választása az előző lépésekből** után a **dinamikus tartalom hozzáadása** lista jelenik meg, válassza ki a **hírcsatorna-hivatkozások** tömb, amely az RSS-trigger kimenete. 

   ![Válassza ki a dinamikus tartalmú listából](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Választhat *csak* az előző lépésben kimenete tömb.

   A kiválasztott tömbben most itt jelenik meg:

   ![Válassza ki a tömböt](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Művelet futtatása a tömb mindegyik elemén, húzza a **e-mail küldése** be a ciklus a művelet. 

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
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>"Foreach" ciklus: Szekvenciális

Alapértelmezés szerint a "Foreach" hurkot a könyvtárfában párhuzamosan futnak. Az egyes ciklusok egymás után futnak, állítsa be a hurok **szekvenciális** lehetőséget. "Foreach" hurkok kell egymás után futnak, amikor a beágyazott hurkok vagy hurkokat belül változók kiszámíthatóság várt. 

1. A hurok jobb felső sarokban, válassza ki a **három pont** (**...** ) > **Beállítások**.

   ![A "Foreach" ciklus, válassza a "..." > "Beállítások"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. A **egyidejűség-vezérlés**, kapcsolja be a **egyidejűség-vezérlés** beállítást **a**. Helyezze át a **mértékét a párhuzamosság** csúszkát a **1**, és válassza **kész**.

   ![Egyidejűség-vezérlés bekapcsolása](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Ha dolgozik a logikai alkalmazás JSON-definícióját, akkor használhatja a `Sequential` hozzáadásával a beállítás a `operationOptions` paramétert, például:

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

## <a name="until-loop"></a>"Until" ciklus
  
Ismétlődő műveletek, amíg egy feltétel teljesül beolvasása, vagy egy állapota megváltozik, helyezze el egy "Csak" hurkot ezeket a műveleteket. Az alábbiakban néhány gyakori forgatókönyvet, ahol használhatja egy "Csak" hurok:

* A végpont hívja, amíg a kívánt választ kap.

* Hozzon létre egy rekordot egy adatbázisban. Várjon, amíg egy adott mezőben abban, hogy a rekord jóváhagyását. Folytatja a feldolgozást. 

8:00 órakor naponta kezdődően ez a példa a logikai alkalmazás egy változó értékének növelése mindaddig, amíg a változó értéke 10. A logikai alkalmazás majd küld egy e-mailt, amely megerősíti, hogy a jelenlegi érték. 

> [!NOTE]
> Ezeket a lépéseket használja az Office 365 Outlook, de használhatja, amely támogatja a Logic Apps bármely e-mail-szolgáltatónál. 
> [Ellenőrizze, hogy az itt felsorolt összekötőket](https://docs.microsoft.com/connectors/). Ha egy másik e-mail-fiókot használ, az általános lépések ugyanazok, de a felhasználói felület kis mértékben eltérő lehet. 

1. Üres logikai alkalmazás létrehozása. A Logikaialkalmazás-Tervező, a keresőmező alatt válassza a **összes**. Keressen a "recurrence". 
   Az eseményindítók listában jelölje ki az eseményindító: **Ismétlődés - ütemezés**

   !["Ismétlődési – ütemezés" eseményindító hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Adja meg, amikor az eseményindító aktiválódik az időköz, gyakoriságát és a nap órájának beállításával. Válassza ki, hogy az óra, **speciális beállítások megjelenítése**.

   ![Ismétlődési ütemezés beállítása](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Tulajdonság | Érték |
   | -------- | ----- |
   | **Intervallum** | 1 | 
   | **Gyakoriság** | Nap |
   | **Ezekben az órákban** | 8 |
   ||| 

1. Az eseményindító területén válassza a **új lépés**. 
   Keresse meg a "változók", és válassza a következő műveletet: **Változó - változók inicializálása**

   !["Változó – változó inicializálása" művelet hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Állítsa be ezeket az értékeket a változót:

   ![Változó tulajdonságainak megadása](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Tulajdonság | Érték | Leírás |
   | -------- | ----- | ----------- |
   | **Name (Név)** | Korlát | A változó neve | 
   | **Típus** | Egész szám | A változó adattípusa | 
   | **Érték** | 0 | A változó értéke a indítása | 
   |||| 

1. Alatt a **változó inicializálása** műveletet, válassza a **új lépés**. 

1. A keresőmező alatt válassza ki a **összes**. Keressen az "until", és válassza a következő műveletet: **UNTIL - vezérlő**

   !["Until" ciklus hozzáadása](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Válassza a hurok kilépési feltétele készítését a **korlátot** változót és a **egyenlő** operátor. 
   Adja meg **10** összehasonlító értéket.

   ![Build leállítása folyamatban van a hurok kilépési feltétele](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. A hurok belül válassza **művelet hozzáadása**. 

1. A keresőmező alatt válassza ki a **összes**. Keresse meg a "változók", és válassza a következő műveletet: **Változó értékének növelése - változók**

   ![Művelet hozzáadása a változó növekszik](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. A **neve**, jelölje be a **korlát** változó. A **érték**, adja meg az "1". 

     ![1 "Limit" növekmény](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Kívül, és az iteráció alatt válassza a **új lépés**. 

1. A keresőmező alatt válassza ki a **összes**. 
     Keresse meg, és adjon hozzá egy műveletet, amely küld e-mailt, például: 

     ![E-mailt elküldő művelet hozzáadása](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Ha a rendszer kéri, jelentkezzen be az e-mail-fiókjába.

1. Művelet tulajdonságai állíthatók be az e-mailt. Adja hozzá a **korlát** változó a tulajdonosnak. Így ellenőrizheti, a változó aktuális értéke megfelel a megadott feltétel, például:

      ![E-mail-tulajdonságok beállítása](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Tulajdonság | Érték | Leírás |
      | -------- | ----- | ----------- | 
      | **Címzett** | *<email-address\@domain>* | a címzett e-mail címét. Tesztelésre a saját e-mail cím használható. | 
      | **Tárgy** | Jelenlegi "Limit" értéke **korlát** | Adja meg az e-mail tárgyát. Ebben a példában győződjön meg arról, hogy tartalmazzák a **korlát** változó. | 
      | **Törzs** | <*email-content*> | Adja meg az e-mail üzenet tartalma is szeretne küldeni. Ebben a példában adja meg bármilyen kívánt szöveg. | 
      |||| 

1. Mentse a logikai alkalmazást. Manuális tesztelése a logikai alkalmazást, a Tervező eszköztárán válassza a **futtatása**.

      Miután a logikai futni kezd, kap egy e-mailt a megadott tartalommal:

      ![Fogadott e-mailben](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

## <a name="prevent-endless-loops"></a>Végtelen hurkok elkerülése

Egy "Csak" hurkot rendelkezik alapértelmezett korlátai, állítsa le a végrehajtást, ha bármely feltétel:

| Tulajdonság | Alapértelmezett érték | Leírás | 
| -------- | ------------- | ----------- | 
| **Száma** | 60 | Hurkok, amely előtt a hurok kilépjen számát vesszük figyelembe. Az alapértelmezett érték 60 ciklusokat. | 
| **Időtúllépés** | PT1H | A legtöbb mennyi ideig való futtatása előtt a hurok hurkot kilép. Az alapértelmezett érték egy óra és az ISO 8601 formátumban van megadva. <p>Az időtúllépés értéke abban az esetben minden ciklus ciklus. A hurok bármely művelet, mint a megadott időkorláton hosszabb időt vesz igénybe, állítsa le az aktuális ciklus nem. A következő ciklusig az újbóli azonban csak nem indul el, mert a korlát feltétel nem teljesül. | 
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
      }
   }
}
```

Ebben a példában "Csak" hurkot meghív egy HTTP-végpontot, amely létrehoz egy erőforrást. A hurok leáll, amikor a HTTP-válasz törzsében ad vissza, amelyben `Completed` állapotát. Végtelen hurkok elkerülése érdekében a hurok azt is leáll, ha bármely feltétel:

* A hurok 10 alkalommal által megadott futtatta a `count` attribútum. Az alapértelmezett érték 60 alkalommal. 

* A hurok által megadott két órákig futottak a `timeout` attribútum ISO 8601 formátumban. Az alapértelmezett érték egy óra.
  
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
* Az elküldés és a funkciók és javaslatok, szavazzon [Azure Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Hajtsa végre a lépéseket, egy feltételt (feltételes kifejezések) alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Hajtsa végre a lépéseket, a különböző értékek (switch-utasítások) alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ág)](../logic-apps/logic-apps-control-flow-branches.md)
* [Hajtsa végre a lépéseket, a csoportosított műveleti állapota (hatóköröket) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)

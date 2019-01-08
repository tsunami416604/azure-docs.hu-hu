---
title: Változók értékei – Azure Logic Apps mentése létrehozása |} A Microsoft Docs
description: Mentse és kezelése az Azure Logic Appsben változók létrehozásával
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: bb84c7d5e483b0a2abc3b7d1a37de8760513d203
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063216"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Mentés és kezelése az Azure Logic Appsben értékek változók létrehozása

Ez a cikk bemutatja, hogyan tárolhatja és változók létrehozásával a logikai alkalmazás teljes értékekkel működik. Például változók segítségével, amely hurkot hányszor száma. Amikor léptetés keresztül egy tömb, vagy az adott elem egy tömb ellenőrzése, használhatja egy változót való hivatkozáshoz a tömb mindegyik elemén indexszámát. 

Az adattípusok, például az egész szám, lebegőpontos, logikai érték, karakterlánc, tömb vagy objektum változókat hozhat létre. Miután létrehozott egy változót, egyéb feladatok, például hajthatja végre:

* GET, vagy hivatkozzon a változó értékét.
* Növelheti vagy csökkentheti a változót egy állandó érték, más néven *növekmény* és *csökkentés*.
* Rendeljen egy másik értéket a változóhoz.
* Helyezze be vagy *hozzáfűzése* a változó értéke a legutóbbi alkalommal tömb vagy karakterlánc.

Változók és azok globális csak belül a logikaialkalmazás-példányt, amely létrehozza őket. Emellett azok kivonatuk bármely ciklus ismétléseinek belül egy logikaialkalmazás-példányt. Ha egy változóra hivatkozik, használja a változó nevét a jogkivonatot, nem a művelet neve, amely a szokásos módon való hivatkozáshoz egy műveleti kimenetek. 

> [!IMPORTANT]
> Alapértelmezés szerint a "Foreach" hurkot a könyvtárfában párhuzamosan futnak. Hurkok változókat használ, futtassa a hurok [egymás után](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) változók kiszámítható eredményt ad vissza. 

Ha nem rendelkezik Azure-előfizetésem, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>. 

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk követéséhez az alábbiakban szükséges elemek:

* A logikai alkalmazás, ahol szeretné változó létrehozása 

  Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás első lépéseként 

  Az változók létrehozására és kezelésére vonatkozó műveletek hozzáadása előtt a logikai alkalmazás egy eseményindítóval kell elindítania.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Változó inicializálása

Hozzon létre egy változót, és az adattípus és a kezdeti érték – egy műveletet a logikai alkalmazásban lévő összes deklarálható. Akkor is csak deklarálja a változókat globális szinten, nem a hatókörök, feltételek és ciklusok belül. 

1. Az a <a href="https://portal.azure.com" target="_blank">az Azure portal</a> vagy a Visual Studio, a logikai alkalmazás megnyitása a Logikaialkalmazás-tervezőben. 

   Ebben a példában egy meglévő eseményindítót használ az Azure portal és a egy logikai alkalmazást.

2. A logikai alkalmazásban kívánja adjon hozzá egy változóhoz, feladatütemezésekben kövesse az alábbi lépéseket: 

   * Válassza ki az utolsó lépés a művelet hozzáadása, **új lépés** > **művelet hozzáadása**.

     ![Művelet felvétele](./media/logic-apps-create-variables-store-values/add-action.png)

   * Lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, megjelenik a plusz jelre (+). 
   Válassza a plusz jelre, majd ez után **művelet hozzáadása**.

3. A Keresés mezőbe írja be a "változók" szűrőként. Válassza a műveletek listájának **változók – változó inicializálása**.

   ![Művelet kiválasztása](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Adja meg ezt az információt a változó:

   | Tulajdonság | Szükséges | Érték |  Leírás |
   |----------|----------|-------|--------------|
   | Name (Név) | Igen | <*a változó neve*> | Növelje a változó neve | 
   | Típus | Igen | <*változó-típus*> | A változó adattípusa | 
   | Érték | Nem | <*Start-érték*> | A változó kezdeti értéke <p><p>**Tipp**: Bár nem kötelező, csak ezt az értéket ajánlott eljárásként, hogy mindig tudja, a kezdő érték a változó. | 
   ||||| 

   ![Változó inicializálása](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Most már folytathatja a hozzáadni kívánt műveleteket. Ha elkészült, a Tervező eszköztárán válassza a **mentése**.

Ha átvált a tervezőben a nézet Kódszerkesztő, Íme módja a **változó inicializálása** művelet a logikai alkalmazás definíciójának, amely JavaScript Object Notation (JSON) formátumú belül jelenik meg:

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

Az alábbiakban néhány egyéb változótípusok példákat:

*Karakterlánc-változóhoz*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Logikai változó*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Az egész számok tömb*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Karakterláncot tartalmazó tömb*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>A változó értékének beolvasása

Beolvasni, vagy egy változó tartalmát hivatkozhat, használhatja a [variables() függvény](../logic-apps/workflow-definition-language-functions-reference.md#variables) a Logikaialkalmazás-Tervező, és a Kódszerkesztő megtekintése.
Ha egy változóra hivatkozik, használja a változó nevét a jogkivonatot, nem a művelet neve, amely a szokásos módon való hivatkozáshoz egy műveleti kimenetek. 

Például ez a kifejezés a elemeinek beolvasása a tömbváltozó [ebben a cikkben korábban létrehozott](#append-value) használatával a **variables()** függvény. A **string()** függvény karakterlánc-formátum a változó tartalmát adja vissza: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Változó értékének növelése 

Növelheti vagy *növekmény* egy változót egy állandó értékkel adja hozzá a **változók – változó értékének növelése** művelet a logikai alkalmazáshoz. Ez a művelet csak egész szám és lebegőpontos szám értékű változók működik.

1. Logikaialkalmazás-Tervező, hol bővíteni szeretné egy meglévő változó, feladatütemezésekben válassza **új lépés** > **művelet hozzáadása**. 

   Például a logikai alkalmazás már rendelkezik egy eseményindítót és a egy műveletet, amely egy változó létrehozva. Adjunk hozzá egy új művelet a következő lépéseket:

   ![Művelet felvétele](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Meglévő lépések közötti művelet hozzáadása, vigye az egérmutatót a csatlakozó mutató nyílra, hogy a plusz jelre (+) jelenik meg. Válassza a plusz jelre, majd ez után **művelet hozzáadása**.

2. A Keresés mezőbe írja be a "változó értékének növelése" szűrőként. A műveletek listájában válassza ki a **változók – változó értékének növelése**.

   !["Változó értékének növelése" művelet kiválasztása](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Az információkat növekszik a változót:

   | Tulajdonság | Szükséges | Érték |  Leírás |
   |----------|----------|-------|--------------|
   | Name (Név) | Igen | <*a változó neve*> | Növelje a változó neve | 
   | Érték | Nem | <*növekmény-érték*> | Növekszik a változó értékét. Az alapértelmezett érték egyike. <p><p>**Tipp**: Bár nem kötelező, csak ezt az értéket ajánlott eljárásként, hogy mindig tudja, adott növekszik a változó értékét. | 
   |||| 

   Példa: 
   
   ![Növekmény érték példa](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Ha elkészült, a Tervező eszköztárán válassza a **mentése**. 

Ha átvált a tervezőben a nézet Kódszerkesztő, Íme módja a **változó értékének növelése** művelet belül a logikai alkalmazás definíciójának, amely JSON formátumban jelenik meg:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Példa: Hurok számláló létrehozása

A számbavételi hurkot futtat hányszor gyakran használják a változókat. Ez a példa bemutatja, hogyan hozzon létre és változók használni ezt a feladatot hoz létre, amely a mellékleteket egy e-mailben counts hurkot.

1. Az Azure Portalon hozzon létre egy üres logikai alkalmazás. Adjon hozzá egy eseményindítót, amely ellenőrzi, hogy új e-mailt és a mellékleteit. 

   Ez a példa az Office 365 Outlook eseményindítót használ **új e-mail érkezésekor**. 
   Ez az eseményindító csak akkor, ha az e-mailt címkéz üzenetszám állíthat.
   Használhatja azonban minden olyan összekötőt, amely ellenőrzi a mellékleteket, például az Outlook.com-összekötőt tartalmazó új e-mailekhez.

2. Az eseményindító területén válassza **speciális beállítások megjelenítése**. Beállíthatja, hogy az eseményindító mellékletek ellenőrzése és a mellékletek átadandó a logikai alkalmazás munkafolyamat **Igen** ezekhez a tulajdonságokhoz:
   
   * **Melléklettel rendelkezik** 
   * **Mellékletek is** 

   ![Keresse meg és a mellékletek is](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Adja hozzá a [ **változó inicializálása** művelet](#create-variable). Hozzon létre egy egész szám típusú változó nevű **száma** esetében a kezdeti érték.

   ![A "Változó inicializálása" művelet hozzáadása](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Az egyes mellékletek léptetés, adjon hozzá egy *minden* kiválasztásával hurok **új lépés** > **további** > **hozzáadása egy az egyes**.

   ![Adjon hozzá egy "for each" iterációt](./media/logic-apps-create-variables-store-values/add-loop.png)

5. A hurok, kattintson a **kimenet választása az előző lépésekből** mezőbe. Ha a dinamikus tartalmak listája jelenik meg, válassza ki a **mellékletek**. 

   ![A „Mellékletek” elem kiválasztása](./media/logic-apps-create-variables-store-values/select-attachments.png)

   A **mellékletek** mező átadja egy tömb, amely a hurokhoz azokat a trigger kimenetéből származó e-mail melléklettel rendelkezik.

6. Válassza a "mindegyikre" hurkot, **művelet hozzáadása**. 

   ![Válassza a "Művelet hozzáadása"](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. A Keresés mezőbe írja be a "változó értékének növelése" szűrőként. Válassza a műveletek listájának **változók – változó értékének növelése**.

   > [!NOTE]
   > Győződjön meg arról, hogy a **változó értékének növelése** művelet belül a hurok jelenik meg. Ha a műveletet a tömbön kívül jelenik meg, húzza a ciklus a művelet.

8. Az a **változó értékének növelése** műveletet, az a **neve** listáról válassza ki a **száma** változó. 

   ![Válassza ki a "Count" változó](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. Adja hozzá a hurok bármely művelet, amely a Csatolmányok számát. A működés közben, az értéket tartalmazza a **száma** változó, például: 

   ![Adjon hozzá egy műveletet, amely elküldi az eredményeket](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot. 

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás nincs engedélyezve, a logikai alkalmazás menüjében válassza a **áttekintése**. Az eszköztáron válassza **engedélyezése**. 

2. Logikaialkalmazás-Tervező eszköztárán válassza a **futtatása**. Ezzel a lépéssel manuálisan indíthatja a logikai alkalmazást.

3. Ebben a példában használt e-mail-fiókjába egy vagy több mellékleteket tartalmazó e-mail küldése.

   Ez a lépés akkor aktiválódik, a logikai alkalmazás eseményindítójának, amely hoz létre, és a logikai alkalmazás munkafolyamat egy példányát futtatja.
   Ennek eredményeképpen a logikai alkalmazás küld Önnek egy üzenet vagy e-mailt, amely az e-mailben elküldött mellékleteket számát jeleníti meg.

Ha átvált a tervezőben a nézet Kódszerkesztő, Íme a módja a "for each" hurok jelenik meg, amelyen a **változó értékének növelése** művelet a logikai alkalmazás definíciójának, amely JSON formátumban vannak.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Változó értékének csökkentése

A csökkentése vagy *csökkentenie* egy változót egy állandó értékkel, kövesse a lépéseket [változó növelése](#increment-value) azzal a különbséggel, hogy keresse meg, és válassza ki a **változók – változó értékének csökkentése**művelet helyett. Ez a művelet csak egész szám és lebegőpontos szám értékű változók működik.

Az alábbiakban a tulajdonságokat a **változó értékének csökkentése** művelet:

| Tulajdonság | Szükséges | Érték |  Leírás |
|----------|----------|-------|--------------|
| Name (Név) | Igen | <*a változó neve*> | Csökkentenie kell a változó neve | 
| Érték | Nem | <*növekmény-érték*> | Csökkenő a változó értékét. Az alapértelmezett érték egyike. <p><p>**Tipp**: Bár nem kötelező, csak ezt az értéket ajánlott eljárásként, hogy mindig tudja, csökkenő egyedi értékét a változót. | 
||||| 

Ha átvált a tervezőben a nézet Kódszerkesztő, Íme módja a **változó értékének csökkentése** művelet belül a logikai alkalmazás definíciójának, amely JSON formátumban jelenik meg.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```


<a name="assign-value"></a>

## <a name="set-variable"></a>Változó beállítása

Egy másik értéket rendel egy létező változó, kövesse a [változó növelése](#increment-value) , kivéve, amikor: 

1. Keresse meg és válassza a **változók – változó beállítása** művelet helyett. 

2. Adja meg a változó nevét és a hozzárendelni kívánt érték. Változó pedig az új értéket az azonos típusúnak kell lennie.
A értékét kötelező megadni, mert ez a művelet nem rendelkezik alapértelmezett értékkel. 

Az alábbiakban a tulajdonságokat a **változó beállítása** művelet:

| Tulajdonság | Szükséges | Érték |  Leírás | 
|----------|----------|-------|--------------| 
| Name (Név) | Igen | <*a változó neve*> | Módosíthatja a változó neve | 
| Érték | Igen | <*új érték*> | A változó hozzárendelni kívánt érték. Írja be a ugyanazokat az adatokat is kell rendelkeznie. | 
||||| 

> [!NOTE]
> Kivéve, ha Ön a növekvő vagy csökkenő változót, hurkokat belül változók módosítása *előfordulhat, hogy* ciklusok futtatása alapértelmezés szerint párhuzamos, vagy egy időben, mert a váratlan eredményeket hozhatnak létre. Ezekben az esetekben próbálja meg egymás után futnak a hurok beállítást. Ha szeretné a változó értékét a hurok található hivatkozásra, és ugyanazt az értéket a kezdő és a végén a hurok-példány várható, például kövesse az alábbi lépéseket módosításához, hogyan a ciklus fut: 
>
> 1. A hurok jobb felső sarokban a három pontra (...) gombra, és válassza **beállítások**.
> 
> 2. Alatt **egyidejűség-vezérlés**, módosítsa a **alapértelmezett felülbírálás** beállítást **a**.
>
> 3. Húzza a **mértékét a párhuzamosság** csúszka **1**.

Ha átvált a tervezőben a nézet Kódszerkesztő, Íme módja a **változó beállítása** művelet belül a logikai alkalmazás definíciójának, amely JSON formátumban jelenik meg. Ebben a példában a "Count" változó aktuális értéke átvált egy másik értéket. 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>A változó hozzáfűzése

Változók karakterláncok vagy a tömb tároló beszúrhatja vagy *hozzáfűzése* egy változó értékének ezeket a karakterláncok vagy tömbök utolsó helyén. A lépések a [változó növelése](#increment-value) azzal a különbséggel, hogy ehelyett hajtsa végre ezeket a lépéseket: 

1. Keresse meg és válassza ki az egyik ilyen művelet, a változó e karakterlánc vagy egy tömb alapján: 

  * **Változók – hozzáfűzni kívánt karakterlánc-változóhoz**
  * **Változók – Hozzáfűzés tömbváltozóhoz** 

2. Adja meg a tömb vagy karakterlánc utolsó elemként hozzáfűzni kívánt értékét. Kötelezően megadandó érték. 

Az alábbiakban a tulajdonságokat a **hozzáfűzése...**  műveletek:

| Tulajdonság | Szükséges | Érték |  Leírás | 
|----------|----------|-------|--------------| 
| Name (Név) | Igen | <*a változó neve*> | Módosíthatja a változó neve | 
| Érték | Igen | <*Hozzáfűzés-érték*> | A Hozzáfűzés, kívánt érték, amely bármilyen típusú | 
|||||  

Ha átvált a tervezőben a nézet Kódszerkesztő, Íme módja a **Hozzáfűzés tömbváltozóhoz** művelet belül a logikai alkalmazás definíciójának, amely JSON formátumban jelenik meg.
Ez a példa létrehoz egy tömbváltozó, és hozzáadja egy másik értéket, az utolsó elem a tömbben. Az eredmény az lesz egy frissített változó, amely tartalmazza ezt a tömböt: `[1,2,3,"red"]` 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* Ismerje meg [Logic Apps-összekötők](../connectors/apis-list.md)

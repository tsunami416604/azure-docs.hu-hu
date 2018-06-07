---
title: Értékek - Azure Logic Apps mentése változók létrehozása |} Microsoft Docs
description: Mentse és kezelése az Azure Logic Apps változók létrehozásával
services: logic-apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: cc464edf416a2b036d84e65e05810104d2706041
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655152"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Mentés és kezelése az Azure Logic Apps értékek változók létrehozása

Ez a cikk bemutatja, hogyan tárolhatja és változók létrehozásával a Logic Apps alkalmazást egészében meghiúsulhatnak értékekkel. Például változók segítségével hurkot futtató hányszor száma. Ha egy tömb keresztül léptetés, vagy egy adott cikk tömb ellenőrzése, segítségével változó minden tömb elemhez indexszámát hivatkozik. 

Létrehozhat például az egész szám, lebegőpontos, logikai érték, karakterlánc, tömb vagy objektum adattípusok változói. Miután létrehozott egy változót, más feladatok elvégzése, például:

* GET, vagy hivatkozzon a változó értékét.
* Növelheti vagy csökkentheti a változót egy állandó értékkel, más néven *növekmény* és *csökkentheti*.
* Rendelje hozzá a változó egy másik értéket.
* Helyezze be vagy *hozzáfűzése* egy karakterlánc vagy tömb legutóbbi, a változó értékét.

Változók és azok globális csak az őket létrehozó logic app-példány belül. Továbbá megőrzik a hurok ismétlési belül a logic app-példány között. Való hivatkozáskor az egy változót, használja a változó nevét a jogkivonatot, nem a művelet neve, amely a szokásos módon való hivatkozáshoz egy művelet kimenetének létrehozása.

Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiók</a>. 

## <a name="prerequisites"></a>Előfeltételek

Kövesse a cikkben, az alábbiakban a szükséges elemeket:

* A logikai alkalmazást, ahová változó létrehozása 

  Ha most ismerkedik a logic apps, tekintse át a [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [gyors üzembe helyezés: az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) a Logic Apps alkalmazást első lépéseként 

  Mielőtt hozzáadhatna műveleteket az változók létrehozására és kezelésére, a logikai alkalmazás eseményindítót kell kezdődnie.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Változó inicializálása

Hozzon létre egy változót, és az adattípus és a kezdeti érték – a Logic Apps alkalmazást az egy-egy művelettel belül deklarálható. Csak deklarálhatnak változók globális szinten, nem esik a hatókörök, a feltételek és a hurkok. 

1. Az a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a> vagy a Visual Studio, nyissa meg a Logic Apps alkalmazást a Logic App tervezőben. 

   Ebben a példában az Azure portál és a logikai alkalmazás egy meglévő eseményindítót használ.

2. A Logic Apps alkalmazást, ahol egy változó hozzáadni kívánt feladatütemezésekben kövesse az alábbi lépések egyikét: 

   * Az utolsó lépésben művelet hozzáadása, válassza ki a **új lépés** > **művelet hozzáadása**.

     ![művelet hozzáadása](./media/logic-apps-create-variables-store-values/add-action.png)

   * Lépések közötti művelet hozzáadása az egérmutatót a kapcsolódó nyíl ezért a pluszjel (+) jelenik meg. 
   Válassza ki a plusz jelre, és válassza a **művelet hozzáadása**.

3. A keresési mezőbe írja be a "változók" szűrőként. Válassza a műveletek listájának **változók - inicializálási változó**.

   ![Kijelölési művelet](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Ezek az információk a változó:

   | Tulajdonság | Szükséges | Érték |  Leírás |
   |----------|----------|-------|--------------|
   | Name (Név) | Igen | <*változó-neve*> | Növelje a változó neve | 
   | Típus | Igen | <*változó-típus*> | A változó az adattípus | 
   | Érték | Nem | <*Kezdőérték*> | A változó kezdeti értéke <p><p>**Tipp**: nem kötelező, állítsa ezt az értéket az ajánlott eljárás, a változó mindig tudja kezdőértékét. | 
   ||||| 

   ![Változó inicializálása](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Most már folytathatja a hozzáadni kívánt műveleteket. Amikor elkészült, a designer eszköztáron válassza **mentése**.

Ha átvált a tervező a kód nézet szerkesztése, ez a módszer a **inicializálása változó** művelet belül a logic app-definíciót, JavaScript Object Notation (JSON) formátumban jelenik meg:

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

Az alábbiakban néhány más változó típusok példái:

*Karakterlánc-változóvá*

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

*Egész számok tömb*

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

*Karakterláncok tömb*

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

Beolvasása, vagy hivatkozzon egy változó tartalmát, is használhatja a [variables() függvény](../logic-apps/workflow-definition-language-functions-reference.md#variables) a Logic App Designer és a kód nézet szerkesztése.
Való hivatkozáskor az egy változót, használja a változó nevét a jogkivonatot, nem a művelet neve, amely a szokásos módon való hivatkozáshoz egy művelet kimenetének létrehozása. 

Például ebben a kifejezésben lekérése az elemeket a tömbváltozó [ebben a cikkben korábban létrehozott](#append-value) használatával a **variables()** függvény. A **string()** függvény karakterlánc-formátum a változó tartalmát adja vissza: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Változó értékének növelése 

Növeléséhez vagy *növekmény* egy változó egy állandó értékkel adja hozzá a **változók - növekmény változó** műveletet úgy, hogy a Logic Apps alkalmazást. Ez a művelet csak egész és lebegőpontos változók működik.

1. Logic App tervezőben, ahol egy létező változó bővíteni szeretné feladatütemezésekben válasszon **új lépés** > **művelet hozzáadása**. 

   Például a logikai alkalmazás már rendelkezik egy eseményindító és egy művelet által létrehozott egy változót. Igen adjon hozzá egy új műveletet az alábbi lépéseket:

   ![művelet hozzáadása](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Meglévő lépései közötti művelet hozzáadása az egérmutatót a kapcsolódó mutató nyílra, hogy a plusz jelre (+) jelenik meg. Válassza ki a plusz jelre, és válassza a **művelet hozzáadása**.

2. A keresési mezőbe írja be a "növekmény változó" szűrőként. A műveletek listában válassza ki a **változók - növekmény változó**.

   ![Válassza ki a "Biztonsági változó" művelet](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Adja meg ezt az információt a változó növekvő:

   | Tulajdonság | Szükséges | Érték |  Leírás |
   |----------|----------|-------|--------------|
   | Name (Név) | Igen | <*változó-neve*> | Növelje a változó neve | 
   | Érték | Nem | <*növekményérték*> | Növekvő a változó értékét. Az alapértelmezett érték: egyet. <p><p>**Tipp**: nem kötelező, állítsa ezt az értéket az ajánlott eljárás, hogy mindig a meghatározott növekvő a változó értéke. | 
   |||| 

   Példa: 
   
   ![Növekvő értéket – példa](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Amikor elkészült, a designer eszköztáron válassza **mentése**. 

Ha átvált a tervező a kód nézet szerkesztése, ez a módszer a **növekmény változó** művelet belül a logic app-definíciót, JSON formátumban jelenik meg:

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

## <a name="example-create-loop-counter"></a>Példa: Hurok számlálójának létrehozása

A szám, ahányszor egy hurok futtató leltárhoz gyakran használják a változók. Ez a példa bemutatja, hogyan hozzon létre és változók használni ezt a feladatot hoz létre, amely megjeleníti a mellékleteket, e-mailben hurkot.

1. Az Azure-portálon hozzon létre egy üres logikai alkalmazást. Adjon hozzá egy eseményindító, amely az új e-mailek és a mellékleteit ellenőrzi. 

   Ez a példa az Office 365 Outlook eseményindítója a következőnek: **egy új e-mailt fogadásakor**. 
   Ehhez az eseményindítóhoz állíthat be az érvényesítést, csak ha az e-mail mellékleteket.
   Azonban az új e-mailekhez, a mellékleteket, például az Outlook.com-os összekötő, amely ellenőrzi a csatlakozókat is használhatja.

2. Válassza ki az eseményindító **speciális beállítások megjelenítése**. Beállíthatja, hogy az eseményindító mellékletek kereséséhez, és továbbítja azokat a mellékleteket a Logic Apps alkalmazást munkafolyamat **Igen** ezekhez a tulajdonságokhoz:
   
   * **Melléklettel rendelkezik** 
   * **Mellékletek is** 

   ![Ellenőrizze a és a mellékletek](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Adja hozzá a [ **inicializálása változó** művelet](#create-variable). Hozzon létre egy egész szám típusú változó nevű **száma** kezd értéke nulla.

   !["Initialize változó" műveletének hozzáadása](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Váltás az egyes mellékletek, vegye fel a *minden* kiválasztásával hurok **új lépés** > **további** > **hozzáadása egy az egyes**.

   !["Az egyes" hurkot hozzáadása](./media/logic-apps-create-variables-store-values/add-loop.png)

5. Belül a hurok, kattintson a **kimenetnek válassza az előző lépéseiből** mezőbe. Amikor megjelenik a dinamikus tartalom listába, válassza ki a **mellékletek**. 

   ![A „Mellékletek” elem kiválasztása](./media/logic-apps-create-variables-store-values/select-attachments.png)

   A **mellékletek** mező olyan tömb, amely rendelkezik az e-mail mellékletek az eseményindító kimeneti azokat a hurok továbbítja.

6. Válassza a "az egyes" hurok **művelet hozzáadása**. 

   ![Válassza a "Művelet hozzáadása"](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. A keresési mezőbe írja be a "növekmény változó" szűrőként. Válassza a műveletek listájának **változók - növekmény változó**.

   > [!NOTE]
   > Győződjön meg arról, hogy a **növekmény változó** művelet a hurkon belül jelenik meg. Ha a művelet jelenik meg a hurok kívül, húzza a művelet a hurok.

8. Az a **növekmény változó** művelet, a a **neve** listáról válassza ki a **száma** változó. 

   ![Válassza ki a "Count" változó](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. Adja hozzá a hurok bármely művelet, amely küld Önnek a Csatolmányok száma. A művelet tartalmaznak az értéket a **száma** változó, például: 

   ![Eredmények küldő művelet hozzáadása](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot. 

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás nincs engedélyezve a logic app menüben válassza a **áttekintése**. Az eszköztáron válassza **engedélyezése**. 

2. A Logic App Designer eszköztáron válassza **futtatása**. Ebben a lépésben kézzel indítja el a Logic Apps alkalmazást.

3. A használt e-mail fiókba egy vagy több mellékletet tartalmazó e-mailt küldeni ebben a példában.

   Ez a lépés akkor következik be, a logikai alkalmazás eseményindító, amely hoz létre, és a logikai alkalmazás munkafolyamat-példány futtatja.
   Ennek eredményeképpen a logic app küld Önnek egy üzenet vagy e-mailek, az e-mailben elküldött mellékleteket számát jeleníti meg.

Ha átvált a tervező a kód nézet szerkesztése, itt módja a "az egyes" hurok jelenik meg, amelyen a **növekmény változó** művelet belül a logic app-definíciót, JSON formátumban.

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

Csökkenti a vagy *csökkentheti* egy változó egy állandó értékkel, kövesse a [változó növelése](#increment-value) azzal a különbséggel, hogy keresse meg és jelölje ki a **változók - csökkentést változó**művelet helyette. Ez a művelet csak egész és lebegőpontos változók működik.

Az alábbiakban tulajdonságai között a **csökkentést változó** művelet:

| Tulajdonság | Szükséges | Érték |  Leírás |
|----------|----------|-------|--------------|
| Name (Név) | Igen | <*változó-neve*> | A csökkentenie kell a változó nevét | 
| Érték | Nem | <*növekményérték*> | A csökkenő a változó értéke. Az alapértelmezett érték: egyet. <p><p>**Tipp**: nem kötelező, állítsa ezt az értéket az ajánlott eljárás közöljük mindig csökkenő megadott értékét a változót. | 
||||| 

Ha átvált a tervező a kód nézet szerkesztése, ez a módszer a **csökkentést változó** művelet belül a logic app-definíciót, JSON formátumban jelenik meg.

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

Egy másik értéket rendelni egy létező változó, kövesse a lépéseket a [változó növelése](#increment-value) , kivéve, hogy: 

1. Keresse meg és jelölje ki a **változók - készlet változó** művelet helyette. 

2. Adja meg a változó nevét és a hozzárendelni kívánt értékét. Változó pedig az új érték a azonos típusúnak kell lennie.
Az értéket kell adni, mert ez a művelet nem tartalmazhat alapértelmezett értéket. 

Az alábbiakban tulajdonságai között a **Set változó** művelet:

| Tulajdonság | Szükséges | Érték |  Leírás | 
|----------|----------|-------|--------------| 
| Name (Név) | Igen | <*változó-neve*> | Módosítsa a változó neve | 
| Érték | Igen | <*új érték*> | Szeretne hozzárendelni a változó értéke. Mind a azonos típusúnak kell lennie. | 
||||| 

> [!NOTE]
> Csak abban az esetben növekvő vagy csökkenő változók, hurkokat belül változók módosítása *előfordulhat, hogy* váratlan eredményekhez létrehozni, mert a hurkok párhuzamosan, vagy egy időben, alapértelmezés szerint futnak. Ezekben az esetekben próbálja ki a hurok egymás után futnak beállítása. Ha szeretne hivatkozni, a változó értékét a hurok belül, és elvárják, ugyanazt az értéket a kezdő és a hurok példánynak végén, például kövesse az alábbi lépéseket a hurok működésével módosítása: 
>
> 1. A hurok jobb felső sarokban, a három ponttal (…) gombra, és válassza a **beállítások**.
> 
> 2. A **egyidejűség-vezérlési**, módosítsa a **felülbírálása alapértelmezett** beállítást **a**.
>
> 3. Húzza a **mértékben a párhuzamosság** csúszkát **1**.

Ha átvált a tervező a kód nézet szerkesztése, ez a módszer a **Set változó** művelet belül a logic app-definíciót, JSON formátumban jelenik meg. Ebben a példában a "Count" változó aktuális értéke egy másik értéket módosítja. 

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

## <a name="append-to-variable"></a>Változó hozzáfűzése

A karakterlánc vagy tömb tároló változók, beszúrásához vagy *hozzáfűzése* e karakterlánc vagy tömb utolsó eleme, a változó értékét. A lépések a [változó növelése](#increment-value) azzal a különbséggel, hogy ehelyett kövesse ezeket a lépéseket: 

1. Keresse meg és válassza ki a következő műveletek alapján-e a változó a karakterlánc vagy tömb: 

  * **Változók - karakterlánc-változóvá hozzáfűzése**
  * **Változók - tömbváltozó hozzáfűzése** 

2. Adja meg a hozzáfűzendő karakterlánc vagy tömb utolsó elemként értékét. Kötelezően megadandó érték. 

Az alábbiakban tulajdonságai között a **hozzáfűzése...**  műveletek:

| Tulajdonság | Szükséges | Érték |  Leírás | 
|----------|----------|-------|--------------| 
| Name (Név) | Igen | <*változó-neve*> | Módosítsa a változó neve | 
| Érték | Igen | <*hozzáfűzendő érték*> | A szeretne hozzáfűzni, érték, amely bármilyen | 
|||||  

Ha átvált a tervező a kód nézet szerkesztése, ez a módszer a **tömbváltozó Hozzáfűzés** művelet belül a logic app-definíciót, JSON formátumban jelenik meg.
Ebben a példában egy tömbváltozó hoz létre, és egy másik értéket hozzáadja a tömb utolsó elemként. A eredménye egy frissített változó, amely a tömb tartalmazza: `[1,2,3,"red"]` 

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
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További tudnivalók [Logic Apps-összekötők](../connectors/apis-list.md)

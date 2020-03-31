---
title: Változók létrehozása és kezelése értékek tárolására és továbbítására
description: Ismerje meg, hogyan tárolhatja, kezelheti, használhatja és adhatja át az értékeket az Azure Logic Apps alkalmazásokkal létrehozott automatizált feladatok és munkafolyamatok változóinak használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 55984082a6b287e9f7cdca005a24ef3c18032491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456693"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Értékek tárolása és kezelése változók használatával az Azure Logic Apps-ben

Ez a cikk bemutatja, hogyan hozhat létre és dolgozhat a logikai alkalmazásban értékek tárolására használt változókkal. A változók például segíthetnek nyomon követni, hogy egy ciklus hányszor fut. Egy tömb önfelhasználásához vagy egy adott elem tömbjének ellenőrzéséhez egy változó segítségével hivatkozhat az egyes tömbelemek indexszámára.

Változókat hozhat létre adattípusokhoz, például egész, lebegő, logikai, karakterlánc, tömb és objektum. Miután létrehozott egy változót, más feladatokat is végrehajthat, például:

* A változó értékének bekérése vagy hivatkozása.
* A változó növelése vagy csökkentése állandó értékkel, más néven *növekményrel* és *csökkenéssel.*
* Rendeljen egy másik értéket a változóhoz.
* A változó értékének beszúrása vagy *hozzáfűzése* karakterláncban vagy tömbben utoljára.

Változók léteznek, és globális csak a logikai alkalmazás példány, amely létrehozza őket. Emellett a logikai alkalmazáspéldányon belüli ciklusismétlések között is megmaradnak. Amikor egy változóra hivatkozik, a változó nevét használja tokenként, ne pedig a művelet nevét, amely a művelet kimenetére való hivatkozás szokásos módja.

> [!IMPORTANT]
> Alapértelmezés szerint a ciklusok egy "Minden" ciklus párhuzamosan futnak. Ha változókat használ a hurkokban, futtassa a [ciklust egymás után,](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) hogy a változók kiszámítható eredményeket adjanak vissza.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik előfizetéssel, [regisztráljon egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)

* Az a logikai alkalmazás, amelynek célja a változó létrehozása

  Ha most kezdi meg a logikai alkalmazások, tekintse át [a Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md) és a rövid [útmutató: Az első logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás első lépéseként

  Mielőtt műveleteket adhatna hozzá a változók létrehozásához és a változókhoz való munkához, a logikai alkalmazásnak egy eseményindítóval kell kezdődnie.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Változó inicializálása

Létrehozhat egy változót, és deklarálhatja annak adattípusát és kezdeti értékét – mindezt egy műveleten belül a logikai alkalmazásban. A változók at globális szinten csak deklarálhatja, a hatókörökön, feltételeken és hurkokon belül nem.

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer.

   Ez a példa az Azure Portalt és egy meglévő eseményindítóval rendelkező logikai alkalmazást használja.

1. A logikai alkalmazásban, abban a lépésben, ahol egy változót szeretne hozzáadni, kövesse az alábbi lépések egyikét: 

   * Ha az utolsó lépés alatt szeretne műveletet felvenni, válassza az **Új lépés lehetőséget.**

     ![Művelet hozzáadása](./media/logic-apps-create-variables-store-values/add-action.png)

   * Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egeret**+** a csatlakozó nyíl fölé, hogy a pluszjel ( ) jelenjen meg. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása csoport**keresőmezőjében `variables` írja be szűrőként jelölőnégyzetet. A műveletek listájában válassza a **Változó inicializálása**lehetőséget.

   ![Művelet kijelölése](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Adja meg ezt az információt a változóról az alábbiak szerint:

   | Tulajdonság | Kötelező | Érték |  Leírás |
   |----------|----------|-------|--------------|
   | **Név** | Igen | <*változónév*> | A változó neve növekményig |
   | **Típus** | Igen | <*változó típusú*> | A változó adattípusa |
   | **Érték** | Nem | <*kezdő érték*> | A változó kezdeti értéke <p><p>**Tipp**: Bár nem kötelező, állítsa be ezt az értéket ajánlott eljárásként, hogy mindig tudja a változó kezdőértékét. |
   |||||

   Példa:

   ![Változó inicializálása](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Most folytassa a kívánt műveletek hozzáadását. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

Ha a tervezőről a kódnézet-szerkesztőre vált, az **inicializálás változó** művelet e módja a logikai alkalmazás definíciójában jelenik meg, amely JavaScript-objektumjelölés (JSON) formátumban van:

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

> [!NOTE]
> Bár az **Inicializálás változó** művelet egy `variables` tömbként strukturált szakaszt hoz létre, a művelet egyszerre csak egy változót hozhat létre. Minden új változóhoz egyedi **Inicicializálni változó** művelet szükséges.

Íme néhány példa néhány más változótípusra:

*Karakterlánc változó*

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

*Tömb egész számokkal*

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

*Tömb karakterláncokkal*

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

## <a name="get-the-variables-value"></a>A változó értékének bekérése

Egy változó tartalmának beolvasásához vagy hivatkozásához használhatja a [variables() függvényt](../logic-apps/workflow-definition-language-functions-reference.md#variables) a Logic App Designer ben és a kódnézet-szerkesztőben is. Változóra való hivatkozáskor a változó nevét használja tokenként, ne pedig a művelet nevét, amely a művelet kimenetére való hivatkozás szokásos módja.

Ez a kifejezés például a [cikkben korábban létrehozott](#append-value) tömbváltozó elemeit a `variables()` függvény használatával kapja le. A `string()` függvény a változó tartalmát adja vissza karakterlánc formátumban:`"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Növekmény változó 

Ha egy változót állandó értékkel szeretne növelni vagy *növelni,* adja hozzá a **Növekmény változó** műveletet a logikai alkalmazáshoz. Ez a művelet csak egész és lebegőváltozókkal működik.

1. A Logic App Designer ben, abban a lépésben, ahol növelni szeretné egy meglévő változót, válassza az **Új lépés**lehetőséget. 

   Ez a logikai alkalmazás például már rendelkezik egy eseményindítóval és egy változót létrehozó művelettel. Így adjon hozzá egy új műveletet az alábbi lépések alá:

   ![Művelet hozzáadása](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Ha meglévő lépések közé szeretne műveletet hozzáadni, vigye az egeret a csatlakozó nyíl fölé, hogy a pluszjel (+) megjelenjen. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja be szűrőként a "növekményváltozó" kifejezést. A műveletek listájában válassza a **Növekmény változó t.**

   ![Válassza a "Növedékváltozó" műveletet](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Adja meg ezt az információt a változó növekményének megforgatásához:

   | Tulajdonság | Kötelező | Érték |  Leírás |
   |----------|----------|-------|--------------|
   | **Név** | Igen | <*változónév*> | A változó neve növekményig |
   | **Érték** | Nem | <*növekmény-érték*> | A változó növekményének növeléséhez használt érték. Az alapértelmezett érték egy. <p><p>**Tipp:** Bár nem kötelező, állítsa be ezt az értéket ajánlott eljárásként, hogy mindig tudja, hogy a változó növekményének konkrét értéke. |
   ||||

   Példa:

   ![Példa növekményértékre](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Ha elkészült, a tervező eszköztárán válassza a **Mentés gombot.**

Ha a tervezőről a kódnézet-szerkesztőre vált, az alábbiakszerint jelenik meg a **Növekmény változó** művelet a logikai alkalmazás definíciójában, amely JSON formátumban van:

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

## <a name="example-create-loop-counter"></a>Példa: Hurokszámláló létrehozása

A változókat általában a ciklusok futásának számának megszámlálására használják. Ez a példa bemutatja, hogyan hozhat létre és használhat változókat ehhez a feladathoz egy olyan hurok létrehozásával, amely megszámolja az e-mailmellékleteket.

1. Az Azure Portalon hozzon létre egy üres logikai alkalmazást. Adjon hozzá egy eseményindítót, amely ellenőrzi az új e-maileket és a mellékleteket.

   Ez a példa az Office 365 Outlook eseményindítóját használja: **Amikor új e-mail érkezik.** Beállíthatja, hogy ez az eseményindító csak akkor aktiválódjon, ha az e-mail mellékleteket tartalmaz. Azonban bármilyen összekötőt használhat, amely új e-maileket keres mellékletekkel, például a Outlook.com-összekötőt.

1. Az eseményindítóban a mellékletek ellenőrzéséhez és a mellékletek logikai alkalmazás munkafolyamatába való átadásához válassza az **Igen** lehetőséget az alábbi tulajdonságokhoz:

   * **Melléklettel rendelkezik**
   * **Mellékletek is**

   ![Mellékletek ellenőrzése és belefoglalása](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Adja hozzá az [ **Inicializálás változó** műveletet](#create-variable). Hozzon létre egy `Count` nulla kezdőértékű egész változót.

   ![Művelet hozzáadása a "Változó inicializálása" művelethez](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Az egyes mellékletek között való váltáshoz adjon hozzá *egy-t minden egyes ciklushoz.*

   1. Az **Inicializálás változó** művelet csoportban válassza az **Új lépés lehetőséget.**

   1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja `for each` be a keresési szűrőt, és válassza **az Egyes höz lehetőséget.**

      ![Adjon hozzá egy "minden" hurkot](./media/logic-apps-create-variables-store-values/add-loop.png)

1. A hurokban kattintson a Kimenet kiválasztása az **előző lépésekből** mezőben. Amikor megjelenik a dinamikus tartalomlista, válassza **a Mellékletek lehetőséget.**

   ![A „Mellékletek” elem kiválasztása](./media/logic-apps-create-variables-store-values/select-attachments.png)

   A **Mellékletek** tulajdonság egy tömböt, amely az eseményindító kimenetéről származó e-mail mellékleteket a hurokba továbbítja.

1. Az **egyes ciklusok esetében** válassza **a Művelet hozzáadása**lehetőséget.

   ![Válassza a "Művelet hozzáadása" lehetőséget.](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. A keresőmezőbe írja be szűrőként a "növekményváltozó" kifejezést. A műveletlistában válassza a **Növekmény változó t.**

   > [!NOTE]
   > Győződjön meg arról, hogy a **Növekmény változó** művelet jelenik meg a cikluson belül. Ha a művelet a cikluson kívül jelenik meg, húzza a műveletet a ciklusba.

1. A **Növekmény változó** művelet, a **Név** lista, válassza ki a **Count** változó.

   ![Válassza a "Count" változót](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. A hurok alatt adjon hozzá minden olyan műveletet, amely elküldi a mellékletek számát. A műveletben adja meg a **Count** változó értékét, például:

   ![Eredményeket küldő művelet hozzáadása](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás nincs engedélyezve, a logikai alkalmazás menüjében válassza **az Áttekintés**lehetőséget. Az eszköztáron válassza az **Engedélyezés**lehetőséget.

1. A Logic App Designer **eszközsorfuttatása**gombolása. Ez a lépés manuálisan elindítja a logikai alkalmazást.

1. E-mailt küldhet egy vagy több melléklettel a példában használt e-mail fiókba.

   Ez a lépés elindítja a logikai alkalmazás eseményindítóját, amely létrehoz és futtat egy példányt a logikai alkalmazás munkafolyamatához. Ennek eredményeképpen a logikai alkalmazás egy üzenetet vagy e-mailt küld, amely megmutatja az elküldött e-mailmellékletek számát.

Ha a tervezőről a kódnézet-szerkesztőre vált, itt van az a mód, ahogyan a **For each** ciklus megjelenik a logikai alkalmazás definícióján belüli **Növekmény változó** művelettel együtt, amely JSON formátumban van.

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

## <a name="decrement-variable"></a>Növekmény változó

Ha egy változót állandó értékkel szeretne csökkenteni vagy *csökkenteni,* kövesse a [változó növelésének](#increment-value) lépéseit, kivéve, ha megkeresi és helyette a **Decrement változó** műveletet választja. Ez a művelet csak egész és lebegőváltozókkal működik.

A **Decrement változó** művelet tulajdonságai a következők:

| Tulajdonság | Kötelező | Érték |  Leírás |
|----------|----------|-------|--------------|
| **Név** | Igen | <*változónév*> | A leállítandó változó neve | 
| **Érték** | Nem | <*növekmény-érték*> | A változó dekreálásának értéke. Az alapértelmezett érték egy. <p><p>**Tipp:** Bár nem kötelező, állítsa be ezt az értéket ajánlott eljárásként, hogy mindig tudja, hogy a változó értékének dekreálása adott értéket jelent.Tipp: Although optional, set this value as a best practice so you always know the specific value for decrementing your variable. |
||||| 

Ha a tervezőről a kódnézet-szerkesztőre vált, itt látható, hogy a **Decrement változó** művelet megjelenik a logikai alkalmazás definícióján belül, amely JSON formátumban van.

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

Ha egy meglévő változóhoz más értéket szeretne hozzárendelni, kövesse a [változó növelésének](#increment-value) lépéseit, azzal a kivételével, hogy:

1. Keresse meg és jelölje ki helyette a **Változó beállítása** műveletet.

1. Adja meg a hozzárendelni kívánt változó nevét és értékét. Mind az új értéknek, mind a változónak azonos adattípussal kell rendelkeznie. Az érték szükséges, mert ez a művelet nem rendelkezik alapértelmezett értékkel.

A **Változó beállítása** művelet tulajdonságai a következők:

| Tulajdonság | Kötelező | Érték |  Leírás |
|----------|----------|-------|--------------|
| **Név** | Igen | <*változónév*> | A módosítandó változó neve |
| **Érték** | Igen | <*új érték*> | A változóhoz rendelni kívánt érték. Mindkettőnek azonos adattípussal kell rendelkeznie. |
||||| 

> [!NOTE]
> Hacsak nem növekszik vagy csökkent a változók, a változók módosítása a hurkokon belül nem várt eredményeket *hozhat* létre, mert a hurkok alapértelmezés szerint párhuzamosan vagy egyidejűleg futnak. Ezekben az esetekben próbálja meg a hurok egymás utáni futtatását. Ha például a cikluson belüli változóértékre szeretne hivatkozni, és a cikluspéldány elején és végén azonos értéket szeretne várni, kövesse az alábbi lépéseket a ciklus futásának módosításához: 
>
> 1. A hurok jobb felső sarkában jelölje ki a három pont (**...**) gombot, majd a **Beállítások**lehetőséget.
> 
> 2. Az **Egyidejűség-vezérlés csoportban**módosítsa az **Alapértelmezett felülírás** beállítást **Be**értékre.
>
> 3. Húzza a **párhuzamosság mértékét** csúszka **1-re.**

Ha a tervezőről a kódnézet-szerkesztőre vált, itt látható, hogyan jelenik meg a **Változó beállítása** a logikai alkalmazás definíciójában, amely JSON formátumban van. Ez a `Count` példa a változó aktuális értékét egy másik értékre módosítja.

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

## <a name="append-to-variable"></a>Hozzáfűzés a változóhoz

A karakterláncokat vagy tömböket tároló változók esetében a változó értékét a karakterláncok vagy tömbök utolsó elemeként szúrhatja be vagy *fűzheti* hozzá. A [változók növelésének](#increment-value) lépéseit követheti, azzal a különbséggel, hogy ehelyett az alábbi lépéseket hajthatja végre: 

1. A műveletek egyikének megkeresése és kiválasztása annak alapján, hogy a változó karakterlánc vagy tömb: 

   * **Hozzáfűzés karakterlánc-változóhoz**
   * **Hozzáfűzés tömbváltozóhoz** 

1. Adja meg a hozzáfűzni kívánt értéket a karakterlánc vagy tömb utolsó elemeként. Kötelezően megadandó érték.

Itt vannak a tulajdonságok a **hozzáfűzés ...** műveletek:

| Tulajdonság | Kötelező | Érték |  Leírás |
|----------|----------|-------|--------------|
| **Név** | Igen | <*változónév*> | A módosítandó változó neve |
| **Érték** | Igen | <*függelék-érték*> | A hozzáfűzni kívánt érték, amely bármilyen típusú lehet |
|||||

Ha a tervezőről a kódnézet-szerkesztőre vált, itt látható, hogy a **tömbhöz való hozzáfűzés változóművelet** hogyan jelenik meg a logikai alkalmazásdefinícióban, amely JSON formátumban van. Ez a példa tömbváltozót hoz létre, és egy másik értéket ad hozzá a tömb utolsó elemeként. Az eredmény egy frissített változó, amely ezt a tömböt tartalmazza:`[1,2,3,"red"]`

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

## <a name="next-steps"></a>További lépések

* Tudnivalók a [Logic Apps-összekötőkről](../connectors/apis-list.md)

---
title: Műveletek végrehajtása adatokon
description: Adatkimenetek és -formátumok konvertálása, kezelése és kezelése az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: baa6e5732221d120ff71217a3a86a942794c53f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283939"
---
# <a name="perform-data-operations-in-azure-logic-apps"></a>Adatműveletek végrehajtása az Azure Logic Apps alkalmazásban

Ez a cikk bemutatja, hogyan dolgozhat a logikai alkalmazásokban lévő adatokkal, ha műveleteket ad hozzá ezekhez a feladatokhoz és egyebekhez:

* Táblák létrehozása tömbökből.
* Tömbök létrehozása más tömbökből egy feltétel alapján.
* Felhasználóbarát jogkivonatokat hozhat létre a JavaScript Object Notation (JSON) objektumtulajdonságaiból, így könnyedén használhatja ezeket a tulajdonságokat a munkafolyamatban.

Ha itt nem találja a kívánt műveletet, próbálja meg böngészni az Azure Logic Apps által biztosított számos különböző [adatkezelési függvényt.](../logic-apps/workflow-definition-language-functions-reference.md)

Ezek a táblázatok összefoglalja a használható adatműveleteket, és a műveletek által használt forrásadat-típusok alapján vannak rendszerezve, de minden leírás betűrendben jelenik meg.

**Tömbműveletek** 

Ezek a műveletek segítenek a tömbökben lévő adatokkal való munkában.

| Műveletek | Leírás |
|--------|-------------|
| [**CSV-tábla létrehozása**](#create-csv-table-action) | Vesszővel tagolt értéktábla (CSV) létrehozása tömbből. |
| [**HTML-táblázat létrehozása**](#create-html-table-action) | HTML-táblázat létrehozása tömbből. |
| [**Tömb szűrése**](#filter-array-action) | Tömbrészhalmaz létrehozása tömbből a megadott szűrő vagy feltétel alapján. |
| [**Csatlakozzon**](#join-action) | Hozzon létre egy karakterláncot egy tömb összes eleméből, és válassza el az egyes elemeket a megadott karakterrel. |
| [**Válassza ki**](#select-action) | Hozzon létre egy tömböt a megadott tulajdonságokból egy másik tömb összes eleméhez. |
||| 

**JSON-műveletek**

Ezek a műveletek segítenek a JavaScript-objektumjelölés (JSON) formátumú adatokkal való munkában.

| Műveletek | Leírás |
|--------|-------------|
| [**Össze**](#compose-action) | Hozzon létre egy üzenetet vagy karakterláncot több, különböző adattípussal rendelkező bemenetből. Ezután használhatja ezt a karakterláncot egyetlen bemenetként, ahelyett, hogy ismételten beírná ugyanazokat a bemeneteket. Létrehozhat például egyetlen JSON-üzenetet különböző bemenetekből. |
| [**JSON elemzés**](#parse-json-action) | Felhasználóbarát adattokeneket hozhat létre a JSON-tartalom tulajdonságaihoz, így könnyebben használhatja a logikai alkalmazások tulajdonságait. |
|||

Összetettebb JSON-átalakítások létrehozásáról a [Speciális JSON-átalakítások végrehajtása Folyékony sablonokkal](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)című témakörben olvashat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A logikai alkalmazás, ahol az adatokkal való munka során szükség van a műveletre

  Ha most kezdi meg a logikai alkalmazások, tekintse át [a Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md) és a rövid [útmutató: Az első logikai alkalmazás létrehozása.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts) a logikai alkalmazás első lépéseként 

  Az adatműveletek csak műveletekként érhetők el, így mielőtt ezeket a műveleteket használhatna, indítsa el a logikai alkalmazást egy eseményindítóval, és tartalmazza a kívánt kimenetek létrehozásához szükséges egyéb műveleteket.

<a name="compose-action"></a>

## <a name="compose-action"></a>Művelet összeállítása

Egyetlen kimenet, például egy JSON-objektum több bemenetből történő létrehozásához használhatja a **Közösségműveletet.** A bemenetek különböző típusú, például egész számok, logikai tömbök, tömbök, JSON-objektumok és bármely más natív típusú, amely az Azure Logic Apps támogatja, például a bináris és XML. Ezután használhatja a kimenetet a **Konkonálás** művelet után következő műveletekben. A **Logika** művelet azt is mentheti, hogy a logikai alkalmazás munkafolyamatának létrehozása közben ismételten ugyanazokat a bemeneteket adja meg.

Létrehozhat például egy JSON-üzenetet több változóból, például olyan karakterlánc-változókból, amelyek az emberek kereszt- és vezetéknevét tárolják, valamint egy egész változót, amely az emberek életkorát tárolja. Itt a **Compose** művelet elfogadja a következő bemeneteket:

`{ "age": <ageVar>, "fullName": "<lastNameVar>, <firstNameVar>" }`

és létrehozza ezt a kimenetet:

`{"age":35,"fullName":"Owens,Sophie"}`

Egy példa kipróbálásához kövesse az alábbi lépéseket a Logic App Designer használatával. Vagy ha inkább a kódnézet-szerkesztőben szeretne dolgozni, másolhatja a **példát, hogy készítsen belőle** változóművelet-definíciókat ebből a cikkből a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójába: **Initialize variable** [Adatművelet-kód példák – Összeállítás](../logic-apps/logic-apps-data-operations-code-samples.md#compose-action-example) 

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer.

   Ez a példa az Azure Portalt és egy logikai alkalmazást használ **egy Ismétlődés eseményindítóval** és több **inicializálási** változóművelettel. Ezek a műveletek két karakterlánc-változó és egy egész változó létrehozására vannak beállítva. Amikor később teszteli a logikai alkalmazást, manuálisan futtathatja az alkalmazást anélkül, hogy megvárana az eseményindító indítására.

   ![Mintalogikai alkalmazás indítása a "Compose" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-compose-action.png)

1. A logikai alkalmazásban, ahol létre szeretné hozni a kimenetet, kövesse az alábbi lépések egyikét: 

   * Ha az utolsó lépés alatt szeretne műveletet felvenni, válassza az **Új lépés lehetőséget.**

     ![Válassza az "Új lépés" lehetőséget a "Compose" művelethez](./media/logic-apps-perform-data-operations/add-compose-operation-action.png)

   * Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egeret a csatlakozó nyíl fölé, hogy a pluszjel (**+**) jelenjen meg. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása csoport**keresőmezőjében `compose` írja be szűrőként jelölőnégyzetet. A műveletek listájában válassza a **Közösségművelet** lehetőséget.

   ![Válassza a "Compose" műveletet](./media/logic-apps-perform-data-operations/select-compose-action.png)

1. A **Bemenetek mezőben** adja meg a kimenet létrehozásához kívánt bemeneteket.

   Ebben a példában, amikor a **Bevitelek** mezőben kattint, megjelenik a dinamikus tartalomlista, így kiválaszthatja a korábban létrehozott változókat:

   ![A "Compose" művelethez használandó bemenetek kiválasztása](./media/logic-apps-perform-data-operations/configure-compose-action.png)

   Itt van a kész példa **Összeállítás** a művelet: 

   ![Kész példa a "Compose" művelethez](./media/logic-apps-perform-data-operations/finished-compose-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

Az alapul szolgáló munkafolyamat-definícióban erről a műveletről a [Közösségművelet című témakörben](../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)talál további információt.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **Compose** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **Compose** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti az eredményeket a **Compose** műveletből.

1. Ebben a műveletben kattintson arra a helyre, ahová meg szeretné jelenjenek az eredmények. Amikor megnyílik a dinamikus tartalomlista, az **Írás** művelet alatt válassza a **Kimenet**lehetőséget.

   Ez a példa az **E-mail küldése** műveletet használja, és tartalmazza az e-mail törzsében és tárgyában lévő **Kimenet** mezőket:

   !["Kimenet" mezők a "Compose" művelethez](./media/logic-apps-perform-data-operations/send-email-compose-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás**lehetőséget.

   A használt e-mail-összekötő alapján az eredmények et kapja:

   ![E-mail a "Compose" művelet eredmények](./media/logic-apps-perform-data-operations/compose-email-results.png)

<a name="create-csv-table-action"></a>

## <a name="create-csv-table-action"></a>CSV-táblaművelet létrehozása

Ha olyan vesszővel tagolt értéktáblát (CSV) szeretne létrehozni, amely egy tömb JavaScript objektumainak (JSON) tulajdonságaival és értékeivel rendelkezik, használja a **CSV tábla létrehozása** műveletet. Ezután az eredményül kapott táblát használhatja a **CSV-tábla létrehozása** műveletet követő műveletekben.

Ha inkább a kódnézet-szerkesztőben szeretne dolgozni, másolhatja a **példát, a CSV-tábla létrehozása** és a változóművelet-definíciók **inicializálása** ebből a cikkből a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójába: [Adatműveletkód példák – CSV-tábla létrehozása](../logic-apps/logic-apps-data-operations-code-samples.md#create-csv-table-action-example)

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer.

   Ez a példa az Azure Portalt és egy logikai alkalmazást használ **egy ismétlődési** eseményindítóval és egy **Inicializálási változó** művelettel. A művelet egy olyan változó létrehozására van beállítva, amelynek kezdeti értéke olyan tömb, amely bizonyos tulajdonságokkal és értékekkel rendelkezik JSON formátumban. Amikor később teszteli a logikai alkalmazást, manuálisan futtathatja az alkalmazást anélkül, hogy megvárana az eseményindító indítására.

   ![Minta logikai alkalmazás indítása a "CSV-tábla létrehozása" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. A logikai alkalmazásban, ahol létre szeretné hozni a CSV-táblát, kövesse az alábbi lépések egyikét: 

   * Ha az utolsó lépés alatt szeretne műveletet felvenni, válassza az **Új lépés lehetőséget.**

     ![Válassza az "Új lépés" lehetőséget a "CSV-tábla létrehozása" művelethez](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egeret a csatlakozó nyíl fölé, hogy a pluszjel (**+**) jelenjen meg. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása csoport**keresőmezőjében `create csv table` írja be szűrőként jelölőnégyzetet. A műveletek listájában válassza a **CSV-tábla létrehozása** műveletet.

   ![Válassza a "CSV-tábla létrehozása" műveletet](./media/logic-apps-perform-data-operations/select-create-csv-table-action.png)

1. A **Bejelölve** mezőben adja meg a tábla létrehozásához kívánt tömböt vagy kifejezést.

   Ebben a példában, amikor a **Bejelölő** mezőre kattint, megjelenik a dinamikus tartalomlista, így kiválaszthatja a korábban létrehozott változót:

   ![Tömbkimenet kiválasztása CSV-tábla létrehozásához](./media/logic-apps-perform-data-operations/configure-create-csv-table-action.png)

   > [!TIP]
   > Ha felhasználóbarát jogkivonatokat szeretne létrehozni a JSON-objektumok tulajdonságaihoz, hogy ezeket a tulajdonságokat bemenetként kiválaszthassa, használja a [Parse JSON-t](#parse-json-action) a **CSV-tábla létrehozása** művelet hívása előtt.

   Itt van a kész példa **CsV tábla létrehozása** művelet: 

   ![Kész példa a "CSV-tábla létrehozása" művelethez](./media/logic-apps-perform-data-operations/finished-create-csv-table-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

### <a name="customize-table-format"></a>Táblázatformátum testreszabása

Alapértelmezés szerint az **Oszlopok** tulajdonság úgy van beállítva, hogy automatikusan létrehozza a táblaoszlopokat a tömbelemek alapján. Egyéni fejlécek és értékek megadásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az **Oszlopok** listát, és válassza az **Egyéni**lehetőséget.

1. A **Fejléc** tulajdonságban adja meg a helyette használandó egyéni fejlécszöveget.

1. Az **Érték** tulajdonságban adja meg a helyette használandó egyéni értéket.

Értékek visszaadásához a tömbből használhatja a [ `item()` függvényt](../logic-apps/workflow-definition-language-functions-reference.md#item) a **CSV-tábla létrehozása** művelettel. Egy `For_each` hurokban használhatja a [ `items()` funkciót.](../logic-apps/workflow-definition-language-functions-reference.md#items)

Tegyük fel például, hogy olyan táblaoszlopokat szeretne, amelyek csak a tulajdonságértékeket tartalmazják, és nem egy tömb tulajdonságneveit. Ha csak ezeket az értékeket szeretné visszaadni, kövesse az alábbi lépéseket a tervezői vagy kódnézetbeli munkához. Itt van az eredmény, hogy ez a példa visszatér:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Munka tervezői nézetben

A műveletben tartsa üresen a **Fejléc** oszlopot. Az **Érték** oszlop minden sorában vonhatja vissza a kívánt tömbtulajdonságok hivatkozását. **Az Érték** csoport minden sora a megadott tömbtulajdonság összes értékét visszaadja, és a tábla oszlopává válik.

1. Az **Érték**csoportban minden kívánt sorban kattintson a szerkesztési mezőre, hogy megjelenjen a dinamikus tartalomlista.

1. A dinamikus tartalomlistában válassza a **Kifejezés**lehetőséget.

1. A kifejezésszerkesztőben adja meg ezt a kifejezést, amely megadja a kívánt tömbtulajdonság-értéket, és válassza az **OK**gombot.

   `item()?['<array-property-name>']`

   Példa:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![A "Leírás" hivatkozásának törlése a "CSV-tábla létrehozása" esetében](./media/logic-apps-perform-data-operations/csv-table-expression.png)

1. Ismételje meg az előző lépéseket minden kívánt tömbtulajdonsághoz. Ha végzett, a művelet a következő példához hasonlóan néz ki:

   !["item()" függvény a "CSV-tábla létrehozása" mezőben](./media/logic-apps-perform-data-operations/finished-csv-expression.png)

1. A kifejezések leíróbb verziókra való feloldásához váltson kódnézetre és vissza tervezőnézetbe, majd nyissa meg újra az összecsukott műveletet:

   A **CSV-tábla létrehozása** művelet most a következő példához hasonlóan jelenik meg:

   !["CSV-tábla létrehozása" - megoldott kifejezések, fejlécek nélkül](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

#### <a name="work-in-code-view"></a>Munka kódnézetben

A művelet JSON-definíciójában a `columns` tömbön `header` belül állítsa a tulajdonságot egy üres karakterláncra. Minden `value` tulajdonság esetében a kívánt tömbtulajdonságok dereference.

1. A tervező eszköztárán válassza a **Kód nézet lehetőséget.**

1. A kódszerkesztőben a művelet `columns` tömbjében adja `header` hozzá `value` az üres tulajdonságot és ezt a kifejezést a kívánt tömbértékek minden oszlopához:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Példa:

   ```json
   "Create_CSV_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "CSV",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Váltson vissza tervezőnézetre, és nyissa meg újra az összecsukott műveletet.

   A **CSV-tábla létrehozása** művelet most a példahoz hasonlóan jelenik meg, és a kifejezések leíróbb verziókra oldódtak:

   !["CSV-tábla létrehozása" - megoldott kifejezések fejlécek nélkül](./media/logic-apps-perform-data-operations/resolved-csv-expression.png)

Az alapul szolgáló munkafolyamat-definícióban erről a műveletről a [Táblázat művelet ben](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)talál további információt.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **CSV-tábla létrehozása** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **CSV-tábla létrehozása** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti az eredményeket a **CSV-tábla létrehozása** műveletből.

1. Ebben a műveletben kattintson arra a helyre, ahová meg szeretné jelenjenek az eredmények. Amikor megnyílik a dinamikus tartalomlista, a **CSV-tábla létrehozása** művelet alatt válassza a **Kimenet**lehetőséget. 

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletet használja, és tartalmazza a **Kimenet mezőt** az e-mail törzsében:

   !["Kimenet" mezők a "CSV-tábla létrehozása" művelethez](./media/logic-apps-perform-data-operations/send-email-create-csv-table-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás**lehetőséget.

   A használt e-mail-összekötő alapján az eredmények et kapja:

   ![E-mail a "CSV-tábla létrehozása" művelet eredményekkel](./media/logic-apps-perform-data-operations/create-csv-table-email-results.png)

<a name="create-html-table-action"></a>

## <a name="create-html-table-action"></a>HTML-táblázatművelet létrehozása

Ha olyan HTML-táblázatot szeretne létrehozni, amely egy tömb JavaScript-objektumjelölési (JSON) objektumainak tulajdonságait és értékeit tartalmazza, használja a **HTML-táblázat létrehozása** műveletet. Ezután az eredményül kapott táblázatot használhatja a **HTML-táblázat létrehozása** műveletet követő műveletekben.

Ha inkább a kódnézet-szerkesztőben szeretne dolgozni, másolhatja a példát **HTML-tábla létrehozása** és a változóművelet-definíciók **inicializálása** ebből a cikkből a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójába: [Adatműveletkód példák – HTML-tábla létrehozása](../logic-apps/logic-apps-data-operations-code-samples.md#create-html-table-action-example) 

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer.

   Ez a példa az Azure Portalt és egy logikai alkalmazást használ **egy ismétlődési** eseményindítóval és egy **Inicializálási változó** művelettel. A művelet egy olyan változó létrehozására van beállítva, amelynek kezdeti értéke olyan tömb, amely bizonyos tulajdonságokkal és értékekkel rendelkezik JSON formátumban. Amikor később teszteli a logikai alkalmazást, manuálisan futtathatja az alkalmazást anélkül, hogy megvárana az eseményindító indítására.

   ![Mintalogikai alkalmazás indítása a "HTML-tábla létrehozása" számára](./media/logic-apps-perform-data-operations/sample-starting-logic-app-create-table-action.png)

1. A logikai alkalmazásban, ahol HTML-táblázatot szeretne létrehozni, kövesse az alábbi lépések egyikét:

   * Ha az utolsó lépés alatt szeretne műveletet felvenni, válassza az **Új lépés lehetőséget.**

     ![Válassza az "Új lépés" lehetőséget a "HTML-táblázat létrehozása" művelethez](./media/logic-apps-perform-data-operations/add-create-table-action.png)

   * Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egeret a csatlakozó nyíl fölé, hogy a pluszjel (**+**) jelenjen meg. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása csoport**keresőmezőjében `create html table` írja be szűrőként jelölőnégyzetet. A műveletek listájában válassza a **HTML-táblázat létrehozása** műveletet.

   ![Válassza a "HTML-táblázat létrehozása" műveletet](./media/logic-apps-perform-data-operations/select-create-html-table-action.png)

1. A **Bejelölve** mezőben adja meg a tábla létrehozásához kívánt tömböt vagy kifejezést.

   Ebben a példában, amikor a **Bejelölő** mezőre kattint, megjelenik a dinamikus tartalomlista, így kiválaszthatja a korábban létrehozott változót:

   ![Tömbkimenet kijelölése HTML-táblázat létrehozásához](./media/logic-apps-perform-data-operations/configure-create-html-table-action.png)

   > [!TIP]
   > Ha felhasználóbarát jogkivonatokat szeretne létrehozni a JSON-objektumok tulajdonságaihoz, hogy ezeket a tulajdonságokat bemenetként kiválaszthassa, használja a [Parse JSON-t](#parse-json-action) a **HTML-tábla létrehozása** művelet hívása előtt.

   Itt van a kész példa **Html tábla létrehozása** művelet:

   ![Kész példa a "HTML-táblázat létrehozása"](./media/logic-apps-perform-data-operations/finished-create-html-table-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

### <a name="customize-table-format"></a>Táblázatformátum testreszabása

Alapértelmezés szerint az **Oszlopok** tulajdonság úgy van beállítva, hogy automatikusan létrehozza a táblaoszlopokat a tömbelemek alapján. Egyéni fejlécek és értékek megadásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg az **Oszlopok** listát, és válassza az **Egyéni**lehetőséget.

1. A **Fejléc** tulajdonságban adja meg a helyette használandó egyéni fejlécszöveget.

1. Az **Érték** tulajdonságban adja meg a helyette használandó egyéni értéket.

Ha értékeket szeretne visszaadni a tömbből, használhatja a [ `item()` függvényt](../logic-apps/workflow-definition-language-functions-reference.md#item) a **HTML-táblázat létrehozása** művelettel. Egy `For_each` hurokban használhatja a [ `items()` funkciót.](../logic-apps/workflow-definition-language-functions-reference.md#items)

Tegyük fel például, hogy olyan táblaoszlopokat szeretne, amelyek csak a tulajdonságértékeket tartalmazják, és nem egy tömb tulajdonságneveit. Ha csak ezeket az értékeket szeretné visszaadni, kövesse az alábbi lépéseket a tervezői vagy kódnézetbeli munkához. Itt van az eredmény, hogy ez a példa visszatér:

```text
Apples,1
Oranges,2
```

#### <a name="work-in-designer-view"></a>Munka tervezői nézetben

A műveletben tartsa üresen a **Fejléc** oszlopot. Az **Érték** oszlop minden sorában vonhatja vissza a kívánt tömbtulajdonságok hivatkozását. **Az Érték** csoport minden sora a megadott tulajdonság összes értékét visszaadja, és a tábla oszlopává válik.

1. Az **Érték**csoportban minden kívánt sorban kattintson a szerkesztési mezőre, hogy megjelenjen a dinamikus tartalomlista.

1. A dinamikus tartalomlistában válassza a **Kifejezés**lehetőséget.

1. A kifejezésszerkesztőben adja meg ezt a kifejezést, amely megadja a kívánt tömbtulajdonság-értéket, és válassza az **OK**gombot.

   `item()?['<array-property-name>']`

   Példa:

   * `item()?['Description']`
   * `item()?['Product_ID']`

   ![Hivatkozásmentesítése tulajdonság a "HTML-tábla létrehozása" műveletben](./media/logic-apps-perform-data-operations/html-table-expression.png)

1. Ismételje meg az előző lépéseket minden kívánt tömbtulajdonsághoz. Ha végzett, a művelet a következő példához hasonlóan néz ki:

   !["item()" függvény a "HTML-táblázat létrehozása" mezőben](./media/logic-apps-perform-data-operations/finished-html-expression.png)

1. A kifejezések leíróbb verziókra való feloldásához váltson kódnézetre és vissza tervezőnézetbe, majd nyissa meg újra az összecsukott műveletet:

   A **HTML-táblázat létrehozása** művelet most a következő példához hasonlóan jelenik meg:

   !["HTML-táblázat létrehozása" - megoldott kifejezések, fejlécek nélkül](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

#### <a name="work-in-code-view"></a>Munka kódnézetben

A művelet JSON-definíciójában a `columns` tömbön `header` belül állítsa a tulajdonságot egy üres karakterláncra. Minden `value` tulajdonság esetében a kívánt tömbtulajdonságok dereference.

1. A tervező eszköztárán válassza a **Kód nézet lehetőséget.**

1. A kódszerkesztőben a művelet `columns` tömbjében adja `header` hozzá `value` az üres tulajdonságot és ezt a kifejezést a kívánt tömbértékek minden oszlopához:

   ```json
   {
      "header": "",
      "value": "@item()?['<array-property-name>']"
   }
   ```

   Példa:

   ```json
   "Create_HTML_table": {
      "inputs": {
         "columns": [
            { 
               "header": "",
               "value": "@item()?['Description']"
            },
            { 
               "header": "",
               "value": "@item()?['Product_ID']"
            }
         ],
         "format": "HTML",
         "from": "@variables('myJSONArray')"
      }
   }
   ```

1. Váltson vissza tervezőnézetre, és nyissa meg újra az összecsukott műveletet.

   A **HTML-táblázat létrehozása** művelet most a példahoz hasonlóan jelenik meg, és a kifejezések leíróbb verziókká váltak:

   !["HTML-táblázat létrehozása" - megoldott kifejezések fejlécek nélkül](./media/logic-apps-perform-data-operations/resolved-html-expression.png)

Az alapul szolgáló munkafolyamat-definícióban erről a műveletről a [Táblázat művelet ben](../logic-apps/logic-apps-workflow-actions-triggers.md#table-action)talál további információt.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **HTML-táblázat létrehozása** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **HTML-táblázat létrehozása** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti a **HTML-táblázat létrehozása** művelet eredményeit.

1. Ebben a műveletben kattintson arra a helyre, ahová meg szeretné jelenjenek az eredmények. Amikor megnyílik a dinamikus tartalomlista, a **HTML-táblázat létrehozása** művelet alatt válassza a **Kimenet**lehetőséget. 

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletet használja, és tartalmazza a **Kimenet mezőt** az e-mail törzsében:

   !["Kimenet" mezők a "HTML-tábla létrehozása" mezőihez](./media/logic-apps-perform-data-operations/send-email-create-html-table-action.png)

   > [!NOTE]
   > Ha a HTML-tábla kimenetét e-mailben adja meg, győződjön meg arról, hogy az **Is HTML tulajdonságot** **Igen-re** állította az e-mail művelet speciális beállításaiközött. Így az e-mail művelet helyesen formázza a HTML-táblázatot.

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás**lehetőséget.

   A használt e-mail-összekötő alapján az eredmények et kapja:

   ![E-mail a "HTML-táblázat létrehozása" eredményekkel](./media/logic-apps-perform-data-operations/create-html-table-email-results.png)

<a name="filter-array-action"></a>

## <a name="filter-array-action"></a>Tömbszűrési művelet

Ha egy meglévő tömbből olyan kisebb tömböt szeretne létrehozni, amely meghatározott feltételeknek megfelelő elemeket tartalmaz, használja a **Tömbszűrés** műveletet. Ezután használhatja a szűrt tömbműveleteket a **Tömb szűrése** művelet után következő műveletekben.

> [!NOTE]
> Az adott feltételben használt szűrőszöveg nem tudja figyelembe a kis- és nagybetűket. Ez a művelet nem módosíthatja a tömb elemeinek formátumát vagy összetevőit. 
> 
> A **tömbszűrő** művelet tömbkimenetének használatához vagy ezeket a műveleteket el kell fogadnia tömbként, vagy át kell alakítania a kimeneti tömböt egy másik kompatibilis formátumba.
> 
> Ha http-végpontot hív meg, és JSON-választ kap, használja a **Parse JSON** műveletet a JSON-válasz feldolgozásához. 
> Ellenkező esetben a **szűrő tömb** művelet csak a választörzset olvashatja, a JSON hasznos adat szerkezetét nem.

Ha inkább a kódnézet-szerkesztőben szeretne dolgozni, a példa **szűrőtömböt** és a változóművelet-definíciók **inicializálását** a cikkből a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójába másolhatja: [Adatműveletkód példák – Tömb szűrése](../logic-apps/logic-apps-data-operations-code-samples.md#filter-array-action-example)

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer.

   Ez a példa az Azure Portalt és egy logikai alkalmazást használ **egy ismétlődési** eseményindítóval és egy **Inicializálási változó** művelettel. A művelet egy olyan változó létrehozására van beállítva, amelynek kezdeti értéke olyan tömb, amely néhány minta egész számokkal rendelkezik. Amikor később teszteli a logikai alkalmazást, manuálisan futtathatja az alkalmazást anélkül, hogy megvárana az eseményindító indítására.

   > [!NOTE]
   > Bár ez a példa egy egyszerű egész tömböt használ, ez a művelet különösen hasznos a JSON objektumtömbök esetében, ahol az objektumok tulajdonságai és értékei alapján szűrhet.

   ![Mintalogikai alkalmazás indítása a "Tömb szűrése" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-filter-array-action.png)

1. A logikai alkalmazásban, ahol létre szeretné hozni a szűrt tömböt, kövesse az alábbi lépések egyikét: 

   * Ha az utolsó lépés alatt szeretne műveletet felvenni, válassza az **Új lépés lehetőséget.**

     ![Válassza az "Új lépés" lehetőséget a "Tömb szűrése" művelethez](./media/logic-apps-perform-data-operations/add-filter-array-action.png)

   * Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egeret a csatlakozó nyíl fölé, hogy a pluszjel (**+**) jelenjen meg. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja `filter array` be szűrőként. A műveletek listájában jelölje ki a **Tömbszűrés** műveletet.

   !["Tömb szűrése" művelet kijelölése](./media/logic-apps-perform-data-operations/select-filter-array-action.png)

1. A **From (A kapcsolat)** mezőben adja meg a szűrni kívánt tömböt vagy kifejezést.

   Ebben a példában, amikor a **Bejelölő** mezőre kattint, megjelenik a dinamikus tartalomlista, így kiválaszthatja a korábban létrehozott változót:

   ![Tömbkimenet kiválasztása a szűrt tömb létrehozásához](./media/logic-apps-perform-data-operations/configure-filter-array-action.png)

1. A feltételhez adja meg az összehasonlítandó tömbelemeket, válassza ki az összehasonlító operátort, és adja meg az összehasonlítási értéket.

   Ez a `item()` példa a tömb minden elemének elérésére használja a függvényt, míg a **Szűrő tömb** művelet olyan tömbelemeket keres, amelyek értéke nagyobb, mint egy:

   ![Kész példa a "Tömb szűrése" művelethez](./media/logic-apps-perform-data-operations/finished-filter-array-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

Az alapul szolgáló munkafolyamat-definícióban erről a műveletről a [Lekérdezési művelet című](../logic-apps/logic-apps-workflow-actions-triggers.md#query-action)témakörben talál további információt.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **Szűrő tömbművelet** létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **Tömb szűrő** műveletkimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti a **tömb szűrése** művelet eredményeit.

1. Ebben a műveletben kattintson arra a helyre, ahová meg szeretné jelenjenek az eredmények. Amikor megnyílik a dinamikus tartalomlista, válassza **a Kifejezés**lehetőséget. Ha a tömbkimenetet a **Szűrő tömb** műveletből szeretné leadni, írja be ezt a kifejezést, amely tartalmazza a **Tömbszűrő** művelet nevét:

   `@actionBody('Filter_array')`

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletet használja, és tartalmazza az **actionBody('Filter_array')** kifejezés kimeneteit az e-mail törzsében:

   ![Műveletkimenetek a "Tömb szűrése" műveletből](./media/logic-apps-perform-data-operations/send-email-filter-array-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás**lehetőséget.

   A használt e-mail-összekötő alapján az eredmények et kapja:

   ![E-mail a "Tömb szűrése" művelet eredmények](./media/logic-apps-perform-data-operations/filter-array-email-results.png)

<a name="join-action"></a>

## <a name="join-action"></a>Csatlakozás művelet

Ha olyan karakterláncot szeretne létrehozni, amely egy tömb összes elemét tartalmazná, és el szeretné különválasztani azokat egy adott határoló karakterrel, használja a **Csatlakozás** műveletet. Ezután használhatja a karakterláncot a **Csatlakozás** művelet után következő műveletekben.

Ha inkább a kódnézet-szerkesztőben szeretne dolgozni, a példa **illesztése** és a változóművelet-definíciók **inicializálása** ebből a cikkből a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójába másolhatja: [Adatműveletkód példák – Csatlakozás](../logic-apps/logic-apps-data-operations-code-samples.md#join-action-example)

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer.

   Ez a példa az Azure Portalt és egy logikai alkalmazást használ **egy ismétlődési** eseményindítóval és egy **Inicializálási változó** művelettel. Ez a művelet egy olyan változó létrehozására van beállítva, amelynek kezdeti értéke olyan tömb, amely néhány minta egész számokkal rendelkezik. Amikor később teszteli a logikai alkalmazást, manuálisan futtathatja az alkalmazást anélkül, hogy megvárana az eseményindító indítására.

   ![Mintalogikai alkalmazás indítása a "Csatlakozás" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-join-action.png)

1. A logikai alkalmazásban, ahol tömbből szeretné létrehozni a karakterláncot, kövesse az alábbi lépések egyikét:

   * Ha az utolsó lépés alatt szeretne műveletet felvenni, válassza az **Új lépés lehetőséget.**

     ![SSelect "Új lépés" a "Csatlakozás" művelethez](./media/logic-apps-perform-data-operations/new-step-add-join-action.png)

   * Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egeret a csatlakozó nyíl fölé, hogy a pluszjel (**+**) jelenjen meg. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja `join` be szűrőként. A műveletek listájában válassza a következő műveletet: **Csatlakozás**

   ![Válassza a "Csatlakozás" műveletet](./media/logic-apps-perform-data-operations/select-join-operation-action.png)

1. A **From (A mintázat)** mezőben adja meg azt a tömböt, amely a karakterláncként csatlakozni kívánt elemeket tartalmaz.

   Ebben a példában, amikor a **Bejelölő** mezőre kattint, megjelenik a dinamikus tartalomlista, amely kiválaszthatja a korábban létrehozott változót:  

   ![Tömbkimenet kiválasztása a karakterlánc létrehozásához](./media/logic-apps-perform-data-operations/configure-join-action.png)

1. A **Csatlakozás ezzel** mezőbe írja be az egyes tömbelemek elválasztásához kívánt karaktert. 

   Ez a példa kettőspontot (:) mint a szeparátor.

   ![Adja meg az elválasztó karaktert](./media/logic-apps-perform-data-operations/finished-join-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

Az alapul szolgáló munkafolyamat-definícióban erről a műveletről a [Csatlakozás művelet című témakörben](../logic-apps/logic-apps-workflow-actions-triggers.md#join-action)talál további információt.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **Csatlakozás** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **Csatlakozás** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti az eredményeket a **Csatlakozás** műveletből.

1. Ebben a műveletben kattintson arra a helyre, ahová meg szeretné jelenjenek az eredmények. Amikor megnyílik a dinamikus tartalomlista, a **Csatlakozás** művelet csoportban válassza a **Kimenet**lehetőséget. 

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletet használja, és tartalmazza a **Kimenet mezőt** az e-mail törzsében:

   !["Kimenet" mezők a "Join" művelethez](./media/logic-apps-perform-data-operations/send-email-join-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás**lehetőséget.

   A használt e-mail-összekötő alapján az eredmények et kapja:

   ![E-mail a "Csatlakozás" művelet eredményeivel](./media/logic-apps-perform-data-operations/join-send-email-results.png)

<a name="parse-json-action"></a>

## <a name="parse-json-action"></a>JSON-művelet elemzése

A JavaScript-objektumnotúcintor (JSON) tartalom tulajdonságaira való hivatkozáshoz vagy eléréséhez felhasználóbarát mezőket vagy jogkivonatokat hozhat létre ezekhez a tulajdonságokhoz a **Parse JSON** művelet használatával. Így kiválaszthatja ezeket a tulajdonságokat a dinamikus tartalomlistából, amikor bemeneteket ad meg a logikai alkalmazáshoz. Ehhez a művelethez vagy json sémát adhat meg, vagy json-sémát hozhat létre a minta JSON-tartalomból vagy hasznos adatból.

Ha inkább a kódnézet-szerkesztőben szeretne dolgozni, másolhatja a példa **Parse JSON-t,** és **inicializálhatja** a változóművelet-definíciókat ebből a cikkből a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójába: [Adatművelet-kód példák - JSON elemzés](../logic-apps/logic-apps-data-operations-code-samples.md#parse-json-action-example)

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer.

   Ez a példa az Azure Portalt és egy logikai alkalmazást használ **egy ismétlődési** eseményindítóval és egy **Inicializálási változó** művelettel. A művelet egy olyan változó létrehozására van beállítva, amelynek kezdeti értéke egy JSON-objektum, amely tulajdonságokkal és értékekkel rendelkezik. Amikor később teszteli a logikai alkalmazást, manuálisan futtathatja az alkalmazást anélkül, hogy megvárana az eseményindító indítására.

   ![Mintalogikai alkalmazás indítása "Parse JSON" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-parse-json-action.png)

1. A logikai alkalmazásban, ahol elemezni szeretné a JSON-tartalmat, kövesse az alábbi lépések egyikét:

   * Ha az utolsó lépés alatt szeretne műveletet felvenni, válassza az **Új lépés lehetőséget.**

     ![Válassza az "Új lépés" lehetőséget a "Parse JSON" művelethez](./media/logic-apps-perform-data-operations/add-parse-json-action.png)

   * Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egeret a csatlakozó nyíl fölé, hogy a pluszjel (**+**) jelenjen meg. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A keresőmezőbe írja `parse json` be szűrőként. A műveletek listájában válassza a **Parse JSON** műveletet.

   ![Válassza a "JSON-elemzés" műveletet](./media/logic-apps-perform-data-operations/select-parse-json-action.png)

1. A **Tartalom** mezőben adja meg az elemezni kívánt JSON-tartalmat.

   Ebben a példában, amikor a **Tartalom** mezőre kattint, megjelenik a dinamikus tartalomlista, így kiválaszthatja a korábban létrehozott változót:

   ![JSON-objektum kijelölése a JSON-művelet elemzéséhez](./media/logic-apps-perform-data-operations/configure-parse-json-action.png)

1. Adja meg a JSON-sémát, amely leírja az általad elemezni kívánt JSON-tartalmat.

   Ebben a példában a JSON-séma látható:

   ![JSON-séma biztosítása az elemezni kívánt JSON-objektumhoz](./media/logic-apps-perform-data-operations/provide-schema-parse-json-action.png)

   Ha nem rendelkezik a séma, akkor létrehozhatja a sémát a JSON-tartalomból, vagy *hasznos adatból,* amelyet elemez. 
   
   1. A **Parse JSON** műveletben válassza **a Mintahasznos adat használata séma létrehozásához**lehetőséget.

   1. Az **Enter (JSON-tartalom) megadása vagy beillesztése csoportban**adja meg a JSON-tartalmat, majd válassza a **Kész**lehetőséget.

      ![Adja meg a Séma létrehozásához szolgáló JSON-tartalmat](./media/logic-apps-perform-data-operations/generate-schema-parse-json-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

Az alapul szolgáló munkafolyamat-definícióban szereplő műveletről további információt a [JSON-elemzési művelet című](../logic-apps/logic-apps-workflow-actions-triggers.md)témakörben talál.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **Parse JSON** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a **Parse JSON** művelet kimenetét.

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti az eredményeket a **Parse JSON** műveletből.

1. Ebben a műveletben kattintson arra a helyre, ahová meg szeretné jelenjenek az eredmények. Amikor a dinamikus tartalomlista megnyílik, a **Parse JSON** művelet alatt kiválaszthatja a tulajdonságokat az elemzési JSON-tartalomból.

   Ez a példa az Office 365 Outlook **E-mail küldése** műveletet használja, és tartalmazza a **Keresztnév**, **Vezetéknév**és **E-mail** mezőket az e-mail törzsében:

   ![JSON-tulajdonságok az "E-mail küldése" műveletben](./media/logic-apps-perform-data-operations/send-email-parse-json-action.png)

   Itt van a kész e-mail fellépés:

   ![Kész példa az e-mail művelethez](./media/logic-apps-perform-data-operations/send-email-parse-json-action-2.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás**lehetőséget. 

   A használt e-mail-összekötő alapján az eredmények et kapja:

   ![E-mail a "Parse JSON" akcióeredmények](./media/logic-apps-perform-data-operations/parse-json-email-results.png)

<a name="select-action"></a>

## <a name="select-action"></a>Művelet kijelölése

Ha olyan tömböt szeretne létrehozni, amelynek JSON-objektumai egy meglévő tömb értékeiből épülnek fel, használja a **Kijelölés** műveletet. Létrehozhat például egy JSON-objektumot egy egész tömb minden egyes értékéhez, ha megadja az egyes JSON-objektumok által rendelkeznie kell tulajdonságokat, és hogyan kell a forrástömb értékeit ezekhez a tulajdonságokhoz hozzáképezni. És bár a JSON-objektumok összetevőit módosíthatja, a kimeneti tömb mindig ugyanannyi elemet tartalmaz, mint a forrástömb.

> [!NOTE]
> A **Select** művelet tömbkimenetének használatához vagy ezeket a műveleteket el kell fogadnia tömbként, vagy át kell alakítania a kimeneti tömböt egy másik kompatibilis formátumba. 

Ha inkább a kódnézet-szerkesztőben szeretne dolgozni, a példát a változóművelet-definíciók **kiválasztása** és **inicializálása** a cikkből a saját logikai alkalmazás alapjául szolgáló munkafolyamat-definíciójába másolja: [Adatműveletkód példák – Kijelölés](../logic-apps/logic-apps-data-operations-code-samples.md#select-action-example) 

1. Az [Azure Portalon](https://portal.azure.com) vagy a Visual Studio, nyissa meg a logikai alkalmazást a Logic App Designer.

   Ez a példa az Azure Portalt és egy logikai alkalmazást használ **egy ismétlődési** eseményindítóval és egy **Inicializálási változó** művelettel. A művelet egy olyan változó létrehozására van beállítva, amelynek kezdeti értéke olyan tömb, amely néhány minta egész számokkal rendelkezik. Amikor később teszteli a logikai alkalmazást, manuálisan futtathatja az alkalmazást anélkül, hogy megvárana az eseményindító indítására.

   ![Mintalogikai alkalmazás indítása a "Kijelölés" művelethez](./media/logic-apps-perform-data-operations/sample-starting-logic-app-select-action.png)

1. A logikai alkalmazásban, ahol létre szeretné hozni a tömböt, kövesse az alábbi lépések egyikét: 

   * Ha az utolsó lépés alatt szeretne műveletet felvenni, válassza az **Új lépés lehetőséget.**

     ![Válassza az "Új lépés" lehetőséget a "Kijelölés" művelethez](./media/logic-apps-perform-data-operations/add-select-operation-action.png)

   * Ha lépéseket szeretne hozzáadni a lépések közé, vigye az egeret a csatlakozó nyíl fölé, hogy a pluszjel (**+**) jelenjen meg. Jelölje ki a pluszjelet, majd válassza **a Művelet hozzáadása**lehetőséget.

1. A **Művelet kiválasztása** **csoportban**válassza a Beépített lehetőséget. A keresőmezőbe írja `select` be szűrőként. A műveletek listájában válassza a **Kijelölés** műveletet.

   ![A "Kijelölés" művelet kijelölése](./media/logic-apps-perform-data-operations/select-select-action.png)

1. A **Forrás** mezőben adja meg a kívánt forrástömböt.

   Ebben a példában, amikor a **Bejelölő** mezőre kattint, megjelenik a dinamikus tartalomlista, így kiválaszthatja a korábban létrehozott változót:

   ![Forrástömb kijelölése a Kijelölés művelethez](./media/logic-apps-perform-data-operations/configure-select-action.png)

1. A **Térkép** mező bal oldali oszlopában adja meg a forrástömb egyes értékeit hozzárendelni kívánt tulajdonságnevet. A jobb oldali oszlopban adjon meg egy kifejezést, amely a tulajdonsághoz rendelni kívánt értéket jelöli.

   Ez a példa a "Product_ID" értéket adja meg az egész tömb `item()` minden értékének hozzárendeléséhez az egyes tömbökhöz hozzáférő kifejezés függvényének használatával. 

   ![Adja meg a JSON objektumtulajdonságot és értékeket tömb létrehozásához](./media/logic-apps-perform-data-operations/configure-select-action-2.png)

   Itt van a kész akció:

   ![Kész példa a "Kijelölés" művelethez](./media/logic-apps-perform-data-operations/finished-select-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés gombot.**

A műveletről az alapul szolgáló munkafolyamat-definícióban további információt a [Művelet kiválasztása című témakörben talál.](../logic-apps/logic-apps-workflow-actions-triggers.md)

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

Annak ellenőrzéséhez, hogy a **Kijelölés** művelet létrehozza-e a várt eredményeket, küldjön magának egy értesítést, amely tartalmazza a Kijelölés művelet **kimenetét.**

1. A logikai alkalmazásban adjon hozzá egy műveletet, amely elküldheti az eredményeket a **Kijelölés** műveletből.

1. Ebben a műveletben kattintson arra a helyre, ahová meg szeretné jelenjenek az eredmények. Amikor megnyílik a dinamikus tartalomlista, válassza **a Kifejezés**lehetőséget. Ha a **Kijelölés** műveletből szeretné leadni a tömbkimenetet, írja be ezt a kifejezést, amely tartalmazza a **Kijelölés** művelet nevét:

   `@actionBody('Select')`

   Ez a példa az Office 365 Outlook **E-mail** `@actionBody('Select')` küldése műveletet használja, és tartalmazza az e-mail törzsében lévő kifejezés kimeneteit:

   ![Műveletkimenetek a "Kijelölés" műveletből](./media/logic-apps-perform-data-operations/send-email-select-action.png)

1. Most manuálisan futtassa a logikai alkalmazást. A tervező eszköztárán válassza a **Futtatás**lehetőséget.

   A használt e-mail-összekötő alapján az eredmények et kapja:

   ![E-mail a "Select" művelet eredményeivel](./media/logic-apps-perform-data-operations/select-email-results.png)

## <a name="next-steps"></a>További lépések

* Tudnivalók a [Logic Apps-összekötőkről](../connectors/apis-list.md)

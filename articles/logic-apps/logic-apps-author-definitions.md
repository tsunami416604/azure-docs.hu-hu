---
title: Logikai alkalmazás JSON-munkafolyamat-definícióinak létrehozása, szerkesztése és kiterjesztése
description: A logikai alkalmazás JSON-munkafolyamat-definícióinak írása, szerkesztése és kiterjesztése az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/01/2018
ms.openlocfilehash: 0f5f01c757bf651beddaa76fc3eb8046b21b31eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979392"
---
# <a name="create-edit-or-extend-json-for-logic-app-workflow-definitions-in-azure-logic-apps"></a>JSON létrehozása, szerkesztése és kiterjesztése a logikai alkalmazások munkafolyamat-definícióihoz az Azure Logic Apps alkalmazásban

Amikor az [Azure Logic Apps](../logic-apps/logic-apps-overview.md)automatikus munkafolyamatokkal vállalati integrációs megoldásokat hoz létre, az alapul szolgáló logikai alkalmazásdefiníciók egyszerű és deklaratív JavaScript-objektumnottudomású (JSON) és a [munkafolyamat-definíciós nyelv (WDL) sémát](../logic-apps/logic-apps-workflow-definition-language.md) használnak a leírásukhoz és az érvényesítésükhöz. Ezek a formátumok megkönnyítik a logikai alkalmazásdefiníciók olvasását és megértését anélkül, hogy sokat tudnánk a kódról.
Ha automatizálni szeretné a logikai alkalmazások létrehozását és üzembe helyezését, a logikai alkalmazásdefiníciókat [Azure-erőforrásokként](../azure-resource-manager/management/overview.md) is [felveheti az Azure Resource Manager-sablonokba.](../azure-resource-manager/templates/overview.md)
Logikai alkalmazások létrehozásához, kezeléséhez és üzembe helyezéséhez ezután használhatja az [Azure PowerShellt,](https://docs.microsoft.com/powershell/module/az.logicapp)az [Azure CLI-t](../azure-resource-manager/templates/deploy-cli.md)vagy az [Azure Logic Apps REST API-kat.](https://docs.microsoft.com/rest/api/logic/)

Ha a JSON-ban logikai alkalmazásdefiníciókkal szeretne dolgozni, nyissa meg a Kódnézet-szerkesztőt, amikor az Azure Portalon vagy a Visual Studióban dolgozik, vagy másolja a definíciót a kívánt szerkesztőbe.
Ha még nem ért idát a logikai alkalmazások, tekintse át, [hogyan hozhat létre az első logikai alkalmazást.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

> [!NOTE]
> Egyes Azure Logic Apps-képességek, például a paraméterek definiálása és több eseményindítók a logikai alkalmazás definícióiban, csak a JSON-ban érhetők el, a Logic Apps Designer ben nem.
> Tehát ezekhez a feladatokhoz kódnézetben vagy más szerkesztőben kell dolgoznia.

## <a name="edit-json---azure-portal"></a>JSON szerkesztése – Azure-portál

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra.</a>

2. A bal oldali menüben válassza a **Minden szolgáltatás lehetőséget.**
A keresőmezőben keresse meg a "logikai alkalmazásokat", majd az eredmények közül válassza ki a logikai alkalmazást.

3. A logikai alkalmazás menüjében, a **Fejlesztői eszközök**csoportban válassza a **Logikai alkalmazáskód nézet lehetőséget.**

   Megnyílik a Kódnézet-szerkesztő, és json formátumban jeleníti meg a logikai alkalmazás definícióját.

## <a name="edit-json---visual-studio"></a>Szerkesztés JSON - Visual Studio

Mielőtt a Visual Studio-ban dolgozna a logikai alkalmazás definícióján, győződjön meg arról, hogy [telepítette a szükséges eszközöket.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites)
Ha logikai alkalmazást szeretne létrehozni a Visual Studio alkalmazással, tekintse át [a rövid útmutatót: Feladatok és folyamatok automatizálása az Azure Logic Apps – Visual Studio segítségével.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)

A Visual Studióban megnyithatja a létrehozott és üzembe helyezett logikai alkalmazásokat közvetlenül az Azure Portalról, vagy a Visual Studio Azure Resource Manager-projektjeiként.

1. Nyissa meg a Visual Studio-megoldást vagy az [Azure Resource Group](../azure-resource-manager/management/overview.md) projektet, amely a logikai alkalmazást tartalmazza.

2. Keresse meg és nyissa meg a logikai alkalmazás definícióját, amely alapértelmezés szerint egy **LogicApp.json**nevű [Erőforrás-kezelő sablonban](../azure-resource-manager/templates/overview.md)jelenik meg.
Ezt a sablont használhatja és testreszabhatja a különböző környezetekben történő telepítéshez.

3. Nyissa meg a logikai alkalmazás definíciójának és sablonjának helyi menüjét.
Válassza a lehetőséget az elem **megnyitására a Logikaialkalmazás-tervezővel**.

   ![Logikai alkalmazás megnyitása Visual Studio-megoldásban](./media/logic-apps-author-definitions/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a Visual Studio legújabb frissítéseivel.

4. A tervező alján válassza a **Kódnézet lehetőséget.**

   Megnyílik a Kódnézet-szerkesztő, és json formátumban jeleníti meg a logikai alkalmazás definícióját.

5. A tervezőnézethez való visszatéréshez a Kódnézet-szerkesztő alján válassza a **Tervezés**lehetőséget.

## <a name="parameters"></a>Paraméterek

A központi telepítési életciklus általában különböző környezetekben fejlesztési, tesztelési, átmeneti és éles környezetben. Ha olyan értékekkel rendelkezik, amelyeket a logikai alkalmazás ban hardcoding nélkül szeretne újra felhasználni, vagy amelyek a központi telepítési igényektől függően változnak, létrehozhat egy [Azure Resource Manager-sablont](../azure-resource-manager/management/overview.md) a munkafolyamat-definícióhoz, hogy automatizálhassa a logikai alkalmazások központi telepítését is.

Az alábbi általános lépések végrehajtásával *paraméterezi*a , vagy definiálja és használja a paramétereket ezekhez az értékekhez. Ezután az értékeket egy külön paraméterfájlban adja meg, amely ezeket az értékeket továbbítja a sablonnak. Így könnyebben módosíthatja ezeket az értékeket anélkül, hogy frissítenie kellene és újra kellene telepítenie a logikai alkalmazást. További részletek: [Áttekintés: A logikai alkalmazások üzembe helyezésének automatizálása az Azure Resource Manager-sablonokkal című témakörben.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

1. A sablonban adja meg a sablon paramétereket és a munkafolyamat-definíciós paramétereket a központi telepítéskor és a futásidőben használandó értékek elfogadásához.

   A sablonparaméterek a munkafolyamat-definíción kívül lévő paraméterek szakaszban vannak definiálva, míg a munkafolyamat-definícióparaméterei a munkafolyamat-definíción belüli paraméterek szakaszban vannak definiálva.

1. Cserélje le a kódolt értékeket olyan kifejezésekre, amelyek ezekre a paraméterekre hivatkoznak. A sablonkifejezések a munkafolyamat-definíciós kifejezésektől eltérő szintaxist használnak.

   Ne bonyolítsa a kódot, ha nem használja a sablonkifejezéseket, amelyeket a telepítéskor kiértékelnek, a munkafolyamat-definíciós kifejezéseken belül, amelyeket futásidőben értékel ki. Csak a munkafolyamat-definíción kívüli sablonkifejezéseket használjon. Csak munkafolyamat-definíciós kifejezéseket használjon a munkafolyamat-definíción belül.

   Amikor megadja a munkafolyamat-definícióparamétereinek értékeit, hivatkozhat a sablonparaméterekre a paraméterek szakasz használatával, amely kívül esik a munkafolyamat-definícióján, de még mindig a logikai alkalmazás erőforrás-definícióján belül van. Így sablonparaméter-értékeket adhat át a munkafolyamat-definícióparamétereinek.

1. Tárolja a paraméterek értékeit egy külön [paraméterfájlban,](../azure-resource-manager/templates/parameter-files.md) és adja meg a fájlt a központi telepítésnek.

## <a name="process-strings-with-functions"></a>Folyamatok feldolgozása függvényekkel

A Logic Apps különböző funkciókkal rendelkezik a karakterláncokkal való munkához.
Tegyük fel például, hogy egy vállalatnevet szeretne átadni egy rendelésből egy másik rendszernek.
A karakterkódolás megfelelő kezelésében azonban nem biztos.
Ezt a karakterláncot base64 kódolással is elvégezheti, de az URL-címben való kiszabadulások elkerülése érdekében több karaktert is lecserélhet. Emellett csak egy részkarakterláncra van szükség a vállalat nevéhez, mert az első öt karakter nincs használatban.

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder1",
        "companyName": "NAME=Contoso"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "request": {
      "type": "Request",
      "kind": "Http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
      }
    }
  },
  "outputs": {}
}
```

Ezek a lépések azt ismertetik, hogy ez a példa hogyan dolgozza fel ezt a karakterláncot, belülről és kívülről dolgozva:

```
"uri": "https://www.example.com/?id=@{replace(replace(base64(substring(parameters('order').companyName,5,sub(length(parameters('order').companyName), 5) )),'+','-') ,'/' ,'_' )}"
```

1. [`length()`](../logic-apps/logic-apps-workflow-definition-language.md) A cégnév bekésezése, így a karakterek teljes számát kapja meg.

2. Ha rövidebb karakterláncot szeretne `5`kapni, vonja ki a kifejezést.

3. Most kap [`substring()`](../logic-apps/logic-apps-workflow-definition-language.md)egy .
Kezdje az `5`indexnél, és lépjen a karakterlánc többi részére.

4. A részkarakterlánc átalakítása [`base64()`](../logic-apps/logic-apps-workflow-definition-language.md) karakterláncsá.

5. Most [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) az `+` összes `-` karakter karakter.

6. Végül, [`replace()`](../logic-apps/logic-apps-workflow-definition-language.md) az `/` összes `_` karakter karakter.

## <a name="map-list-items-to-property-values-then-use-maps-as-parameters"></a>Listaelemek hozzárendelése tulajdonságértékekhez, majd leképezések használata paraméterekként

Ha egy tulajdonság értékét különböző eredmények alapján szeretné elérni, létrehozhat egy olyan leképezést, amely megfelel az egyes tulajdonságértékeknek egy eredménynek, majd ezt a leképezést használja paraméterként.

Ez a munkafolyamat például bizonyos kategóriákat paraméterekként és egy olyan leképezésként határoz meg, amely megfelel ezeknek a kategóriáknak egy adott URL-címmel.
Először is, a munkafolyamat leveszi a cikkek listáját. Ezután a munkafolyamat a térkép segítségével megkeresi az egyes cikkelyek kategóriájának megfelelő URL-címet.

*   A [`intersection()`](../logic-apps/logic-apps-workflow-definition-language.md) függvény ellenőrzi, hogy a kategória megfelel-e egy ismert meghatározott kategóriának.

*   Miután megkapta az egyező kategóriát, a példa szögletes zárójelek használatával lekéri az elemet a térképről:`parameters[...]`

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "specialCategories": {
      "defaultValue": [
        "science",
        "google",
        "microsoft",
        "robots",
        "NSA"
      ],
      "type": "Array"
    },
    "destinationMap": {
      "defaultValue": {
        "science": "https://www.nasa.gov",
        "microsoft": "https://www.microsoft.com/en-us/default.aspx",
        "google": "https://www.google.com",
        "robots": "https://en.wikipedia.org/wiki/Robot",
        "NSA": "https://www.nsa.gov/"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "Request",
      "kind": "http"
    }
  },
  "actions": {
    "getArticles": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://ajax.googleapis.com/ajax/services/feed/load?v=1.0&q=https://feeds.wired.com/wired/index"
      }
    },
    "forEachArticle": {
      "type": "foreach",
      "foreach": "@body('getArticles').responseData.feed.entries",
      "actions": {
        "ifGreater": {
          "type": "if",
          "expression": "@greater(length(intersection(item().categories, parameters('specialCategories'))), 0)",
          "actions": {
            "getSpecialPage": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('destinationMap')[first(intersection(item().categories, parameters('specialCategories')))]"
              }
            }
          }
        }
      },
      "runAfter": {
        "getArticles": [
          "Succeeded"
        ]
      }
    }
  }
}
```

## <a name="get-data-with-date-functions"></a>Adatok bekéselése dátumfüggvényekkel

Ha olyan adatforrásból szeretne adatokat beszerezni, amely nem támogatja az *eseményindítókat,* használhatja a Dátum függvényeket az időpontok és dátumok használatához.
Ez a kifejezés például azt vizsgálja meg, hogy mennyi ideig tartanak a munkafolyamat lépései, belülről és kívülről dolgozva:

``` json
"expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
```

1. Az `order` műveletből bontsa ki a műveletből a `startTime`.
2. Az aktuális idő `utcNow()`betöltése a segítségével
3. Vonjon ki egy másodpercet:

   [`addseconds(..., -1)`](../logic-apps/logic-apps-workflow-definition-language.md)

   Használhatja más időegységek, `minutes` mint `hours`a vagy .

3. Most összehasonlíthatja ezt a két értéket.

   Ha az első érték kisebb, mint a második érték, akkor több mint egy másodperc telt el a rendelés első elhelyezése óta.

A dátumok formázásához használhatja a karakterlánc formatters.To format dates, you can use string formatters. Az RFC1123 lekérni például [`utcnow('r')`](../logic-apps/logic-apps-workflow-definition-language.md)a használatát.
További információ a [dátumformázásról.](../logic-apps/logic-apps-workflow-definition-language.md)

``` json
{
  "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "order": {
      "defaultValue": {
        "quantity": 10,
        "id": "myorder-id"
      },
      "type": "Object"
    }
  },
  "triggers": {
    "Request": {
      "type": "request",
      "kind": "http"
    }
  },
  "actions": {
    "order": {
      "type": "Http",
      "inputs": {
        "method": "GET",
        "uri": "https://www.example.com/?id=@{parameters('order').id}"
      }
    },
    "ifTimingWarning": {
      "type": "If",
      "expression": "@less(actions('order').startTime,addseconds(utcNow(),-1))",
      "actions": {
        "timingWarning": {
          "type": "Http",
          "inputs": {
            "method": "GET",
            "uri": "https://www.example.com/?recordLongOrderTime=@{parameters('order').id}&currentTime=@{utcNow('r')}"
          }
        }
      },
      "runAfter": {
        "order": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="next-steps"></a>További lépések

* [Feltételek (feltételes utasítások) alapján lépések futtatása](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (kapcsolóutasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Lépések futtatása és ismétlése (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Lépések futtatása csoportosított műveletállapot (hatókörök) alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
* További információ az [Azure Logic Apps munkafolyamat-definíciós nyelvi sémájáról](../logic-apps/logic-apps-workflow-definition-language.md)
* További információ az [Azure Logic Apps munkafolyamat-műveleteiről és eseményindítóiról](../logic-apps/logic-apps-workflow-actions-triggers.md)

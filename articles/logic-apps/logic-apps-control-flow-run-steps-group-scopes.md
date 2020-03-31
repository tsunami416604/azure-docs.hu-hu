---
title: Műveletek csoportosítása és futtatása hatókör szerint
description: Az Azure Logic Apps csoportállapota alapján futó hatókörrel rendelkező műveletek létrehozása
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: b84db69f79b1611347a4c55d929e5426141e7ac6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74791488"
---
# <a name="run-actions-based-on-group-status-by-using-scopes-in-azure-logic-apps"></a>Műveletek futtatása a csoport állapota alapján az Azure Logic Apps hatókörei használatával

Ha a műveleteket csak egy másik műveletcsoport sikeres vagy sikertelen után szeretné futtatni, csoportosítsa ezeket a műveleteket egy *hatókörön*belül. Ez a struktúra akkor hasznos, ha a műveleteket logikai csoportként szeretné rendszerezni, kiszeretné értékelni a csoport állapotát, és a hatókör állapotán alapuló műveleteket hajt végre. Miután a hatókör összes művelete befejeződik, a hatókör saját állapotot is kap. A hatókört például használhatja, ha [kivétel- és hibakezelést](../logic-apps/logic-apps-exception-handling.md#scopes)szeretne megvalósítani. 

A hatókör állapotának ellenőrzéséhez ugyanazokat a feltételeket használhatja, mint a logikai alkalmazások futtatási állapotának meghatározásához, például "Sikeres", "Sikertelen", "Megszakítva" és így tovább. Alapértelmezés szerint, ha a hatókör összes művelete sikeres, a hatókör állapota "Sikeres" jelöléssel van ellátva. De ha a hatókör bármely művelete sikertelen vagy megszakad, a hatókör állapota "Sikertelen" jelöléssel van ellátva. A hatókörök korlátait lásd: [Korlátok és konfiguráció.](../logic-apps/logic-apps-limits-and-config.md) 

Például itt van egy magas szintű logikai alkalmazás, amely egy hatókört használ adott műveletek futtatásához, és egy feltételt a hatókör állapotának ellenőrzéséhez. Ha a hatókör bármely művelete váratlanul sikertelen ül vagy végződik, a hatókör "Sikertelen" vagy "Megszakított" jelölésű, és a logikai alkalmazás "Hatókör sikertelen" üzenetet küld. Ha az összes hatókörrel végzett művelet sikeres, a logikai alkalmazás "Hatókör sikeres" üzenetet küld.

!["Ütemezés - Ismétlődés" eseményindító beállítása](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő példa követéséhez az alábbi elemekre van szükség:

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* E-mail fiók bármely, a Logic Apps által támogatott e-mail szolgáltatótól. Ez a példa Outlook.com használ. Ha másik szolgáltatót használ, az általános folyamat ugyanaz marad, de a felhasználói felület másként jelenik meg.

* Bing Maps kulcs. A kulcs beszerezése a <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Bing Térképek kulcs beszerezhető.</a>

* Alapvető ismeretek [a logikai alkalmazások létrehozásához](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Mintalogikai alkalmazás létrehozása

Először hozza létre ezt a mintalogikai alkalmazást, hogy később is hozzáadhategy hatókört:

![Mintalogikai alkalmazás létrehozása](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* **Ütemezés – Ismétlődés** eseményindító, amely a Bing Maps szolgáltatást a megadott időközönként ellenőrzi
* **Bing térképek – Útvonalművelet,** amely két hely közötti utazási időt ellenőriz
* Feltételes nyilatkozat, amely ellenőrzi, hogy az utazási idő meghaladja-e a megadott utazási időt
* Olyan művelet, amely e-mailt küld, hogy az aktuális utazási idő meghaladja a megadott időt

A logikai alkalmazást bármikor mentheti, ezért gyakran mentheti a munkáját.

1. Jelentkezzen be az <a href="https://portal.azure.com" target="_blank">Azure Portalra,</a>ha még nem tette meg. Üres logikai alkalmazás létrehozása.

1. Az **Ütemezés - Ismétlődés** eseményindító hozzáadása a következő beállításokkal: **Interval** = "1" és **Frequency** = "Minute"

   !["Ütemezés - Ismétlődés" eseményindító beállítása](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Ha vizuálisan egyszerűsíteni szeretné a nézetet, és el szeretné rejteni az egyes műveletek részleteit a tervezőben, csukja össze az egyes műveletek alakzatait, ahogy végighalad ezekkel a lépésekkel.

1. A **Bing-térképek hozzáadása – Útvonalművelet beszerezni.** 

   1. Ha még nem rendelkezik Bing Maps-kapcsolattal, a rendszer kéri, hogy hozzon létre egy kapcsolatot.

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **Kapcsolat neve** | BingMapsConnection | Adja meg a kapcsolat nevét. | 
      | **API-kulcs** | <*a-Bing-Maps-kulcs*> | Adja meg a korábban kapott Bing Térképek-kulcsot. | 
      ||||  

   1. Állítsa be az **Útvonal beadása** műveletet az alábbi táblázat ban látható módon:

      ![A "Bing Térképek – Útvonal bemásolása" művelet beállítása](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      További információk ezekről a paraméterekről: [Útvonal kiszámítása](https://msdn.microsoft.com/library/ff701717.aspx).

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **1. útvonalpont** | <*Elkezd*> | Adja meg az útvonal eredetét. | 
      | **2. útvonalpont** | <*Végén*> | Adja meg az útvonal úti célját. | 
      | **Elkerülés** | None | Adja meg az okat, amelyeket el kell kerülni az útvonalon, például autópályákat, útdíjakat és így tovább. A lehetséges értékekről az [Útvonal számítása](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimalizálja** | timeWithTraffic | Válasszon ki egy paramétert az útvonal optimalizálásához, például a távolságot, az időt az aktuális forgalmi információkkal és így tovább. Ez a példa ezt az értéket használja: "timeWithTraffic" | 
      | **Távolság mértékegysége** | <*az ön által előnyben részesített*> | Adja meg az útvonal kiszámításához a távolság mértékegységét. Ez a példa ezt az értéket használja: "Mile" | 
      | **Közlekedési mód** | Vezetés | Adja meg az útvonal utazási módját. Ez a példa ezt az értéket használja a "Driving" értékre. | 
      | **Tranzit dátuma és időpontja** | None | Csak tranzitmódra vonatkozik. | 
      | **Tranzit dátum-típus típusa** | None | Csak tranzitmódra vonatkozik. | 
      ||||  

1. [Adjon hozzá egy feltételt,](../logic-apps/logic-apps-control-flow-conditional-statement.md) amely ellenőrzi, hogy az aktuális utazási idő a forgalommal meghaladja-e a megadott időt. 
   Ebben a példában kövesse az alábbi lépéseket:

   1. Nevezze át a feltételt ezzel a leírással: **Ha a forgalmi idő több, mint a megadott idő**

   1. A bal szélső oszlopban kattintson az **Érték kiválasztása** mezőben, hogy a dinamikus tartalomlista megjelenjen. Ebből a listából válassza ki az **Utazási időtartam forgalom** mezőt, amely másodpercben jelenik meg. 

      ![Feltétel létrehozása](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. A középső mezőben válassza ki ezt az operátort: **nagyobb, mint**

   1. A jobb szélső oszlopban adja meg ezt az összehasonlítási értéket, amely másodpercben van megadva, és 10 percnek felel meg: **600**

      Ha elkészült, a feltétel a következő példához hasonlít:

      ![Kész állapot](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Az **Ha igaz** ágban adjon hozzá egy "küldési e-mail" műveletet az e-mail szolgáltatóhoz. 
   A művelet et a kép alábbi lépései vel állíthatja be:

   !["E-mail küldése" művelet hozzáadása az "Ha igaz" ághoz](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. A **Címzett** mezőben adja meg e-mail címét tesztelési célokra.

   1. A **Tárgy** mezőbe írja be a következő szöveget:

      ```Time to leave: Traffic more than 10 minutes```

   1. A **Törzs** mezőbe írja be ezt a záró szóközt tartalmazó szöveget: 

      ```Travel time:```

      Amíg a kurzor megjelenik a **Törzs** mezőben, a dinamikus tartalomlista nyitva marad, így kiválaszthatja az ezen a ponton elérhető paramétereket.

   1. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget.

   1. Keresse meg és válassza ki a **div()** függvényt. 
      Helyezze a kurzort a függvény zárójeleibe.

   1. Amíg a kurzor a függvény zárójelein belül van, válassza a **Dinamikus tartalom** lehetőséget, hogy a dinamikus tartalomlista megjelenjen. 
   
   1. Az **Útvonal leválasztása** csoportban válassza ki a **Forgalom időtartama forgalom mezőt.**

      ![Válassza a "Forgalom időtartama forgalom" lehetőséget.](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Miután a mező JSON formátumra oldódik,```,```adjon hozzá ```60``` egy **vesszőt** ( ) , majd a számot, hogy a **Forgalom időtartama forgalom ban** az értéket másodpercről percre konvertálja. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      A kifejezés most így néz ki:

      ![Befejezés kifejezés](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Ha elkészült, válassza az **OK** gombot.

   <!-- markdownlint-disable MD038 -->
   1. Miután a kifejezés feloldása, adja hozzá ezt a szöveget egy sorköz:``` minutes```
  
       A **Body** mező most így néz ki:

       ![Kész "Body" mező](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy hatókört, hogy csoportosíthassa a konkrét műveleteket, és kiértékelhesse azok állapotát.

## <a name="add-a-scope"></a>Hatókör hozzáadása

1. Ha még nem tette meg, nyissa meg a logikai alkalmazást a Logic App Designerben. 

1. Adjon hozzá egy hatókört a kívánt munkafolyamat-helyen. Ha például a logikai alkalmazás munkafolyamatának meglévő lépései közé szeretne hatókört hozzáadni, kövesse az alábbi lépéseket: 

   1. Vigye az egérmutatót azon a nyíl fölé, amelyhez a hatókört hozzá szeretné adni. 
   Válassza ki a**+** **pluszjelet** ( ) > **Művelet hozzáadása**lehetőséget.

      ![Hatókör hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. A keresőmezőbe írja be szűrőként a "hatókör" kifejezést. 
   Jelölje be a **Hatókör** műveletet.

## <a name="add-steps-to-scope"></a>Lépések hozzáadása a hatókörhöz

1. Most adja hozzá a lépéseket, vagy húzza a hatókörön belül futtatni kívánt meglévő lépéseket. Ebben a példában húzza a műveleteket a hatókörbe:
      
   * **Útvonal beszerezése**
   * **Ha a forgalmi idő meghaladja a megadott időt**, amely magában foglalja mind a **valódi,** mind a **hamis** ágakat

   A logikai alkalmazás most így néz ki:

   ![Hatókör hozzáadva](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. A hatókör alatt adjon hozzá egy feltételt, amely ellenőrzi a hatókör állapotát. A feltétel átnevezése ezzel a leírással: **Ha a hatókör nem sikerült**

   ![Feltétel hozzáadása a hatókör állapotának ellenőrzéséhez](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. A feltételben adja hozzá ezeket a kifejezéseket, amelyek ellenőrzik, hogy a hatókör állapota "Sikertelen" vagy "Megszakított" lesz-e. 

   1. Másik sor hozzáadásához válassza a **Hozzáadás gombot.** 

   1. Minden sorban kattintson a bal oldali mezőre, hogy megjelenjen a dinamikus tartalomlista. 
   A dinamikus tartalomlistában válassza a **Kifejezés lehetőséget.** A szerkesztés mezőbe írja be ezt a kifejezést, majd kattintson az **OK gombra:** 
   
      `result('Scope')[0]['status']`

      ![A hatókör állapotát összevető kifejezés hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Mindkét sor esetében a kijelölés **megegyezik** operátorral. 
   
   1. Az összehasonlítási értékekhez írja be `Failed`az első sorba a . 
   A második sorban `Aborted`írja be a . 

      Ha elkészült, a feltétel a következő példához hasonlít:

      ![A hatókör állapotát összevető kifejezés hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Most állítsa be a `runAfter` feltétel tulajdonságát, így a feltétel ellenőrzi a hatókör állapotát, és futtatja a későbbi lépésekben meghatározott egyeztetési műveletet.

   1. Az **Ha hatókör sikertelen** feltételen válassza a **három pont** (...) gombot, majd válassza a **Futtatás konfigurálása után**lehetőséget.

      ![A 'runAfter' tulajdonság konfigurálása](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Jelölje ki az összes hatókör-állapotot: **sikeres**, **sikertelen**, **kimarad,** és **időtúljárva van**

      ![Hatókör állapotának kiválasztása](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Ha elkészült, válassza a **Kész** lehetőséget. 
   A feltétel most egy "információ" ikont jelenít meg.

1. Az **Ha igaz** és **ha hamis** elágazások között adja hozzá az egyes hatókörállapotok alapján végrehajtani kívánt műveleteket, például küldjön egy e-mailt vagy üzenetet.

   ![Végrehajtandó műveletek hozzáadása a hatókör állapota alapján](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Mentse a logikai alkalmazást.

A kész logikai alkalmazás most így néz ki:

![Kész logikai alkalmazás hatókörrel](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>A munka tesztelése

A tervező eszköztárán válassza a **Futtatás gombot.** Ha az összes hatókörrel végzett művelet sikeres, "Hatókör sikeres" üzenet jelenik meg. Ha a hatókörrel végzett műveletek nem sikeresek, "Hatókör sikertelen" üzenet jelenik meg. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definíció

Ha kódnézetben dolgozik, a logikai alkalmazás JSON-definíciójában definiálhat egy hatókörstruktúrát. Például itt van a JSON-definíció az eseményindítók és műveletek az előző logikai alkalmazásban:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciók és javaslatok elküldéséhez vagy az azokra való szavazáshoz látogasson el az [Azure Logic Apps felhasználói visszajelzési webhelyére.](https://aka.ms/logicapps-wish)

## <a name="next-steps"></a>További lépések

* [Feltételek (feltételes utasítások) alapján lépések futtatása](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Lépések futtatása különböző értékek alapján (kapcsolóutasítások)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Lépések futtatása és ismétlése (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Párhuzamos lépések futtatása vagy egyesítése (ágak)](../logic-apps/logic-apps-control-flow-branches.md)

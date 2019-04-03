---
title: Csoport állapota – Azure Logic Apps szerinti futtató hatókört is felvehet |} A Microsoft Docs
description: Hogyan hozhat létre, amely alapján a csoport műveleti állapota az Azure Logic Apps munkafolyamat-műveletek hatókörök
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 48fb2d14cd4cf99510fff88b25b9ae45814a92a8
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882412"
---
# <a name="run-actions-based-on-group-status-with-scopes-in-azure-logic-apps"></a>Az Azure Logic Appsben hatókörökkel csoport állapota alapján műveletek futtatása

Csak azt követően a műveletek egy másik csoport sikeres vagy sikertelen műveletek futtatására, csoportban lévő műveletek egy *hatókör*. Ez a struktúra akkor hasznos, ha a kívánt logikai csoportként műveletek rendezhet, kiértékelheti, hogy a csoport állapota és a hatókör állapot alapuló műveletek végrehajtására. A hatókör az összes művelet után a hatókört is lekéri a saját állapota. Például használhat hatókörök, amikor a megvalósítani kívánt [kivétel és hibakezelés](../logic-apps/logic-apps-exception-handling.md#scopes). 

A hatókör állapotának ellenőrzéséhez használhatja ugyanezeket a feltételeket, amelyek segítségével meghatározhatja egy logikai alkalmazás futtatási állapota, például a "Sikeres", "Sikertelen", "Megszakítva" és így tovább. Alapértelmezés szerint a hatókör művelet sikeres legyen, amikor állapota a hatókör meg van jelölve "sikeres" üzenet. De ha a hatókörében bármilyen művelet sikertelen, vagy meg lett szakítva, a hatókör állapota "Sikertelen." van megjelölve Hatókörök korlátozásairól lásd: [határértékek és konfiguráció](../logic-apps/logic-apps-limits-and-config.md). 

Ha például Íme egy bizonyos műveletek és a egy feltétel, a hatókör állapotának ellenőrzéséhez futtassa a hatókört használó magas szintű logikai alkalmazást. Ha a hatókörében műveletek sikertelen, vagy váratlanul leáll, a hatókör meg van jelölve "Sikertelen" vagy "Aborted" jelölik, és a logikai alkalmazás "Hatókör nem sikerült" üzenetet küld. Ha a hatókörrel rendelkező műveletek sikeres, a logikai alkalmazás "Hatókör sikeres" üzenetet küld.

![Állítsa be a "Ütemezés – ismétlődés" eseményindító](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Előfeltételek

Kövesse az ebben a cikkben szereplő példa, ezek az elemek szükségesek:

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* A Logic Apps által támogatott bármely e-mail-szolgáltatónál e-mail-fiók. Ebben a példában az Outlook.com-ot használ. Ha egy másik szolgáltatót használ, az általános folyamat változatlan marad, de a felhasználói felület különböző jelenik meg.

* A Bing térképek-kulcsot. A kulcs lekéréséhez lásd: <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">Bing térképek-kulcs lekérése</a>.

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Minta logikai alkalmazás létrehozása

Először hozza létre a minta logikai alkalmazás, így később is hozzáadhat egy hatókör:

![Minta logikai alkalmazás létrehozása](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* A **ütemezés – ismétlődés** eseményindítót, amely a Bing Maps szolgáltatás ellenőrzi a megadott időközönként
* A **Bing térképek – útvonal lekérése** műveletet, amely ellenőrzi az utazási időt két helye között
* Egy feltételes utasítás, amely ellenőrzi, hogy az utazási idő meghaladja a megadott utazási idő
* Egy műveletet, amely elküldi az e-mail-e a jelenlegi utazási idő meghaladja a megadott időtartam

A logikai alkalmazás mentése bármikor, ezért mentse gyakran a munkáját.

1. Jelentkezzen be a <a href="https://portal.azure.com" target="_blank">az Azure portal</a>, ha még nem tette. Üres logikai alkalmazás létrehozása.

1. Adja hozzá a **ütemezés – ismétlődés** eseményindító ezekkel a beállításokkal: **Intervallum** = "1" és a **gyakorisága** = "Perc."

   ![Állítsa be a "Ütemezés – ismétlődés" eseményindító](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Vizuálisan a nézet leegyszerűsítése érdekében, és minden művelet részletei a tervezőben elrejtése, alakzat összecsukása a minden művelet állapotát a fenti lépéseket.

1. Adja hozzá a **Bing térképek – útvonal lekérése** művelet. 

   1. Ha még nem rendelkezik Bing térképek-kapcsolattal, kéri, hogy hozzon létre egy kapcsolatot.

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **Kapcsolat neve** | BingMapsConnection | Adja meg a kapcsolat nevét. | 
      | **API-kulcs** | <*saját-Bing-Térképek-kulcsa*> | Adja meg a korábban kapott Bing Térképek-kulcsot. | 
      ||||  

   1. Állítsa be a **útvonal lekérése** művelet, ez az ábra alatti táblázatban látható módon:

      ![Állítsa be a "Bing térképek – útvonal lekérése" művelet](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      További információk ezekről a paraméterekről: [Útvonal kiszámítása](https://msdn.microsoft.com/library/ff701717.aspx).

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **Waypoint 1** | <*Indítsa el*> | Adja meg az útvonal kiindulópontja. | 
      | **Waypoint 2** | <*vége*> | Adja meg az útvonal célállomása. | 
      | **Avoid** | None | Adja meg az elemek elkerülése érdekében az útvonal, például autópályák, útdíjak, és így tovább. A lehetséges értékek megtekintéséhez [útvonal kiszámítása](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimalizálás** | timeWithTraffic | Válassza ki például távolság, időt a jelenlegi forgalom adatait, és így tovább, az útvonal optimalizálására szolgáló paraméter. Ebben a példában ez az érték: "timeWithTraffic" | 
      | **Távolság mértékegysége** | <*igény-szerint*> | Adja meg az irányítása kiszámítására távolság egysége. Ebben a példában ez az érték: "Mérföld" | 
      | **Közlekedési mód** | Vezetés | Adja meg az utazási mód a útvonal. Ebben a példában ez az érték "Vezetői" | 
      | **Tranzit dátuma és időpontja** | None | Csak a tranzit módra vonatkozik. | 
      | **Átvitel dátuma-típusú típusa** | None | Csak a tranzit módra vonatkozik. | 
      ||||  

1. [Feltétel hozzáadása](../logic-apps/logic-apps-control-flow-conditional-statement.md) , amely ellenőrzi, hogy a forgalom a jelenlegi utazási idő meghaladja-e adott ideje. 
   Ebben a példában az alábbi lépéseket követve:

   1. Nevezze át a feltételt a következő leírással: **Ha a forgalom idő több, mint a megadott időtartam**

   1. Kattintson a bal szélső oszlopban a **válasszon egy értéket** mezőbe, így a dinamikus tartalmak listája jelenik meg. A listában jelölje ki a **utazás időtartama forgalommal** mező, amely másodpercek alatt. 

      ![Feltétel létrehozása](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. A középső mezőben válassza ki azt a operátort: **nagyobb, mint**

   1. A jobb szélső oszlopban adja meg a összehasonlító értéket, amely másodperc és az azzal egyenértékű, 10 percre van: **600**

      Ha elkészült, a feltétel a következő példához hasonlít:

      ![Befejezett feltétel](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Az a **ha igaz** ágban, adjon hozzá egy "e-mail küldése" műveletet az e-mail-szolgáltatóhoz. 
   Állítsa be ezt a műveletet a következő kép alatti lépéseket követve:

   ![Adja hozzá a "E-mail küldése" művelet "Ha true" ág](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Az a **való** mezőbe írja be az e-mail-címét a tesztelési célokra.

   1. Az a **tulajdonos** mezőbe írja be a következő szöveget:

      ```Time to leave: Traffic more than 10 minutes```

   1. Az a **törzs** mezőbe írja be a szöveget, záró szóközzel: 

      ```Travel time:```

      Amíg a kurzor megjelenik a **törzs** mező, a dinamikus tartalmú lista nyitva marad, hogy kiválaszthatja a paramétereket, érhetők el ezen a ponton.

   1. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget.

   1. Keresse meg és válassza a **div()** függvény. 
      Vigye az egérmutatót az a függvény zárójelek.

   1. Bár a kurzort a függvény zárójelek között van, válassza ki **dinamikus tartalom** , hogy a dinamikus tartalmak listája jelenik meg. 
   
   1. Az a **útvonal lekérése** szakaszban jelölje be a **forgalom időtartama forgalommal** mező.

      ![Válassza ki a "Forgalom időtartama forgalommal"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. A mező feloldása egy olyan JSON-formátumban, miután hozzá egy **vesszővel** (```,```) és a számát ```60``` úgy, hogy az érték konvertálása **forgalom időtartama forgalommal** perc, másodperc. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      A kifejezés a példához hasonlóan néz ki:

      ![A Befejezés kifejezés](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Ha elkészült, válassza ki a **OK**.

   <!-- markdownlint-disable MD038 -->
   1. Oldja fel a kifejezés, miután adja hozzá ezt a szöveget egy vezető terület: ``` minutes```
  
       A **törzs** mező a következőhöz hasonló ebben a példában:

       ![Befejezett "Törzs" mező](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy hatókörhöz, hogy bizonyos műveletek csoport, és azok állapotának értékeléséhez.

## <a name="add-a-scope"></a>Hatókör hozzáadása

1. Ha még nem tette, nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben. 

1. Hatókör hozzáadása a munkafolyamat helyen, amelyeket szeretne. Ha például a logikai alkalmazás munkafolyamatának a meglévő lépések közötti hatókör hozzáadása, kövesse az alábbi lépéseket: 

   1. Helyezze az egérmutatót a nyíl felett, ahová a hatókör hozzáadása. 
   Válassza ki a **plusz jelre** (**+**) > **művelet hozzáadása**.

      ![Hatókör hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. A Keresés mezőbe írja be szűrőként "hatókör". 
   Válassza ki a **hatókör** művelet.

## <a name="add-steps-to-scope"></a>Vegyen fel olyan lépéseket hatókör

1. Most adja hozzá a lépéseket, vagy húzza át a meglévő hatókörében futtatni kívánt lépéseket. Ebben a példában húzza át ezeket a műveleteket a hatókör:
      
   * **Útvonal lekérése**
   * **Ha a forgalom idő több, mint a megadott időtartam**, is magában foglalja a **igaz** és **false (hamis)** ágak

   A logikai alkalmazás most már példához hasonlóan néz ki:

   ![Hozzáadott hatókör](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. A hatókör alatt adjon hozzá egy feltételt, amely a hatókör állapotát ellenőrzi. Nevezze át a feltételt a következő leírással: **Ha a hatókör nem sikerült**

   ![Hatókör állapotának feltétel hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Adja hozzá a feltétellel, ezek a kifejezések, ellenőrizze, hogy a hatókör állapota "Sikertelen" vagy "Aborted" egyenlő. 

   1. Új sor hozzáadásához válassza ki a **Hozzáadás**. 

   1. Minden egyes sorban kattintson a bal oldali mezőbe, így a dinamikus tartalmak listája jelenik meg. 
   A dinamikus tartalmú listából válassza ki a **kifejezés**. A beviteli mezőbe írja be a kifejezést, és válassza **OK**: 
   
      `result('Scope')[0]['status']`

      ![Kifejezés, amely ellenőrzi a hatókör hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Mindkét sorok kiválasztása **egyenlő** kezelőként. 
   
   1. Az összehasonlítás értékeket, az első sorban adja meg `Failed`. 
   A második sorban adja meg a `Aborted`. 

      Ha elkészült, a feltétel a következő példához hasonlít:

      ![Kifejezés, amely ellenőrzi a hatókör hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Most állítsa a feltétel `runAfter` tulajdonság, a hatókör állapotát ellenőrzi a feltételt, és futtatja a megfelelő műveletet, amely azt adja meg a későbbi lépésekben.

   1. A a **hatókör sikertelen** feltételt, válassza ki a **három pontot ábrázoló** (...) gombra, és válassza **konfigurálása futtassa**.

      !["RunAfter" tulajdonsága konfigurálása](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Válassza ki a hatókör-állapotok: **sikeres**, **sikertelen**, **kimarad**, és **túllépte az időkorlátot**

      ![Válassza ki a hatókör-állapotok](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Ha elkészült, válassza a **Kész** lehetőséget. 
   A feltétel most jeleníti meg a "-információk" ikont.

1. Az a **ha igaz** és **ha hamis** ágak, adja hozzá a végrehajtani kívánt műveletek alapján minden hatókör állapotát, például az egy e-mailben vagy az üzenet küldése.

   ![A hatókör állapot alapján elvégzendő műveletek hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Mentse a logikai alkalmazást.

A befejezett logikai alkalmazás most már példához hasonlóan néz ki:

![Hatókörrel rendelkező befejezett logikai alkalmazás](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>A munkahelyi tesztelése

A Tervező eszköztárán válassza **futtatása**. Ha a hatókörrel rendelkező műveletek sikeres legyen, "Hatókör sikeres" üzenetet kap. Ha hatókörrel rendelkező műveletek nem sikerül, a "Hatókör nem sikerült" üzenet jelenik meg. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definíció

Ha kód nézetben dolgozik, megadhat egy hatókör struktúra a logikai alkalmazás JSON-definíciójában helyette. Ha például itt látható a JSON-definíciót trigger és műveletek az előző logikai alkalmazásban:

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
* Küldje el, vagy szavazhat a funkciók és javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzések oldalon](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Hajtsa végre a lépéseket, egy feltételt (feltételes kifejezések) alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Hajtsa végre a lépéseket, a különböző értékek (switch-utasítások) alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ág)](../logic-apps/logic-apps-control-flow-branches.md)

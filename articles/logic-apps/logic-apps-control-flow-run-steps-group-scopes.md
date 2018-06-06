---
title: A csoport állapot - Azure Logic Apps alapján műveletek futtatása hatókörök hozzáadása |} Microsoft Docs
description: Rendszert futtató munkafolyamat-műveleteket az Azure Logic Apps csoport állapota alapján hatókörök létrehozása
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: cfowler
ms.date: 03/05/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: c6f6b54e17d12ff21d50748810699e78e3a14757
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726380"
---
# <a name="create-scopes-that-run-workflow-actions-based-on-group-status-in-azure-logic-apps"></a>Hozzon létre hatóköröket rendszert futtató munkafolyamat-műveleteket az Azure Logic Apps csoport állapota alapján

Csak azután egy másik csoport műveletek lesz sikeres vagy sikertelen műveletek futtatására, ezeket a műveleteket belül csoport egy *hatókör*. Ez a struktúra akkor hasznos, ha a kívánt logikai csoport műveletek rendezéséhez, a csoport állapotának kiértékelésére és a hatókör állapotát alapuló műveleteket hajthatja végre. A hatókör összes művelet után a hatóköre is lekéri a saját állapotát. Például a hatókörökkel helyzet megvalósítása [kivétel és hibakezelés](../logic-apps/logic-apps-exception-handling.md#scopes). 

A hatókör állapotának ellenőrzéséhez használja a ugyanazok a feltételek, amelyek segítségével állapítja meg, a logikai alkalmazások futtatása állapotára, például "Succeeded", "Sikertelen", "Megszakítva" és így tovább. Alapértelmezés szerint a hatókör összes művelet sikeres, a hatókör állapotát megjelölve "Sikeres". De ha a hatókörében bármely művelet sikertelen, vagy megszakítja, a hatókör állapotát jelölésű "Sikertelen". Hatókörök használati korlátait, lásd: [korlátozásai és konfigurációs](../logic-apps/logic-apps-limits-and-config.md). 

Például ez egy magas szintű logikai alkalmazást használó hatókör konkrét műveletek és a hatókör állapotának feltétel futtatásához. Ha a hatókörében műveletek sikertelenek, vagy váratlanul leáll, a hatókör van jelölve "Sikertelen" vagy "Megszakadt" rendre, és a logikai alkalmazás "Hatókör nem sikerült" üzenet küldése. A hatókörbe tartozó műveleteket sikeres, ha a logikai alkalmazás "Hatókör sikeresen befejeződött" üzenet küldése.

![Állítsa be "Ütemezés – ismétlődési" eseményindító](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Előfeltételek

Kövesse az ebben a cikkben példa, ezek az elemek szükségesek:

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). 

* Az e-mail-fiók bármely e-mail szolgáltató Logic Apps által támogatott. A példában az Outlook.com-os. Ha külön szolgáltatót használ, az általános folyamat ugyanaz marad, de a felhasználói felület különböző jelenik meg.

* A Bing Maps kulcs. Ahhoz, hogy ezt a kulcsot, lásd: <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">a Bing Maps kulcs lekérése</a>.

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Minta logikai alkalmazás létrehozása

Először hozzon létre ezen PéldaAlkalmazás logika, hogy a hatókör később veheti fel:

![Minta logikai alkalmazás létrehozása](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* A **ütemezés - ismétlődési** , amely a Bing Maps szolgáltatás ellenőrzi a megadott időközönként eseményindító
* A **a Bing Maps - Get-útvonal** műveletet, amely ellenőrzi a utazás idő két hely között
* Egy feltételes utasítást, amely ellenőrzi, hogy a utazás idő meghaladja-e a megadott utazás idő
* Egy művelet által küldött e-mailben elküldi aktuális utazás idő meghaladja a megadott idő

Mentse a Logic Apps alkalmazást tetszőleges időpontban, ezért gyakran mentsen.

1. Jelentkezzen be a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>, ha még nem tette meg. Üres logikai alkalmazás létrehozása.

2. Adja hozzá a **ütemezés - ismétlődési** ezekkel a beállításokkal eseményindító: **időköz** = "1" és **gyakoriság** = "Perc"

   ![Állítsa be "Ütemezés – ismétlődési" eseményindító](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Vizuálisan egyszerűsítése a nézetben, és minden művelet részleteit a tervezőben elrejtése, csukja össze ezeket a lépéseket lépésein végighaladva minden művelet alakú.

3. Adja hozzá a **a Bing Maps - Get-útvonal** művelet. 

   1. Ha még nem rendelkezik a Bing Maps kapcsolat, megkérdezi, VPN-kapcsolat létrehozásához.

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **Kapcsolat neve** | BingMapsConnection | Adja meg a kapcsolat nevét. | 
      | **API-kulcs** | <*saját-Bing-Térképek-kulcsa*> | Adja meg a korábban kapott Bing Térképek-kulcsot. | 
      ||||  

   2. Állítsa be a **Get útvonal** művelet, ez a rendszerkép az alábbi táblázatban látható módon:

      ![Állítsa be a "Bing Maps - útvonal Get" művelet](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      További információk ezekről a paraméterekről: [Útvonal kiszámítása](https://msdn.microsoft.com/library/ff701717.aspx).

      | Beállítás | Érték | Leírás |
      | ------- | ----- | ----------- |
      | **1. útvonalpont** | <*Indítsa el*> | Adja meg a útvonal forrása. | 
      | **2. útvonalpont** | <*Vége*> | Adja meg a útvonal cél. | 
      | **Elkerülés** | None | Adja meg az elemek elkerülése érdekében a útvonalon, például autópályákat, autópályadíjak, és így tovább. A lehetséges értékeket lásd: [útvonal kiszámításához](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimalizálás** | timeWithTraffic | Jelölje ki a paramétert, az útvonal, például a távolság, aktuális forgalmi információk, vagy más idő optimalizálása érdekében. Ez a példa ezt az értéket: "timeWithTraffic" | 
      | **Távolság mértékegysége** | <*igény-szerint*> | Adja meg a egység távolság a útvonal kiszámításához. Ez a példa ezt az értéket: "Mérföldes" | 
      | **Közlekedési mód** | Vezetés | Adja meg a útvonal utazás módját. A példában ez az érték "Vezetői" | 
      | **Tranzit dátuma és időpontja** | None | Csak az átvitel közben üzemmódban vonatkozik. | 
      | **Átvitel dátuma-típusának** | None | Csak az átvitel közben üzemmódban vonatkozik. | 
      ||||  

4. Feltétel hozzáadása ellenőrzése, hogy forgalommal aktuális utazás idő elér egy megadott időpontot. Ehhez a példához kövesse a a lemezkép:

   ![Feltétel létrehozása](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Nevezze át a leírást a feltételei: **Ha forgalom a megadott időtartamnál hosszabb ideje**

   2. A paraméter listájából válassza ki a **utazás időtartama forgalom** mező, amely másodpercben. 

   3. Ez az operátor. Válassza a összehasonlító operátor: **nagyobb, mint**

   4. Adja meg az összehasonlítási érték esetében **600**, amely másodpercben és egyenértékű 10 percre van.

5. A feltételben **igaz értéke esetén** fiókirodai "e-mail küldési" művelet hozzáadása az e-mailek szolgáltató. Ez a művelet a adatokkal beállítása alapján ez a kép táblázatban látható módon:

   ![Adja hozzá a "E-mail küldési" művelet "igaz értéke esetén" ág](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Az a **való** mezőbe írja be az e-mail cím tesztelési célokra.

   2. Az a **tulajdonos** mezőbe írja be ezt a szöveget:

      ```Time to leave: Traffic more than 10 minutes```

   3. Az a **törzs** mezőbe írja be a szöveget egy záró szóköz: 

      ```Travel time: ```

      Megjelenik a kurzort, amíg a **törzs** mezőt, a dinamikus tartalom listába nyitva marad, hogy választhatja ki, amelyek ezen a ponton a rendelkezésre álló paramétereket.

   4. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget.

   5. Keresse meg és jelölje ki a **div (-)** függvény.

   6. Bár a kurzort a függvénynek zárójelbe belül, válassza ki **dinamikus tartalom** , hogy adhat hozzá a **forgalom időtartama forgalom** paraméter tovább.

   7. A **Get útvonal** dinamikus paraméter listájából válassza ki a **forgalom időtartama forgalom** mező.

      ![Válassza ki a "Forgalom időtartama forgalom"](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   8. Után a mező feloldása egy olyan JSON formátumban, hozzáadhatja egy **vesszővel** (```,```) és a számát ```60``` , hogy az érték átalakítása **forgalom időtartama forgalom** percre másodperc. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      A kifejezés most ebben a példában néz ki:

      ![Befejezés kifejezés](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   9. Győződjön meg arról, hogy a kiválasztott **OK** befejezése.

  10. Után oldja fel a kifejezést, adja hozzá ezt a szöveget kezdődhet szóközzel: ``` minutes```
  
      A **törzs** mező most néz ebben a példában:

      ![Befejeződött a "Törzs" mező](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)

6. Mentse a logikai alkalmazást.

Ezután adja hozzá a hatókör, hogy a konkrét műveletek csoportban, és azok állapotának kiértékelésére.

## <a name="add-a-scope"></a>Hatókör hozzáadása

1. Ha még nem tette meg, nyissa meg a Logic Apps alkalmazást Logic App tervezőben. 

2. A munkafolyamat-helyet, amelyet a hatókör hozzáadása. Példa:

   * A logic app munkafolyamat meglévő lépések között hatókör hozzáadása, vigye az egérmutatót a nyíl ahol a hatókör hozzáadni kívánt. 
   Válassza ki a **pluszjel** (**+**) > **hatókör hozzáadása**.

     ![Hatókör hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

     Ha hozzá szeretne adni egy hatókört a Logic Apps alkalmazást alján, a munkafolyamat végén válassza **+ új lépés** > **... További** > **hatókör hozzáadása**.

3. Most adja hozzá a lépéseket, vagy húzza a meglévő lépéseket, amelyek hatókörében futtatni szeretné. Ehhez a példához húzza ezeket a műveleteket a hatókör:
      
   * **Útvonal beolvasása**
   * **Ha a forgalom megadott időtartamnál hosszabb ideje**, is magában foglalja a **igaz** és **hamis** ágak

   A logikai alkalmazás most már a következőképpen néz ebben a példában:

   ![Hozzáadott hatókör](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

4. A hatókör adja hozzá olyan feltétel, amely a hatókör állapotát ellenőrzi. Nevezze át a leírást a feltételei: **hatókör sikertelen**

   ![Hatókör állapotának feltétel hozzáadása](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
5. A kifejezés, amely ellenőrzi, hogy a hatókör állapotát egyenlő összeállítása `Failed` vagy `Aborted`.

   ![Adja hozzá, amely ellenőrzi a hatókör állapotát](./media/logic-apps-control-flow-run-steps-group-scopes/build-expression-check-scope-status.png)

   Vagy adja meg a kifejezés szövegként, válassza a **speciális módban szerkesztése**.

   ```@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')```

6. Az a **igaz értéke esetén** és **hamis** elágazásokat, vegye fel a végrehajtandó műveleteket szeretne végrehajtani, például küldjön e-mailben vagy egy üzenetet.

   ![Adja hozzá, amely ellenőrzi a hatókör állapotát](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

7. Mentse a logikai alkalmazást.

A befejezett logikai alkalmazás most néz ebben a példában a alakzatokhoz kibontva:

![Befejezett logikai alkalmazás hatókörű](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>A munkahelyi tesztelése

A Tervező eszköztáron válassza **futtatása**. A hatókörbe tartozó műveleteket sikeres, a "Hatókör sikeresen befejeződött" üzenet jelenik meg. A hatókört használó műveletek nem sikerül, a "Hatókör nem sikerült" üzenet jelenik meg. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-definícióból

Ha kódnézetben dolgozik, megadhat egy hatókör struktúra a Logic Apps alkalmazást JSON-definícióban helyette. Például ez a JSON-definícióból eseményindító és a korábbi logic App műveletek:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    },
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
            "Body": "Scope failed",
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
              "Body": "None",
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
    "expression": "@equals('@result(''Scope'')[0][''status'']', 'Failed, Aborted')",
    "runAfter": {
      "Scope": [
        "Succeeded"
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
      "If_traffic_time_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'], 60)} minutes",
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
        "expression": "@greater(body('Get_route')?['travelDurationTraffic'], 600)",
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
}
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Küldje el, vagy szavazna funkciók és javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [A lépéseket (feltételes utasítások) feltétel alapján](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Futtatás (kapcsoló utasítások) eltérő értékek alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ágak)](../logic-apps/logic-apps-control-flow-branches.md)

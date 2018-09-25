---
title: A figyelő logikaialkalmazás-futtatást a Log Analytics – Azure Logic Apps |} A Microsoft Docs
description: Alkalmazható információkhoz és a logikai alkalmazás futtatásának Log Analytics szolgáltatással kapcsolatos adatokat kapcsolatos hibaelhárítás és diagnosztika hibakeresés
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/19/2018
ms.openlocfilehash: 75094ad17474cd07039724fb0b5e5e377808b51f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956656"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitorozása és elemzése a Log Analytics logikaialkalmazás-futtatások kapcsolatos

Figyelési és gazdagabb hibakeresési információ bekapcsolhatja a Log Analytics egyszerre egy logikai alkalmazás létrehozásakor. A log Analytics biztosít a diagnosztikai naplózás és figyelés a logikai alkalmazás futtatása az Azure Portalon keresztül. A Logic Apps Management megoldás hozzáadásakor az összesített állapota kap a logikaialkalmazás-futtatások és állapotának, végrehajtási idő, futtatásnak az újraküldése állapotát és korrelációs azonosítók például konkrét részleteket.

Ez a cikk bemutatja, hogyan kapcsolja be a Log Analytics, hogy runtime az események és a logikai alkalmazás adatok futtathatók.

 > [!TIP]
 > A meglévő logic apps figyelése, az alábbi lépéseket követve [kapcsolja be a diagnosztikai naplózás és a logikai alkalmazás futtatókörnyezeti adatokat küldeni a Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Követelmények

A Kezdés előtt szüksége van egy Log Analytics-munkaterületet. Ismerje meg, [Log Analytics-munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Logikai alkalmazások létrehozásának diagnosztikai naplózás bekapcsolása

1. A [az Azure portal](https://portal.azure.com), hozzon létre egy logikai alkalmazást. Válasszon **erőforrás létrehozása** > **vállalati integráció** > **logikai alkalmazás**.

   ![Logikai alkalmazás létrehozása](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. Az a **logikai alkalmazás létrehozása** lapon, a feladatok végrehajtásához látható módon:

   1. Adja meg a logikai alkalmazás nevét, és válassza ki az Azure-előfizetésében. 
   2. Hozzon létre vagy válasszon ki egy Azure-erőforrás.
   3. Állítsa be **Log Analytics** való **a**. 
   Válassza ki a Log Analytics-munkaterületet, ahol szeretne küldeni, adatok, a logikai alkalmazás futtatása. 
   4. Amikor elkészült, válassza ki a **rögzítés az irányítópulton** > **létrehozás**.

      ![Logikai alkalmazás létrehozása](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Miután elvégezte ezt a lépést, az Azure létrehozza a logikai alkalmazást, amely mostantól a Log Analytics-munkaterülethez társítva. 
      Emellett ebben a lépésben is automatikusan telepíti a Logic Apps Management megoldás a munkaterületén.

3. A logikai alkalmazás megtekintéséhez fut, [folytassa a következő lépéseket](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>A Logic Apps Management megoldás telepítése

Ha már van kapcsolva a Log Analytics, a logikai alkalmazás létrehozásakor, kihagyhatja ezt a lépést. Már rendelkezik a Logic Apps Management megoldás telepítve.

1. Az a [az Azure portal](https://portal.azure.com), válassza a **további szolgáltatások**. Keresse meg a "log analytics" szűrőként, és válassza a **Log Analytics** látható módon:

   ![Válassza a "Log Analytics"](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. A **Log Analytics**, keresse meg és válassza ki a Log Analytics-munkaterületre. 

   ![Válassza ki a Log Analytics-munkaterület](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. A **felügyeleti**, válassza a **áttekintése**.

   ![Válassza a "OMS-portál"](media/logic-apps-monitor-your-logic-apps-oms/ibiza-portal-page.png)

4. Az Áttekintés oldalon válassza ki a **Hozzáadás** nyithatja meg a felügyeleti megoldások csempét. 

   ![Válassza a "Logic Apps Management"](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

5. Görgessen lefelé a listában, **felügyeleti megoldások**, válassza a **Logic Apps Management** megoldás, és válassza a **létrehozása** telepítheti a – áttekintés oldalra.

   ![Válassza az "Add" a "Logic Apps Management"](media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-log-analytics-workspace"></a>A logikai alkalmazás futtatásának megtekintése a Log Analytics-munkaterület

1. Számát és a logikaialkalmazás-futtatások állapotának megtekintéséhez nyissa meg a Log Analytics-munkaterület áttekintő oldala. Tekintse át a részleteket az a **Logic Apps Management** csempére.

   ![Logikai alkalmazás futtatásának száma és az állapotot megjelenítő áttekintés csempe](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

2. További részletekkel a logikaialkalmazás-futtatások összegzése megtekintéséhez válassza a **Logic Apps Management** csempére.

   A logikai alkalmazás futtatásának itt név alapján, vagy végrehajtási állapot szerint vannak csoportosítva. Megtekintheti a műveletek vagy triggerek a logikaialkalmazás-futtatások a hibák részleteit is.

   ![Állapotösszegzés a logikai alkalmazás futtatása](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. Egy adott logikai alkalmazás vagy állapota minden Futtatás megtekintéséhez jelölje ki a logikai alkalmazás vagy egy állapota.

   A következő példa bemutatja, egy adott logikai alkalmazás a fut:

   ![Egy logikai alkalmazásban vagy egy állapot nézet futtatások](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   Ezen az oldalon két speciális lehetőség van:
   * **Nyomon követett tulajdonságok:** ebben az oszlopban látható a nyomon követett tulajdonságok, műveletek, a logikai alkalmazás szerint vannak csoportosítva. Nyomon követett tulajdonságainak megtekintéséhez válassza **nézet**. Az oszlop szűrő használatával kereshet a nyomon követett tulajdonságok.
   
     ![A logikai alkalmazás a nyomon követett tulajdonságainak megtekintése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Minden olyan újonnan hozzáadott nyomon követett tulajdonságok csak akkor jelennek meg az első 10 – 15 percet is igénybe vehet. Ismerje meg, [nyomon követett tulajdonságok hozzáadása a logikai alkalmazás](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Küldje el újból:** is küldje el újra egy vagy több logikai alkalmazás futtatásának sikeres, sikertelen vagy a futó. Jelölje be a jelölőnégyzeteket a futtatások újraküldése, és válassza a kívánt **küldje el újra**. 

     ![Logikaialkalmazás-futtatások újraküldése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

4. Ezek az eredmények szűréséhez hajthat végre ügyféloldali és a kiszolgálóoldali szűrés.

   * Ügyféloldali szűrő: minden oszlophoz, válassza ki a kívánt szűrőket. 
   Néhány példa:

     ![Példa oszlopszűrők](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * Kiszolgálóoldali szűrés: egy adott időpont ablakban válassza a megjelenő futtatások számának korlátozásához, vagy a hatókör vezérlőt használja az oldal tetején. 
   Alapértelmezés szerint csak 1000 rekordok jelennek meg egyszerre. 
   
     ![Az időtartomány módosítása](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. Összes műveletet és a egy adott Futtatás részleteinek megtekintéséhez válassza ki egy sort a logikai alkalmazás futtatását.

   Íme egy példa, amely egy adott logikai alkalmazás futtatását az összes műveletet megjeleníti:

   ![A logikai alkalmazás futtatását a műveletek megtekintése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
6. Bármely eredményeit az oldalon a lekérdezés mögött az eredmények megtekintéséhez vagy az összes eredmény megtekintéséhez válasszon **tekintse meg az összes**, amely megnyitja a naplók keresése oldalát.
   
   ![Tekintse meg az összes eredmény oldalakon](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   A naplóbeli keresés lapon
   * Egy tábla a lekérdezés eredményeinek megtekintéséhez válassza **tábla**.
   * Ha módosítani szeretné a lekérdezést, szerkesztheti a lekérdezési karakterláncot a keresősávba. 
   Válassza a jobb felhasználói élményt, **Advanced Analytics**.

     ![Műveletek és a egy logikai alkalmazás futtatásának részletei](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Itt az Azure Log Analytics lapon frissítheti lekérdezéseket, és tekintse meg az eredményeket a táblából. 
     Ez a lekérdezés használ [Kusto-lekérdezés nyelvi](https://aka.ms/LogAnalyticsLanguageReference), amely szerkesztheti, ha meg szeretné tekinteni a különböző eredményt. 

     ![Az Azure Log Analytics - lekérdezés megtekintése](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>További lépések

* [B2B üzenetek megfigyelése](../logic-apps/logic-apps-monitor-b2b-message.md)


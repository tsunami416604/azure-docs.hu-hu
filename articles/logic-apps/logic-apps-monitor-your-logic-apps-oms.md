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
ms.date: 10/11/2018
ms.openlocfilehash: 177c361734a88acab5fc10d6b460645be82bf437
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457138"
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-log-analytics"></a>Monitorozása és elemzése a Log Analytics logikaialkalmazás-futtatások kapcsolatos

Figyelési és gazdagabb hibakeresési információ bekapcsolhatja a Log Analytics egyszerre egy logikai alkalmazás létrehozásakor. A log Analytics biztosít a diagnosztikai naplózás és figyelés a logikai alkalmazás futtatása az Azure Portalon keresztül. A Logic Apps Management megoldás hozzáadásakor az összesített állapota kap a logikaialkalmazás-futtatások és állapotának, végrehajtási idő, futtatásnak az újraküldése állapotát és korrelációs azonosítók például konkrét részleteket.

Ez a cikk bemutatja, hogyan kapcsolja be a Log Analytics, hogy runtime az események és a logikai alkalmazás adatok futtathatók.

 > [!TIP]
 > A meglévő logic apps figyelése, az alábbi lépéseket követve [kapcsolja be a diagnosztikai naplózás és a logikai alkalmazás futtatókörnyezeti adatokat küldeni a Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

## <a name="requirements"></a>Követelmények

A Kezdés előtt szüksége van egy Log Analytics-munkaterületet. Ismerje meg, [Log Analytics-munkaterület létrehozása](../log-analytics/log-analytics-quick-create-workspace.md). 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>Logikai alkalmazások létrehozásának diagnosztikai naplózás bekapcsolása

1. A [az Azure portal](https://portal.azure.com), hozzon létre egy logikai alkalmazást. Válasszon **erőforrás létrehozása** > **integrációs** > **logikai alkalmazás**.

   ![Logikai alkalmazás létrehozása](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

1. A **logikai alkalmazás létrehozása**, ahogy a műveletek végrehajtásához:

   1. Adja meg a logikai alkalmazás nevét, és válassza ki az Azure-előfizetésében. 

   1. Hozzon létre vagy válasszon ki egy Azure-erőforrás.

   1. Állítsa be **Log Analytics** való **a**. 

   1. A lista a Log Analytics munkaterület listából válassza ki a munkaterületet, ahol szeretne küldeni, adatok, a logikai alkalmazás futtatása. 

      ![Logikai alkalmazás létrehozása](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      Miután elvégezte ezt a lépést, az Azure létrehozza a logikai alkalmazást, amely mostantól a Log Analytics-munkaterülethez társítva. 
      Emellett ebben a lépésben is automatikusan telepíti a Logic Apps Management megoldás a munkaterületén.

   1. Ha elkészült, kattintson a **Létrehozás** gombra.

1. A logikai alkalmazás megtekintéséhez fut, [folytassa a következő lépéseket](#view-logic-app-runs-oms).

## <a name="install-the-logic-apps-management-solution"></a>A Logic Apps Management megoldás telepítése

Ha már van kapcsolva a Log Analytics, a logikai alkalmazás létrehozásakor, kihagyhatja ezt a lépést. Már rendelkezik a Logic Apps Management megoldás telepítve.

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. A keresőmezőbe, írja be a "log analytics" szűrőként, és válassza ki **Log Analytics**.

   ![Válassza a "Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. A **Log Analytics**, keresse meg és válassza ki a Log Analytics-munkaterületre. 

   ![Válassza ki a Log Analytics-munkaterület](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. A **figyelési megoldások konfigurálása**, válassza a **megoldások megtekintése**.

   ![Válassza a "Megoldások megtekintése"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Az Áttekintés oldalon válassza ki a **Hozzáadás**, kattintással megnyílik a **felügyeleti megoldások** listája. Válassza ki a listáról, **Logic Apps Management**. 

   ![Válassza ki a "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Ha nem találja a megoldást, a lista alján válassza **Továbbiak betöltése** , amíg megjelenik a megoldás.

1. Válasszon **létrehozás**, amely telepíti a megoldás.

   ![Válassza az "Add" a "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-runs-in-log-analytics-workspace"></a>A Log Analytics-munkaterület nézet logic app-futtatások

1. Számát és a logikaialkalmazás-futtatások állapotának megtekintéséhez nyissa meg a Log Analytics-munkaterületet, és az Áttekintés lap megnyitásához. 

   A logikaialkalmazás-futtatások részleteit jelenik meg a **Logic Apps Management** csempére. További részletekkel a logikaialkalmazás-futtatások összegzése megtekintéséhez válassza a **Logic Apps Management** csempére. 

   ![Logikai alkalmazás futtatásának száma és az állapotot megjelenítő áttekintés csempe](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   A logikai alkalmazás futtatásának itt név alapján, vagy végrehajtási állapot szerint vannak csoportosítva. 
   Ezen a lapon hibákkal kapcsolatos részleteket is műveletek vagy triggerek a logikai alkalmazás futtatásának jeleníti meg.

   ![Állapotösszegzés a logikai alkalmazás futtatása](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
1. Egy adott logikai alkalmazás vagy állapota minden Futtatás megtekintéséhez jelölje ki a logikai alkalmazás vagy egy állapota.

   A következő példa bemutatja, egy adott logikai alkalmazás a fut:

   ![Egy logikai alkalmazásban vagy egy állapot nézet futtatások](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   A lapon a következő speciális beállítások:

   * **A nyomon követett tulajdonságok:**

     Ebben az oszlopban látható a nyomon követett tulajdonságok, műveletek, a logikai alkalmazás szerint vannak csoportosítva. Nyomon követett tulajdonságainak megtekintéséhez válassza **nézet**. 
     Keresés a nyomon követett tulajdonságok, az oszlop szűrő használata.
   
     ![A logikai alkalmazás a nyomon követett tulajdonságainak megtekintése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-tracked-properties.png)

     Minden olyan újonnan hozzáadott nyomon követett tulajdonságok csak akkor jelennek meg az első 10 – 15 percet is igénybe vehet. Ismerje meg, [nyomon követett tulajdonságok hozzáadása a logikai alkalmazás](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details).

   * **Küldje el újból:** is küldje el újra egy vagy több logikai alkalmazás futtatásának sikeres, sikertelen vagy a futó. Jelölje be a jelölőnégyzeteket a futtatások újraküldése, és válassza a kívánt **küldje el újra**. 

     ![Logikaialkalmazás-futtatások újraküldése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Ezek az eredmények szűréséhez hajthat végre ügyféloldali és a kiszolgálóoldali szűrés.

   * **Ügyféloldali szűrő**: minden oszlophoz, válassza ki a kívánt szűrőket, például:

     ![Példa oszlopszűrők](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Kiszolgálóoldali szűrés**: egy adott időpont ablakban válassza a megjelenő futtatások számának korlátozásához, vagy a hatókör vezérlőt használja az oldal tetején. Alapértelmezés szerint csak 1000 rekordok jelennek meg egyszerre.
   
     ![Az időtartomány módosítása](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
1. Összes műveletet és a egy adott Futtatás részleteinek megtekintéséhez válassza ki egy sort a logikai alkalmazás futtatását.

   Íme egy példa, amely egy adott logikai alkalmazás futtatását az összes műveletet megjeleníti:

   ![A logikai alkalmazás futtatását a műveletek megtekintése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-action-details.png)
   
1. Bármely eredményeit az oldalon a lekérdezés mögött az eredmények megtekintéséhez vagy az összes eredmény megtekintéséhez válasszon **tekintse meg az összes**, amely megnyitja a naplók keresése oldalát.
   
   ![Tekintse meg az összes eredmény oldalakon](media/logic-apps-monitor-your-logic-apps-oms/logic-app-seeall.png)
   
   A naplóbeli keresés lapon

   * Egy tábla a lekérdezés eredményeinek megtekintéséhez válassza **tábla**.

   * Ha módosítani szeretné a lekérdezést, szerkesztheti a lekérdezési karakterláncot a keresősávba. 
   Válassza a jobb felhasználói élményt, **Advanced Analytics**.

     ![Műveletek és a egy logikai alkalmazás futtatásának részletei](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)
     
     Az Azure Log Analytics lapon frissítse a lekérdezéseket, és tekintse meg az eredményeket a táblából. Ez a lekérdezés használ [Kusto-lekérdezés nyelvi](https://aka.ms/LogAnalyticsLanguageReference), amely szerkesztheti, ha meg szeretné tekinteni a különböző eredményt. 

     ![Az Azure Log Analytics - lekérdezés megtekintése](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>További lépések

* [B2B üzenetek megfigyelése](../logic-apps/logic-apps-monitor-b2b-message.md)
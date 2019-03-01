---
title: Az Azure Monitor naplóira – Azure Logic Apps és logic apps figyelése |} A Microsoft Docs
description: Információkhoz és a hibakeresési adatokat hibaelhárítási és diagnosztizálásához az Azure Log Analytics a logikaialkalmazás-futtatások
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: 3f890e6cabd757fdd38374befaaccd1a10c9bd96
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192462"
---
# <a name="monitor-logic-apps-with-azure-monitor-logs"></a>Az Azure Monitor naplóira rendelkező logikai alkalmazások figyelése

Figyelheti, és a logic apps gazdagabb hibakeresési adatainak, kapcsolja be a [naplózza az Azure Monitor](../log-analytics/log-analytics-overview.md) a logikai alkalmazás létrehozásakor. Az Azure Monitor naplóira biztosít a diagnosztikai naplózás és figyelés a logic Apps, a Logic Apps Management megoldás telepítésekor, az Azure Portalon. Ez a megoldás is biztosít a jelenít meg összesített adatokat a logikai alkalmazás fut, például az állapotának, végrehajtási idő, futtatásnak az újraküldése állapotát és korrelációs azonosítók részletes. Ez a cikk bemutatja, hogyan kapcsolja be a Azure Monitor naplóira, így runtime az események és a logikai alkalmazás adatok futtatja.

A meglévő logic apps az Azure Monitor naplóira bekapcsolásához kövesse az alábbi lépéseket a [kapcsolja be a diagnosztikai naplózás és a logic app futásidejű adatokat küldeni az Azure Monitor naplóira](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

> [!NOTE]
> Ezen a lapon a fentiekben már említettük, az a Microsoft Operations Management Suite (OMS), amely a feladatok végrehajtásához szükséges lépéseket [kivonása a január 2019](../azure-monitor/platform/oms-portal-transition.md), hanem az Azure Log Analytics váltja fel ezeket a lépéseket. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt szüksége van egy Log Analytics-munkaterületet. Ismerje meg, [Log Analytics-munkaterület létrehozása](../azure-monitor/learn/quick-create-workspace.md). 

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

## <a name="install-logic-apps-management-solution"></a>Logic Apps Management megoldás telepítése

Ha már be van kapcsolva az Azure Monitor naplózza a logikai alkalmazás létrehozásakor, ezt a lépést kihagyhatja. Már rendelkezik a Logic Apps Management megoldás telepítve.

1. Az [Azure Portalon](https://portal.azure.com) válassza a **Minden szolgáltatás** elemet. A keresőmezőbe, keresse meg a "log analytics", és válassza ki **Log Analytics**.

   ![Válassza a "Log Analytics"](./media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

1. A **Log Analytics**, keresse meg és válassza ki a Log Analytics-munkaterületre. 

   ![Válassza ki a Log Analytics-munkaterület](./media/logic-apps-monitor-your-logic-apps-oms/select-log-analytics-workspace.png)

1. A **Ismerkedés a Log Analytics** > **figyelési megoldások konfigurálása**, válassza a **megoldások megtekintése**.

   ![Válassza a "Megoldások megtekintése"](media/logic-apps-monitor-your-logic-apps-oms/log-analytics-workspace.png)

1. Az Áttekintés oldalon válassza ki a **Hozzáadás**, kattintással megnyílik a **felügyeleti megoldások** listája. Válassza ki a listáról, **Logic Apps Management**. 

   ![Válassza ki a "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

   Ha nem találja a megoldást, a lista alján válassza **Továbbiak betöltése** , amíg megjelenik a megoldás.

1. Válasszon **létrehozás**, erősítse meg a Log Analytics-munkaterületet, ahol szeretné telepíteni a megoldás, és válassza a **létrehozás** újra.   

   ![Válassza a "Create" a "Logic Apps Management"](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-apps-management-solution.png)

   Ha nem szeretné használni egy meglévő munkaterületet, most is létrehozhat egy új munkaterületet.

   Ha elkészült, a Logic Apps Management megoldás az Áttekintés lap jelenik meg. 

<a name="view-logic-app-runs-oms"></a>

## <a name="view-logic-app-run-information"></a>Logikai alkalmazás futtatásának információk megtekintése

A logikai alkalmazás futtatását követően megtekintheti az állapotát és ezeket a futtatások száma a **Logic Apps Management** csempére. 

1. Nyissa meg a Log Analytics-munkaterületet, és az Áttekintés lap megnyitásához. Válasszon **Logic Apps Management**. 

   ![A logikai alkalmazás futtatásának állapota és száma](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

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

   * **Küldje el újra:** Is küldje el újra egy vagy több logikai alkalmazás futtatásának a sikertelen, sikeres volt, vagy továbbra is futnak. Jelölje be a jelölőnégyzeteket a futtatások újraküldése, és válassza a kívánt **küldje el újra**. 

     ![Logikaialkalmazás-futtatások újraküldése](media/logic-apps-monitor-your-logic-apps-oms/logic-app-resubmit.png)

1. Ezek az eredmények szűréséhez hajthat végre ügyféloldali és a kiszolgálóoldali szűrés.

   * **Ügyféloldali szűrő**: Az egyes oszlopok válassza ki a kívánt szűrőket, például:

     ![Példa oszlopszűrők](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * **Kiszolgálóoldali szűrés**: Egy adott időtartomány kiválasztásához, vagy fut, amely megjelenik, számának korlátozásához használja a hatókör-vezérlő az oldal tetején. Alapértelmezés szerint csak 1000 rekordok jelennek meg egyszerre.
   
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
     
     A log analytics lapon frissítse a lekérdezéseket, és tekintse meg az eredményeket a táblából. Ez a lekérdezés használ [Kusto-lekérdezés nyelvi](https://aka.ms/LogAnalyticsLanguageReference), amely szerkesztheti, ha meg szeretné tekinteni a különböző eredményt. 

     ![a log analytics - lekérdezés megtekintése](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>További lépések

* [B2B üzenetek megfigyelése](../logic-apps/logic-apps-monitor-b2b-message.md)
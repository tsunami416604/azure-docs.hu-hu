---
title: "B2B tranzakciók figyelése és naplózás - Azure Logic Apps beállítása |} Microsoft Docs"
description: "Figyelő AS2, X 12 és EDIFACT-üzenetek, indítsa el az integrációs fiók diagnosztikai naplózás"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/21/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: f717dae9a70a96944b623f22b90cf8c5a943f382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-set-up-diagnostics-logging-for-b2b-communication-in-integration-accounts"></a>Figyelheti és integrációs fiókok B2B kommunikáció diagnosztikai naplózás beállítása

Miután beállította a B2B kommunikációját két üzleti folyamatok vagy a integrációs fiókon keresztül alkalmazásokat futtató entitásokból tudjon cserélni egymással üzeneteket. Ez a kommunikáció megfelelően működik-e, beállíthatja az AS2, X12, figyelés és EDIFACT-üzenetek, együtt a integrációs fiókhoz diagnosztikai naplózás megerősítéséhez a [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md) szolgáltatás. Ez a szolgáltatás [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md) figyeli a felhőalapú és helyszíni környezetben, így azok rendelkezésre állását és teljesítményét, karbantartása és is a futásidejű részleteit, illetve a szélesebb hibakereséshez eseményeket gyűjti. Emellett [használja a diagnosztikai adatok más szolgáltatásokkal](#extend-diagnostic-data), például az Azure Storage és az Azure Event Hubs.

## <a name="requirements"></a>Követelmények

* Egy logikai alkalmazást a diagnosztikai naplózás be van állítva. Ismerje meg, [adott logikai alkalmazás naplózásának beállítása](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

  > [!NOTE]
  > Miután teljesítette ezt a követelményt, rendelkeznie kell egy munkaterület a [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md). Az azonos OMS-munkaterület beállításakor integrációs fiókja naplózás használjon. Ha még nem rendelkezik az OMS-munkaterület, [OMS-munkaterület létrehozása](../log-analytics/log-analytics-get-started.md).

* Integráció fiók, amely csatolva van a Logic Apps alkalmazást. Ismerje meg, [integrációs fiók létrehozása egy hivatkozás a Logic Apps alkalmazást](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging-for-your-integration-account"></a>Diagnosztika integrációs fiókja naplózásának engedélyezése

Vagy közvetlenül integrációs fiókjából naplózás bekapcsolása vagy [az Azure-figyelő szolgáltatás](#azure-monitor-service). Az Azure biztosít alapvető figyelési infrastruktúra szintű adatokkal. További információ [Azure figyelő](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md).

### <a name="turn-on-diagnostics-logging-directly-from-your-integration-account"></a>Diagnosztika közvetlen integráció fiókjából naplózás engedélyezése

1. Az a [Azure-portálon](https://portal.azure.com), található, és válassza ki a integrációs fiókját. A **figyelés**, válassza a **diagnosztikai naplók** itt látható módon:

   ![Keresés, és válassza ki a integrációs fiókját, válassza a "Diagnosztikai naplók"](media/logic-apps-monitor-b2b-message/integration-account-diagnostics.png)

2. Miután kiválasztotta a integrációs fiók, akkor a következő értékek automatikusan ki van jelölve. Ha ezek az értékek megfelelőek, válassza ki a **a diagnosztika bekapcsolásához**. Ellenkező esetben válassza ki a kívánt értékeket:

   1. A **előfizetés**, válassza ki az Azure-előfizetés, amelyet a integrációs fiókját.
   2. A **erőforráscsoport**, válassza ki az erőforráscsoportot, amelyet a integrációs fiókját.
   3. A **erőforrástípus**, jelölje be **integrációs fiókok**. 
   4. A **erőforrás**, válassza ki a integrációs fiókját. 
   5. Válasszon **a diagnosztika bekapcsolásához**.

   ![Diagnosztika a integrációs fiók beállítása](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. A **diagnosztikai beállítások**, majd **állapot**, válassza a **a**.

   ![Kapcsolja be az Azure Diagnostics](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Az OMS-munkaterület és a naplózási használandó, ahogy adatok most kijelölése:

   1. Válassza ki **küldeni a Naplóelemzési**. 
   2. A **Naplóelemzési**, válassza a **konfigurálása**. 
   3. A **OMS-munkaterület**, jelölje be az OMS-munkaterület naplózásának használni.
   4. A **napló**, jelölje be a **IntegrationAccountTrackingEvents** kategóriát.
   5. Válassza a **Mentés** elemet.

   ![Diagnosztikai adatokat küldhet a napló Naplóelemzési beállítása](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Most [állítsa be az OMS B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-diagnostics-logging-through-azure-monitor"></a>Diagnosztikai naplózás keresztül Azure-figyelő bekapcsolása

1. Az a [Azure-portálon](https://portal.azure.com), a fő Azure menüben válasszon **figyelő**, **diagnosztikai naplók**. Ezután válassza ki a integrációs fiókját, ahogy az itt látható:

   ![Válassza a "Figyelése", "A diagnosztikai naplók", az integráció fiók kiválasztása](media/logic-apps-monitor-b2b-message/monitor-service-diagnostics-logs.png)

2. Miután kiválasztotta a integrációs fiók, akkor a következő értékek automatikusan ki van jelölve. Ha ezek az értékek megfelelőek, válassza ki a **a diagnosztika bekapcsolásához**. Ellenkező esetben válassza ki a kívánt értékeket:

   1. A **előfizetés**, válassza ki az Azure-előfizetés, amelyet a integrációs fiókját.
   2. A **erőforráscsoport**, válassza ki az erőforráscsoportot, amelyet a integrációs fiókját.
   3. A **erőforrástípus**, jelölje be **integrációs fiókok**.
   4. A **erőforrás**, válassza ki a integrációs fiókját.
   5. Válasszon **a diagnosztika bekapcsolásához**.

   ![Diagnosztika a integrációs fiók beállítása](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

3. A **diagnosztikai beállítások**, válassza a **a**.

   ![Kapcsolja be az Azure Diagnostics](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account-2.png)

4. Naplózási OMS munkaterületet, és az esemény kategória most kijelölése látható módon:

   1. Válassza ki **küldeni a Naplóelemzési**. 
   2. A **Naplóelemzési**, válassza a **konfigurálása**. 
   3. A **OMS-munkaterület**, jelölje be az OMS-munkaterület naplózásának használni.
   4. A **napló**, jelölje be a **IntegrationAccountTrackingEvents** kategóriát.
   5. Amikor elkészült, válassza ki a **mentése**.

   ![Diagnosztikai adatokat küldhet a napló Naplóelemzési beállítása](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

5. Most [állítsa be az OMS B2B üzenetek nyomon követése](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="extend-how-and-where-you-use-diagnostic-data-with-other-services"></a>Hogyan és hol diagnosztikai adatok használja más szolgáltatásokkal

Azure Naplóelemzés, valamint bővítheti, hogyan használhatja a Logic Apps alkalmazást diagnosztikai adatok más Azure-szolgáltatásokkal, például: 

* [Az Azure diagnosztikai naplók az Azure Storage archív](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md)
* [Az adatfolyam Azure diagnosztikai naplók az Azure Event hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

Ezután a get valós idejű figyelés telemetriai adatok és más szolgáltatások analytics segítségével például [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) és [Power BI](../log-analytics/log-analytics-powerbi.md). Példa:

* [Az adatfolyam Stream Analytics Eseményközpontokból származó adatokat](../stream-analytics/stream-analytics-define-inputs.md)
* [A Stream Analytics adatfolyam-továbbítási adatok elemzése és a Power bi-ban a valós idejű elemzési irányítópult létrehozása](../stream-analytics/stream-analytics-power-bi-dashboard.md)

A beállítások határozzák meg, amely azt szeretné beállítani, győződjön meg arról, hogy Ön első [az Azure storage-fiók létrehozása](../storage/common/storage-create-storage-account.md) vagy [hozzon létre egy Azure event hubs](../event-hubs/event-hubs-create.md). Válassza a beállítások érhetők el, hol szeretné elküldeni a diagnosztikai adatok:

![Adatok küldése az Azure storage-fiók vagy esemény hub](./media/logic-apps-monitor-b2b-message/storage-account-event-hubs.png)

> [!NOTE]
> Megőrzési időtartamú alkalmazása csak akkor, ha úgy dönt, hogy a tárfiókot használja.

## <a name="supported-tracking-schemas"></a>Támogatott sémák nyomon követése

Azure támogatja ezeket sématípusok, amely kivéve az egyéni típus sémák rögzített nyomon követését.

* [AS2-követési séma](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési séma](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Egyéni követési séma](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Következő lépések

* [Az OMS B2B üzenetek nyomon](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "OMS követése B2B üzenetek")
* [További tudnivalók a vállalati integrációs csomag](../logic-apps/logic-apps-enterprise-integration-overview.md "további információ a vállalati integrációs csomag")


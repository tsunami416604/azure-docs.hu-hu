---
title: Azure Security Center riasztások és javaslatok exportálása a SIEM-re | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állítható be a biztonsági riasztások és javaslatok folyamatos exportálása a SIEM-re
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: bad3be86bd85f7e5cebcf9445d14d836c73a87ef
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470792"
---
# <a name="export-security-alerts-and-recommendations-preview"></a>Biztonsági riasztások és javaslatok exportálása (előzetes verzió)

Azure Security Center részletes biztonsági riasztásokat és javaslatokat hoz létre. Ezeket megtekintheti a portálon vagy a programozott eszközökön keresztül is. Előfordulhat, hogy exportálnia kell ezeket az adatokat, vagy a környezetében más figyelési eszközökre is el kell küldenie. 

Ez a cikk azokat az előzetes verziójú eszközöket ismerteti, amelyek lehetővé teszik a riasztások és javaslatok manuális vagy folyamatos exportálását.

Az alábbi eszközöket használhatja:

* Részletes jelentések készítése CSV-ként
* Exportálás Log Analytics munkaterületekre
* Exportálás az Azure Event Hubsba (harmadik féltől származó SIEM-ekkel való integrációhoz)

## <a name="setting-up-a-continuous-export"></a>Folyamatos exportálás beállítása

1. A Security Center oldalsávján kattintson a **díjszabás & beállítások**elemre.

1. Válassza ki azt az előfizetést, amelyhez be szeretné állítani az adatexportálást.
    
1. Az előfizetés beállítások oldalának oldalsávján válassza a **folyamatos Exportálás (előzetes verzió)** lehetőséget.

    [![exportálási lehetőségek a Azure Security Center](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Itt láthatja az exportálási beállításokat. Minden elérhető exportálási célponthoz van egy lap. 

1. Válassza ki az exportálni kívánt adattípust, és válasszon az egyes típusok szűrőinek közül (például csak a nagy súlyosságú riasztások exportálása).

1. Az "exportálási cél" területen válassza ki, hogy hová szeretné menteni az adatok mentését. Az adattárolók egy másik előfizetésben lévő célhelyre menthetők (például egy központi Event hub-példányon vagy egy központi Log Analytics munkaterületen).

1. Kattintson a **Save** (Mentés) gombra.

## <a name="continuous-export-through-azure-event-hubs"></a>Folyamatos Exportálás az Azure Event Hubs  

> [!NOTE]
> A legtöbb esetben az Azure Event Hubs-t használja a leghatékonyabb módszer a megfigyelési és a külső eszközökre való továbbításra. [Ez a cikk](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) rövid leírást tartalmaz arról, hogy miként továbbíthatja a különböző forrásokból származó figyelési adatok egy Event hubhoz való továbbítását, és részletes útmutatásra mutató hivatkozásokat talál.

> [!NOTE]
> Ha korábban Security Center riasztásokat exportált egy SIEM-re az Azure Activity log használatával, az alábbi eljárás lecseréli ezt a módszert.

Az exportált adattípusok esemény-sémáinak megtekintéséhez keresse fel az [Event hub esemény-sémáit](https://aka.ms/ASCAutomationSchemas).

### <a name="to-integrate-with-a-siem"></a>Integrálás a SIEM-mel 

Miután konfigurálta a kiválasztott Security Center-adatait az Azure Event Hubsba, beállíthatja a megfelelő összekötőt a SIEM-hez az alábbi utasításokat követve.

Kövesse az SIEM-hez kapcsolódó utasításokat [ezen az oldalon](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/?cdn=disable) , és használja a megfelelő összekötőt:

* **Splunk** – a [splunk Azure monitor-bővítményének](https://splunkbase.splunk.com/app/3534/) használata
* **IBM QRadar** – [manuálisan konfigurált naplózási forrás](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_logsource_microsoft_azure_event_hubs.html) használata
* **ArcSight** – a [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) használata

Ha az **Azure Sentinel**szolgáltatást használja, használja a natív Azure Security Center riasztási [adatösszekötőt](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) .

Ha a folyamatosan exportált adatok automatikusan a konfigurált esemény központból az Azure Adatkezelőba helyezi át, használja az [Event hub adatainak](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)betöltése az Azure-ba adatkezelő című témakör utasításait.


## <a name="continuous-export-to-a-log-analytics-workspace"></a>Folyamatos exportálás Log Analytics munkaterületre

Log Analytics munkaterületre való exportáláshoz a munkaterületen engedélyezni kell Security Center ingyenes vagy standard szintű Log Analytics megoldásait. Ha a Azure Portal használja, a folyamatos exportálás engedélyezésekor a rendszer automatikusan engedélyezi a Security Center ingyenes szintű megoldást. Ha azonban programozott módon konfigurálja a folyamatos exportálási beállításokat, manuálisan kell kiválasztania a szükséges munkaterület ingyenes vagy standard díjszabási szintjét a **díjszabási & beállításain**belül.  

### <a name="log-analytics-tables-and-schemas"></a>Táblák és sémák Log Analytics

A biztonsági riasztások és javaslatok tárolása a *SecurityAlert* és a *SecurityRecommendations* táblákban történik. Az ezeket a táblákat tartalmazó Log Analytics megoldás neve attól függ, hogy az ingyenes vagy a standard szinten van-e (lásd a [díjszabást](security-center-pricing.md)): biztonság ("Security and audit") vagy SecurityCenterFree.

![A * SecurityAlert * tábla Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Az exportált adattípusok esemény-sémáinak megtekintéséhez keresse fel a [log Analytics Table-sémákat](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Az exportált biztonsági riasztások és javaslatok megtekintése Azure Monitor

Bizonyos esetekben dönthet úgy, hogy megtekinti az exportált biztonsági riasztásokat és/vagy javaslatokat [Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor egységes riasztási élményt nyújt számos Azure-riasztáshoz, beleértve a diagnosztikai naplót, a metrikus riasztásokat és az egyéni riasztásokat Log Analytics munkaterület-lekérdezések alapján.

Ha Azure Monitor Security Center riasztásait és javaslatait szeretné megtekinteni, Log Analytics lekérdezéseken alapuló riasztási szabályt állítson be (naplózási riasztás):

1. A Azure Monitor **riasztásai** lapon kattintson az **új riasztási szabály**elemre.

    ![Azure Monitor riasztások lapja](./media/continuous-export/azure-monitor-alerts.png)

1. A szabály létrehozása lapon konfigurálja az új szabályt (ugyanúgy, mint a [naplózási riasztási szabály konfigurálása a Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * Az **erőforrás**mezőben válassza ki azt a log Analytics munkaterületet, amelyhez biztonsági riasztásokat és javaslatokat exportált.

    * A **feltétel**beállításnál válassza az **egyéni naplók keresése**lehetőséget. A megjelenő oldalon konfigurálja a lekérdezést, az lookback időszakot és a gyakorisági időszakot. A keresési lekérdezésben beírhatja a *SecurityAlert* vagy a *SecurityRecommendation* kifejezést, hogy lekérdezze azokat az adattípusokat, amelyek Security Center folyamatos exportálást végeznek, amikor engedélyezi a folyamatos exportálást log Analytics szolgáltatásba. 
    
    * Megadhatja az aktiválni kívánt [műveleti csoportot](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) is. A műveleti csoportok elindíthatják az e-mailek küldését, a ITSM jegyeket, a webhookokat és egyebeket.
    ![Azure Monitor riasztási szabálya](./media/continuous-export/azure-monitor-alert-rule.png)

Ekkor megjelenik az új Azure Security Center riasztások vagy javaslatok (a konfigurációtól függően) Azure Monitor riasztások, a műveleti csoport automatikus indítása (ha meg van megadva).

## <a name="manual-one-time-export-of-security-alerts"></a>Biztonsági riasztások manuális egyszeri exportálása

A riasztások vagy javaslatok CSV-jelentésének letöltéséhez nyissa meg a **biztonsági riasztások** vagy **javaslatok** lapot, és kattintson a **CSV-jelentés letöltése** gombra.

[riasztási adat![ek letöltése CSV-fájlként](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Ezek a jelentések riasztásokat és javaslatokat tartalmaznak az aktuálisan kijelölt előfizetésekben lévő erőforrásokra vonatkozóan.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja a javaslatok és riasztások folyamatos exportálását. Azt is megtanulta, hogyan töltheti le a riasztási adatait CSV-fájlként. 

Kapcsolódó anyagok esetében tekintse meg a következő dokumentációt: 

- [Az Azure Event Hubs dokumentációja](https://docs.microsoft.com/azure/event-hubs/)
- [Az Azure Sentinel dokumentációja](https://docs.microsoft.com/azure/sentinel/)
- [Az Azure Monitor dokumentációja](https://docs.microsoft.com/azure/azure-monitor/)
- [Munkafolyamat-automatizálás és folyamatos exportálás adattípusú sémák](https://aka.ms/ASCAutomationSchemas)

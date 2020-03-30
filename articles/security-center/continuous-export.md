---
title: Az Azure Security Center riasztásainak és javaslatainak exportálása a SIEM-ek beára ) | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan lehet beállítani a biztonsági riasztások és ajánlások folyamatos exportálását a SIEM-ekbe
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: 19fdcc7b590c6ad6873c7808ae26d218bbda7f5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158969"
---
# <a name="export-security-alerts-and-recommendations"></a>Biztonsági riasztások és javaslatok exportálása

Az Azure Security Center részletes biztonsági riasztásokat és javaslatokat hoz létre. Megtekintheti őket a portálon vagy programozott eszközöksegítségével. Előfordulhat, hogy ezeket az adatokat exportálnia kell, vagy el kell küldenie a környezetmás figyelési eszközöknek. 

Ez a cikk azokat az eszközöket ismerteti, amelyek lehetővé teszik a riasztások és javaslatok manuális exportálását vagy folyamatos, folyamatos módon történő exportálását.

Az alábbi eszközökkel:

* Folyamatos exportálás a Log Analytics-munkaterületekre
* Folyamatos exportálás az Azure Event Hubs szolgáltatásba (külső SIEM-ekkel való integrációhoz)
* Exportálás CSV-re (egyszeri)


## <a name="setting-up-a-continuous-export"></a>Folyamatos exportálás beállítása

Az alábbi lépések szükségesek, függetlenül attól, hogy a Log Analytics munkaterületre vagy az Azure Event Hubs-ba való folyamatos exportálást állít-e be.

1. A Biztonsági központ oldalsávján válassza **a & beállítások díjszabása lehetőséget.**

1. Válassza ki azt az előfizetést, amelyhez konfigurálni szeretné az adatexportálást.
    
1. Az előfizetés beállítási lapjának oldalsávján válassza a **Folyamatos exportálás**lehetőséget.

    [Exportálási beállítások az Azure Security Centerben ![](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) Itt láthatja az exportálási beállításokat. Minden elérhető exportálási célhoz van egy lap. 

1. Válassza ki az exportálni kívánt adattípust, és válasszon az egyes típusok szűrői közül (például csak a súlyos riasztásokat exportálja).

1. Az "Exportálási cél" területen válassza ki, hogy hová szeretné menteni az adatokat. Az adatok menthetők egy másik előfizetés (például egy Központi Eseményközpont-példány vagy egy központi Log Analytics-munkaterület) célban.

1. Kattintson a **Mentés** gombra.



## <a name="configuring-siem-integration-via-azure-event-hubs"></a>A SIEM-integráció konfigurálása az Azure Event Hubs-on keresztül

Az Azure Event Hubs nagyszerű megoldás a streamelési adatok programozott módon történő fogyasztásához. Az Azure Security Center-riasztások és javaslatok esetén ez az előnyben részesített módja a külső SIEM-em-kel való integrációnak.

> [!NOTE]
> A legtöbb esetben a figyelési adatok külső eszközökre történő streamelésének leghatékonyabb módja az Azure Event Hubs használata. [Ez a cikk](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) rövid leírást ad arról, hogyan streamelheti a figyelési adatokat a különböző forrásokból egy Eseményközpontba, és hogyan hivatkozhat részletes útmutatásra.

> [!NOTE]
> Ha korábban exportált Security Center riasztások egy SIEM az Azure-tevékenység napló, az alábbi eljárás váltja fel ezt a módszert.

Az exportált adattípusok eseménysémák megtekintéséhez látogasson el az [Event Hub eseménysémákba.](https://aka.ms/ASCAutomationSchemas)


### <a name="to-integrate-with-a-siem"></a>Integrálás egy SIEM 

Miután konfigurálta a kiválasztott Security Center-adatok folyamatos exportálását az Azure Event Hubs-ba, beállíthatja a megfelelő összekötőt a SIEM-hez:

* **Azure Sentinel** – használja a natív Azure Security Center riasztások [adatösszekötő](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) ott kínált.
* **Splunk** - Az [Azure Monitor bővítményhasználata splunkhoz](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md)
* **IBM QRadar** - [Manuálisan konfigurált naplóforrás](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html) használata
* **ArcSight** – [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) használata

Továbbá, ha a folyamatosan exportált adatokat automatikusan át szeretné helyezni a konfigurált Event Hubról az Azure Data Explorerbe, kövesse az [Event Hubból az adatok betöltése az Azure Data Explorerbe](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)vonatkozó utasításokat.



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Folyamatos exportálás a Log Analytics-munkaterületre

Ha az Azure Security Center adatait szeretné elemezni egy Log Analytics-munkaterületen belül, vagy azure-riasztásokat szeretne használni a Security Centerrel együtt, állítsa be a folyamatos exportálást a Log Analytics-munkaterületre.

A Log Analytics-munkaterületre való exportáláshoz engedélyeznie kell a Security Center Log Analytics-megoldásait a munkaterületen. Ha az Azure Portalt használja, a Security Center ingyenes szintű megoldása automatikusan engedélyezve lesz, ha engedélyezi a folyamatos exportálást. Ha azonban programozott módon konfigurálja a folyamatos exportálási beállításokat, manuálisan kell kiválasztania a szükséges munkaterület ingyenes vagy szabványos tarifacsomagját a **díjszabási & beállítások között.**  

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics-táblák és sémák

A biztonsági riasztások és javaslatok a *SecurityAlert* és *securityrecommendations* táblákban tárolódnak. A táblákat tartalmazó Log Analytics-megoldás neve attól függ, hogy az ingyenes vagy a normál szinten (lásd: [díjszabás](security-center-pricing.md)): Biztonság("Biztonság és naplózás") vagy securitycenterfree.

![A *SecurityAlert* tábla a Log Analytics szolgáltatásban](./media/continuous-export/log-analytics-securityalert-solution.png)

Az exportált adattípusok eseménysémáinak megtekintéséhez látogasson el a [Log Analytics táblasémákba.](https://aka.ms/ASCAutomationSchemas)

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Exportált biztonsági riasztások és javaslatok megtekintése az Azure Monitorban

Bizonyos esetekben dönthet úgy, hogy az exportált biztonsági riasztásokat és/vagy javaslatokat az [Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)tekinti meg. 

Az Azure Monitor egységes riasztási élményt biztosít a különböző Azure-riasztásokhoz, beleértve a diagnosztikai naplót, a metrikariasztásokat és a Log Analytics-munkaterületi lekérdezéseken alapuló egyéni riasztásokat.

Az Azure Monitor Security Center szolgáltatásából érkező riasztások és javaslatok megtekintéséhez állítson be egy riasztási szabályt a Log Analytics-lekérdezések (Log Alert) alapján:

1. Az Azure Monitor **riasztások** lapján kattintson az **Új riasztási szabály**elemre.

    ![Az Azure Monitor riasztási lapja](./media/continuous-export/azure-monitor-alerts.png)

1. A szabály létrehozása lapon konfigurálja az új szabályt (ugyanúgy, ahogy az [Azure Monitorban naplóriasztási szabályt konfigurálna):](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

    * **Erőforrás esetén**válassza ki azt a Log Analytics-munkaterületet, amelybe biztonsági riasztásokat és javaslatokat exportált.

    * A **Feltétel mezőben**válassza **az Egyéni naplókeresés lehetőséget.** A megjelenő lapon konfigurálja a lekérdezést, a visszatekintési időszakot és a gyakorisági időszakot. A keresési lekérdezésben a *SecurityAlert* vagy a *SecurityRecommendation* beírhatja a Security Center által folyamatosan exportált adattípusok lekérdezését a Log Analytics folyamatos exportálása funkció engedélyezésével. 
    
    * Szükség esetén konfigurálja az aktiválni kívánt [műveletcsoportot.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) A műveletcsoportok aktiválhatják az e-mailek küldését, az ITSM-jegyeket, a WebHook-okat és egyebeket.
    ![Az Azure Monitor riasztási szabály](./media/continuous-export/azure-monitor-alert-rule.png)

Most antól megjelennek az Azure Security Center új riasztásai vagy javaslatai (a konfigurációtól függően) az Azure Monitor-riasztásokban, egy műveletcsoport automatikus aktiválásával (ha van ilyen).

## <a name="manual-one-time-export-of-security-alerts"></a>A biztonsági riasztások manuális egyszeri exportálása

Ha csv-jelentést szeretne letölteni riasztásokhoz vagy javaslatokhoz, nyissa meg a **Biztonsági riasztások** vagy **javaslatok** lapot, és kattintson a **CSV-jelentés letöltése** gombra.

[![A riasztások adatainak letöltése CSV-fájlként](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Ezek a jelentések riasztásokat és javaslatokat tartalmaznak az aktuálisan kiválasztott előfizetések ből származó erőforrásokhoz.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja a javaslatok és riasztások folyamatos exportálását. Azt is megtanulta, hogyan töltheti le a riasztási adatokat CSV-fájlként. 

A kapcsolódó anyagokat lásd a következő dokumentációban: 

- [Az Azure Event Hubs dokumentációja](https://docs.microsoft.com/azure/event-hubs/)
- [Az Azure Sentinel dokumentációja](https://docs.microsoft.com/azure/sentinel/)
- [Az Azure Monitor dokumentációja](https://docs.microsoft.com/azure/azure-monitor/)
- [Munkafolyamat-automatizálás és folyamatos exportálási adattípusok sémái](https://aka.ms/ASCAutomationSchemas)

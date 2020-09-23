---
title: Azure Security Center riasztások és javaslatok exportálása a SIEM-re | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állítható be a biztonsági riasztások és javaslatok folyamatos exportálása a SIEM-re
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: memildin
ms.openlocfilehash: cf8fdd8d91c035d374277c4752fb761c0c4e72c7
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905620"
---
# <a name="export-security-alerts-and-recommendations"></a>Biztonsági riasztások és javaslatok exportálása

Azure Security Center részletes biztonsági riasztásokat és javaslatokat hoz létre. Ezeket megtekintheti a portálon vagy a programozott eszközökön keresztül is. Előfordulhat, hogy exportálnia kell ezeket az adatokat, vagy a környezetében más figyelési eszközökre is el kell küldenie. 

Ez a cikk azokat az eszközöket ismerteti, amelyek lehetővé teszik a riasztások és javaslatok manuális exportálását manuálisan vagy folyamatos, folyamatos módon.

Az alábbi eszközöket használhatja:

* Folyamatos exportálás Log Analytics munkaterületekre
* Folyamatos Exportálás az Azure Event Hubsba (harmadik féltől származó SIEM-ekkel való integráció esetén)
* Exportálás CSV-be (egy alkalommal)



## <a name="availability"></a>Rendelkezésre állás

|Szempont|Részletek|
|----|:----|
|Kiadás állapota:|Általánosan elérhető (GA)|
|Árképzési|Ingyenes|
|Szükséges szerepkörök és engedélyek:|**Biztonsági rendszergazdai szerepkör** az erőforráscsoporthoz (vagy **tulajdonos**)<br>A cél erőforráshoz is írási engedéllyel kell rendelkeznie|
|Felhők|![Yes](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Yes](./media/icons/yes-icon.png) US Gov<br>![Yes](./media/icons/yes-icon.png) Kínai gov (az Event hub-hoz), egyéb gov|
|||



## <a name="set-up-a-continuous-export"></a>Folyamatos exportálás beállítása

Az alábbi lépések szükségesek, függetlenül attól, hogy folyamatos exportálást állít be Log Analytics munkaterületre vagy az Azure Event Hubsra.

1. A Security Center oldalsávján válassza a **díjszabás & beállítások**lehetőséget.

1. Válassza ki azt az előfizetést, amelyhez be szeretné állítani az adatexportálást.
    
1. Az előfizetés beállítások oldalának oldalsávján válassza a **folyamatos exportálás**lehetőséget.

    A [ ![ Azure Security Center exportálási](media/continuous-export/continuous-export-options-page.png)](media/continuous-export/continuous-export-options-page.png#lightbox) lehetőségei itt láthatók az exportálási beállítások. Minden elérhető exportálási célponthoz van egy lap. 

1. Válassza ki az exportálni kívánt adattípust, és válasszon az egyes típusok szűrőinek közül (például csak a nagy súlyosságú riasztások exportálása).

1. Ha a választás a következő négy javaslat egyikét tartalmazza, akkor a sebezhetőségi felmérés eredményei együttesen is felvehetők:

    - A sebezhetőségi felmérés eredményeit az SQL-adatbázisokban szervizelni kell
    - A biztonsági rések felmérésének eredményeit a gépeken lévő SQL-kiszolgálókon szervizelni kell (előzetes verzió)
    - A Azure Container Registry lemezképekben található biztonsági réseket szervizelni kell (Qualys-alapú)
    - A virtuális gépek biztonsági réseit szervizelni kell

    Ahhoz, hogy a megállapítások szerepeljenek a javaslatokban, engedélyezze a **biztonsági megállapítások belefoglalása** lehetőséget.

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Biztonsági megállapítások bekapcsolása a folyamatos exportálási konfigurációban" :::


1. Az "exportálási cél" területen válassza ki, hogy hová szeretné menteni az adatok mentését. Az adattárolók egy másik előfizetésben lévő célhelyre menthetők (például egy központi Event hub-példányon vagy egy központi Log Analytics munkaterületen).

1. Kattintson a **Mentés** gombra.


## <a name="set-up-continuous-export-via-the-rest-api"></a>Folyamatos exportálás beállítása a REST API használatával

A folyamatos exportálás funkció a Azure Security Center [automations API](https://docs.microsoft.com/rest/api/securitycenter/automations)használatával konfigurálható és kezelhető. Ezzel az API-val létrehozhat vagy frissíthet automatizálásokat a következő lehetséges célhelyek bármelyikére való exportáláshoz:

- Azure Event Hub
- Log Analytics-munkaterület
- Azure Logic Apps 

Az API olyan további funkciókat biztosít, amelyek nem érhetők el a Azure Portal, például:

* **Nagyobb mennyiség** – az API lehetővé teszi, hogy több exportálási konfigurációt hozzon létre egyetlen előfizetésen. Security Center portál felhasználói felületének **folyamatos exportálás** lapja csak egy exportálási konfigurációt támogat az előfizetések esetében.

* **További funkciók** – az API olyan további paramétereket kínál, amelyek nem jelennek meg a felhasználói felületen. Hozzáadhat például címkéket az Automation-erőforráshoz, valamint megadhatja az exportálást a riasztások és javaslatok szélesebb készlete alapján, mint amelyek az Security Center portál felhasználói felületének **folyamatos exportálás** lapján elérhetők.

* Koncentráltabb **hatókör** – az API részletesebb szintet biztosít az exportálási konfigurációk hatóköréhez. Az API-val való exportálás meghatározásakor ezt az erőforráscsoport szintjén teheti meg. Ha a **folyamatos exportálás** lapot használja Security Center portál felhasználói felületén, azt az előfizetés szintjén kell megadnia.

    > [!TIP]
    > Ha az API használatával több exportálási konfigurációt állított be, vagy ha csak API-paramétereket használt, akkor ezek a további funkciók nem jelennek meg a Security Center felhasználói felületen. Ehelyett megjelenik egy szalagcím, amely tájékoztatja, hogy más konfigurációk léteznek.

További információ az automations API-ról a [REST API dokumentációjában](https://docs.microsoft.com/rest/api/securitycenter/automations).



## <a name="configure-siem-integration-via-azure-event-hubs"></a>SIEM-integráció konfigurálása az Azure Event Hubs használatával

Az Azure Event Hubs nagyszerű megoldás a folyamatos átviteli adatmennyiség programozott módon. Azure Security Center riasztások és javaslatok esetében ez az előnyben részesített módszer a harmadik féltől származó SIEM integrálására.

> [!NOTE]
> A legtöbb esetben az Azure Event Hubs-t használja a leghatékonyabb módszer a megfigyelési és a külső eszközökre való továbbításra. [Ez a cikk](https://docs.microsoft.com/azure/azure-monitor/platform/stream-monitoring-data-event-hubs) rövid leírást tartalmaz arról, hogy miként továbbíthatja a különböző forrásokból származó figyelési adatok egy Event hubhoz való továbbítását, és részletes útmutatásra mutató hivatkozásokat talál.

> [!NOTE]
> Ha korábban Security Center riasztásokat exportált egy SIEM-re az Azure Activity log használatával, az alábbi eljárás lecseréli ezt a módszert.

Az exportált adattípusok esemény-sémáinak megtekintéséhez keresse fel az [Event hub esemény-sémáit](https://aka.ms/ASCAutomationSchemas).


### <a name="to-integrate-with-a-siem"></a>Integrálás a SIEM-mel 

Miután konfigurálta a kiválasztott Security Center-adatainak az Azure Event Hubsba való folyamatos exportálását, beállíthatja az SIEM-hez tartozó megfelelő összekötőt:

* **Azure Sentinel** – használja az itt elérhető natív Azure Security Center riasztások [adatösszekötőjét](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center) .
* **Splunk** – a [splunk Azure monitor-bővítményének](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) használata
* **IBM QRadar** – [manuálisan konfigurált naplózási forrás](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/t_dsm_guide_microsoft_azure_enable_event_hubs.html) használata
* **ArcSight** – a [SmartConnector](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) használata

Ha a folyamatosan exportált adatok automatikusan a konfigurált esemény központból az Azure Adatkezelőba helyezi át, használja az [Event hub adatainak](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub)betöltése az Azure-ba adatkezelő című témakör utasításait.



## <a name="continuous-export-to-a-log-analytics-workspace"></a>Folyamatos exportálás Log Analytics munkaterületre

Ha egy Log Analytics-munkaterületen belül szeretné elemezni Azure Security Center adatait, vagy az Azure-riasztásokat az Security Center használatával együtt kívánja használni, állítsa be a folyamatos exportálást a Log Analytics munkaterületre.

Log Analytics munkaterületre való exportáláshoz a munkaterületen engedélyezni kell Security Center Log Analytics megoldásait. Ha a Azure Portal használja, a folyamatos exportálás engedélyezésekor a rendszer automatikusan engedélyezi Security Center ingyenes megoldását. Ha programozott módon konfigurálja a folyamatos exportálási beállításokat, a **díjszabási & beállítások** lapján manuálisan be kell kapcsolnia az Azure Defender be-vagy kikapcsolását.

### <a name="log-analytics-tables-and-schemas"></a>Táblák és sémák Log Analytics

A biztonsági riasztások és javaslatok tárolása a *SecurityAlert* és a *SecurityRecommendations* táblákban történik. Az alábbi táblákat tartalmazó Log Analytics-megoldás neve attól függ, hogy engedélyezve van-e az Azure Defender: Security ("Security and Audit") vagy a SecurityCenterFree.

![A * SecurityAlert * tábla Log Analytics](./media/continuous-export/log-analytics-securityalert-solution.png)

Az exportált adattípusok esemény-sémáinak megtekintéséhez keresse fel a [log Analytics Table-sémákat](https://aka.ms/ASCAutomationSchemas).

###  <a name="view-exported-security-alerts-and-recommendations-in-azure-monitor"></a>Az exportált biztonsági riasztások és javaslatok megtekintése Azure Monitor

Bizonyos esetekben dönthet úgy, hogy megtekinti az exportált biztonsági riasztásokat és/vagy javaslatokat [Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview). 

Azure Monitor egységes riasztási élményt nyújt számos Azure-riasztáshoz, beleértve a diagnosztikai naplót, a metrikus riasztásokat és az egyéni riasztásokat Log Analytics munkaterület-lekérdezések alapján.

Ha Azure Monitor Security Center riasztásait és javaslatait szeretné megtekinteni, Log Analytics lekérdezéseken alapuló riasztási szabályt állítson be (naplózási riasztás):

1. A Azure Monitor **riasztások** lapján válassza az **új riasztási szabály**lehetőséget.

    ![Azure Monitor riasztások lapja](./media/continuous-export/azure-monitor-alerts.png)

1. A szabály létrehozása lapon konfigurálja az új szabályt (ugyanúgy, mint a [naplózási riasztási szabály konfigurálása a Azure monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)):

    * Az **erőforrás**mezőben válassza ki azt a log Analytics munkaterületet, amelyhez biztonsági riasztásokat és javaslatokat exportált.

    * A **feltétel**beállításnál válassza az **egyéni naplók keresése**lehetőséget. A megjelenő oldalon konfigurálja a lekérdezést, az lookback időszakot és a gyakorisági időszakot. A keresési lekérdezésben beírhatja a *SecurityAlert* vagy a *SecurityRecommendation* kifejezést, hogy lekérdezze azokat az adattípusokat, amelyek Security Center folyamatos exportálást végeznek, amikor engedélyezi a folyamatos exportálást log Analytics szolgáltatásba. 
    
    * Megadhatja az aktiválni kívánt [műveleti csoportot](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) is. A műveleti csoportok elindíthatják az e-mailek küldését, a ITSM jegyeket, a webhookokat és egyebeket.
    ![Azure Monitor riasztási szabály](./media/continuous-export/azure-monitor-alert-rule.png)

Ekkor megjelenik az új Azure Security Center riasztások vagy javaslatok (a konfigurációtól függően) Azure Monitor riasztások, a műveleti csoport automatikus indítása (ha meg van megadva).

## <a name="manual-one-time-export-of-security-alerts"></a>Biztonsági riasztások manuális egyszeri exportálása

A riasztások vagy javaslatok CSV-jelentésének letöltéséhez nyissa meg a **biztonsági riasztások** vagy **javaslatok** lapot, és kattintson a **CSV-jelentés letöltése** gombra.

[![Riasztási adatfájlok letöltése CSV-fájlként](media/continuous-export/download-alerts-csv.png)](media/continuous-export/download-alerts-csv.png#lightbox)

> [!NOTE]
> Ezek a jelentések riasztásokat és javaslatokat tartalmaznak az aktuálisan kijelölt előfizetésekben lévő erőforrásokra vonatkozóan.



## <a name="faq---continuous-export"></a>Gyakori kérdések – folyamatos exportálás

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Milyen költségekkel jár az adatexportálás?

A folyamatos Exportálás engedélyezése díjmentes. A Log Analytics munkaterületen lévő adatok betöltésének és megőrzésének költségei az ott konfigurált konfigurációtól függően merülhetnek fel. 

További információ a [log Analytics munkaterület díjszabásáról](https://azure.microsoft.com/pricing/details/monitor/).

További információ az [Azure Event hub díjszabásáról](https://azure.microsoft.com/pricing/details/event-hubs/).


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja a javaslatok és riasztások folyamatos exportálását. Azt is megtanulta, hogyan töltheti le a riasztási adatait CSV-fájlként. 

Kapcsolódó anyagok esetében tekintse meg a következő dokumentációt: 

- [Az Azure Event Hubs dokumentációja](https://docs.microsoft.com/azure/event-hubs/)
- [Az Azure Sentinel dokumentációja](https://docs.microsoft.com/azure/sentinel/)
- [Az Azure Monitor dokumentációja](https://docs.microsoft.com/azure/azure-monitor/)
- [Munkafolyamat-automatizálás és folyamatos exportálás adattípusú sémák](https://aka.ms/ASCAutomationSchemas)

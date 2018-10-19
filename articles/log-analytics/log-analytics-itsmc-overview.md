---
title: Informatikai szolgáltatásfelügyeleti összekötő az Azure Log Analytics |} A Microsoft Docs
description: Ez a cikk ismerteti IT Service Management Connector (ITSMC), és ez a megoldás segítségével központilag felügyelhető és kezelhető az ITSM információ az Azure Log Analytics-munkaelemek, és hárítsa el a hibákat gyorsan.
services: log-analytics
documentationcenter: ''
author: jyothirmaisuri
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: v-jysur
ms.component: ''
ms.openlocfilehash: b99c14e6022fa34d41caaa02bfc9feecb3c840ce
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49407503"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Csatlakozás az Azure ITSM-eszközeit IT Service Management-összekötő használatával

![Informatikai szolgáltatásfelügyeleti összekötő szimbólum](./media/log-analytics-itsmc/itsmc-symbol.png)

Az IT Service Management Connector (ITSMC) lehetővé teszi az Azure és a egy támogatott IT Service Management (ITSM) termék vagy szolgáltatás összekapcsolása.

Azure-szolgáltatásokhoz hasonlóan a Log Analytics és az Azure Monitor biztosítanak eszközöket észlelheti, elemzése és az Azure- és -Azure erőforrások kapcsolatos hibaelhárítás. A munkaelemek általában egy problémához kapcsolódó azonban az ITSM-termék vagy szolgáltatás található. Az ITSM-összekötő problémák gyorsabb megoldását segítő Azure-ban és ITSM eszközök közötti kétirányú kapcsolatot biztosít.

ITSMC kapcsolatok az alábbi ITSM-eszközöket támogatja:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Az ITSMC segítségével

-  Munkaelem létrehozása az ITSM-eszközhöz, az Azure-riasztások (metrikákhoz kapcsolódó riasztások, a tevékenységnapló riasztások és a Log Analytics-riasztásokkal.) alapján.
-  Igény szerint szinkronizálja az incidenst, és módosítja az ITSM-eszközhöz kérelem adatai egy Azure Log Analytics-munkaterületet.


Megkezdheti az ITSM-összekötő az alábbi lépéseket követve:

1.  [Az ITSM-összekötő megoldás hozzáadása](#adding-the-it-service-management-connector-solution)
2.  [Az ITSM-kapcsolat létrehozása](#creating-an-itsm-connection)
3.  [A kapcsolat használata](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Felvétele az IT Service Management Connector megoldás

Kapcsolat létrehozása előtt kell hozzáadni az ITSM-összekötő megoldással.

1.  Az Azure Portalon, kattintson a **+ új** ikonra.

    ![Az Azure új erőforrás](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  Keresse meg **informatikai szolgáltatásfelügyeleti összekötő** a Marketplace-en, majd kattintson a **létrehozás**.

    ![ITSMC megoldás hozzáadása](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  Az a **OMS-munkaterület** területen válassza ki az Azure Log Analytics-munkaterületet, amelyre telepíteni a megoldás.
   >[!NOTE]
   >Az Azure Monitor folyamatos Váltás a Microsoft Operations Management Suite (OMS) részeként az OMS-munkaterületek most nevezzük Log Analytics-munkaterületek.
4.  Az a **OMS-munkaterület beállításait** területen válassza ki az erőforráscsoport, ahol szeretné a megoldás-erőforrás létrehozásához.

    ![ITSMC munkaterület](./media/log-analytics-itsmc/itsmc-solution-workspace.png)
    >[!NOTE]
    >Az Azure Monitor folyamatos Váltás a Microsoft Operations Management Suite (OMS) részeként az OMS-munkaterületek most nevezzük Log Analytics-munkaterületek.

5.  Kattintson a **Create** (Létrehozás) gombra.

Ha a megoldás erőforrás üzembe van helyezve, az oldal tetején megjelenik egy értesítés, az ablak jobb.


## <a name="creating-an-itsm--connection"></a>ITSM-kapcsolat létrehozása

Amint telepítette a megoldást, létrehozhat egy kapcsolatot.

Kapcsolat létrehozásához szüksége lesz az ITSM-eszközhöz, hogy a kapcsolat az ITSM-összekötő megoldásból származó adatok előkészítése.  

Az ITSM-termék csatlakozik, attól függően használja az alábbi lépéseket:

- [A System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Ha rendelkezik elő lett készítve az ITSM-eszközeit, az alábbi lépésekkel kapcsolat létrehozása:

1.  Lépjen a **összes erőforrás**, keressen **ServiceDesk(YourWorkspaceName)**.
2.  A **MUNKATERÜLET ADATFORRÁSAI** a bal oldali ablaktáblán kattintson a **ITSM-kapcsolatok**.
    ![ITSM-kapcsolatok](./media/log-analytics-itsmc/itsm-connections.png)

    Ez a lap megjeleníti a kapcsolatok listájában.
3.  Kattintson a **kapcsolat hozzáadása**.

    ![ITSM-kapcsolat hozzáadása](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  Adja meg a kapcsolati beállításokat leírtak szerint [ITSMC kapcsolat beállítása az ITSM-termékekkel/szolgáltatásokkal cikkébe](log-analytics-itsmc-connections.md).

    > [!NOTE]

    > Alapértelmezés szerint a ITSMC 24 óránként egyszer a frissíti a kapcsolat konfigurációs adatokat. Instantly szerkesztések és sablon szoftverfrissítésekkel, ellenőrizze a kapcsolati adatok frissítéséhez kattintson a **szinkronizálási** gombra a kapcsolat panelen.

    ![Kapcsolat frissítése](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>A megoldás használata
   Az ITSM-összekötő megoldással munkaelemek hozhat létre Azure-riasztások, a Log Analytics-riasztások és a Log Analytics-rekordok naplózása.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-munkatétel létrehozása az Azure-riasztások

Miután létrehozott ITSM-kapcsolatot, az ITSM-eszközben az Azure-riasztások alapján hozhat létre munkahelyi elem a **ITSM művelet** a **Műveletcsoportok**.

Műveletcsoportok műveleteket az Azure-riasztások aktiválása, moduláris és újrafelhasználható módszert biztosítanak. Műveletcsoportok használata metrikákhoz kapcsolódó riasztások, a tevékenységnapló-riasztások és az Azure Log Analytics-riasztások az Azure Portalon.

Kövesse az alábbi eljárást:

1. Az Azure Portalon, kattintson a **figyelő**.
2. A bal oldali ablaktáblán kattintson a **Műveletcsoportok**. A **műveleti csoport hozzáadása** ablak jelenik meg.

    ![Műveletcsoportok](media/log-analytics-itsmc/action-groups.png)

3. Adja meg **neve** és **ShortName** a műveleti csoport számára. Válassza ki a **erőforráscsoport** és **előfizetés** kívánja a műveletcsoport létrehozásához.

    ![Csoport részletei](media/log-analytics-itsmc/action-groups-details.png)

4. A műveletek listájában válassza ki a **ITSM** a legördülő menüből a **művelettípus**. Adjon meg egy **neve** a műveletet, majd kattintson a **részleteinek szerkesztése**.
5. Válassza ki a **előfizetés** ahol Log Analytics-munkaterület megtalálható. Válassza ki a **kapcsolat** neve (ITSM-összekötő neve), a munkaterület neve követ. Ha például a "MyITSMMConnector(MyWorkspace)."

    ![ITSM-művelet részletei](./media/log-analytics-itsmc/itsm-action-details.png)

6. Válassza ki **munkaelem** típusát a legördülő menüből.
   Válasszon egy meglévő sablont, vagy adja meg az ITSM-termék szükséges mezőket.
7. Kattintson az **OK** gombra.

Azure riasztási szabály létrehozása/szerkesztése, amikor egy műveletcsoportot, amely rendelkezik egy ITSM műveletet használja. A riasztás aktiválásakor a munkaelem szerepel az ITSM-eszköz létrehozása vagy frissítése.

>[!NOTE]

> ITSM-művelet díjszabási információkért tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/monitor/) a Műveletcsoportok.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Megjelenítheti és elemezheti az incidens, és módosítsa a kérelem adatai

A konfiguráció alapján a kapcsolat beállításához, ITSM-összekötő szinkronizálhatók az Incidenshez és Változáskéréshez kérelem adatok legfeljebb 120 nap. Ezek az adatok log rekord sémáját megtalálható a [következő szakasz](#additional-information).

Az incidenshez és változáskéréshez kérelem adatai az alábbi ábra a megoldás az ITSM-összekötő irányítópulttal.

![Log Analytics képernyő](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

Az irányítópult összekötő állapotát, amely a kapcsolatok problémák elemzéséhez kiindulási pontként használható információkat is biztosít.

Emellett az incidenseket, szemben az érintett számítógépek, a Service Map megoldás belül szinkronizált jelenítheti meg.

A Service Map automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és feltérképezi a szolgáltatások közötti kommunikációt. Lehetővé teszi, hogy a kiszolgálók megtekintéséhez, ahogyan Ön gondol rájuk: rendszerekként, amelyek kritikus fontosságú szolgáltatások biztosításához. A Service Map megmutatja a kapcsolatokat kiszolgálók, folyamatok és portok között, bármely TCP-kapcsolattal összekötött architektúrában semmilyen beállítást nem szükséges ügynököt telepíteni. [További információk](../operations-management-suite/operations-management-suite-service-map.md).

Ha a Service Map megoldást használja, az ügyfélszolgálati elemek létrehozása az ITSM-megoldások az alábbi példában látható módon tekintheti meg:

![Log Analytics képernyő](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

További információ: [Service Map](../operations-management-suite/operations-management-suite-service-map.md)


## <a name="additional-information"></a>További információ

### <a name="data-synced-from-itsm-product"></a>ITSM-termékben a szinkronizált adatok
Incidensek és változáskérések az ITSM-termékben a rendszer szinkronizálja a Log Analytics-munkaterületen, a kapcsolat konfigurációja alapján.

A következő információkat ITSMC által gyűjtött adatokat példákat mutat be:

> [!NOTE]

> A munkaelem-típusok függően importálja a Log Analytics **ServiceDesk_CL** a következő mezőket tartalmazzák:

**Munkaelem:** **incidensek**  
ServiceDeskWorkItemType_s="Incident"

**Mezők**

- ServiceDeskConnectionName
- Ügyfélszolgálati azonosító
- Állapot
- Sürgősség
- Hatás
- Prioritás
- Eszkalálás
- Létrehozta
- Megoldó
- Lezárta
- Forrás
- Társítás
- Kategória
- Beosztás
- Leírás
- Létrehozás dátuma
- Lezárás dátuma
- Megoldás dátuma
- Utolsó módosítás dátuma
- Computer


**Munkaelem:** **Változáskérések**

ServiceDeskWorkItemType_s="ChangeRequest"

**Mezők**
- ServiceDeskConnectionName
- Ügyfélszolgálati azonosító
- Létrehozta
- Lezárta
- Forrás
- Társítás
- Beosztás
- Típus
- Kategória
- Állapot
- Eszkalálás
- Ütközés állapota
- Sürgősség
- Prioritás
- Kockázat
- Hatás
- Társítás
- Létrehozás dátuma
- Lezárás dátuma
- Utolsó módosítás dátuma
- Kért dátuma
- A tervezett kezdési dátum
- Tervezett befejezési dátum
- Munkahelyi kezdő dátuma
- Munkahelyi záró dátum
- Leírás
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Egy ServiceNow-eseményt kimeneti adatait

| Log Analytics mező | A ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| IncidentState_s | Állapot |
| Urgency_s |Sürgősség |
| Impact_s |Hatás|
| Priority_s | Prioritás |
| CreatedBy_s | Megnyitotta |
| ResolvedBy_s | Feloldotta|
| ClosedBy_s  | Lezárta |
| Source_s| Kapcsolattartó típusa |
| AssignedTo_s | Hozzárendelve a következőhöz:  |
| Category_s | Kategória |
| Title_s|  Rövid leírás |
| Description_s|  Megjegyzések |
| CreatedDate_t|  Megnyitott |
| ClosedDate_t| Lezárt|
| ResolvedDate_t|Feloldva|
| Computer  | Konfigurációs elem |

## <a name="output-data-for-a-servicenow-change-request"></a>A ServiceNow kimeneti adatait változáskérés

| Log Analytics | ServieNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| CreatedBy_s | Kérelmező |
| ClosedBy_s | Lezárta |
| AssignedTo_s | Hozzárendelve a következőhöz:  |
| Title_s|  Rövid leírás |
| Type_s|  Típus |
| Category_s|  Kategória |
| CRState_s|  Állapot|
| Urgency_s|  Sürgősség |
| Priority_s| Prioritás|
| Risk_s| Kockázat|
| Impact_s| Hatás|
| RequestedDate_t  | A kért dátum szerint |
| ClosedDate_t | Lezárás dátuma |
| PlannedStartDate_t  |     A tervezett kezdési dátum |
| PlannedEndDate_t  |   Tervezett befejezési dátum |
| WorkStartDate_t  | Tényleges kezdési dátum |
| WorkEndDate_t | Tényleges befejezési dátum|
| Description_s | Leírás |
| Computer  | Konfigurációs elem |


## <a name="troubleshoot-itsm-connections"></a>ITSM-kapcsolatok hibaelhárítása
1.  Ha a kapcsolat hibája esetén a felhasználói felület csatlakoztatott forrás egy **hiba történt a kapcsolat mentése** üzenet, az alábbi lépéseket:
 - A ServiceNow, Cherwell és Provance kapcsolatok esetében  
    - Ellenőrizze, helyes megadása a felhasználónév, jelszó, ügyfél-azonosító és titkos Ügyfélkód az egyes kapcsolatok.  
    - Ellenőrizze, hogy az a megfelelő ITSM-termékben hozhatja létre a kapcsolatot, hogy megfelelő jogosultságokkal.  
 - A Service Manager-kapcsolatok esetén  
    - Győződjön meg arról, hogy a webalkalmazás sikeresen telepítve lesz, és a hibrid kapcsolat jön létre. A helyszíni Service Manager géppel sikeresen létrejött a kapcsolat ellenőrzéséhez keresse fel a webes alkalmazás URL-CÍMÉT, így a dokumentációban leírt módon a [hibrid kapcsolat](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).  

2.  Ha ServiceNow adatait nem első szinkronizálva van a Log Analyticsbe, győződjön meg arról, hogy a ServiceNow-példány nem alszik. ServiceNow fejlesztési példányok néha lépjen alvó állapotba üresjáratban hosszabb ideig. Ellenkező esetben jelentse a problémát.
3.  Ha a Log Analytics-riasztásokkal aktiválódik, de a munka elemek nem jönnek létre az ITSM-termékben vagy konfigurációelemek nem létrehozott/kapcsolódó munkaelemek vagy egyéb általános információkat, tekintse meg a következő helyeken:
 -  ITSMC: A megoldás munkahelyi kapcsolatok elemek/számítógépek stb összegzését jeleníti meg. Kattintson a csempére ábrázoló **összekötő állapota**, amely veszi, hogy **naplóbeli keresés** a megfelelő lekérdezéssel. Tekintse meg a rekordok naplózása LogType_S a hiba, további információt.
 - **Naplóbeli keresés** lap: közvetlenül a lekérdezéssel kapcsolatos hibák/információk megtekintéséhez `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>A Service Manager webes alkalmazás üzembe helyezési hibáinak elhárítása
1.  Webes alkalmazás üzembe helyezése bármely problémái esetén győződjön meg arról, hogy a megfelelő engedélyekkel az előfizetésben már említettük, hozzon létre és helyezhet üzembe erőforrásokat.
2.  Ha egy **"Objektumhivatkozás nincs beállítva az objektum-példányra"** hibát a futtatásakor a [parancsfájl](log-analytics-itsmc-service-manager-script.md), győződjön meg arról, hogy a megadott értékek alapján **felhasználói konfiguráció** szakasz .
3.  Ha nem sikerült létrehozni a service bus relay-névteret, győződjön meg arról, hogy a szükséges erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha nem regisztrált, manuálisan service bus relay-névtér létrehozása az Azure Portalról. Is létrehozhat, miközben [a hibrid kapcsolat létrehozása](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) az Azure Portalról.


## <a name="contact-us"></a>Kapcsolat

Bármely lekérdezések vagy az IT Service Management-összekötő visszajelzésnek, írjon nekünk az [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>További lépések
[ITSM-termékekkel/szolgáltatásokkal felvétele az IT Service Management-összekötő](log-analytics-itsmc-connections.md).

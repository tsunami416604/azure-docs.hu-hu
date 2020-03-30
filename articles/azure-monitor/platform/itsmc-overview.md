---
title: IT-szolgáltatáskezelési összekötő az Azure Log Analytics szolgáltatásban | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az IT Service Management Connector (ITSMC) és arról, hogyan használja ezt a megoldást központilag figyelheti és kezelheti az ITSM munkaelemek az Azure Log Analytics, és a problémák gyors megoldásához.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 50bab4c26046059b993c19a030a8f840ae336ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274540"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Az Azure csatlakoztatása ITSM-eszközökhöz az IT Service Management Connector használatával

![IT szolgáltatásfelügyeleti összekötő szimbóluma](media/itsmc-overview/itsmc-symbol.png)

Az IT Service Management Connector (ITSMC) lehetővé teszi az Azure és a támogatott IT Service Management (ITSM) termék/szolgáltatás csatlakoztatását.

Az Azure-szolgáltatások, például a Log Analytics és az Azure Monitor eszközöket biztosítanak az Azure-ral és a nem Azure-erőforrásokkal kapcsolatos problémák észlelésére, elemzésére és elhárítására. A problémához kapcsolódó munkaelemek azonban általában egy ITSM termékben/szolgáltatásban vannak. Az ITSM-összekötő kétirányú kapcsolatot biztosít az Azure és az ITSM-eszközök között, így gyorsabban oldhatja meg a problémákat.

Az ITSMC a következő ITSM-eszközökkel támogatja a kapcsolatokat:

-   ServiceNow
-   Rendszerközpont-szolgáltatáskezelő
-   Provance között
-   Cherwell

Az ITSMC segítségével a következőket teheti:

-  Hozzon létre munkaelemeket az ITSM-eszközben az Azure-riasztások (metrikariasztások, tevékenységnapló-riasztások és loganalytics-riasztások) alapján.
-  Szükség esetén szinkronizálhatja az incidenst, és módosíthatja az ITSM-eszközből származó kérelemadatokat egy Azure Log Analytics-munkaterületre.

További információ a jogi feltételekről és az [adatvédelmi politikáról.](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9)

Az ITSM-összekötő t a következő lépésekkel kezdheti el használni:

1.  [Az ITSM-összekötő megoldás hozzáadása](#adding-the-it-service-management-connector-solution)
2.  ITSM-kapcsolat létrehozása
3.  [A kapcsolat használata](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Az IT-szolgáltatásfelügyeleti összekötő megoldás hozzáadása

A kapcsolat létrehozása előtt hozzá kell adnia az ITSM-összekötő megoldást.

1. Az Azure Portalon kattintson **a + Új** ikonra.

   ![Új Azure-erőforrás](media/itsmc-overview/azure-add-new-resource.png)

2. Keresse meg az **IT Service Management Connector-t** a Piactéren, és kattintson a **Létrehozás gombra.**

   ![ITSMC-megoldás hozzáadása](media/itsmc-overview/add-itsmc-solution.png)

3. Az **OMS-munkaterület** szakaszban válassza ki az Azure Log Analytics munkaterületet, ahol telepíteni szeretné a megoldást.
   >[!NOTE]
   > * A Microsoft Operations Management Suite (OMS) szolgáltatásról az Azure Monitorra való folyamatos áttérés részeként az OMS-munkaterületeket mostantól Log Analytics-munkaterületnek nevezzük.
   > * Az ITSM-összekötő csak a következő régiókban telepíthető a Log Analytics-munkaterületeken: USA keleti régiója, USA nyugati régiója, USA délnyugati régiója, Közép-Kanada, Nyugat-Európa, Dél-Egyesült Királyság, Délkelet-Ázsia, Kelet-Japán, Közép-India, Délkelet-Ausztrália.

4. Az **OMS-munkaterület beállításai** csoportban válassza ki azt az erőforráscsoportot, amelyhez létre szeretné hozni a megoldáserőforrást.

   ![ITSMC munkaterület](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >A Microsoft Operations Management Suite (OMS) szolgáltatásról az Azure Monitorra való folyamatos áttérés részeként az OMS-munkaterületeket mostantól Log Analytics-munkaterületnek nevezzük.

5. Kattintson **a Létrehozás gombra.**

A megoldáserőforrás üzembe helyezésekor egy értesítés jelenik meg az ablak jobb felső részén.


## <a name="creating-an-itsm--connection"></a>ITSM-kapcsolat létrehozása

Miután telepítette a megoldást, létrehozhat egy kapcsolatot.

A kapcsolat létrehozásához elő kell készítenie az ITSM eszközt, hogy engedélyezze a kapcsolatot az ITSM-összekötő megoldásból.  

Attól függően, hogy az AZ ITSM-termék, amelyhez csatlakozik, kövesse az alábbi lépéseket:

- [System Center szolgáltatáskezelő (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance között](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Miután előkészítette az ITSM-eszközöket, a kapcsolat létrehozásához kövesse az alábbi lépéseket:

1. Nyissa meg a **Minden erőforrás**, keresse meg a **ServiceDesk(YourWorkspaceName)**.
2. A bal oldali ablaktábla **MUNKATERÜLET-adatforrásai** csoportban kattintson az **ITSM-kapcsolatok gombra.**
   ![ITSM-kapcsolatok](media/itsmc-overview/itsm-connections.png)

   Ez a lap a kapcsolatok listáját jeleníti meg.
3. Kattintson **a Kapcsolat hozzáadása gombra.**

   ![ITSM-kapcsolat hozzáadása](media/itsmc-overview/add-new-itsm-connection.png)

4. Adja meg a kapcsolat beállításait [az ITSM-kapcsolat konfigurálása az ITSM-termékekkel/szolgáltatásokkal című cikkben leírtak szerint.](../../azure-monitor/platform/itsmc-connections.md)

   > [!NOTE]
   >
   > Alapértelmezés szerint az ITSMC 24 óránként egyszer frissíti a kapcsolat konfigurációs adatait. Ha azonnal frissíteni szeretné a kapcsolat adatait az ön által végzett módosításokhoz vagy sablonfrissítésekhez, kattintson a kapcsolat paneljének **Szinkronizálás** gombjára.

   ![Kapcsolat frissítése](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>A megoldás használata
   Az ITSM-összekötő megoldás használatával munkaelemeket hozhat létre az Azure-riasztásokból, a Log Analytics-riasztásokból és a Log Analytics-naplórekordokból.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-munkaelemek létrehozása az Azure-riasztásokból

Miután létrehozta az ITSM-kapcsolatot, az ITSM-eszközben létrehozhat munkaelemet az Azure-riasztások alapján, a **műveletcsoportokban**végrehajtott **ITSM-művelet** használatával.

A műveletcsoportok moduláris és újrafelhasználható módot biztosítanak az Azure-riasztásokhoz való műveletek indítására. A műveletcsoportokat metrikariasztásokkal, tevékenységnapló-riasztásokkal és Azure Log Analytics-riasztásokkal használhatja az Azure Portalon.

Kövesse az alábbi eljárást:

1. Az Azure Portalon kattintson a **Figyelő**gombra.
2. A bal oldali ablaktáblában kattintson a **Műveletcsoportok gombra.** Megjelenik **a Műveletcsoport hozzáadása** ablak.

    ![Műveletcsoportok](media/itsmc-overview/action-groups.png)

3. Adja meg **a műveletcsoport nevét** és **rövidnevét.** Válassza ki azt az **erőforráscsoportot** és **előfizetést,** ahol létre szeretné hozni a műveletcsoportot.

    ![Műveletcsoportok részletei](media/itsmc-overview/action-groups-details.png)

4. A Műveletek listában válassza az **ITSM** elemet a Művelettípus legördülő **menüjében.** Adja meg a művelet **nevét,** és kattintson a **Részletek szerkesztése gombra.**
5. Válassza ki azt az **előfizetést,** ahol a Log Analytics-munkaterület található. Válassza ki a **Kapcsolat** nevet (az ITSM-összekötő nevét), majd a munkaterület nevét. Például: "MyITSMMConnector(MyWorkspace)."

    ![ITSM művelet részletei](media/itsmc-overview/itsm-action-details.png)

6. Válassza a **Munkaelem** típusát a legördülő menüből.
   Válasszon egy meglévő sablon használatával, vagy töltse ki az ITSM-termékhez szükséges mezőket.
7. Kattintson az **OK** gombra.

Egy Azure-riasztási szabály létrehozásakor/szerkesztésekor használjon egy műveletcsoportot, amely rendelkezik egy ITSM-művelettel. Amikor a riasztás aktiválódik, a munkaelem létrejön/frissül az ITSM eszközben.

> [!NOTE]
>
> Az ITSM-művelet díjszabásáról a műveletcsoportok [díjszabási oldalán](https://azure.microsoft.com/pricing/details/monitor/) talál.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Az incidens megjelenítése és elemzése, valamint a kérelem adatainak módosítása

A kapcsolat beállításakor a konfiguráció alapján az ITSM-összekötő akár 120 nap incidens- és módosítási kérelemadatokat is szinkronizálhat. Az adatok naplórekord-sémája a [következő szakaszban](#additional-information)található.

Az incidens és a változáskérés i. adatai a megoldás ITSM-összekötő irányítópultjával jeleníthetők meg.

![Log Analytics képernyő](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Az irányítópult az összekötő állapotával kapcsolatos információkat is tartalmaz, amelyek kiindulási pontként használhatók a kapcsolatokkal kapcsolatos problémák elemzéséhez.

A Szolgáltatástérkép-megoldáson belül is megjelenítheti az érintett számítógépekkel szinkronizált incidenseket.

A Service Map automatikusan felderíti az alkalmazás-összetevőket Windows és Linux rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Használatával a kiszolgálók úgy jelennek meg, ahogyan Ön gondol rájuk: egymással összekapcsolt rendszerekként, amelyek kritikus fontosságú szolgáltatásokat tesznek elérhetővé. A Service Map megmutatja a kapcsolatokat mindazon kiszolgálók, folyamatok és portok között, amelyek TCP-kapcsolattal összekötött architektúrában találhatóak, és ehhez nem szükséges semmilyen konfiguráció, csupán az ügynököt kell telepíteni. [További információ](../../azure-monitor/insights/service-map.md).

Ha a Service Map megoldást használja, megtekintheti az ITSM-megoldásokban létrehozott ügyfélszolgálati elemeket az alábbi példában látható módon:

![Log Analytics képernyő](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

További információ: [Szolgáltatástérkép](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>További információ

### <a name="data-synced-from-itsm-product"></a>AZ ITSM-termékből szinkronizált adatok
Az incidensek és a változáskérések szinkronizálása az ITSM-termékből a Log Analytics-munkaterületre a kapcsolat konfigurációja alapján történik.

Az alábbi információk példákat mutatnak be az ITSMC által gyűjtött adatokra:

> [!NOTE]
>
> A Log Analytics szolgáltatásba importált munkaelem-típustól függően **ServiceDesk_CL** a következő mezőket tartalmazza:

**Munkaelem:** **Incidensek**  
ServiceDeskWorkItemType_s="Incidens"

**Mezők**

- ServiceDeskConnectionName
- Ügyfélszolgálati azonosító
- Állapot
- Sürgősség
- Hatás
- Prioritás
- Eszkalálás
- Created By (Létrehozó)
- Feloldotta a
- Lezárva
- Forrás
- Hozzárendelve
- Kategória
- Cím
- Leírás
- Létrehozás dátuma
- Lezárás dátuma
- Megoldás dátuma
- Utolsó módosítás dátuma
- Computer


**Munkaelem:** **Módosítási kérelmek**

ServiceDeskWorkItemType_s="ChangeRequest"

**Mezők**
- ServiceDeskConnectionName
- Ügyfélszolgálati azonosító
- Created By (Létrehozó)
- Lezárva
- Forrás
- Hozzárendelve
- Cím
- Típus
- Kategória
- Állapot
- Eszkalálás
- Ütközés állapota
- Sürgősség
- Prioritás
- Kockázat
- Hatás
- Hozzárendelve
- Létrehozás dátuma
- Lezárás dátuma
- Utolsó módosítás dátuma
- Kért dátum
- Tervezett kezdési dátum
- Tervezett befejezési dátum
- Munka kezdési dátuma
- Munka záró dátuma
- Leírás
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow-incidens kimeneti adatai

| Log Analytics mező | ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| IncidentState_s | Állapot |
| Urgency_s |Sürgősség |
| Impact_s |Hatás|
| Priority_s | Prioritás |
| CreatedBy_s | Megnyitás: |
| ResolvedBy_s | Feloldotta:|
| ClosedBy_s  | Lezárt: |
| Source_s| Kapcsolattartó típusa |
| AssignedTo_s | Felelős  |
| Category_s | Kategória |
| Title_s|  Rövid leírás |
| Description_s|  Megjegyzések |
| CreatedDate_t|  Kinyitott |
| ClosedDate_t| lezárva|
| ResolvedDate_t|Feloldva|
| Computer  | Konfigurációelem |

## <a name="output-data-for-a-servicenow-change-request"></a>A ServiceNow módosítási kérelem kimeneti adatai

| Log Analytics | ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| CreatedBy_s | Által kért |
| ClosedBy_s | Lezárt: |
| AssignedTo_s | Felelős  |
| Title_s|  Rövid leírás |
| Type_s|  Típus |
| Category_s|  Kategória |
| CRState_s|  Állapot|
| Urgency_s|  Sürgősség |
| Priority_s| Prioritás|
| Risk_s| Kockázat|
| Impact_s| Hatás|
| RequestedDate_t  | Kért dátum szerint |
| ClosedDate_t | Lezárt dátum |
| PlannedStartDate_t  |     Tervezett kezdési dátum |
| PlannedEndDate_t  |   Tervezett befejezési dátum |
| WorkStartDate_t  | Tényleges kezdési dátum |
| WorkEndDate_t | Tényleges záró dátum|
| Description_s | Leírás |
| Computer  | Konfigurációs elem |


## <a name="troubleshoot-itsm-connections"></a>ITSM-kapcsolatok – problémamegoldás
1. Ha a kapcsolat nem sikerül a csatlakoztatott forrás felhasználói felületén egy **hiba mentése kapcsolat** üzenet, kövesse az alábbi lépéseket:
   - A ServiceNow, Cherwell és Provance csatlakozások esetén  
   - győződjön meg arról, hogy helyesen adta meg a felhasználónevet, a jelszót, az ügyfélazonosítót és az ügyféltitkos kulcsot az egyes kapcsolatokhoz.  
   - ellenőrizze, hogy rendelkezik-e elegendő jogosultsággal a megfelelő ITSM termékben a kapcsolat létrejöttéhez.  
   - A Service Manager-kapcsolatok esetén  
   - győződjön meg arról, hogy a webalkalmazás telepítése sikeresen megtörtént, és hibrid kapcsolat jött létre. Annak ellenőrzéséhez, hogy a kapcsolat sikeresen létrejött-e a helyszíni Service Manager-géppel, keresse fel a webalkalmazás URL-címét a [hibrid kapcsolat](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection)létrehozásához szükséges dokumentációban részletezett módon.  

2. Ha a ServiceNow-ból származó adatok nem lesznek szinkronizálva a Log Analytics szolgáltatással, győződjön meg arról, hogy a ServiceNow-példány nem alszik. ServiceNow fejlesztői példányok néha alvó állapotba, ha hosszú ideig tétlen. Különben jelentse a problémát.
3. Ha a Log Analytics figyelmeztet, hogy tűz keletkezik, de a munkaelemek nem jönnek létre az ITSM-termékben, vagy a konfigurációs elemek nem jönnek létre/nem kapcsolódnak munkaelemekhez vagy más általános információhoz, tekintse meg a következő helyeken:
   -  ITSMC: A megoldás a kapcsolatok/munkaelemek/számítógépek stb. Kattintson az **Összekötő állapotát**megjelenítő csempére, amely a megfelelő lekérdezéssel a **Naplókeresés** gombra vezet. További információért tekintse meg a LogType_S hibaként tartalmazó naplórekordokat.
   - **Naplókeresés** lap: a hibák/kapcsolódó információk `*`megtekintése`*`közvetlenül a lekérdezés ServiceDeskLog_CL használatával.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>A Service Manager Web App telepítésének hibaelhárítása
1.  A webalkalmazás-telepítéssel kapcsolatos problémák esetén győződjön meg arról, hogy rendelkezik az erőforrások létrehozásához/üzembe helyezéséhez említett előfizetésben szükséges engedélyekkel.
2.  Ha a [parancsfájl](itsmc-service-manager-script.md)futtatásakor **"Az objektumhivatkozás nincs objektumpéldányra állítva"** hibaüzenet jelenik meg, győződjön meg arról, hogy érvényes értékeket adott meg a **Felhasználó konfigurációja** szakaszban.
3.  Ha nem sikerül létrehoznia a szolgáltatásbusz-továbbító névtere, győződjön meg arról, hogy a szükséges erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha nincs regisztrálva, manuálisan hozza létre a szolgáltatásbusz-továbbító névterét az Azure Portalról. Azt is létrehozhatja, miközben a hibrid kapcsolat az Azure Portalon [létre.](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection)


## <a name="contact-us"></a>Kapcsolat

Ha bármilyen kérdése vagy visszajelzése van az [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com)IT Service Management Connector-on, lépjen kapcsolatba velünk a.

## <a name="next-steps"></a>További lépések
[ADD hozzá az ITSM-termékeket/szolgáltatásokat az IT Service Management Connector-hoz.](../../azure-monitor/platform/itsmc-connections.md)

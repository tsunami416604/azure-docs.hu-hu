---
title: Informatikai Management-összekötő az Azure Naplóelemzés szolgáltatás |} Microsoft Docs
description: Ez a cikk áttekintést IT Service Management Connector (ITSMC), és ez a megoldás segítségével központilag figyelheti és kezelheti a ITSM információ az Azure Log Analytics-munkaelemek, és gyorsan hárítson el minden problémát.
services: log-analytics
documentationcenter: ''
author: JYOTHIRMAISURI
manager: riyazp
editor: ''
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: v-jysur
ms.openlocfilehash: a51b96641c80a6575c64f58fa09e0bf6078e4185
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Csatlakozás Azure ITSM eszközök IT Service Management-összekötő használatával

![Informatikai Management-összekötő szimbólum](./media/log-analytics-itsmc/itsmc-symbol.png)

Az informatikai szolgáltatás Management Connector (ITSMC) lehetővé teszi, hogy csatlakozzon az Azure és egy támogatott IT Service Management (ITSM) termék vagy szolgáltatás.

Az Azure-szolgáltatásokat, mint a Naplóelemzési és Azure figyelő biztosítanak eszközöket az észlelhető, elemzése és elhárítása az Azure és az-Azure erőforrások. Általában egy problémához kapcsolódó munkaelemek azonban egy ITSM termékszolgáltatásból találhatók. A ITSM összekötő gyorsabb a problémák megoldását segítő Azure és ITSM eszközök közötti kétirányú kapcsolatot biztosít.

ITSMC-kapcsolatok a következő ITSM eszközöket támogatja:

-   ServiceNow
-   A System Center Service Manager
-   Provance
-   Cherwell

Az ITSMC segítségével

-  Munkaelemek létrehozása ITSM eszközben, az Azure riasztások (metrika riasztások, tevékenységnapló riasztások és Naplóelemzési riasztások) alapján.
-  Lehetősége van az esemény szinkronizálása, és módosítsa a ITSM eszközből kérelem adatai az Azure Log Analytics-munkaterülethez.


Megkezdheti az összekötővel ITSM az alábbi lépéseket követve:

1.  [Adja hozzá a ITSM összekötő megoldás](#adding-the-it-service-management-connector-solution)
2.  [ITSM-kapcsolat létrehozásához](#creating-an-itsm-connection)
3.  [A kapcsolat használata](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Összekötő megoldás hozzáadása az informatikai szolgáltatás

A kapcsolat létrehozása előtt kell hozzáadnia a ITSM összekötő megoldás.

1.  Az Azure portálon kattintson **+ új** ikonra.

    ![Új Azure-erőforrás](./media/log-analytics-itsmc/azure-add-new-resource.png)

2.  Keresse meg **IT Service Management-összekötő** a piactéren, és kattintson a **létrehozása**.

    ![Adja hozzá a ITSMC megoldás](./media/log-analytics-itsmc/add-itsmc-solution.png)

3.  Az a **OMS-munkaterület** területen válassza ki az Azure Naplóelemzési munkaterület, ahol a megoldás telepíteni szeretné.
4.  Az a **OMS-munkaterület beállításai** területen válassza ki a ResourceGroup, ahová a megoldás erőforrás létrehozása.

    ![ITSMC munkaterület](./media/log-analytics-itsmc/itsmc-solution-workspace.png)

5.  Kattintson a **Create** (Létrehozás) gombra.

A megoldás erőforrás telepítésekor megjelenik egy értesítés, a bal felső sarkában az ablak.


## <a name="creating-an-itsm--connection"></a>ITSM kapcsolat létrehozása

A megoldás telepítése után létrehozhat egy kapcsolatot.

A kapcsolat létrehozásához, szüksége lesz a ITSM összekötő megoldásban való csatlakozás engedélyezése a ITSM eszköz előkészítése.  

Attól függően, hogy a ITSM termék csatlakozik tegye a következőket:

- [System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Miután a ITSM eszközök rendelkezik prepped, az alábbi lépésekkel VPN-kapcsolat létrehozásához:

1.  Ugrás a **összes erőforrás**, keressen **ServiceDesk(YourWorkspaceName)**.
2.  A **MUNKATERÜLET adatforrások** kattintson a bal oldali ablaktáblában **ITSM kapcsolatok**.
    ![ITSM kapcsolatok](./media/log-analytics-itsmc/itsm-connections.png)

    Ez a lap a kapcsolatok listáját jeleníti meg.
3.  Kattintson a **kapcsolat hozzáadása a**.

    ![ITSM kapcsolat hozzáadása](./media/log-analytics-itsmc/add-new-itsm-connection.png)

4.  Adja meg a kapcsolat beállításait, a [ITSMC kapcsolat beállítása a ITSM termékek vagy szolgáltatások cikk](log-analytics-itsmc-connections.md).

    > [!NOTE]

    > Alapértelmezés szerint ITSMC 24 óránként egyszer a frissítésre, a kapcsolat konfigurációs adatokat. Azonnal az esetleges módosításokat, vagy a Szolgáltatássablon-frissítések, amelyek miatt, a "Frissítés" gombra a kapcsolat mellett megjelenik a kapcsolati adatok frissítéséhez.

    ![Kapcsolat frissítése](./media/log-analytics-itsmc/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>A megoldás használata
   Az összekötő ITSM megoldás használatával létrehozhat munkaelemek Azure riasztásokat, a Naplóelemzési riasztások és a Naplóelemzési naplórekordokat.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Az Azure riasztásokból ITSM munkaelemek létrehozása

Miután a ITSM kapcsolatot létrehozni, létrehozhat munkahelyi elem (ek) a ITSM eszköz használatával Azure riasztások alapján a **ITSM művelet** a **művelet csoportok**.

Művelet csoportok lehetőséget nyújtanak olyan moduláris és újrafelhasználható, az Azure-riasztások műveleteket váltanak ki. A művelet csoportok használható metrika riasztásokat, a tevékenységnapló riasztások és az Azure Naplóelemzés riasztásait az Azure portálon.

Kövesse az alábbi eljárást:

1. Az Azure portálon kattintson **figyelő**.
2. Kattintson a bal oldali ablaktáblában **művelet csoportok**. A **művelet csoport hozzáadása** ablak jelenik meg.

    ![Műveletcsoportok](media/log-analytics-itsmc/action-groups.png)

3. Adjon meg **neve** és **rövid_név** művelet csoport. Válassza ki a **erőforráscsoport** és **előfizetés** hol szeretne létrehozni a műveleti csoport.

    ![Csoportok részletei](media/log-analytics-itsmc/action-groups-details.png)

4. A műveletek listában válassza ki a **ITSM** a legördülő menüből a **művelettípus**. Adjon meg egy **neve** a műveletet, majd kattintson a **részletes adatainak szerkesztéséhez**.
5. Válassza ki a **előfizetés** ahol a Naplóelemzési munkaterület. Válassza ki a **kapcsolat** neve (a ITSM összekötő neve), a munkaterület neve követ. Például "MyITSMMConnector(MyWorkspace)".

    ![ITSM művelet részletei](./media/log-analytics-itsmc/itsm-action-details.png)

6. Válassza ki **munkaelem** típusát a legördülő menüből.
   Válasszon egy meglévő sablont, vagy töltse ki a mezőket a ITSM termékhez szükséges.
7. Kattintson az **OK** gombra.

Azure riasztási szabály létrehozása/szerkesztése, során művelet csoportot, amelynek ITSM művelet használni. A riasztás akkor váltja ki, amikor a munkaelem szerepel a ITSM eszköz létrehozása vagy frissítése.

>[!NOTE]

> ITSM művelet árakkal kapcsolatos információkért lásd: a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/monitor/) művelet csoportok számára.


## <a name="create-itsm-work-items-from-log-analytics-alerts"></a>Log Analytics-riasztások ITSM munkaelemek létrehozása

Konfigurálhatja a riasztási szabályok az Azure Naplóelemzés portáljáról ITSM eszköz, a munkaelemek létrehozásához hajtsa végre az alábbi eljárást.

1. A **naplófájl-keresési** ablakban futtassa a napló keresési lekérdezés adatainak megtekintéséhez. Lekérdezés eredményei munkaelemek forrását.
2. A **naplófájl-keresési**, kattintson a **riasztási** megnyitásához a **riasztási szabály hozzáadása** lap.

    ![Napló elemzési képernyő](./media/log-analytics-itsmc/itsmc-work-items-for-azure-alerts.png)

3. Az a **riasztási szabály hozzáadása** ablakban adja meg a szükséges adatokat a **neve**, **súlyossági**, **keresési lekérdezés**, és **riasztás feltételek** (ablakban/Időmetrika mérési).
4. Válassza ki **Igen** a **ITSM műveletek**.
5. Válassza ki a ITSM kapcsolatot a **válassza ki a kapcsolat** listája.
6. Adja meg szükség szerint adatait.
7. Minden naplóbejegyzés a riasztás egy külön munkaelemet létrehozásához válassza a **minden naplóbejegyzés egyes munkaelemek létrehozása** jelölőnégyzetet.

    Vagy

    hagyja üresen ezt a jelölőnégyzetet, jelölje ki az ebben a riasztásban vonta naplóbejegyzések tetszőleges számú csak egy munkaelem létrehozásához.

7. Kattintson a **Save** (Mentés) gombra.

Megtekintheti a Log Analytics-riasztás alapján létrehozott **beállítások > értesítések**. A megfelelő ITSM kapcsolat munkaelemek jönnek létre, ha a megadott riasztás feltétel teljesül.


## <a name="create-itsm-work-items-from-log-analytics-log-records"></a>Log Analytics naplórekordokat ITSM munkaelemek létrehozása

A csatlakoztatott ITSM forrásokban közvetlenül egy naplóbejegyzést képviselnek a munkaelemek is létrehozhat. Ez a kapcsolat megfelelően működik-e tesztelje, hogy is használható.


1. A **naplófájl-keresési**, keresse meg a szükséges adatokat, válassza ki a részleteket, és kattintson **létrehozás munkaelem**.

    A **ITSM munkaelem létrehozása** ablak jelenik meg:

    ![Napló elemzési képernyő](media/log-analytics-itsmc/itsmc-work-items-from-azure-logs.png)

2.   Adja hozzá a következő adatokat:

  - **Munkaelem-cím**: a munkaelem címét.
  - **Munkaelem-leírás**: az új munkaelemhez leírását.
  - **Érintett számítógép**: Ha a napló adatokat talált a számítógép nevét.
  - **Válassza ki a kapcsolat**: ITSM kapcsolat, amelyben szeretné létrehozni ezt a munkaelemet.
  - **Munkaelem**: típusú munkaelemet.

3. Incidens egy meglévő munkaelemsablonból használatához kattintson **Igen** alatt **készítése működik elemet a sablon alapján** lehetőséget, majd kattintson a **létrehozása**.

    Vagy

    Kattintson a **nem** Ha lehetővé szeretné tenni a szabott értékekhez.

4. Adja meg a megfelelő értékeket a **ügyfél típusú**, **hatás**, **sürgősség**, **kategória**, és **alkategória** szövegmezőbe, és kattintson **létrehozása**.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Megjelenítheti és az incidens elemzése és kérelem adatainak módosítása

A konfiguráció alapján, a kapcsolat beállításához, ITSM összekötő szinkronizálhatják Incidenshez és Változáskéréshez kérelem adatok legfeljebb 120 nap. A rekord séma ezeket az adatokat a rendszer biztosítja a [következő szakasz](#additional-information).

Az incidenshez és változáskéréshez kérelem adatai is ábrázolt, az összekötő ITSM irányítópulttal a megoldásban.

![Napló elemzési képernyő](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

Az irányítópult az összekötő állapota, amely elemzéséhez problémák merülnek fel a kapcsolatot a kiindulási pontként használható információkat is biztosít.

Meg is jelenítheti meg az incidensek a Service Map megoldáson az érintett számítógépek szinkronizálva.

Szolgáltatástérkép automatikusan észleli az alkalmazás-összetevők, a Windows és Linux rendszerek, és leképezi a szolgáltatások közötti kommunikáció. Lehetővé teszi a kiszolgálók, úgy gondolja, hogy azok –, hogy a kritikus szolgáltatások összekapcsolt rendszerként. Szolgáltatástérkép jeleníti meg a kiszolgálók, a folyamatok közötti kapcsolatokat, és portok között bármely TCP-csatlakoztatott architektúra a konfiguráció nem szükséges másik ügynököt telepíteni. [További információk](../operations-management-suite/operations-management-suite-service-map.md).

Ha a Service Map megoldást használ, a szolgáltatás ügyfélszolgálati létrehozott elemek szerepelnek a ITSM megoldások a következő példában látható módon tekintheti meg:

![Napló elemzési képernyő](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)

További információ: [Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md)


## <a name="additional-information"></a>További információ

### <a name="data-synced-from-itsm-product"></a>Az ITSM termék szinkronizált adatok
Incidensek és változáskérések a ITSM terméket vannak szinkronizálva a kapcsolat konfigurációja alapján Naplóelemzési munkaterületet.

Az alábbi információkat jeleníti meg a ITSMC által összegyűjtött adatok:

> [!NOTE]

> Attól függően, hogy a munkaelem-típusban importálni a Log Analyticshez **ServiceDesk_CL** a következő mezőket tartalmazza:

**Munkaelem:** **incidensek**  
ServiceDeskWorkItemType_s="Incident"

**Mezők**

- ServiceDeskConnectionName
- Service Desk ID
- Állapot
- Sürgős
- Hatás
- Prioritás
- Eszkalálás
- Létrehozta
- Megoldó
- Lezárt
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
- Service Desk ID
- Létrehozta
- Lezárt
- Forrás
- Társítás
- Beosztás
- Típus
- Kategória
- Állapot
- Eszkalálás
- Ütközés állapota
- Sürgős
- Prioritás
- Kockázat
- Hatás
- Társítás
- Létrehozás dátuma
- Lezárás dátuma
- Utolsó módosítás dátuma
- Kért dátuma
- Tervezett kezdő dátuma
- Tervezett befejezési dátum
- Munkahelyi kezdő dátuma
- Munka befejezési dátum
- Leírás
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>A ServiceNow, amelyhez a kimeneti adatok

| Log Analytics mező | A ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| IncidentState_s | Állapot |
| Urgency_s |Sürgős |
| Impact_s |Hatás|
| Priority_s | Prioritás |
| CreatedBy_s | Által megnyitott |
| ResolvedBy_s | Feloldotta|
| ClosedBy_s  | Lezárt |
| Source_s| Kapcsolat típusa |
| AssignedTo_s | Hozzárendelve a következőhöz:  |
| Category_s | Kategória |
| Title_s|  Rövid leírás |
| Description_s|  Megjegyzések |
| CreatedDate_t|  Megnyitva |
| ClosedDate_t| Lezárt|
| ResolvedDate_t|Megoldva|
| Computer  | konfigurációs elem |

## <a name="output-data-for-a-servicenow-change-request"></a>A ServiceNow kimeneti adatok változáskérés

| Log Analytics | ServieNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| CreatedBy_s | Kérelmező |
| ClosedBy_s | Lezárt |
| AssignedTo_s | Hozzárendelve a következőhöz:  |
| Title_s|  Rövid leírás |
| Type_s|  Típus |
| Category_s|  Kategória |
| CRState_s|  Állapot|
| Urgency_s|  Sürgős |
| Priority_s| Prioritás|
| Risk_s| Kockázat|
| Impact_s| Hatás|
| RequestedDate_t  | A kért dátum szerint |
| ClosedDate_t | Lezárás dátuma |
| PlannedStartDate_t  |     Tervezett kezdő dátuma |
| PlannedEndDate_t  |   Tervezett befejezési dátum |
| WorkStartDate_t  | Tényleges kezdési dátuma |
| WorkEndDate_t | Tényleges befejezési dátum|
| Description_s | Leírás |
| Computer  | Konfigurációs elem |


## <a name="troubleshoot-itsm-connections"></a>ITSM kapcsolatok hibáinak elhárítása
1.  Ha a kapcsolat hibája esetén a csatlakoztatott adatforrás felhasználói felület egy **hiba történt a kapcsolat mentése** üzenet, a következő lépéseket:
- A ServiceNow, Cherwell és Provance kapcsolatok esetén  
           -Győződjön meg arról, megfelelően beírt a felhasználónév, a jelszó, az ügyfél-azonosító és a titkos ügyfélkulcs az egyes kapcsolatok.  
           -Ellenőrizze, hogy ha a megfelelő engedélyekkel rendelkezik a megfelelő ITSM termékben való csatlakozáshoz.  
- A Service Manager-kapcsolatok esetén  
           -Ellenőrizze, hogy a webalkalmazás telepítése sikeres volt, és a hibrid kapcsolat jön létre. Ellenőrizze, hogy sikeresen létrejött a kapcsolat a helyszíni Service Manager számítógéppel, látogasson el a webes alkalmazás URL-CÍMÉT, hogy dokumentációjában ismertetett módon a [a hibrid kapcsolat](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).  

2.  Ha ServiceNow adatait nem első szinkronizálva van a Naplóelemzési, győződjön meg arról, hogy a példány nem alszik ServiceNow. A ServiceNow fejlesztői példányok néha Ugrás üresjáratban alvó hosszú ideig. Más jelentse a hibát.
3.  Ha OMS riasztások érvényesítést, de működik elemek nem jönnek létre ITSM termék és konfigurációs elemek nincsenek létrehozva/kapcsolódó munkaelemek vagy más általános információkat, keresse meg a következő helyen:
 -  ITSMC: A megoldás kapcsolatok munkahelyi elemek/számítógépek stb összegzését jeleníti meg. Kattintson a csempére ábrázoló **összekötő állapota**, amely veszi, hogy **naplófájl-keresési** a megfelelő lekérdezéssel. Tekintse meg a naplóbejegyzések LogType_S a hiba, további információt.
 - **Naplófájl-keresési** lap: tekintse meg a/kapcsolatos hibák közvetlenül a lekérdezéssel *típus = ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>A Service Manager webes alkalmazás telepítésének hibaelhárítása
1.  Esetén a merül fel a webes alkalmazás telepítése győződjön meg arról, hogy megfelelő engedélyekkel rendelkezik az előfizetés említett erőforrások létrehozása vagy telepítése.
2.  Ha egy **"Objektum hivatkozás nincs beállítva egy objektumpéldányra"** hiba, ha futtatja a [parancsfájl](log-analytics-itsmc-service-manager-script.md), győződjön meg arról, hogy az érvényes értékek **felhasználói konfiguráció** szakasz .
3.  Ha Ön nem tudja létrehozni a service bus relay-névtér, győződjön meg arról, hogy a szükséges erőforrás-szolgáltató regisztrálva van az előfizetés. Ha nincs regisztrálva, manuálisan service bus relay-névtér létrehozása az Azure portálról. Akkor is létrehozható közben [a hibrid kapcsolat létrehozása](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) Azure-portálról.


## <a name="contact-us"></a>Kapcsolat

A lekérdezést vagy az informatikai szolgáltatás Management-összekötő visszajelzést, lépjen velünk kapcsolatba [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>További lépések
[ITSM termékek és szolgáltatások hozzáadása IT Service Management-összekötő](log-analytics-itsmc-connections.md).

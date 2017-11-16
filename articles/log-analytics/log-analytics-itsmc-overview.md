---
title: "Informatikai Management-összekötő az Azure Naplóelemzés szolgáltatás |} Microsoft Docs"
description: "Az informatikai szolgáltatás Management-összekötő használatával központilag figyelheti és az Azure Naplóelemzés ITSM munkaelemek kezeléséhez, és gyorsan hárítson el minden problémát."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: ba8542640fcec6e4bc63d8f0a41bf85b221d4c5e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Központi kezelését az informatikai szolgáltatás Management Connector (előzetes verzió) segítségével ITSM munkaelemek

![Informatikai Management-összekötő szimbólum](./media/log-analytics-itsmc/itsmc-symbol.png)

Az informatikai szolgáltatás Management Connector (ITSMC) egy támogatott IT Service Management (ITSM) termék vagy szolgáltatás és a Naplóelemzési kétirányú integrációt biztosít.  Ezen a kapcsolaton keresztül is létrehozhat incidensek, a riasztások és a események Log Analyticshez riasztások vagy naplórekordokat alapján ITSM termékben. Az összekötő is különféle adatok, például incidensek és változáskérések ITSM termékből az OMS szolgáltatáshoz.

A ITSMC a következőket teheti:

  - Az incidenskezelés eljárások a ITSM eszközben, az Ön által választott működési riasztások integrálható.
    - Hozzon létre munkaelemeket (például a riasztást, esemény, incidens) ITSM az OMS-riasztások és a keresési napló.
    - Az Azure tevékenységnapló riasztások ITSM tevékenységekkel művelet csoportok alapján munkaelemek létrehozása.

  - A figyelés, a napló és a szolgáltatás felügyeleti adatok a szervezeten belüli egységes.
    - Incidens egyeztetéséhez, és módosítsa a megfelelő naplózott adatokat a Naplóelemzési munkaterület tooling eszköz ITSM kérelem adatai.   
    - Megtekintése a legfelső szintű irányítópultokon áttekintésért incidensek, változáskérések, és érintett rendszerek.
    - Betekintést nyerhet szolgáltatásfelügyelet adatok Naplóelemzési lekérdezéseket írhat.

## <a name="adding-the-it-service-management-connector-solution"></a>Összekötő megoldás hozzáadása az informatikai szolgáltatás

Az informatikai szolgáltatás Management-összekötő megoldás hozzáadni a Naplóelemzési munkaterület, ismertetett eljárással [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md).

A ITSMC csempe Ez a megoldás katalógusban látható módon:

![összekötő csempe](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Sikeres hozzáadása után az IT Service Management-összekötő alatt megjelenik **OMS** > **beállítások** > **csatlakoztatott források.**

![Csatlakoztatott ITSMC](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> Alapértelmezés szerint a ITSMC 24 óránként egyszer a frissíti a kapcsolati adatait. Azonnal az esetleges módosításokat, vagy a Szolgáltatássablon-frissítések, amelyek miatt, a "Frissítés" gombra a kapcsolat mellett megjelenik a kapcsolati adatok frissítéséhez.

 ![ITSMC frissítése](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-connection-with-your-itsm-software"></a>A kapcsolat konfigurálása a ITSM szoftverrel

ITSMC példányokhoz **System Center Service Manager**, **ServiceNow**, **Provance**, és **Cherwell**.

Az alábbi eljárásokkal, szükség szerint:

- [A System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [A ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>A megoldás használata

Miután konfigurálta az összekötőt, elindul az adatgyűjtés a csatlakoztatott ITSM termék vagy szolgáltatás. Incidensek és változáskérések a ITSM termék vagy szolgáltatás számától függően a kezdeti szinkronizálás néhány perc múlva kell végrehajtani.

> [!NOTE]
> - ITSM terméket ITSMC megoldás által importált adatok jelenik meg a Naplóelemzési naplórekordok típusú **ServiceDesk_CL**.
> - Naplóbejegyzés nevű mezőt tartalmaz **ServiceDeskWorkItemType_s**, ez az incidens vagy változáskérés vagy kérelmet, a két típusú adatok importálása a ITSM terméket.

## <a name="data-synced-from-itsm-product"></a>Az ITSM termék szinkronizált adatok
Incidensek és változáskérések attribútumok szinkronizálva lesznek a ITSM termékből az Naplóelemzési munkaterületet.
Az alábbi információkat jeleníti meg a ITSMC által összegyűjtött adatok:

> [!NOTE]

> Attól függően, hogy a munkaelem-típusban importálni a Log Analyticshez **ServiceDesk_CL** a következő mezőket tartalmazza:

**Munkaelem:** **incidensek**  
ServiceDeskWorkItemType_s = "Esemény"

**Mezők**

- ServiceDeskConnectionName
- Szolgáltatás ügyfélszolgálati azonosítója
- Állapot
- Sürgős
- Hatás
- Prioritás
- Eszkalációs
- Létrehozta
- Megoldó
- Lezárt
- Forrás
- Társítás
- Kategória
- Cím
- Leírás
- Létrehozás dátuma
- Lezárás dátuma
- Megoldás dátuma
- Utolsó módosítás dátuma
- Computer


**Munkaelem:** **Változáskérések**

ServiceDeskWorkItemType_s = "módosítási kérés"

**Mezők**
- ServiceDeskConnectionName
- Szolgáltatás ügyfélszolgálati azonosítója
- Létrehozta
- Lezárt
- Forrás
- Társítás
- Cím
- Típus
- Kategória
- Állapot
- Eszkalációs
- Ütközés állapota
- Sürgős
- Prioritás
- Kockázati
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

| OMS mező | ITSM mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| IncidentState_s | Állapot |
| Urgency_s |Sürgős |
| Impact_s |Hatás|
| Priority_s | Prioritás |
| CreatedBy_s | Által megnyitott |
| ResolvedBy_s | Megoldó|
| ClosedBy_s  | Lezárt |
| Source_s| Kapcsolat típusa |
| AssignedTo_s | Rendelt  |
| Category_s | Kategória |
| Title_s|  Rövid leírás |
| Description_s|  Megjegyzések |
| CreatedDate_t|  Megnyitva |
| ClosedDate_t| Lezárt|
| ResolvedDate_t|Megoldva|
| Computer  | konfigurációs elem |

## <a name="output-data-for-a-servicenow-change-request"></a>A ServiceNow kimeneti adatok változáskérés

| OMS mező | ITSM mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| CreatedBy_s | Kérelmező |
| ClosedBy_s | Lezárt |
| AssignedTo_s | Rendelt  |
| Title_s|  Rövid leírás |
| Type_s|  Típus |
| Category_s|  Kategória |
| CRState_s|  Állapot|
| Urgency_s|  Sürgős |
| Priority_s| Prioritás|
| Risk_s| Kockázati|
| Impact_s| Hatás|
| RequestedDate_t  | A kért dátum szerint |
| ClosedDate_t | Lezárás dátuma |
| PlannedStartDate_t  |     Tervezett kezdő dátuma |
| PlannedEndDate_t  |   Tervezett befejezési dátum |
| WorkStartDate_t  | Tényleges kezdési dátuma |
| WorkEndDate_t | Tényleges befejezési dátum|
| Description_s | Leírás |
| Computer  | Konfigurációs elem |

**A minta Naplóelemzési képernyő ITSM adatokat:**

![Napló elemzési képernyő](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="itsmc-integration-with-other-oms-solutions"></a>Egyéb OMS-megoldások ITSMC integrációja

ITSM connector jelenleg a Service Map megoldással való integráció.

Szolgáltatástérkép automatikusan észleli az alkalmazás-összetevők, a Windows és Linux rendszerek, és leképezi a szolgáltatások közötti kommunikáció. Lehetővé teszi a kiszolgálók, úgy gondolja, hogy azok –, hogy a kritikus szolgáltatások összekapcsolt rendszerként. Szolgáltatástérkép jeleníti meg a kiszolgálók, a folyamatok közötti kapcsolatokat, és portok között bármely TCP-csatlakoztatott architektúra a konfiguráció nem szükséges másik ügynököt telepíteni.

További információ: [Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md).

Is használhatja a Service Map megoldás, ha a szolgáltatás ügyfélszolgálati létrehozott elemek szerepelnek a ITSM megoldások a következő példában látható módon tekintheti meg:

![ServiceMap integráció](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Az OMS-értesítések ITSM munkaelemek létrehozása

Az ITSMC megoldás helyen beállíthatja az OMS-riasztások elindítani a csatlakoztatott ITSM eszköz munkaelemek létrehozása. Kövesse az alábbi eljárást:

1. A **naplófájl-keresési** ablakban futtassa a napló keresési lekérdezés adatainak megtekintéséhez. Lekérdezés eredményei munkaelemek forrását.
2. A **naplófájl-keresési**, kattintson a **riasztási** megnyitásához a **riasztási szabály hozzáadása** lap.

    ![Napló elemzési képernyő](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. Az a **riasztási szabály hozzáadása** ablakban adja meg a szükséges adatokat a **neve**, **súlyossági**, **keresési lekérdezés**, és **riasztás feltételek** (ablakban/Időmetrika mérési).
4. Válassza ki **Igen** a **ITSM műveletek**.
5. Válassza ki a ITSM kapcsolatot a **válassza ki a kapcsolat** listája.
6. Adja meg szükség szerint adatait.
7. Minden naplóbejegyzés a riasztás egy külön munkaelemet létrehozásához válassza a **minden naplóbejegyzés egyes munkaelemek létrehozása** jelölőnégyzetet.

    Vagy

    hagyja üresen ezt a jelölőnégyzetet, jelölje ki az ebben a riasztásban vonta naplóbejegyzések tetszőleges számú csak egy munkaelem létrehozásához.

7. Kattintson a **Save** (Mentés) gombra.

A létrehozott OMS riasztás alatt látható **beállítások**>**riasztások**. A megfelelő ITSM kapcsolat munkaelemek jönnek létre, ha a megadott riasztás feltétel teljesül.

## <a name="create-itsm-work-items-from-oms-logs"></a>Az OMS-naplók ITSM munkaelemek létrehozása

A csatlakoztatott ITSM forrásokban közvetlenül egy naplóbejegyzést képviselnek a munkaelemek is létrehozhat. Kövesse az alábbi eljárást:

1. A **naplófájl-keresési**, keresse meg a szükséges adatokat, válassza ki a részleteket, és kattintson **létrehozás munkaelem**.

    A **ITSM munkaelem létrehozása** ablak jelenik meg:

    ![Napló elemzési képernyő](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

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

## <a name="create-itsm-work-items-from-azure-alerts"></a>Az Azure riasztásokból ITSM munkaelemek létrehozása
ITSMC integrálva van a művelet csoportok.

[A művelet csoportok](../monitoring-and-diagnostics/monitoring-action-groups.md) lehetőséget nyújtanak olyan moduláris és újrafelhasználható, az Azure-riasztások műveleteket váltanak ki. A ITSM művelet művelet csoportok használatával hozhat létre az ITSM termék, amely rendelkezik egy létező kapcsolatot ITSM összekötő megoldáshoz munkaelemek.

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

Azure riasztási szabály létrehozása/szerkesztése, során művelet csoportot, amelynek ITSM művelet használni. A riasztás akkor váltja ki, amikor a ITSM eszköz munkaelem jön létre.

>[!NOTE]

> Jelenleg csak a tevékenység napló riasztások támogatják az ITSM műveletet. Az egyéb Azure értesítések ITSM a művelet nem támogatott.


## <a name="troubleshoot-itsm-connections-in-oms"></a>Az OMS ITSM kapcsolatok hibáinak elhárítása
1.  Ha a kapcsolat hibája esetén a csatlakoztatott adatforrás felhasználói felület egy **hiba történt a kapcsolat mentése** üzenet, a következő lépéseket:
 - A ServiceNow, Cherwell és Provance kapcsolatok esetén – győződjön meg arról, megfelelően beírt a felhasználónév, a jelszó, az ügyfél-azonosító és a titkos ügyfélkulcs az egyes kapcsolatok.
        -Ellenőrizze, hogy ha a megfelelő engedélyekkel rendelkezik a megfelelő ITSM termékben való csatlakozáshoz.
 - A Service Manager-kapcsolatok esetén – győződjön meg arról, hogy a webalkalmazás telepítése sikeres volt, és a hibrid kapcsolat jön létre. Ellenőrizze, hogy sikeresen létrejött a kapcsolat a helyszíni Service Manager számítógéppel, látogasson el a webes alkalmazás URL-CÍMÉT, hogy dokumentációjában ismertetett módon a [a hibrid kapcsolat](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).

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

## <a name="next-steps"></a>Következő lépések
[ITSM termékek és szolgáltatások hozzáadása IT Service Management-összekötő](log-analytics-itsmc-connections.md).

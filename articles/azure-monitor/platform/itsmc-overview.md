---
title: IT-szolgáltatásmenedzsmenti csatoló a Log Analytics
description: Ez a cikk áttekintést nyújt a IT-szolgáltatásmenedzsmenti csatolóokról (ITSMC), valamint arról, hogyan használható az alkalmazás a ITSM-munkaelemek figyelésére és felügyeletére Log Analytics és a problémák gyors megoldásához.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: c34cd8e399a005f5eadb3751fb0575f6ecfc27ed
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380878"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Az Azure és a ITSM-eszközök összekötése IT-szolgáltatásmenedzsmenti csatoló használatával

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT-szolgáltatásmenedzsmenti csatoló (ITSMC) lehetővé teszi, hogy az Azure-t egy támogatott informatikai szolgáltatás-felügyeleti (ITSM) termékhez vagy szolgáltatáshoz kapcsolódjon.

Az Azure-szolgáltatások, például az Azure Log Analytics és Azure Monitor eszközöket biztosítanak az Azure-beli és nem Azure-beli erőforrásokkal kapcsolatos problémák észleléséhez, elemzéséhez és hibaelhárításához. A problémához kapcsolódó munkaelemek azonban általában egy ITSM-termékben vagy-szolgáltatásban találhatók. A ITSMC kétirányú kapcsolatot biztosít az Azure és a ITSM eszközök között, így gyorsabban megoldhatja a problémákat.

A ITSMC a következő ITSM-eszközökkel támogatja a kapcsolatokat:

-   ServiceNow
-   System Center Service Manager
-   Megjelenő
-   Cherwell

A ITSMC a következőket teheti:

-  Munkaelemeket hozhat létre a ITSM eszközben az Azure-riasztások (metrikus riasztások, műveletnapló-riasztások és Log Analytics riasztások) alapján.
-  Lehetőség van arra is, hogy szinkronizálja az incidenst, és módosítsa a ITSM eszközről az Azure Log Analytics-munkaterületre.

A jogi feltételekkel és az adatvédelmi szabályzattal kapcsolatos információkért lásd: a [Microsoft adatvédelmi nyilatkozata](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Az alábbi lépések végrehajtásával kezdheti meg a ITSMC használatát:

1.  [ITSMC hozzáadása.](#add-it-service-management-connector)
2.  [Hozzon létre egy ITSM-kapcsolatokat.](#create-an-itsm-connection)
3.  [Használja a kapcsolatokat.](#use-itsmc)


##  <a name="add-it-service-management-connector"></a>IT-szolgáltatásmenedzsmenti csatoló hozzáadása

A kapcsolatok létrehozása előtt hozzá kell adnia a ITSMC.

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget:

   ![Az erőforrás létrehozása menüelemet megjelenítő képernyőkép.](media/itsmc-overview/azure-add-new-resource.png)

2. **It-szolgáltatásmenedzsmenti csatoló** keresése az Azure Marketplace-en. Válassza a **Létrehozás** lehetőséget:

   ![Képernyőkép, amely megjeleníti a létrehozás gombot az Azure piactéren.](media/itsmc-overview/add-itsmc-solution.png)

3. A **OMS munkaterület** szakaszban válassza ki azt az Azure log Analytics-munkaterületet, ahol telepíteni szeretné a ITSMC-t.
   >[!NOTE]
   > * A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-munkaterületek már *log Analytics munkaterületek*.
   > * A ITSMC csak Log Analytics munkaterületeken telepíthetők a következő régiókban: USA keleti régiója, USA 2. nyugati régiója, az USA déli középső régiója, az USA nyugati középső régiója, US Gov Arizona, US Gov Virginia, Közép-Kanada, Nyugat-Európa, Dél-Egyesült Királyság, Délkelet-Ázsia, Kelet-Japán, Közép-India és Délkelet-Ausztrália.


4. A **log Analytics munkaterület** szakaszban válassza ki azt az erőforráscsoportot, amelyben létre kívánja hozni a ITSMC-erőforrást:

   ![A Log Analytics munkaterület szakaszt bemutató képernyőkép.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-munkaterületek már *log Analytics munkaterületek*.

5. Kattintson az **OK** gombra.

A ITSMC-erőforrás telepítésekor az ablak jobb felső sarkában megjelenik egy értesítés.


## <a name="create-an-itsm-connection"></a>ITSM-kapcsolatok létrehozása

A ITSMC telepítése után létrehozhat egy-egy kapcsolatokat.

A kapcsolatok létrehozásához elő kell készítenie a ITSM eszközt, hogy engedélyezze a ITSMC való kapcsolódást.  

Azon ITSM-termék alapján, amelyhez csatlakozik, válassza a következő hivatkozások egyikét az utasításokhoz:

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Megjelenő](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Miután elő a ITSM-eszközöket, hajtsa végre a következő lépéseket a kapcsolatok létrehozásához:

1. Az **összes erőforrás** területen keresse meg a **ügyfélszolgálati ( *a munkaterület neve* )** :

   ![A Azure Portal legutóbbi erőforrásait bemutató képernyőkép.](media/itsmc-overview/itsm-connections.png)

1. A bal oldali ablaktábla **munkaterület-adatforrások** területén válassza a **ITSM-kapcsolatok** elemet:

   ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok menüpontot.](media/itsmc-overview/add-new-itsm-connection.png)
   Ez az oldal a kapcsolatok listáját jeleníti meg.
1. Válassza a **kapcsolatok hozzáadása** lehetőséget.

4. Adja meg a [ITSM-termékekkel/szolgáltatásokkal kapcsolatos ITSMC-kapcsolatok konfigurálása](./itsmc-connections.md)című témakörben leírtak szerinti kapcsolatbeállításokat.

   > [!NOTE]
   >
   > Alapértelmezés szerint a ITSMC 24 óránként egyszer frissíti a kapcsolatok konfigurációs szolgáltatásait. Ha azonnal frissíteni szeretné a kapcsolatok adatait, hogy azok megfeleljenek a módosítások vagy a sablonok frissítéseinek, válassza a **szinkronizálás** gombot a kapcsolatok paneljén:
   >
   > ![Képernyőkép, amely megjeleníti a szinkronizálás gombot a kapcsolatok panelen.](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>ITSMC használata
   A ITSMC használatával munkaelemeket hozhat létre az Azure-riasztásokból, Log Analytics a riasztásokból, és Log Analytics naplóbejegyzéseket.

## <a name="template-definitions"></a>Sablon-definíciók
   Vannak olyan munkaelem-típusok, amelyek használhatják a ITSM eszköz által definiált sablonokat.
A sablonok használatával meghatározhatja azokat a mezőket, amelyeket a rendszer a műveleti csoport részeként definiált rögzített értékek alapján automatikusan kitölt. A sablonokat a ITSM eszközben definiálhatja. Megadhatja, hogy melyik sablont szeretné használni a műveleti csoport definíciójának részeként.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-munkaelemek létrehozása az Azure-riasztásokból

A ITSM-kapcsolatok létrehozása után létrehozhat munkaelemeket a ITSM-eszközön az Azure-riasztások alapján. A munkaelemek létrehozásához használja a ITSM műveletet a műveleti csoportokban.

A műveleti csoportok moduláris és újrafelhasználható módot biztosítanak az Azure-riasztások műveleteinek elindításához. A műveleti csoportokat metrikus riasztásokkal, műveletnapló riasztásokkal és Azure Log Analytics riasztásokkal is használhatja a Azure Portal.

> [!NOTE]
> A ITSM-kapcsolatok létrehozása után a szinkronizálási folyamat befejezéséhez 30 percet kell várnia.
> 

Munkaelemek létrehozásához kövesse az alábbi eljárást:

1. A Azure Portal válassza a  **riasztások** elemet.
2. A képernyő felső részén található menüben válassza a **kezelés műveletek** elemet:

    ![Képernyőkép, amely a műveletek kezelése menüelemet jeleníti meg.](media/itsmc-overview/action-groups-selection-big.png)

   Megjelenik a **műveleti csoport létrehozása** ablak.

3. Válassza ki azt az **előfizetést** és **erőforráscsoportot** , amelyben létre szeretné hozni a műveleti csoportot. Adja meg a műveleti csoport **nevét** és **megjelenítendő nevét** . Válassza a Next (tovább) lehetőséget **: értesítések**.

    ![A műveleti csoport létrehozása ablakot megjelenítő képernyőkép.](media/itsmc-overview/action-groups-details.png)

4. Az értesítési listán válassza a **Tovább: műveletek** elemet.
5. A műveletek listában válassza a **ITSM** elemet a **Művelettípus** listában. Adja meg a művelet **nevét** . Válassza a **Szerkesztés részleteit** jelölő toll gombot.
6. Az **előfizetés** listában válassza ki azt az előfizetést, amelyben a log Analytics munkaterület található. A **kapcsolatok** listájában válassza ki a ITSM-összekötő nevét. Ezt a munkaterület neve követi majd. Például: MyITSMConnector (Sajátmunkaterület).

7. Válasszon **munkaelem** -típust.

8. Ha rögzített értékekkel rendelkező, beépített mezőket szeretne kitölteni, válassza az **egyéni sablon használata** lehetőséget. Ellenkező esetben válasszon egy meglévő [sablont](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#template-definitions) a **sablon** listáról, és adja meg a rögzített értékeket a sablon mezőiben.

9. Ha **az egyes konfigurációs elemekhez az egyes** munkaelemek létrehozása lehetőséget választja, minden konfigurációs elemnek saját munkaeleme lesz. A konfigurációs elemek egy munkaelemet kapnak. A rendszer a létrehozandó riasztások alapján frissíti.

   * Abban az esetben, ha a munkaelem legördülő menüben a "incidens" vagy a "riasztás" lehetőséget választotta: Ha törli az egyes **konfigurációs elemek egyéni munkaelemeinek létrehozása** jelölőnégyzet jelölését, minden riasztás új munkaelemet fog létrehozni. Egy konfigurációs elemmel több riasztás is lehet.

   ![A ITSM-jegy ablakát megjelenítő képernyőkép.](media/itsmc-overview/itsm-action-configuration.png)
   
   * Ha a munkaelem legördülő menüben a "esemény" lehetőséget választja: Ha a választógombok kiválasztásakor a különböző munkaelemek **létrehozása** lehetőséget választja, minden riasztás új munkaelemet fog létrehozni. Ha a választógombok kiválasztásakor az egyes **konfigurációs elemek egyéni** munkaelemek létrehozása lehetőséget választja, minden konfigurációs elemnek saját munkaeleme lesz. 

10. Kattintson az **OK** gombra.

Azure-riasztási szabály létrehozásakor vagy szerkesztésekor használjon egy ITSM műveletet tartalmazó műveleti csoportot. A riasztás indításakor a rendszer létrehozza vagy frissíti a munkaelemet a ITSM eszközben.

> [!NOTE]
>
>- A ITSM művelet díjszabásával kapcsolatos információkért tekintse meg a műveleti csoportok [díjszabási lapját](https://azure.microsoft.com/pricing/details/monitor/) .
>
>
>- A riasztási szabály definíciójának rövid leírása mezője 40 karakterre van korlátozva, ha a ITSM művelettel küldi el.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Az incidens megjelenítése és elemzése és a kérelmekre vonatkozó adatváltozás

A kapcsolatok beállításakor a ITSMC legfeljebb 120 napos incidenst tud szinkronizálni, és módosíthatja a kérelmek adatait. Az adatok naplózási rekordjainak sémája a jelen cikk [következő szakaszában](#additional-information) található.

A ITSMC irányítópult használatával megjelenítheti az incidenst és módosíthatja a kérelmeket:

![A ITSMC irányítópultot megjelenítő képernyőkép.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Az irányítópult az összekötő állapotáról is tartalmaz információkat, amelyeket kiindulási pontként használhat a kapcsolatokkal kapcsolatos problémák elemzéséhez.

A Service Map érintett számítógépeken szinkronizált incidenseket is megjelenítheti.

Service Map automatikusan feltérképezi az alkalmazás összetevőit Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Lehetővé teszi, hogy a kiszolgálókat a következőképpen tekintheti meg: olyan összekapcsolt rendszerek, amelyek kritikus szolgáltatásokat biztosítanak. Service Map megjeleníti a kiszolgálók, a folyamatok és a portok közötti kapcsolatokat bármely TCP-kapcsolattal rendelkező architektúrán keresztül. Az ügynök telepítésén kívül nincs szükség konfigurációra. További információ: [a Service Map használata](../insights/service-map.md).

Ha Service Map használ, megtekintheti a ITSM-megoldásokban létrehozott ügyfélszolgálati elemeket az itt látható módon:

![Képernyőkép, amely a Log Analytics képernyőt jeleníti meg.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)


## <a name="additional-information"></a>További információ

### <a name="data-synced-from-your-itsm-product"></a>A ITSM-termékről szinkronizált adatok
Az incidensek és a módosítási kérelmek szinkronizálása a ITSM-termékről a Log Analytics munkaterületre a kapcsolatok konfigurációja alapján történik.

Ez a szakasz néhány példát mutat be a ITSMC által összegyűjtött adatokra.

A **ServiceDesk_CL** mezői a log Analyticsba importált munkaelem típusától függően változnak. A következő lista felsorolja a két munkaelem típusának mezőit:

**Munkaelem:** **incidensek**  
ServiceDeskWorkItemType_s = "incidens"

**Mezők**

- ServiceDeskConnectionName
- Ügyfélszolgálat azonosítója
- Állapot
- Sürgősség
- Hatás
- Prioritás
- Eszkalálás
- Created By (Létrehozó)
- Feloldotta:
- Lezárta
- Forrás
- A jog birtokosa
- Kategória
- Cím
- Leírás
- Létrehozás dátuma
- Lezárás dátuma
- Megoldás dátuma
- Utolsó módosítás dátuma
- Computer


**Munkaelem:** **módosítási kérelmek**

ServiceDeskWorkItemType_s = "módosítási kérelem"

**Mezők**
- ServiceDeskConnectionName
- Ügyfélszolgálat azonosítója
- Created By (Létrehozó)
- Lezárta
- Forrás
- A jog birtokosa
- Cím
- Típus
- Kategória
- Állapot
- Eszkalálás
- Ütközési állapot
- Sürgősség
- Prioritás
- Kockázat
- Hatás
- A jog birtokosa
- Létrehozás dátuma
- Lezárás dátuma
- Utolsó módosítás dátuma
- Kért dátum
- Tervezett kezdési dátum
- Tervezett befejezési dátum
- Munka kezdő dátuma
- Munka befejezési dátuma
- Leírás
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>ServiceNow incidens kimeneti adatkészlete

| Log Analytics mező | ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| IncidentState_s | Állapot |
| Urgency_s |Sürgősség |
| Impact_s |Hatás|
| Priority_s | Prioritás |
| CreatedBy_s | Megnyitva |
| ResolvedBy_s | Feloldotta:|
| ClosedBy_s  | Lezárta |
| Source_s| Kapcsolattartó típusa |
| AssignedTo_s | Felelős  |
| Category_s | Kategória |
| Title_s|  Rövid leírás |
| Description_s|  Jegyzetek |
| CreatedDate_t|  Megnyitott |
| ClosedDate_t| lezárva|
| ResolvedDate_t|Feloldva|
| Computer  | Konfigurációelem |

## <a name="output-data-for-a-servicenow-change-request"></a>ServiceNow-módosítási kérelem kimeneti adatkészlete

| Log Analytics | ServiceNow mező |
|:--- |:--- |
| ServiceDeskId_s| Szám |
| CreatedBy_s | Kérelmező |
| ClosedBy_s | Lezárta |
| AssignedTo_s | Felelős  |
| Title_s|  Rövid leírás |
| Type_s|  Típus |
| Category_s|  Kategória |
| CRState_s|  Állapot|
| Urgency_s|  Sürgősség |
| Priority_s| Prioritás|
| Risk_s| Kockázat|
| Impact_s| Hatás|
| RequestedDate_t  | Kérelem dátuma szerint |
| ClosedDate_t | Lezárt dátum |
| PlannedStartDate_t  |     Tervezett kezdési dátum |
| PlannedEndDate_t  |   Tervezett befejezési dátum |
| WorkStartDate_t  | Tényleges kezdési dátum |
| WorkEndDate_t | Tényleges befejezési dátum|
| Description_s | Leírás |
| Computer  | Konfigurációs tétel |


## <a name="troubleshoot-itsm-connections"></a>ITSM-kapcsolatok hibáinak megoldása
- Ha egy kapcsolat nem sikerül a csatlakoztatott forrás felhasználói felületéről, és hibaüzenet jelenik meg a **kapcsolati üzenet mentésekor** , hajtsa végre a következő lépéseket:
   - ServiceNow, Cherwell és elővance kapcsolatok esetén:  
     - Győződjön meg arról, hogy minden kapcsolathoz helyesen adta meg a felhasználónevet, a jelszót, az ügyfél-azonosítót és az ügyfél-titkot.  
     - Győződjön meg arról, hogy megfelelő jogosultságokkal rendelkezik a megfelelő ITSM-termékben a kapcsolódáshoz.  
   - Service Manager kapcsolatok esetén:  
     - Győződjön meg arról, hogy a webalkalmazás üzembe helyezése sikeres volt, és hogy a hibrid kapcsolat létrejött. Annak ellenőrzéséhez, hogy a kapcsolat sikeresen létrejött-e a helyszíni Service Manager számítógéppel, lépjen a webalkalmazás URL-címére a [hibrid kapcsolat](./itsmc-connections.md#configure-the-hybrid-connection)létrehozásához szükséges dokumentációban leírtak szerint.  

- Ha a ServiceNow származó adatok nem lettek szinkronizálva Log Analyticsre, győződjön meg arról, hogy a ServiceNow-példány nem alvó állapotú. A ServiceNow dev-példányok néha alvó állapotba kerülnek, ha hosszú ideje tétlenek. Ha ez nem történik meg, jelentse a problémát.
- Ha Log Analytics riasztások tüzet, de a munkaelemek nem jönnek létre a ITSM termékben, ha a konfigurációs elemek nem jönnek létre/nem kapcsolódnak munkaelemekhez vagy egyéb információkhoz, tekintse meg ezeket az erőforrásokat:
   -  ITSMC: a megoldás a kapcsolatok, a munkaelemek, a számítógépek és egyebek összegzését jeleníti meg. Válassza ki az **összekötő állapota** címkével ellátott csempét. Ekkor a **Keresés** a megfelelő lekérdezéssel történik. További információért tekintse meg a rekordokat `LogType_S` `ERROR` .
   - **Naplók keresése** oldalon: a hibákat és a kapcsolódó információkat közvetlenül a lekérdezés használatával tekintheti meg `*ServiceDeskLog_CL*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager webalkalmazás központi telepítésének hibáinak megoldása
-   Ha problémák merülnek fel a webalkalmazások üzembe helyezésével kapcsolatban, győződjön meg arról, hogy rendelkezik az előfizetés erőforrásainak létrehozásához/üzembe helyezéséhez szükséges engedélyekkel.
-   Ha a [parancsfájl](itsmc-service-manager-script.md)futtatásakor nem az objektumra **vonatkozó hiba példányára van beállítva** , akkor ellenőrizze, hogy érvényes értékeket adott-e meg a **Felhasználó konfigurációja** szakaszban.
-   Ha nem sikerül létrehoznia a Service Bus Relay-névteret, győződjön meg arról, hogy a szükséges erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha nincs regisztrálva, manuálisan hozza létre a Service Bus Relay-névteret a Azure Portalból. Azt is létrehozhatja, amikor [létrehozza a hibrid kapcsolatokat](./itsmc-connections.md#configure-the-hybrid-connection) a Azure Portalban.


## <a name="contact-us"></a>Kapcsolat

Ha a IT-szolgáltatásmenedzsmenti csatolóról kérdez vagy visszajelzést szeretne küldeni, lépjen kapcsolatba velünk a következő címen: [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Következő lépések
[ITSM-termékek/szolgáltatások hozzáadása a IT-szolgáltatásmenedzsmenti csatolóhoz](./itsmc-connections.md)


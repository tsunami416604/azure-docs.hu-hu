---
title: IT-szolgáltatásmenedzsmenti csatoló az Azure Log Analyticsban | Microsoft Docs
description: Ez a cikk áttekintést nyújt a IT-szolgáltatásmenedzsmenti csatolóról (ITSMC), valamint arról, hogyan használhatja ezt a megoldást az Azure-Log Analytics ITSM-munkaelemeinek központi figyelésére és felügyeletére, valamint a problémák gyors megoldására.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 0a632e8c57ab57869e4454b0d6a4018de6bd5548
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613764"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Az Azure csatlakoztatása ITSM-eszközökhöz az IT Service Management-összekötő használatával

![IT-szolgáltatásmenedzsmenti csatoló szimbólum](media/itsmc-overview/itsmc-symbol.png)

Az IT Service Management-összekötő (ITSMC) lehetővé teszi, hogy az Azure-t egy támogatott IT Service Management- (ITSM-) termékhez/szolgáltatáshoz kapcsolja.

Az Azure-szolgáltatások, például a Log Analytics és a Azure Monitor eszközöket biztosítanak az Azure-beli és nem Azure-beli erőforrásokkal kapcsolatos problémák észleléséhez, elemzéséhez és hibaelhárításához. A problémához kapcsolódó munkaelemek azonban általában egy ITSM termékben vagy szolgáltatásban találhatók. Az ITSM-összekötő kétirányú kapcsolatot biztosít az Azure és a ITSM eszközök között, így könnyebben megoldhatja a problémákat.

A ITSMC a következő ITSM-eszközökkel támogatja a kapcsolatokat:

-   ServiceNow
-   System Center Service Manager
-   Megjelenő
-   Cherwell

A ITSMC a következőket teheti:

-  Munkaelemek létrehozása a ITSM eszközben az Azure-riasztások (metrikus riasztások, műveletnapló-riasztások és Log Analytics riasztások) alapján.
-  Lehetőség van arra is, hogy szinkronizálja az incidenst, és módosítsa a ITSM eszközről az Azure Log Analytics-munkaterületre.

További információ a [jogi feltételekről és az adatvédelmi szabályzatról](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

Az alábbi lépések végrehajtásával kezdheti a ITSM-csatoló használatát:

1.  [A ITSM-csatoló megoldás hozzáadása](#adding-the-it-service-management-connector-solution)
2.  ITSM-kapcsolatok létrehozása
3.  [A kapcsolatok használata](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>A IT-szolgáltatásmenedzsmenti csatoló megoldás hozzáadása

A kapcsolatok létrehozása előtt hozzá kell adnia a ITSM-csatoló megoldást.

1. Azure Portal kattintson az **+ új** ikonra.

   ![Azure új erőforrás](media/itsmc-overview/azure-add-new-resource.png)

2. Keresse meg **it-szolgáltatásmenedzsmenti csatoló** a piactéren, és kattintson a **Létrehozás**gombra.

   ![ITSMC-megoldás hozzáadása](media/itsmc-overview/add-itsmc-solution.png)

3. A **OMS munkaterület** szakaszban válassza ki azt az Azure log Analytics-munkaterületet, amelyre telepíteni kívánja a megoldást.
   >[!NOTE]
   > * A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-munkaterületek már Log Analytics munkaterületek.
   > * A ITSM-csatoló csak Log Analytics munkaterületekre telepíthető a következő régiókban: USA keleti régiója, Nyugat-RÉGIÓJA, USA déli középső régiója, USA nyugati középső régiója, Fairfax, Közép-Kanada, Nyugat-Európa, Dél-Egyesült Királyság, Délkelet-Ázsia, Kelet-Japán, Közép-India, Délkelet-Ausztrália.

4. A **OMS-munkaterület beállításai** szakaszban válassza ki azt a ResourceGroup, amelyben létre szeretné hozni a megoldás erőforrását.

   ![ITSMC-munkaterület](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >A Microsoft Operations Management Suite (OMS) és a Azure Monitor közötti folyamatos áttérés részeként a OMS-munkaterületek már Log Analytics munkaterületek.

5. Kattintson a **Létrehozás** elemre.

A megoldás erőforrásának telepítésekor megjelenik egy értesítés az ablak jobb felső sarkában.


## <a name="creating-an-itsm--connection"></a>ITSM-kapcsolatok létrehozása

A megoldás telepítése után létrehozhat egy-egy kapcsolatokat.

A kapcsolatok létrehozásához elő kell készítenie a ITSM eszközt, hogy engedélyezze a kapcsolódást a ITSM-csatoló-megoldástól.  

Attól függően, hogy melyik ITSM-termékhez csatlakozik, kövesse az alábbi lépéseket:

- [System Center Service Manager (SCSM)](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Megjelenő](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Miután elő a ITSM-eszközöket, kövesse az alábbi lépéseket a kapcsolatok létrehozásához:

1. Nyissa meg az **összes erőforrást**, keresse meg a **ügyfélszolgálati (YourWorkspaceName)**.
2. A bal oldali ablaktábla **munkaterület-ADATforrások** területén kattintson a **ITSM-kapcsolatok**elemre.
   ![ITSM-kapcsolatok](media/itsmc-overview/itsm-connections.png)

   Ez az oldal a kapcsolatok listáját jeleníti meg.
3. Kattintson a **kapcsolatok hozzáadása**lehetőségre.

   ![ITSM-kapcsolatok hozzáadása](media/itsmc-overview/add-new-itsm-connection.png)

4. Adja meg a [ITSM termékek/szolgáltatások ITSMC-kapcsolatok konfigurálása című cikkben](./itsmc-connections.md)leírtak szerint megjelenő kapcsolatbeállításokat.

   > [!NOTE]
   >
   > Alapértelmezés szerint a ITSMC 24 óránként egyszer frissíti a kapcsolatok konfigurációs szolgáltatásait. Ha azonnal frissíteni szeretné a kapcsolatok adatait az Ön által végzett módosítások vagy sablonok frissítéseire, kattintson a **szinkronizálás** gombra a kapcsolatok paneljén.

   ![A kapcsolatok frissítése](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>A megoldás használata
   Az ITSM-csatoló megoldás használatával munkaelemeket hozhat létre az Azure-riasztásokból, Log Analytics a riasztásokat és a Log Analyticsi rekordokat.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-munkaelemek létrehozása az Azure-riasztásokból

Miután létrejött a ITSM-kapcsolat, létrehozhat munkaelemeket a ITSM-eszközön az Azure-riasztások alapján, a **műveleti csoportok** **ITSM műveletének** használatával.

A műveleti csoportok moduláris és újrahasznosítható módszert biztosítanak az Azure-riasztások műveleteinek elindításához. A műveleti csoportokat metrikus riasztásokkal, műveletnapló-riasztásokkal és Azure Log Analytics riasztásokkal is használhatja Azure Portalokban.

> [!NOTE]
> Miután létrehozta a ITSM-csatlakozást, a szinkronizálási folyamat befejezése után 30 percre kell várnia.
> 

Kövesse az alábbi eljárást:

1. Azure Portal kattintson a  **figyelés**elemre.
2. A bal oldali ablaktáblán kattintson a  **műveleti csoportok**elemre. Megjelenik a **műveleti csoport hozzáadása** ablak.

    ![Műveletcsoportok](media/itsmc-overview/action-groups.png)

3. Adja meg a műveleti csoport **nevét** és **gazdagépbejegyzés** . Válassza ki azt az **erőforráscsoportot** és- **előfizetést** , amelyben létre szeretné hozni a műveleti csoportot.

    ![Műveleti csoportok részletei](media/itsmc-overview/action-groups-details.png)

4. A műveletek listában válassza a **ITSM** elemet a **művelet típusa**legördülő menüből. Adja meg a művelet **nevét** , majd kattintson a **részletek szerkesztése**elemre.
5. Válassza ki azt az **előfizetést** , ahol a log Analytics munkaterület található. Válassza ki a **kapcsolatok** nevét (a ITSM-csatoló nevét), majd a munkaterület nevét. Például: "MyITSMMConnector (Sajátmunkaterület)".

    ![ITSM művelet részletei](media/itsmc-overview/itsm-action-details.png)

6. Válassza ki a **munkaelem** típusát a legördülő menüből.
   Válasszon egy meglévő sablont, vagy töltse ki a ITSM termék által megkövetelt mezőket.
7. Kattintson az **OK** gombra.

Azure-riasztási szabály létrehozásakor/szerkesztésekor használjon egy ITSM műveletet tartalmazó műveleti csoportot. A riasztás indításakor a rendszer létrehozza/frissíti a munkaelemet a ITSM eszközben.

> [!NOTE]
>
> A ITSM művelet díjszabásával kapcsolatos információkért tekintse meg a műveleti csoportok [díjszabási lapját](https://azure.microsoft.com/pricing/details/monitor/) .

> [!NOTE]
>
> A riasztási szabály definíciójának rövid leírása mezője legfeljebb 40 karakter hosszú lehet, ha a ITSM művelettel küldi el a rendszer.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Az incidens megjelenítése és elemzése és a kérelmekre vonatkozó adatváltozás

A kapcsolatok beállításakor a ITSM-összekötő akár 120 napos incidenst is képes szinkronizálni, és módosíthatja a kérések adatait. Az adatok naplózási rekordjainak sémája a [következő szakaszban](#additional-information)található.

Az incidens és a változási kérelmek adatai megjeleníthetők a megoldás ITSM-csatoló irányítópultjának használatával.

![Log Analytics képernyő](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Az irányítópult információt nyújt az összekötő állapotáról is, amely kiindulási pontként használható a kapcsolatokkal kapcsolatos problémák elemzéséhez.

Az érintett számítógépeken szinkronizált incidenseket is megjelenítheti a Service Map megoldáson belül.

Service Map automatikusan feltérképezi az alkalmazás összetevőit Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Használatával a kiszolgálók úgy jelennek meg, ahogyan Ön gondol rájuk: egymással összekapcsolt rendszerekként, amelyek kritikus fontosságú szolgáltatásokat tesznek elérhetővé. A Service Map megmutatja a kapcsolatokat mindazon kiszolgálók, folyamatok és portok között, amelyek TCP-kapcsolattal összekötött architektúrában találhatóak, és ehhez nem szükséges semmilyen konfiguráció, csupán az ügynököt kell telepíteni. [További információ](../insights/service-map.md).

Ha a Service Map megoldást használja, megtekintheti a ITSM-megoldásokban létrehozott ügyfélszolgálati elemeket az alábbi példában látható módon:

![Log Analytics képernyő](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

További információ: [Service Map](../insights/service-map.md)


## <a name="additional-information"></a>További információ

### <a name="data-synced-from-itsm-product"></a>ITSM-termékről szinkronizált adatok
Az incidensek és a módosítási kérelmek szinkronizálása a ITSM-termékről a Log Analytics munkaterületre a kapcsolódási konfiguráció alapján történik.

A következő információk példákat mutatnak a ITSMC által összegyűjtött adatokra:

> [!NOTE]
>
> Az Log Analyticsba importált munkaelem típusától függően **ServiceDesk_CL** a következő mezőket tartalmazza:

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
1. Ha a kapcsolat nem sikerül a csatlakoztatott forrás felhasználói felületéről, mert **hiba történt a kapcsolati üzenet mentésekor** , hajtsa végre a következő lépéseket:
   - ServiceNow, Cherwell és elővance kapcsolatok esetén  
   - Győződjön meg arról, hogy minden kapcsolathoz helyesen adta meg a felhasználónevet, a jelszót, az ügyfél-azonosítót és az ügyfél-titkot.  
   - Ellenőrizze, hogy rendelkezik-e megfelelő jogosultságokkal a megfelelő ITSM-termékben a kapcsolódáshoz.  
   - Service Manager kapcsolatok esetén  
   - Győződjön meg arról, hogy a webalkalmazás sikeresen telepítve van, és a hibrid kapcsolat létrejött. Annak ellenőrzéséhez, hogy a kapcsolat sikeresen létrejött-e a helyszíni Service Manager géppel, látogasson el a webalkalmazás URL-címére, amely a [hibrid kapcsolat](./itsmc-connections.md#configure-the-hybrid-connection)készítéséhez szükséges dokumentációban található.  

2. Ha a ServiceNow származó adatok nem lettek szinkronizálva a Log Analyticsba, győződjön meg arról, hogy a ServiceNow-példány nem alvó állapotú. A ServiceNow dev-példányok időnként hosszabb ideig tétlenek maradnak. Máskülönben jelentse a problémát.
3. Ha Log Analytics a riasztások tüzet, de a munkaelemek nem jönnek létre a ITSM termékben, vagy a konfigurációs elemek nem jönnek létre/nem kapcsolódnak munkaelemekhez vagy más általános információkhoz, tekintse meg a következő helyeket:
   -  ITSMC: a megoldás a kapcsolatok/munkaelemek/számítógépek összegzését jeleníti meg. Kattintson az **összekötő állapotát**megjelenítő csempére, amely a megfelelő lekérdezéssel végzett **Keresés naplózására**  kerül. További információkért tekintse meg a napló rekordjait LogType_S as HIBÁval.
   - **Naplók keresése** oldalon: közvetlenül a lekérdezési ServiceDeskLog_CL használatával tekintheti meg a hibákat/kapcsolódó információkat `*` `*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager webalkalmazás központi telepítésének hibáinak megoldása
1.  A webalkalmazások telepítésével kapcsolatos problémák esetén győződjön meg arról, hogy rendelkezik az erőforrások létrehozásához és üzembe helyezéséhez szükséges engedélyekkel az előfizetésben.
2.  Ha **"az objektum hivatkozása nem az objektum példányára"** hibaüzenet jelenik meg, akkor a [parancsfájl](itsmc-service-manager-script.md)futtatásakor ellenőrizze, hogy érvényes értékeket adott-e meg a **Felhasználó konfigurációja** szakaszban.
3.  Ha nem sikerül létrehozni a Service Bus Relay-névteret, győződjön meg arról, hogy a szükséges erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha nincs regisztrálva, manuálisan hozza létre a Service Bus Relay-névteret a Azure Portalból. Azt is létrehozhatja, miközben létrehozza [a hibrid kapcsolatokat](./itsmc-connections.md#configure-the-hybrid-connection) a Azure Portal.


## <a name="contact-us"></a>Kapcsolat

A IT-szolgáltatásmenedzsmenti csatoló kapcsolatos bármilyen lekérdezésért és Visszajelzésért lépjen kapcsolatba velünk a következő címen: [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>További lépések
[ITSM-termékek/-szolgáltatások hozzáadása a it-szolgáltatásmenedzsmenti csatolóhoz](./itsmc-connections.md).


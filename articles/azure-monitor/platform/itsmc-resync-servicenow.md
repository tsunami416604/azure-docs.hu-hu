---
title: ServiceNow-szinkronizálási problémák manuális javítása
description: Állítsa vissza a ServiceNow való kapcsolódást, hogy a riasztások Microsoft Azure újra meghívja a ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 9a6e46c0b52c22df0682034deaebd58bbfeb34a7
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2021
ms.locfileid: "98210069"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Hibaelhárítás az ITSM-összekötőben

Ez a cikk a ITSM-csatoló és a hibaelhárítással kapcsolatos gyakori problémákat ismerteti.

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. A riasztással kapcsolatos további információkért lásd: a Microsoft Azure riasztások áttekintése.
Az ügyfél kiválaszthatja, hogyan szeretnének értesítést kapni a riasztásról, függetlenül attól, hogy e-mailben, SMS-ben vagy webhookban, vagy akár egy megoldás automatizálásában is. Egy másik lehetőség, hogy értesítést kapjon a ITSM használatával.
A ITSM lehetővé teszi a riasztások küldését a külső jegyrendszer, például a ServiceNow számára.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Az incidens megjelenítése és elemzése és a kérelmekre vonatkozó adatváltozás

A kapcsolatok beállításakor a ITSMC legfeljebb 120 napos incidenst tud szinkronizálni, és módosíthatja a kérelmek adatait. Az adatok naplózási rekordjainak sémája a jelen cikk [További információk szakaszában](./itsmc-synced-data.md) található.

A ITSMC irányítópult használatával megjelenítheti az incidenst és módosíthatja a kérelmeket:

![A ITSMC irányítópultot megjelenítő képernyőkép.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Az irányítópult az összekötő állapotáról is tartalmaz információkat, amelyeket kiindulási pontként használhat a kapcsolatokkal kapcsolatos problémák elemzéséhez.

### <a name="error-investigation-using-the-dashboard"></a>Hibák vizsgálata az irányítópult használatával

Az irányítópulton található hibák megtekintéséhez kövesse a következő lépéseket:

1. Az **összes erőforrás** területen keresse meg a **ügyfélszolgálati (*a munkaterület neve*)**:

   ![A Azure Portal legutóbbi erőforrásait bemutató képernyőkép.](media/itsmc-definition/create-new-connection-from-resource.png)

2. A bal oldali ablaktábla **munkaterület-adatforrások** területén válassza a **ITSM-kapcsolatok** elemet:

   ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok menüpontot.](media/itsmc-overview/add-new-itsm-connection.png)

3. A bal oldali mező **Összegzés** területén **it-szolgáltatásmenedzsmenti csatoló** válassza az **Összefoglalás megtekintése** lehetőséget:

    ![A megtekintés összegzését bemutató képernyőkép.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. A bal oldali mező **Összegzés** területén **it-szolgáltatásmenedzsmenti csatoló** kattintson a gráfra:

    ![A diagramot bemutató képernyőkép.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Az irányítópult használatával áttekintheti az összekötő állapotát és hibáit.
    ![Az összekötő állapotát megjelenítő képernyőkép.](media/itsmc-resync-servicenow/connector-dashboard.png)

### <a name="dashboard-elements"></a>Irányítópult-elemek

Az irányítópult a ITSM eszközre a jelen összekötő használatával továbbított riasztásokkal kapcsolatos információkat tartalmaz.
Az irányítópult 4 részre oszlik:

1. Munkaelem létrehozva: a gráf és az alábbi táblázat a munkaelemek számát tartalmazza típus szerint. Ha a diagramra vagy a táblázatra kattint, további részleteket láthat a munkaelemekről.
    ![Képernyőkép, amely a létrehozott munkaelemet jeleníti meg.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Érintett számítógépek: a táblák tartalmazzák a konfigurációs elemeket létrehozó konfigurációs elemek részleteit.
    A táblázatok soraira kattintva további részleteket tudhat meg a konfigurációs elemekről.
    A tábla csak korlátozott számú sort tartalmaz, ha az összes listát látni szeretné, kattintson az összes megjelenítése lehetőségre.
    ![Az érintett számítógépeket bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Összekötő állapota: a gráf és az alábbi táblázat az összekötő állapotával kapcsolatos üzeneteket tartalmaz. A táblázat soraira kattintva további részleteket tudhat meg az összekötő állapota üzenetekről.
    A tábla csak korlátozott számú sort tartalmaz, ha az összes listát látni szeretné, kattintson az összes megjelenítése lehetőségre.
    ![Az összekötő állapotát megjelenítő képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Riasztási szabályok: a táblák tartalmazzák az észlelt riasztási szabályok számával kapcsolatos információkat.
    A táblák soraira kattintva további részleteket tudhat meg az észlelt szabályokról.
    A tábla csak korlátozott számú sort tartalmaz, ha az összes listát látni szeretné, kattintson az összes megjelenítése lehetőségre.
    ![A riasztási szabályokat bemutató képernyőkép.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

### <a name="service-map"></a>Szolgáltatás térképe

A Service Map érintett számítógépeken szinkronizált incidenseket is megjelenítheti.

Service Map automatikusan feltérképezi az alkalmazás összetevőit Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Lehetővé teszi, hogy a kiszolgálókat a következőképpen tekintheti meg: olyan összekapcsolt rendszerek, amelyek kritikus szolgáltatásokat biztosítanak. Service Map megjeleníti a kiszolgálók, a folyamatok és a portok közötti kapcsolatokat bármely TCP-kapcsolattal rendelkező architektúrán keresztül. Az ügynök telepítésén kívül nincs szükség konfigurációra. További információ: [a Service Map használata](../insights/service-map.md).

Ha Service Map használ, megtekintheti a ITSM-megoldásokban létrehozott ügyfélszolgálati elemeket az itt látható módon:

![Képernyőkép, amely a Log Analytics képernyőt jeleníti meg.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="troubleshoot-itsm-connections"></a>ITSM-kapcsolatok hibáinak megoldása

- Ha egy kapcsolat nem tud csatlakozni a ITSM rendszerhez, és hibaüzenet jelenik meg a **kapcsolati üzenet mentésekor** , hajtsa végre a következő lépéseket:
   - ServiceNow, Cherwell és elővance kapcsolatok esetén:  
     - Győződjön meg arról, hogy minden kapcsolathoz helyesen adta meg a felhasználónevet, a jelszót, az ügyfél-azonosítót és az ügyfél-titkot.  
     - Győződjön meg arról, hogy megfelelő jogosultságokkal rendelkezik a megfelelő ITSM-termékben a kapcsolódáshoz.  
   - Service Manager kapcsolatok esetén:  
     - Győződjön meg arról, hogy a webalkalmazás üzembe helyezése sikeres volt, és hogy a hibrid kapcsolat létrejött. Annak ellenőrzéséhez, hogy a kapcsolat sikeresen létrejött-e a helyszíni Service Manager számítógéppel, lépjen a webalkalmazás URL-címére a [hibrid kapcsolat](./itsmc-connections-scsm.md#configure-the-hybrid-connection)létrehozásához szükséges dokumentációban leírtak szerint.  

- Ha a ServiceNow származó adatok nem lettek szinkronizálva Log Analyticsre, győződjön meg arról, hogy a ServiceNow-példány nem alvó állapotú. A ServiceNow dev-példányok néha alvó állapotba kerülnek, ha hosszú ideje tétlenek. Ha ez nem történik meg, jelentse a problémát.
- Ha Log Analytics riasztások tüzet, de a munkaelemek nem jönnek létre a ITSM termékben, ha a konfigurációs elemek nem jönnek létre/nem kapcsolódnak munkaelemekhez vagy egyéb információkhoz, tekintse meg ezeket az erőforrásokat:
   -  ITSMC: a megoldás a kapcsolatok, a munkaelemek, a számítógépek és egyebek összegzését jeleníti meg. Válassza ki az **összekötő állapota** címkével ellátott csempét. Ekkor a **Keresés** a megfelelő lekérdezéssel történik. További információért tekintse meg a rekordokat `LogType_S` `ERROR` .
   - **Naplók keresése** oldalon: a hibákat és a kapcsolódó információkat közvetlenül a lekérdezés használatával tekintheti meg `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager webalkalmazás központi telepítésének hibáinak megoldása

-   Ha problémák merülnek fel a webalkalmazások üzembe helyezésével kapcsolatban, győződjön meg arról, hogy rendelkezik az előfizetés erőforrásainak létrehozásához/üzembe helyezéséhez szükséges engedélyekkel.
-   Ha a [parancsfájl](itsmc-service-manager-script.md)futtatásakor nem az objektumra **vonatkozó hiba példányára van beállítva** , akkor ellenőrizze, hogy érvényes értékeket adott-e meg a **Felhasználó konfigurációja** szakaszban.
-   Ha nem sikerül létrehoznia a Service Bus Relay-névteret, győződjön meg arról, hogy a szükséges erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha nincs regisztrálva, manuálisan hozza létre a Service Bus Relay-névteret a Azure Portalból. Azt is létrehozhatja, amikor [létrehozza a hibrid kapcsolatokat](./itsmc-connections-scsm.md#configure-the-hybrid-connection) a Azure Portalban.

### <a name="how-to-manually-fix-sync-problems"></a>Szinkronizálási problémák manuális javítása

Azure Monitor csatlakozhat a harmadik féltől származó IT-szolgáltatói (ITSM-) szolgáltatóhoz. A ServiceNow az egyik ilyen szolgáltató.

Biztonsági okokból előfordulhat, hogy frissítenie kell a ServiceNow-mel való kapcsolathoz használt hitelesítési tokent.
A következő szinkronizálási folyamat használatával aktiválja újra a kapcsolódást, és frissítse a jogkivonatot:


1. Keresse meg a megoldást a legnépszerűbb keresési szalagcímben, majd válassza ki a megfelelő megoldásokat.

    ![A legnépszerűbb keresési szalagcímet megjelenítő képernyőkép, ahol kiválaszthatja a megfelelő megoldásokat.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. A megoldás képernyőn válassza az összes kijelölése lehetőséget az előfizetés szűrőben, majd a szűrés "ügyfélszolgálati" értékre.

    ![Képernyőkép, amely bemutatja, hogy hol válassza az összes kijelölése és a szűrés ügyfélszolgálati alapján lehetőséget.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Válassza ki a ITSM-kapcsolatok megoldását.
1. Válassza a ITSM-kapcsolatok elemet a bal oldali szalagcímben.

    ![Képernyőkép, amely megjeleníti a ITSM-kapcsolatok kiválasztásának helyét.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Válassza ki az egyes összekötőket a listából. 
    1. A konfigurálásához kattintson az összekötő nevére
    1. A már nem használt összekötők törlése

    1. A mezők frissítése a partner típusa alapján a [következő definíciók](./itsmc-connections.md) szerint

    1. Kattintson a szinkronizálás elemre

       ![Képernyőfelvétel: a szinkronizálás gomb kiemelése.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Kattintson a Mentés gombra

        ![Új kapcsolat](media/itsmc-resync-servicenow/save-8bit.png)

f.    Tekintse át az értesítéseket, és ellenőrizze, hogy elindult-e a folyamat.

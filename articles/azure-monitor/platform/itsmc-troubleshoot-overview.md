---
title: Hibaelhárítás az ITSM-összekötőben
description: Hibaelhárítási problémák a IT-szolgáltatásmenedzsmenti csatoló
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 14f1056bf761eb7b591d04db34610468058bc255
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562852"
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

Az irányítópult vizsgálatával kapcsolatos további információkért lásd: [hibajelentés az irányítópult használatával](./itsmc-dashboard.md).

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

- Ha Log Analytics riasztások tüzet, de a munkaelemek nem jönnek létre a ITSM termékben, ha a konfigurációs elemek nem jönnek létre/nem kapcsolódnak munkaelemekhez vagy egyéb információkhoz, tekintse meg ezeket az erőforrásokat:
   -  ITSMC: a megoldás a kapcsolatok, a munkaelemek, a számítógépek és egyebek összegzését jeleníti meg. Válassza ki az **összekötő állapota** címkével ellátott csempét. Ekkor a **Keresés** a megfelelő lekérdezéssel történik. További információért tekintse meg a rekordokat `LogType_S` `ERROR` .
   - **Naplók keresése** oldalon: a hibákat és a kapcsolódó információkat közvetlenül a lekérdezés használatával tekintheti meg `*ServiceDeskLog_CL*` .

### <a name="troubleshoot-service-manager-web-app-deployment"></a>Service Manager webalkalmazás központi telepítésének hibáinak megoldása

-   Ha problémák merülnek fel a webalkalmazások üzembe helyezésével kapcsolatban, győződjön meg arról, hogy rendelkezik az előfizetés erőforrásainak létrehozásához/üzembe helyezéséhez szükséges engedélyekkel.
-   Ha a [parancsfájl](itsmc-service-manager-script.md)futtatásakor nem az objektumra **vonatkozó hiba példányára van beállítva** , akkor ellenőrizze, hogy érvényes értékeket adott-e meg a **Felhasználó konfigurációja** szakaszban.
-   Ha nem sikerül létrehoznia a Service Bus Relay-névteret, győződjön meg arról, hogy a szükséges erőforrás-szolgáltató regisztrálva van az előfizetésben. Ha nincs regisztrálva, manuálisan hozza létre a Service Bus Relay-névteret a Azure Portalból. Azt is létrehozhatja, amikor [létrehozza a hibrid kapcsolatokat](./itsmc-connections-scsm.md#configure-the-hybrid-connection) a Azure Portalban.
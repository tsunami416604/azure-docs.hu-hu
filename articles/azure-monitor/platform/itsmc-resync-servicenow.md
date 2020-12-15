---
title: ServiceNow-szinkronizálási problémák manuális javítása
description: Állítsa vissza a ServiceNow való kapcsolódást, hogy a riasztások Microsoft Azure újra meghívja a ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 3e836873219bde3836f2863e328b0b6f5b89addc
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507285"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Hibaelhárítás az ITSM-összekötőben

Ez a cikk a ITSM-csatoló és a hibaelhárítással kapcsolatos gyakori problémákat ismerteti.

Azure Monitor riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. A riasztással kapcsolatos további információkért lásd: a Microsoft Azure riasztások áttekintése.
Az ügyfél kiválaszthatja, hogyan szeretnének értesítést kapni a riasztásról, függetlenül attól, hogy e-mailben, SMS-ben vagy webhookban, vagy akár egy megoldás automatizálásában is. Egy másik lehetőség, hogy értesítést kapjon a ITSM használatával.
A ITSM lehetővé teszi a riasztások küldését a külső jegyrendszer, például a ServiceNow számára.

## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Az incidens megjelenítése és elemzése és a kérelmekre vonatkozó adatváltozás

A kapcsolatok beállításakor a ITSMC legfeljebb 120 napos incidenst tud szinkronizálni, és módosíthatja a kérelmek adatait. Az adatok naplózási rekordjainak sémája a jelen cikk [További információk szakaszában](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-overview#additional-information) található.

A ITSMC irányítópult használatával megjelenítheti az incidenst és módosíthatja a kérelmeket:

![A ITSMC irányítópultot megjelenítő képernyőkép.](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Az irányítópult az összekötő állapotáról is tartalmaz információkat, amelyeket kiindulási pontként használhat a kapcsolatokkal kapcsolatos problémák elemzéséhez.

A Service Map érintett számítógépeken szinkronizált incidenseket is megjelenítheti.

Service Map automatikusan feltérképezi az alkalmazás összetevőit Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Lehetővé teszi, hogy a kiszolgálókat a következőképpen tekintheti meg: olyan összekapcsolt rendszerek, amelyek kritikus szolgáltatásokat biztosítanak. Service Map megjeleníti a kiszolgálók, a folyamatok és a portok közötti kapcsolatokat bármely TCP-kapcsolattal rendelkező architektúrán keresztül. Az ügynök telepítésén kívül nincs szükség konfigurációra. További információ: [a Service Map használata](../insights/service-map.md).

Ha Service Map használ, megtekintheti a ITSM-megoldásokban létrehozott ügyfélszolgálati elemeket az itt látható módon:

![Képernyőkép, amely a Log Analytics képernyőt jeleníti meg.](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

## <a name="how-to-manually-fix-servicenow-sync-problems"></a>ServiceNow-szinkronizálási problémák manuális javítása

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

f.    Tekintse át az értesítéseket, és ellenőrizze, hogy a folyamat sikeresen befejeződött-e

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

## <a name="next-steps"></a>További lépések

További információ az [IT Service Management-kapcsolatokról](itsmc-connections.md)

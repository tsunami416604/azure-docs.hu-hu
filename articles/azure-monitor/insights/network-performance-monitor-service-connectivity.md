---
title: Hálózati teljesítményfigyelő megoldás szolgáltatáskapcsolata – Azure Log Analytics
description: A Hálózati teljesítményfigyelő Szolgáltatáskapcsolat-figyelő szolgáltatásával figyelheti a hálózati kapcsolatot bármely olyan végponthoz, amely nyitott TCP-porttal rendelkezik.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 93f3820b7cf1db85b9ff4cd514fe22efc75a90d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249242"
---
# <a name="service-connectivity-monitor"></a>Szolgáltatáskapcsolati monitor

A [Hálózati teljesítményfigyelő](network-performance-monitor.md) szolgáltatáskapcsolat-figyelő szolgáltatásával figyelheti a hálózati kapcsolatot bármely olyan végponthoz, amely nyitott TCP-porttal rendelkezik. Ilyen végpontok közé tartozik a webhelyek, SaaS-alkalmazások, PaaS-alkalmazások és SQL-adatbázisok. 

A Service Connectivity Monitor a következő funkciókat hajthatja végre: 

- Több fiókirodából vagy helyről figyelheti az alkalmazásokhoz és a hálózati szolgáltatásokhoz való hálózati kapcsolatot. Az alkalmazások és a hálózati szolgáltatások közé tartozik az Office 365, a Dynamics CRM, a belső üzletági alkalmazások és az SQL-adatbázisok.
- Beépített tesztek segítségével figyelheti az Office 365 és a Dynamics 365 végpontokkal való hálózati kapcsolatot. 
- Határozza meg a végponthoz való csatlakozás során tapasztalt válaszidőt, hálózati késést és csomagvesztést.
- Határozza meg, hogy az alkalmazások gyenge teljesítményét a hálózat vagy az alkalmazásszolgáltató megszűnésének valamilyen problémája miatt állapítsa meg.
- Azonosítsa azokat a forró pontokat a hálózaton, amelyek az alkalmazás gyenge teljesítményét okozhatják, ha megtekinti az egyes ugrások által a topológiatérképen megadott késést.


![Szolgáltatáskapcsolati monitor](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguráció 
A Hálózati teljesítményfigyelő konfigurációjának megnyitásához nyissa meg a [Hálózati teljesítményfigyelő megoldást,](network-performance-monitor.md) és válassza a **Konfigurálás**lehetőséget.

![A Network Performance Monitor konfigurálása](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Log Analytics-ügynökök konfigurálása figyeléshez
Engedélyezze a következő tűzfalszabályokat a figyeléshez használt csomópontokon, hogy a megoldás felderíthesse a topológiát a csomópontoktól a szolgáltatás végpontjáig: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Szolgáltatáskapcsolat-figyelő tesztek létrehozása 

Indítsa el a tesztek létrehozását a szolgáltatás végpontjaihoz való hálózati kapcsolat figyeléséhez.

1. Válassza a **Szolgáltatáskapcsolat figyelője** lapot.
2. Válassza **a Teszt hozzáadása**lehetőséget, és adja meg a teszt nevét és leírását. Munkaterületenként legfeljebb 450 tesztet hozhat létre. 
3. Válassza ki a teszt típusát:<br>

    * Válassza a **Web** lehetőséget a HTTP/S-kérésekre válaszoló szolgáltatás, például outlook.office365.com vagy bing.com kapcsolatának figyeléséhez.<br>
    * Válassza a **Hálózat** lehetőséget egy olyan szolgáltatás kapcsolatának figyeléséhez, amely válaszol a TCP-kérésekre, de nem válaszol a HTTP/S-kérésekre, például EGY SQL-kiszolgálóra, FTP-kiszolgálóra vagy SSH-portra. 
    * Például: Hozzon létre egy webes tesztet egy blob storage-fiók, válassza a **Web** lehetőséget, és adja meg a cél *a storageaccount*.blob.core.windows.net. Hasonlóképpen ezzel a [hivatkozással](https://docs.microsoft.com/azure/storage/common/storage-account-overview#storage-account-endpoints) teszteket hozhat létre más táblatároláshoz, várólista-tároláshoz és Azure-fájlokhoz.
4. Ha nem szeretne hálózati méréseket végezni, például hálózati késést, csomagvesztést és topológiafelderítést, törölje a jelet a **Hálózati mérések végrehajtása** jelölőnégyzetből. Tartsa kijelölve, hogy maximális hasznot húzhasson a képességből. 
5. A **Cél**mezőbe írja be azt az URL-címet/FQDN/IP-címet, amelyre figyelni szeretné a hálózati kapcsolatot.
6. A **Port szám**mezőbe írja be a célszolgáltatás portszámát. 
7. A **Teszt gyakorisága mezőbe**írja be a teszt futtatásának gyakoriságát. 
8. Válassza ki azokat a csomópontokat, amelyekről figyelni szeretné a hálózati kapcsolatot a szolgáltatáshoz. Győződjön meg arról, hogy a tesztenként hozzáadott megbízottak száma kevesebb, mint 150. Bármely ügynök legfeljebb 150 végpontot/ügynököt tesztelhet.

    >[!NOTE]
    > A Windows kiszolgálóalapú csomópontok esetében a képesség TCP-alapú kérelmeket használ a hálózati mérések végrehajtásához. Windows ügyfélalapú csomópontok esetén a képesség ICMP-alapú kéréseket használ a hálózati mérések végrehajtásához. Bizonyos esetekben a célalkalmazás blokkolja a bejövő ICMP-alapú kérelmeket, amikor a csomópontok Windows ügyfélalapúak. A megoldás nem képes hálózati méréseket végezni. Ilyen esetekben azt javasoljuk, hogy windowsos kiszolgálóalapú csomópontokat használjon. 

9. Ha nem szeretne állapoteseményeket létrehozni a kiválasztott elemekhez, törölje a jelet **az Állapotfigyelés engedélyezése jelölőnégyzetből a teszt által lefedett célokban.** 
10. Válassza ki a figyelési feltételeket. Egyéni küszöbértékeket állíthat be az állapot-esemény létrehozásához a küszöbértékek megadásával. Amikor a feltétel értéke meghaladja a kiválasztott hálózati vagy alhálózati párra vonatkozó kiválasztott küszöbértéket, egy állapotesemény jön létre. 
11. A konfiguráció mentéséhez válassza a **Mentés** gombot. 

    ![Szolgáltatáskapcsolat-figyelő tesztkonfigurációi](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Útmutatás 

Nyissa meg a Hálózati teljesítményfigyelő irányítópultnézetét. A létrehozott különböző tesztek állapotának összegzését a **Szolgáltatáskapcsolat figyelője** lapon kaphatja meg. 

![Szolgáltatáskapcsolat figyelője lap](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Válassza ki a csempét a tesztek részleteinek megtekintéséhez a **Tesztek** lapon. A bal oldali táblázatban megtekintheti a szolgáltatás válaszidejének, a hálózati késés és a csomagvesztés időszerinti állapotát és értékét az összes teszthez. A Hálózati állapotfigyelő vezérlővel a hálózati pillanatképet egy másik időpontban tekintheti meg a múltban. Jelölje ki a tesztet a táblázatban, amelyet meg szeretne vizsgálni. A jobb oldali ablaktábla diagramjain megtekintheti a veszteség, a késés és a válaszidő értékek előzménytrendjét. Válassza a **Teszt részletei** hivatkozást az egyes csomópontok teljesítményének megtekintéséhez.

![Szolgáltatáskapcsolat-figyelő tesztjei](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

A **Tesztcsomópontok** nézetben megfigyelheti a hálózati kapcsolatot az egyes csomópontokról. Válassza ki a teljesítménycsökkenést eredményező csomópontot. Ez az a csomópont, ahol az alkalmazás lassúfutásra kerül.

Határozza meg, hogy az alkalmazás gyenge teljesítménye a hálózat vagy az alkalmazásszolgáltató megszűnésének problémája miatt van-e, ha megfigyeli az alkalmazás válaszideje és a hálózati késés közötti korrelációt. 

* **Alkalmazással kapcsolatos probléma:** A válaszidő kiugrása, de a hálózati késés konzisztenciája azt sugallja, hogy a hálózat megfelelően működik, és a problémát az alkalmazás végén lévő probléma lehet. 

    ![Szolgáltatáskapcsolatfigyelő alkalmazásproblémával kapcsolatos problémája](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Hálózati probléma:** A válaszidő kiugrása a hálózati késés megfelelő csúcsával együtt arra utal, hogy a válaszidő növekedése a hálózati késés növekedésének oka lehet. 

    ![Szolgáltatáskapcsolat figyelőjének hálózati problémája](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Miután megállapította, hogy a probléma a hálózat miatt van, válassza a **Topológia** nézet hivatkozást a topológiatérkép problémás ugrásának azonosításához. Egy példa látható az alábbi képen. A csomópont és az alkalmazás végpontja közötti 105 ms-os teljes késés ből 96 ms a pirossal jelölt ugrás miatt van. Miután azonosította a problémás ugrást, korrekciós intézkedéseket tehet. 

![Szolgáltatáskapcsolat-figyelő tesztjei](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnosztika 

Ha rendellenességet észlel, kövesse az alábbi lépéseket:

* Ha a szolgáltatás válaszideje, a hálózati veszteség és a késés NA-ként jelenik meg, a következő okok lehetnek az okai:

    - Az alkalmazás nem áll meg.
    - A szolgáltatáshálózati kapcsolat ellenőrzésére használt csomópont nem működik.
    - A tesztkonfigurációban megadott cél helytelen.
    - A csomópont nem rendelkezik hálózati kapcsolattal.

* Ha érvényes szolgáltatásválasz-idő jelenik meg, de a hálózati veszteség és a késés NA-ként jelenik meg, a következő okok lehetnek az okai:

    - Ha a szolgáltatás hálózati kapcsolatának ellenőrzésére használt csomópont egy Windows-ügyfélszámítógép, vagy a célszolgáltatás blokkolja az ICMP-kérelmeket, vagy egy hálózati tűzfal blokkolja a csomópontból származó ICMP-kérelmeket.
    - A **Hálózati mérések végrehajtása** jelölőnégyzet üres a tesztkonfigurációban. 

* Ha a szolgáltatás válaszideje NA, de a hálózati veszteség és a késés érvényes, a célszolgáltatás nem lehet webalkalmazás. A tesztkonfiguráció szerkesztése, és a teszttípus kiválasztása **hálózat** helyett **web**. 

* Ha az alkalmazás lassú, állapítsa meg, hogy az alkalmazás gyenge teljesítményét a hálózat vagy az alkalmazásszolgáltató megszűnésének problémája miatt állapítsa meg.

## <a name="gcc-office-urls-for-us-government-customers"></a>GCC Office URL-ek az Egyesült Államok kormányának ügyfelei számára
Az Egyesült Államok kormányának Virginia régió, csak DOD URL-ek beépített NPM. A GCC URL-eket használó ügyfeleknek egyéni teszteket kell létrehozniuk, és minden egyes URL-t külön hozzá kell adniuk.

| Mező | GCC |
|:---   |:--- |
| Office 365 Portál és megosztott | portal.apps.mil |
| Office 365 hitelesítése és identitása | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www .office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| MS csapatok | gov.teams.microsoft.us | 

## <a name="next-steps"></a>További lépések
A részletes hálózati teljesítményadatrekordok megtekintéséhez [keressen naplókat.](../../azure-monitor/log-query/log-query-overview.md)

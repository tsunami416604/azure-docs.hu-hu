---
title: Network Performance Monitor megoldás az Azure Log Analyticsben |} A Microsoft Docs
description: A szolgáltatás Kapcsolatfigyelő funkció használata a Network Performance Monitor bármely végpont, amely egy nyitott TCP-port van hálózati kapcsolat figyeléséhez.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.component: ''
ms.openlocfilehash: 04e348d2b144bdb8fd626c52f990a560d76c08a2
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48044671"
---
# <a name="service-connectivity-monitor"></a>Szolgáltatás-összekapcsolhatósági monitor

A szolgáltatás Kapcsolatfigyelő funkciót is használhatja [Network Performance Monitor](log-analytics-network-performance-monitor.md) bármely végpont, amely egy nyitott TCP-port van hálózati kapcsolat figyeléséhez. Ilyen végpontok közé tartoznak a websites, a SaaS-alkalmazások, a PaaS-alkalmazások és az SQL-adatbázisok. 

A következő függvényeket, a szolgáltatás Kapcsolatfigyelő végezheti el: 

- A hálózati kapcsolat, az alkalmazások és a hálózati szolgáltatások fiókirodákban vagy a helyek figyelése. Alkalmazások és a hálózati szolgáltatások közé tartozik az Office 365, Dynamics CRM, belső – üzletági alkalmazások és SQL Database-adatbázisok.
- Használja az Office 365 és Dynamics 365-végpontokra irányuló hálózati kapcsolat figyeléséhez beépített tesztek. 
- Határozza meg a válaszidőt, a hálózati késés és a csomagveszteség tapasztalt a végponthoz való csatlakozáskor.
- Határozza meg, hogy gyenge teljesítményt miatt a hálózaton, vagy az alkalmazás szolgáltatója End néhány probléma miatt van-e.
- A hotspotok a hálózaton, amelyek gyenge teljesítményt okozza az egyes ugrások meg topológia százalékaránya késés megtekintésével azonosíthatja.


![Szolgáltatás-összekapcsolhatósági monitor](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguráció 
Nyissa meg a Network Performance Monitor konfigurációját, nyissa meg a [Network Performance Monitor megoldás](log-analytics-network-performance-monitor.md) válassza **konfigurálása**.

![A Network Performance Monitor konfigurálása](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Figyelés az Operations Management Suite-ügynökök konfigurálása
A következő tűzfalszabályokat, hogy a megoldás képes felderíteni a topológia a csomópontból a szolgáltatási végpont monitorozásra szolgáló csomóponton engedélyezése: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Szolgáltatás Kapcsolatfigyelő tesztek létrehozása 

A tesztek a szolgáltatásvégpontokra irányuló hálózati kapcsolat figyeléséhez létrehozásának megkezdéséhez.

1. Válassza ki a **szolgáltatás Kapcsolatfigyelő** fülre.
2. Válassza ki **hozzáadása tesztelése**, és adja meg a teszt neve és leírása. 
3. Válassza ki a teszt típusát:<br>

    * Válassza ki **webes** , amely válaszol a HTTP-vagy Https-kérelmekre, például outlook.office365.com vagy a bing.com szolgáltatás kapcsolatának figyelése.<br>
    * Válassza ki **hálózati** kapcsolat egy szolgáltatás, amely a TCP-kérelmekre reagál, de nem válaszol a HTTP-vagy Https-kérelmekre, például egy SQL server, az FTP-kiszolgáló vagy az SSH-port figyelésére. 
4. Ha nem szeretne végrehajtani a hálózati mérések, például a hálózati késés, a csomagvesztés és a topológia felderítése, törölje a jelet a **hálózati mérések végrehajtása** jelölőnégyzetet. Ez kiválasztva legnagyobb előny beolvasni a funkció a megtartása. 
5. A **cél**, adja meg, amelyhez a hálózati kapcsolat figyelni kívánt URL-cím vagy teljes Tartománynevét vagy IP-címet.
6. A **portszám**, adja meg a célként megadott szolgáltatás portszámát. 
7. A **vizsgálati gyakoriság**, adjon meg egy értéket, hogy milyen gyakran kívánja futtatni a vizsgálatot. 
8. Válassza ki a csomópontokat, ahonnan a szolgáltatás a hálózati kapcsolat figyeléséhez. 

    >[!NOTE]
    > A Windows server-alapú csomópontok esetében a funkció használatával a TCP-alapú kérelmeket a hálózati mérések végrehajtása. A Windows ügyfélalapú csomópontok esetében a funkció segítségével az ICMP-alapú kérelmeket a hálózati mérések végrehajtása. Bizonyos esetekben a célalkalmazás bejövő ICMP-alapú kérelmeket blokkol, amikor a csomópontok Windows ügyfél-alapú. A megoldás nem képes hálózati mérések végrehajtása. Azt javasoljuk, hogy ilyen esetekben használhatja a Windows server-alapú csomópontokat. 

9. Ha nem szeretné létrehozni az elemek hálózatállapot-események választja, törölje a jelet **egészségügyi figyelés engedélyezése a tárolók a teszt által kezelt**. 
10. Válassza ki a feltételek figyelése. Beállíthat egyedi küszöbértékeket health-események létrehozása a küszöbértékek megadásával. Minden alkalommal, amikor a feltétel értéke a megadott küszöbértéknél, a kiválasztott hálózat vagy alhálózat pár túllépik, állapottal kapcsolatos esemény jön létre. 
11. Válassza ki **mentése** a konfiguráció mentéséhez. 

    ![Szolgáltatás Kapcsolatfigyelő tesztelési konfigurációkat](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Útmutatás 

A Network Performance Monitor irányítópult nézet megnyitása. A létrehozott különböző vizsgálatok állapotának összegzését lekéréséhez tekintse meg a **szolgáltatás Kapcsolatfigyelő** lap. 

![Szolgáltatás Kapcsolatfigyelő lap](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Kattintson a csempére a teszt részleteinek megtekintéséhez a **tesztek** lapot. A bal oldalon található a táblázatban megtekintheti, időponthoz állapotát és a szolgáltatás válaszideje, hálózati késés és a vizsgálatok során a csomagvesztés értékét. A hálózat állapotát rögzítő vezérlő használatával megtekintheti a hálózati pillanatképet az elmúlt egy másik időpontban. Válassza ki a teszt a vizsgálni kívánt tábla. A jobb oldali panelen a diagramok és megtekintheti az adatveszteség, a késés és a válasz time típusú értékek előzményadatok trendjét. Válassza ki a **részletei** hivatkozásra kattintva ki minden csomópontból a teljesítményt.

![Szolgáltatás Kapcsolatfigyelő tesztek](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

Az a **Tesztcsomópontok** nézetben figyelheti, hogy az egyes csomópontok a hálózati kapcsolat. Válassza ki a csomópontot, a teljesítménycsökkenés rendelkezik. Ez az a csomópont, ahol az alkalmazás lassan fut, meg.

Megállapításához, hogy a gyenge alkalmazásteljesítményt a hálózaton vagy az alkalmazás szolgáltatója End probléma miatt betartásával, amelynek a korrelációját keressük a válaszidő az alkalmazás és a hálózati késést. 

* **Alkalmazással kapcsolatos problémát:** válaszidő ugrásszerű, de a hálózati késést a konzisztencia javasolja, hogy a hálózat megfelelően működik-e, és lehet, hogy a probléma a alkalmazás végén probléma miatt. 

    ![Szolgáltatás Kapcsolatfigyelő alkalmazással kapcsolatos problémát](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **A hálózati problémát:** válaszideje, amely a hálózati késés megfelelő ugrásszerű ugrásszerű javasolja, hogy a hálózati késés növekedése miatt lehet a válaszidő növelése. 

    ![Kapcsolatfigyelő szolgáltatás hálózati hiba](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Miután azt állapítja meg, hogy van-e a probléma miatt a hálózaton, válassza ki a **topológia** megtekintése hivatkozásra a feladatátvételnek Ugrás a topológia-térképként azonosításához. Az alábbi képen egy példa látható. A 105-teljes késése (ms) között a csomópont és az alkalmazás végpontjának, kívül 96 ms miatt a pirossal kiemelt Ugrás van. Miután azonosította a feladatátvételnek Ugrás, korrekciós műveleteket végezheti el. 

![Szolgáltatás Kapcsolatfigyelő tesztek](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnosztika 

Ha egy rendellenesség megfigyelte, kövesse az alábbi lépéseket:

* A szolgáltatás válaszideje, hálózati veszteségeket és késéseket NA jelennek meg, ha egy vagy több, a következő okok lehetnek a ok:

    - Az alkalmazás nem működik.
    - A hálózati kapcsolat és a szolgáltatás ellenőrzésére használt csomópont nem működik.
    - A cél a vizsgálati konfigurációjában megadott helytelen.
    - A csomópont nem rendelkezik hálózati kapcsolattal.

* Ha egy érvényes szolgáltatás válaszideje jelenik meg, de a hálózati veszteséget, valamint a késés NA jelennek meg, egy vagy több, a következő okok miatt is okozhatja:

    - Ha a hálózati kapcsolat és a szolgáltatás ellenőrzésére használt csomópontot egy Windows-ügyfélgép, vagy a célszolgáltatás blokkolja a kérelmeket az ICMP, vagy a hálózati tűzfal blokkolja a csomópont az ICMP-kérelmekkel.
    - A **hálózati mérések végrehajtása** négyzet nincs bejelölve, a vizsgálati konfigurációjában. 

* A szolgáltatás válaszideje NA, de a hálózati veszteséget, valamint a késés érvényesek, ha a célként megadott szolgáltatás nem feltétlenül egy webalkalmazást. A test-konfiguráció szerkesztése, és válassza ki a teszt típusát, **hálózati** helyett **webes**. 

* Ha az alkalmazás lassan fut, megállapítható, hogy van-e a gyenge alkalmazásteljesítményt a hálózaton vagy az alkalmazás szolgáltatója End probléma miatt.


## <a name="next-steps"></a>További lépések
[Naplók keresése](log-analytics-log-searches.md) részletes hálózati teljesítmény adatfelderítési rekordok megtekintéséhez.

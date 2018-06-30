---
title: Teljesítményfigyelő-megoldás az Azure Naplóelemzés hálózati |} Microsoft Docs
description: A hálózati Teljesítményfigyelőben a Service Manager-végpont funkció használatával figyelheti a tetszőleges végpontot, amely egy nyitott TCP-porttal rendelkezik hálózati kapcsolattal.
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
ms.component: na
ms.openlocfilehash: c260371043ddcb8a9ea5952760377282596fff7c
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127193"
---
# <a name="service-connectivity-monitor"></a>Szolgáltatás-összekapcsolhatósági monitor

A szolgáltatás kapcsolódási figyelő funkcióval a [hálózati Teljesítményfigyelő](log-analytics-network-performance-monitor.md) figyelése a tetszőleges végpontot, amely egy nyitott TCP-porttal rendelkezik hálózati kapcsolattal. Ilyen végpontok közé tartoznak a webhelyek, SaaS-alkalmazásokhoz, PaaS alkalmazások és SQL-adatbázisok. 

A szolgáltatás kapcsolódási figyelőjét a következő feladatokat végezheti el: 

- A hálózati kapcsolattal az alkalmazások és a hálózati szolgáltatások több fiókirodákban, vagy a helyek figyelése. Alkalmazások és hálózati szolgáltatások közé tartoznak az Office 365, a Dynamics CRM, a belső üzleti alkalmazások és az SQL-adatbázisok.
- Az Office 365 szolgáltatáshoz figyelőt hálózati kapcsolatot és Dynamics 365 végpontok beépített tesztek használata. 
- Határozza meg a válaszidőt, a hálózati késés és a csomagveszteség tapasztalt a végponthoz való kapcsolódás esetén.
- Megállapításához, hogy gyenge alkalmazásteljesítmény miatt a hálózati vagy az alkalmazás szolgáltatójának End valamilyen probléma miatt.
- A hálózaton, amelyek gyenge alkalmazásteljesítmény okozta a topológia-térképként egyes ugrások által közzétett várakozási megtekintésével interaktív területek azonosítása.


![Szolgáltatás-összekapcsolhatósági monitor](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguráció 
Hálózati Teljesítményfigyelő konfigurációs megnyitásához a [hálózati Teljesítményfigyelő megoldás](log-analytics-network-performance-monitor.md) válassza **konfigurálása**.

![Konfigurálja a hálózati teljesítmény figyelése](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Figyelés az Operations Management Suite-ügynökök konfigurálása
A csomópontokon, hogy a megoldás is feltérképezi a csomópontjairól a szolgáltatásvégpont figyeléséhez használható a következő tűzfalszabályok engedélyezése: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Szolgáltatás kapcsolódási figyelő tesztek létrehozása 

A tesztek figyelése szolgáltatásvégpontokra hálózati kapcsolat létrehozásához.

1. Válassza ki a **kapcsolat Szolgáltatásfigyelő** fülre.
2. Válassza ki **hozzáadása tesztelése**, és adja meg a teszt neve és leírása. 
3. Válassza ki a teszt típusát:<br>

    * Válassza ki **webes** kapcsolat egy szolgáltatás, amely válaszol a HTTP/S kérések, például outlook.office365.com vagy bing.com figyelésére.<br>
    * Válassza ki **hálózati** kapcsolat egy szolgáltatás, amely válaszol a TCP-kérésekre, de nem válaszol a HTTP/S-kérelmekre, például egy SQL server, az FTP-kiszolgáló vagy az SSH-port figyelésére. 
4. Ha nem szeretne végezni hálózati mérések, például a hálózati késés, csomagvesztés és topológia felderítése, törölje a jelet a **hajtsa végre a hálózati mérések** jelölőnégyzetet. Azt a maximális juttatás lekérése a funkció kijelölés megtartása. 
5. A **cél**, adja meg, amelyhez a hálózati kapcsolat figyelni kívánt URL-cím vagy teljes Tartománynevét vagy IP-címét.
6. A **portszámát**, adja meg a célként megadott szolgáltatás portszámát. 
7. A **vizsgálati gyakoriság**, adjon meg egy értéket milyen gyakran szeretné futtatni a vizsgálatot. 
8. Válassza ki a csomópontokat, amelyen a hálózati kapcsolattal szolgáltatásba figyelni kívánt. 

    >[!NOTE]
    > Windows server-alapú csomópontok a funkció TCP-alapú kérelmek végrehajtására használja a hálózati mérési. A Windows ügyfélalapú csomópontok a funkció ICMP-alapú kérelmek végrehajtására használja a hálózati mérési. Bizonyos esetekben a célalkalmazás bejövő ICMP-alapú kéréseket blokkolja, ha a csomópontok Windows ügyfél-alapú. A megoldás nem tudja végrehajtani a hálózati mérések. Azt javasoljuk, hogy ebben az esetben használhatja a Windows server-alapú csomópontok. 

9. Ha nem kívánja a cikkek állapotával kapcsolatos események létrehozása választja, törölje a jelet **állapotfigyelő figyelés engedélyezése a célok fedi le ez a vizsgálat**. 
10. Válassza ki a feltételek figyelése. Beállíthat egyéni küszöbökkel állapotesemény generációs küszöbértékek megadásával. A feltétel értéke a megadott küszöbértéknél, a kijelölt hálózati vagy alhálózat pár fölé megy, amikor a rendszerállapot esemény jön létre. 
11. Válassza ki **mentése** a konfiguráció mentéséhez. 

    ![Végpont-figyelő szolgáltatás teszt konfigurációk](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Útmutatás 

A hálózati teljesítményt figyelő irányítópult nézet megnyitása. Ahhoz, hogy a különböző tesztek létrehozott állapotának összegzését, tekintse meg a **kapcsolat Szolgáltatásfigyelő** lap. 

![Végpont-figyelő szolgáltatás lap](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

A csempe a vizsgálatok részletes adatainak megtekintéséhez válassza ki a **tesztek** lap. A bal oldali tábla megtekintheti a időpontban állapotának és az érték a szolgáltatás válaszidejének, a hálózati késés és a csomagveszteség az összes tesztre vonatkozóan. A hálózati állapotát rögzítő vezérlővel a hálózati pillanatkép megtekintése az elmúlt egy másik időpontban. Válassza ki a teszt a tábla, amelyet meg szeretne vizsgálni. A két diagramot, a jobb oldali ablaktáblán megtekintheti az adatvesztés, a késés és a válasz időértékek korábbi alakulását. Válassza ki a **részletei** minden csomópontjáról teljesítményadatainak megtekintése hivatkozásra.

![Végpont-figyelő szolgáltatás tesztek](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

Az a **teszt csomópontok** nézet, a hálózati kapcsolat minden csomópontjáról figyelheti. Jelölje ki a csomópontot, amely rendelkezik a teljesítmény romlását. Ez az a csomópont, ahol az alkalmazás követi lassú futnia kell.

Megállapításához, hogy gyenge alkalmazásteljesítmény miatt a hálózati vagy a kiadás az alkalmazás szolgáltatójának végén az alkalmazás a válaszidő és a hálózati késés közötti korreláció megfigyelésével. 

* **Alkalmazásproblémák:** egy csúcs az igények válaszideje, de a hálózati késés következetes javasolja, hogy a hálózat jól működik, és lehet, hogy a probléma a alkalmazás végén probléma miatt. 

    ![Alkalmazásproblémák szolgáltatási végpont-figyelő](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

* **A hálózati problémát:** egy csúcs az igények kíséri megfelelő csúcs, a hálózati késés válaszidő javasolja, hogy válaszidő megnövekedett hálózati késés növekedése miatt lehet. 

    ![Végpont-figyelő szolgáltatás hálózati probléma](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Miután eldöntötte, hogy van-e a probléma miatt a hálózaton, válassza ki a **topológia** megtekintése hivatkozásra a problémás Ugrás a topológia-térképként azonosításához. Példa az alábbi képen. 105-ms teljes késését a csomópont és az alkalmazás végpontjának között, kívül 96 ms miatt a pirossal kiemelt Ugrás van. Miután azonosította a problémás Ugrás, javítási műveletek is igénybe vehet. 

![Végpont-figyelő szolgáltatás tesztek](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnosztika 

Ha olyan rendellenességben, kövesse az alábbi lépéseket:

* Ha a szolgáltatás válaszidejének, hálózati veszteségeket és késéseket egyaránt megjelennek az helyeként NA, a következő okok legalább egyike okozhatják:

    - Az alkalmazás nem működik.
    - A hálózati kapcsolatot a szolgáltatás ellenőrzésére használt csomópont nem fut.
    - A konfigurálása a megadott cél érvénytelen.
    - A csomópont nem rendelkezik hálózati kapcsolattal.

* Ha jelenik meg egy érvényes szolgáltatás válaszidejének, de a hálózat veszteségeit, valamint a késés egyaránt megjelennek az helyeként NA, a következő okok legalább egyike lehet az oka:

    - Ha a hálózati kapcsolatot a szolgáltatás ellenőrzésére használt csomópont egy Windows-ügyfélszámítógép, vagy a célszolgáltatás ICMP-kéréseket blokkolja, vagy a hálózati tűzfal blokkolja az csomópontból ICMP-kérelmekkel.
    - A **hajtsa végre a hálózati mérések** négyzet nincs bejelölve, a teszt konfigurációját. 

* A szolgáltatás válaszidejének NA, de a hálózat veszteségeit, valamint a késés érvényesek, ha a célként megadott szolgáltatás nem feltétlenül egy webalkalmazást. A teszt konfigurációjának a szerkesztésével, és válassza a vizsgálati típus **hálózati** helyett **webes**. 

* Ha az alkalmazás lassú fut, megállapításához, hogy gyenge teljesítményének a hálózaton vagy az alkalmazás szolgáltatójának végén egy probléma miatt.


## <a name="next-steps"></a>További lépések
[Naplók keresése](log-analytics-log-searches.md) részletes hálózati teljesítmény rekordok megtekintéséhez.

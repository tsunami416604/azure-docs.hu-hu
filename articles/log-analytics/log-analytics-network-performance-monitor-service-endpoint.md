---
title: "Teljesítményfigyelő-megoldás az Azure Naplóelemzés hálózati |} Microsoft Docs"
description: "A Service Manager-végpont funkció hálózati Teljesítményfigyelőben tetszőleges végpontot, amely egy nyitott TCP-porttal rendelkezik hálózati kapcsolattal figyelését teszi lehetővé."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: ba19a4fc24668bff27c961b5b415f840d1132a34
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="service-endpoint-monitor"></a>Szolgáltatási végpont-figyelő

A végpont-figyelő szolgáltatás képesség [hálózati Teljesítményfigyelő](log-analytics-network-performance-monitor.md) lehetővé teszi a figyelheti a tetszőleges végpontot, amely egy nyitott TCP-porttal rendelkezik hálózati kapcsolattal. Ilyen végpontok közé tartoznak a webhelyek, SaaS-alkalmazásokhoz, PaaS alkalmazások és SQL-adatbázisok. 

A következő feladatokat végezheti el **végpont-figyelő szolgáltatás**: 

- Figyelő az alkalmazások és a hálózati szolgáltatások (például az Office 365, a Dynamics CRM belső üzletági üzleti alkalmazások, az SQL-adatbázis, a stb) a hálózati kapcsolat több helyről fiókirodai irodák / 
- Beépített tesztek Office365 és Dynamics365 végpontok hálózati kapcsolattal figyelése 
- A válaszidőt, hálózati késés, akkor tapasztaltak, amikor a végponthoz való kapcsolódás csomagvesztés meghatározása 
- Megállapításához, hogy gyenge alkalmazásteljesítmény miatt a hálózati vagy az alkalmazás szolgáltatójának végén valamilyen probléma miatt 
- A hálózaton, amelyek gyenge alkalmazásteljesítmény okozza a topológia-térképként egyes ugrások által közzétett várakozási megtekintésével interaktív területek azonosítása. 


![Szolgáltatási végpont-figyelő](media/log-analytics-network-performance-monitor/service-endpoint-intro.png)


## <a name="configuration"></a>Konfiguráció 
A hálózati teljesítményt figyelő konfigurációs megnyitásához a [hálózati Teljesítményfigyelő megoldás](log-analytics-network-performance-monitor.md) , és kattintson a **konfigurálása** gombra.

![Konfigurálja a hálózati teljesítmény figyelése](media/log-analytics-network-performance-monitor/npm-configure-button.png)


### <a name="configure-oms-agents-for-the-monitoring"></a>A figyelés konfigurálása OMS-ügynököt.  
A csomópontokon, hogy a megoldás is feltérképezi a csomópontjairól a szolgáltatásvégpont figyeléséhez használható a következő tűzfalszabályok engedélyezése: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action 
```

### <a name="create-service-endpoint-monitor-tests"></a>Végpont-figyelő szolgáltatás tesztek létrehozása 

A hálózati kapcsolat szolgáltatásvégpontokra figyelése tesztek létrehozásának megkezdése 

1. Kattintson a **végpont-figyelő szolgáltatás** fülre.
2. Kattintson a **teszt hozzáadása** , és adja meg a teszt nevét és leírását. 
3. Válassza ki a teszt típusát:<br>Válassza ki **webteszt** Ha egy szolgáltatás, amely válaszol a HTTP/S kérések, például outlook.office365.com, bing.com kapcsolat figyeli.<br>Válassza ki **hálózati tesztek** Ha egy szolgáltatás, amely válaszol-e a TCP-kérelmet, de nem válaszol a HTTP/S kérelem, például az SQL Server kiszolgálóval, és figyeli a FTP-kiszolgáló SSH port stb. 
4. Ha nem szeretné végrehajtani a hálózati mérések (hálózati késés, csomagvesztés, topológia felderítése), majd törölje a jelet a szövegmezőben. Azt javasoljuk, hogy folyamatosan maximális juttatás lekérése a funkció be van jelölve. 
5. Adja meg, amelyhez a hálózati kapcsolat figyelni kívánt URL-cím vagy teljes Tartománynevét vagy IP-célcím.  
6. Adja meg a célként megadott szolgáltatás portszámát. 
7. Adja meg a tesztet futtatni kívánt gyakoriságát. 
8. Válassza ki a csomópontokat, amelyen a hálózati kapcsolattal szolgáltatásba figyelni kívánt. 

    >[!NOTE]
    > Windows server-alapú csomópontok a funkció TCP-alapú kérelmek végrehajtására használja a hálózati mérési. A Windows ügyfélalapú csomópontok a funkció ICMP-alapú kérelmek végrehajtására használja a hálózati mérési. Bizonyos esetekben a célalkalmazás blokkolja a bejövő ICMP-alapú kérelem miatt, amelyek a csomópontok Windows ügyfél-alapú, ha a megoldás nem tudja elvégezni hálózati mérések. Ezért javasoljuk, ebben az esetben használja a Windows server-alapú csomópontok. 

9. Ha nem kívánja a kijelölt, majd elemeinek állapotával kapcsolatos események törlése létrehozása **engedélyezése a rendszerállapot figyelése a célokat, a teszt által kezelt**. 
10. Válassza ki a feltételek figyelése. Adhatja meg egyéni küszöbökkel állapotát az eseménygenerálás írja be a küszöbértékekhez. A feltétel értéke a megadott küszöbértéknél, a kijelölt hálózati/alhálózat pár fölé megy, amikor a rendszerállapot esemény jön létre. 
11. Kattintson a **mentése** a konfiguráció mentéséhez. 

 ![Szolgáltatási végpont figyelési konfiguráció](media/log-analytics-network-performance-monitor/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Útmutatás 

Helyezze át a hálózati figyelő irányítópult-nézet, és tekintse meg a **végpont-figyelő szolgáltatás** lapot a különböző tesztek létrehozott állapotának összegzését.  

![Szolgáltatási végpont figyelő lap](media/log-analytics-network-performance-monitor/service-endpoint-blade.png)

Kattintson a csempére kattintva részletezése a és a vizsgálatok adatainak megtekintése a **tesztek** lap. Az LHS táblán megtekintheti a időpontban állapotának és az érték a szolgáltatás válaszidejének, a hálózati késés és a csomagveszteség az összes tesztre vonatkozóan. A hálózat állapotát rögzítő vezérlő segítségével megtekintheti a hálózati pillanatkép más időt a részhez. Kattintson a vizsgálat a vizsgálni kívánt táblában. A jobb oldal panelen megtekintheti az adatvesztés, a késés és a válasz idő értékei a diagramok a korábbi trendjét. Kattintson a részletei hivatkozásra kattintva megtekintheti az egyes csomópontok teljesítményét. 

![Szolgáltatási végpont figyelő tesztek](media/log-analytics-network-performance-monitor/service-endpoint-tests.png)

Az a **teszt csomópontok** nézet, a hálózati kapcsolat minden csomópontjáról figyelheti. Kattintson a csomóponton, amelyek teljesítménycsökkenést.  Ez azért, ahol az alkalmazás követi lassú futnia kell a csomópontot. 

Megállapításához, hogy gyenge alkalmazásteljesítmény miatt a hálózati vagy az alkalmazás szolgáltatójának végén valamilyen probléma miatt a korrelációs között az alkalmazás a válaszidő és a hálózati késés - megfigyelésével 

**Alkalmazásproblémák:** ha van egy csúcs válaszidő, de a hálózati késés konzisztens, akkor ez azt sugallja, hogy jól működik a hálózati és a probléma a alkalmazás végén probléma miatt.  

![Szolgáltatási végpont figyelő alkalmazás probléma](media/log-analytics-network-performance-monitor/service-endpoint-application-issue.png)

**A hálózati problémát:** Ha egy csúcs az igények válaszideje a a hálózati késés a megfelelő csúcs együtt, akkor ez arra utalnak, hogy válaszidő megnövekedett hálózati késés növekedése miatt.  

![Szolgáltatási végpont figyelő hálózati probléma](media/log-analytics-network-performance-monitor/service-endpoint-network-issue.png)

Miután meghatározta, hogy van-e a probléma miatt a hálózatra, kattintson a **topológia** megtekintése hivatkozásra a problémás Ugrás a topológia-térképként azonosításához. Például az alábbi ábrán látható, hogy kívül a 105 ms teljes késést a csomópont és az alkalmazás végpontjának között, 96 ms miatt a pirossal kiemelt Ugrás. A problémás Ugrás állapította meg, miután javítási műveletek is igénybe vehet.  

![Szolgáltatási végpont figyelő tesztek](media/log-analytics-network-performance-monitor/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnosztika 

Ha olyan rendellenességben, majd kövesse az alábbi lépéseket:

Ha a szolgáltatás válaszidejének hálózati veszteségeket és késéseket jelennek meg NA, akkor azok a következő okok legalább egyike miatt:
- Az alkalmazás nem működik.
- A szolgáltatás hálózati kapcsolat ellenőrzése használja a csomópont nem fut.
- A konfigurálása a megadott cél érvénytelen.
- A csomópont nem rendelkezik hálózati kapcsolattal.

Ha egy érvényes szolgáltatás válaszidejének látható, de hálózat veszteségeit, valamint a késés egyaránt megjelennek az helyeként NA, majd azok a következő okok legalább egyike miatt:
- Ha a hálózati kapcsolatot a szolgáltatás ellenőrzésére használt csomópont Windows-ügyfélszámítógép, vagy a célszolgáltatás ICMP-kéréseket blokkolja, vagy a hálózati tűzfal blokkolja az csomópontból származó ICMP-kéréseket.
- Jelölőnégyzetét **hajtsa végre a hálózati mérések** el lett távolítva a teszt konfigurációját. 

Ha a szolgáltatás válaszidejének NA, de a hálózat veszteségeit, valamint a késés érvényesek, majd az arra utalnak, hogy a célként megadott szolgáltatás nem egy webalkalmazást. A teszt konfigurációjának a szerkesztésével, és válassza ki a teszt, hálózati teszt a webalkalmazás-teszt helyett. 

Ha az alkalmazás lassú fut, akkor ellenőrizze, hogy gyenge alkalmazásteljesítmény miatt a hálózati vagy az alkalmazás szolgáltatójának végén valamilyen probléma miatt.


## <a name="next-steps"></a>További lépések
* [Naplók keresése](log-analytics-log-searches.md) részletes hálózati teljesítmény rekordok megtekintéséhez.

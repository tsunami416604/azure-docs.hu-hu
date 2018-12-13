---
title: Teljesítmény figyelése a szolgáltatás a Network Performance Monitor megoldás az Azure Log Analyticsben |} A Microsoft Docs
description: A Teljesítményfigyelő funkció a Network Performance Monitor segítséget nyújt a hálózaton lévő különböző pontok hálózati kapcsolatait figyelheti. Figyelemmel kísérheti a magánfelhők számára, és a helyszíni helyeken, több adatközpontok és a fiókirodákban, és a többrétegű alkalmazások üzleti szempontból kritikus vagy mikroszolgáltatás-alapú.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: de9e6d5c8c6b4dc9652ae64c9a4fd0e38d7a7b87
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184763"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Hálózati Teljesítményfigyelő megoldás: Teljesítményfigyelés

A Teljesítményfigyelő képesség [Network Performance Monitor](network-performance-monitor.md) segít nyomon, hogy a hálózat különböző pontok közötti hálózati kapcsolat. Figyelemmel kísérheti a magánfelhők számára, és a helyszíni helyeken, több adatközpontok és a fiókirodákban, és a többrétegű alkalmazások üzleti szempontból kritikus vagy mikroszolgáltatás-alapú. A Teljesítményfigyelő képes észlelni a hálózati problémákat, mielőtt felhasználói panaszok. Fő előnye, hogy a következők: 

- Figyelheti a veszteséget és késéseket különböző alhálózatai között, és riasztásokat állíthat be.
- A hálózaton (beleértve a redundáns elérési utak) összes útvonalának figyelése.
- Végezzen hibaelhárítást a átmeneti és időponthoz – hálózati problémákat, amelyeket nehéz replikálni.
- A hálózat, amely teljesítménycsökkenésért felelős konkrét szegmensét határozza meg.
- Az SNMP protokollt kellene nélkül, a hálózat állapotának figyelése.


![Hálózati teljesítményfigyelő](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguráció
Nyissa meg a Network Performance Monitor konfigurációját, nyissa meg a [Network Performance Monitor megoldás](network-performance-monitor.md), és válassza ki **konfigurálása**.

![A Network Performance Monitor konfigurálása](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Új hálózatok létrehozása

A Network Performance Monitor egy hálózati az alhálózatok logikai tárolója. Ez segít a figyelést a hálózati infrastruktúra, igény szerint rendezheti. Hálózat létrehozása egy rövid nevet, és adjon hozzá alhálózatokat, hogy az üzleti logika alapján. Például hozzon létre egy hálózati London nevű, és adja hozzá az összes alhálózatot az adatközpontbeli London. Vagy létrehozhat egy hálózati nevű *ContosoFrontEnd* , és adja hozzá a hálózat összes alhálózatot Contoso nevű, az alkalmazás előtérrendszerét szolgálnak. A megoldás automatikusan létrehoz egy alapértelmezett hálózat, amely tartalmazza a környezetben észlelt összes alhálózatot. 

Minden alkalommal, amikor létrehoz egy hálózatot, adjon hozzá egy alhálózatot rá. Ezután az alhálózatban az alapértelmezett hálózat törlődik. Ha töröl egy hálózatot, összes alhálózatot automatikusan visszatér az alapértelmezett hálózat. Az alapértelmezett hálózat összes alhálózatot, amely nem található olyan felhasználó által definiált hálózati tárolójaként szolgál. Nem szerkeszthetők, és az alapértelmezett hálózat törlése. Mindig marad a rendszerben. Tetszőleges számú egyéni hálózatok igény szerint hozhat létre. A legtöbb esetben az alhálózatok a szervezetben egynél több hálózaton vannak elrendezve. Hozzon létre egy vagy több hálózatok alhálózatokat, az üzleti logikát a csoporthoz.

Új-hálózat létrehozása:


1. Válassza ki a **hálózatok** fülre.
1. Válassza ki **Hozzáadás hálózati**, majd adja meg a hálózat nevét és leírását. 
2. Egy vagy több alhálózatot, majd válassza ki és **Hozzáadás**. 
3. Válassza ki **mentése** a konfiguráció mentéséhez. 


### <a name="create-monitoring-rules"></a>Figyelési szabály létrehozása 

Teljesítményfigyelő állít elő, ha a hálózati kapcsolatok két alhálózatot vagy két hálózat között, a teljesítmény-küszöbértékének hálózatállapot-események. A rendszer automatikusan további ezeket a küszöbértékeket. Egyedi küszöbértékeket is megadhat. A rendszer automatikusan létrehoz egy alapértelmezett szabályt, amely állít elő, amikor veszteség vagy késés hálózat vagy alhálózat bármely párjai közötti hivatkozások megszegése állapottal kapcsolatos esemény a rendszer megtanult küszöbértéket. Ez az eljárás biztosítja a megoldás a hálózati infrastruktúra monitorozása, amíg explicit módon figyelési szabályokat hozott. Ha engedélyezve van az alapértelmezett szabályt, minden csomóponton szintetikus tranzakciók küldeni a többi csomópont, amely engedélyezve van a megfigyeléshez. Az alapértelmezett szabály hasznos kis hálózatokkal. Ilyen például, egy forgatókönyvet, ahol kis számú mikroszolgáltatások futtató kiszolgálók rendelkezik, és győződjön meg arról, hogy az összes kiszolgáló rendelkezik-e egymással való kapcsolatot szeretne.

>[!NOTE]
> Azt javasoljuk, hogy letiltja az alapértelmezett szabályt, és hozzon létre egyéni monitorozási szabályt, különösen olyan nagyobb hálózatok, ahol nagy számú csomópont használ a figyelést. Figyelési szabályok egyéni csökkentheti a megoldás és a Súgó, ahogy az a hálózat figyelése által generált forgalmat.

Az üzleti logika alapján figyelési szabályokat létrehozni. Ilyen például, hogy szeretné-e a hálózati kapcsolat a központtal két office webhelyek teljesítményének figyeléséhez. Csoport office site1 O1 hálózatban lévő összes alhálózathoz. Majd csoportosítson office site2 O2 hálózatban lévő összes alhálózathoz. Végül csoportban az összes alhálózatot a hálózati H. két figyelési szabályok létrehozása – egy O1 és óra között, és egy másik O2 és H. között az központjában 

Egyéni monitorozási szabályok létrehozásához:

1. Válassza ki **szabály hozzáadása** a a **figyelő** lapra, és adja meg a szabály nevét és leírását.
2. Válassza ki a két hálózat vagy alhálózat hivatkozások a listákból figyeléséhez. 
3. Válassza ki, amely tartalmazza a hálózati legördülő listából válassza ki a kívánt azoknak az alhálózatoknak a hálózaton. Ezután válassza ki azoknak az alhálózatoknak a megfelelő alhálózati legördülő listából. Ha egy hálózati kapcsolat az összes alhálózat figyelni szeretne, válassza ki **összes alhálózat**. Hasonlóképpen válassza ki azt szeretné, a más alhálózatokra. Válassza ki a kizárandó meg az adott alhálózati hivatkozások a kiválasztott beállítások figyelése, **kivétel hozzáadása**. 
4. ICMP és a TCP protokollt szintetikus tranzakciók végrehajtásához választhat. 
5. Ha nem szeretné létrehozni az elemek hálózatállapot-események választotta, törölje a jelet **egészségügyi figyelés engedélyezése a hivatkozások a szabály által kezelt**. 
6. Válassza ki a feltételek figyelése. Health-esemény létrehozása egyéni küszöbértékek beállításához adja meg a küszöbérték. Ha a feltétel értéke meghaladja a megadott küszöbértéknél, a kiválasztott hálózat vagy alhálózat pár, állapottal kapcsolatos esemény jön létre. 
7. Válassza ki **mentése** a konfiguráció mentéséhez. 

Miután menti egy figyelési szabályt, is integrálhatja a szabályhoz a Riasztáskezelési kiválasztásával **riasztás létrehozása**. Riasztási szabály automatikusan létrejön a keresési lekérdezéssel. Egyéb szükséges paramétereket automatikusan kitölti. Egy riasztási szabályt használ, fogadhat e-mail alapú értesítések mellett a Network Performance Monitor meglévő riasztásokra. A riasztások is küldhetnek runbookokkal végrehajtott helyreigazító műveletekre, illetve azok integrálható a meglévő szolgáltatás-felügyeleti megoldások webhookok használatával. Válassza ki **riasztás kezelése** a riasztási beállítások konfigurálása. 

Most már Performance Monitor további szabályokat hozhat létre, vagy helyezze át a megoldás irányítópultjának a funkció használatához.

### <a name="choose-the-protocol"></a>A protokoll kiválasztása

A Network Performance Monitor szintetikus tranzakciók használja például a csomag szolgáltatáskimaradást és -kapcsolat késési hálózati teljesítmény-mérőszámok kiszámításához. A fogalom jobb megértéséhez, fontolja meg a Network Performance Monitor ügynök kapcsolódik a hálózati kapcsolat egyik végén. A Network Performance Monitor ügynök mintavételi csomagok küld egy második a hálózat egy másik végén csatlakozik a Network Performance Monitor-ügynököt. A második ügynök válaszok válasz csomagokkal. Ez a folyamat ismétlődik néhány alkalommal. Megvizsgálja, hogy hány válaszok és minden válasz fogadásához szükséges idő, az első a Network Performance Monitor ügynök kapcsolati késés értékeli, és nyújtása. 

A formátum, méretének és sorozata, ezek a csomagok a protokoll, amelyet úgy dönt, hogy figyelési szabályok létrehozásakor határozza meg. A csomagok által használt protokoll alapján, a közbenső hálózati eszközök, például az útválasztók és kapcsolók, előfordulhat, hogy fel ezek a csomagok eltérően. Ebből következően protokoll tetszőleges hatással van az eredmények pontosságát. Protokoll tetszőleges azt is meghatározza, hogy végre kell hajtania bármely manuális lépéseket a Network Performance Monitor megoldás üzembe helyezése után. 

A Network Performance Monitor biztosít a szintetikus tranzakciók végrehajtásához az ICMP és a TCP protokoll közötti választás. Ha ICMP szintetikus tranzakció szabály létrehozásakor, a Network Performance Monitor-ügynökök az ICMP ECHO-üzenetek használatával számítsa ki a hálózati késés és a csomagveszteség. Az ICMP ECHO használja ugyanazt az üzenetet a hagyományos ping eszköz által küldött. A TCP protokoll használatakor a Network Performance Monitor ügynökök TCP SZIN csomagok küldése a hálózaton keresztül. Ebben a lépésben egy TCP-kézfogás befejezési követi, és a kapcsolatot eltávolította ÜZE csomagok használatával. 

Mielőtt egy protokollt választja, vegye figyelembe a következőket: 

* **Több hálózati útvonal felderítését.** TCP pontosabb, amikor több útvonal felderítése, és szüksége van mindegyik olyan alhálózatban, kevesebb ügynököt. Például egy vagy két olyan ügynökök, amelyek TCP felfedezheti az alhálózatok közötti összes redundáns elérési utat. Szüksége van több ügynökök, amelyek használják az ICMP hasonló eredmény elérése érdekében. ICMP Használatát, akkor használja, ha a két alhálózat közötti útvonalak száma, többre van szüksége, mint a forrás vagy cél alhálózat 5N ügynökök.

* **Az eredmények pontosságát.** Az útválasztók és kapcsolók általában az ICMP ECHO-csomagok TCP csomagok képest alacsonyabb prioritású hozzárendelése. Bizonyos helyzetekben amikor a hálózati eszközök vannak terhelve, a TCP jobban kapott megjelennek-e az adatvesztéssel és -alkalmazások által tapasztalt késés. Ennek oka az, hogy az alkalmazás forgalmának a legtöbb TCP protokollon keresztül zajlik. Ezekben az esetekben az ICMP TCP képest kevésbé pontos eredményeket biztosít. 

* **Tűzfal-konfiguráció.** A TCP protokoll szükséges, hogy a TCP-csomagokat a célport kapnak. A Network Performance Monitor-ügynökök által használt alapértelmezett port 8084. A port módosítható, ha az ügynökök konfigurálása. Győződjön meg arról, hogy a tűzfalak vagy hálózati biztonsági csoport (NSG) szabályai (az Azure-ban) engedélyezik-e a forgalmat a porton. Szükség is győződjön meg arról, hogy azokon a számítógépeken, amelyeken telepítve vannak-e az ügynökök a helyi tűzfal engedélyezi a forgalmat az ezen a porton van konfigurálva. PowerShell-parancsfájlok segítségével tűzfalszabályok konfigurálása a Windows rendszerű számítógépeken, de kell manuálisan konfigurálnia a hálózati tűzfal. Ezzel szemben az ICMP-port használatával nem működik. A legtöbb nagyvállalati forgatókönyvekben ICMP-forgalom számára engedélyezett a tűzfalon keresztül lehetővé teszik, hogy hálózati diagnosztikai eszközök, mint például a ping segédprogramot. Ha egy gép egy másik pingelhető, a tűzfal kézi konfigurálása nélkül használhatja az ICMP protokollt.

>[!NOTE] 
> Néhány tűzfalak blokkolhatják ICMP Használatát, amely, amely a biztonsági információk és az esemény a felügyeleti rendszer események nagy számú eredményez újraküldési vezethet. Ellenőrizze, hogy az Ön által választott protokoll NSG-t vagy hálózati tűzfal nem blokkolja-e. Ellenkező esetben a Network Performance Monitor nem figyelhető hálózati szegmenst. Azt javasoljuk, hogy a TCP figyelési használjon. Használja az ICMP forgatókönyvekben, ahol nem használható a TCP, például ha: 
>
> - Windows-alapú ügyfél csomópontok esetén használható, mert a nyers TCP-szoftvercsatornák a Windows-ügyfelek számára nem engedélyezett.
> - A hálózati tűzfal vagy az NSG blokkolja a TCP.
> - Nem tudom, hogyan váltson a protokollt.

Ha üzembe helyezés során az ICMP használatát választja, átválthat a TCP bármikor azt az alapértelmezett szabály szerkesztésével.

1. Lépjen a **hálózati teljesítményt** > **figyelő** > **konfigurálása**   >  **Figyelő**. Válassza ki **alapértelmezett szabály**. 
2. Görgessen a **protokoll** szakaszt, és válassza ki a használni kívánt protokollt. 
3. Válassza ki **mentése** beállítás alkalmazása. 

Akkor is, ha az alapértelmezett szabályt egy adott protokollt használja, létrehozhat új szabályokat eltérő protokollal rendelkező. Akkor is létrehozhat szabályokat, ha egyes szabályok használata ICMP és mások TCP Protokollt használ vegyesen. 

## <a name="walkthrough"></a>Útmutatás 

Most nézzük meg állapottal kapcsolatos esemény alapvető oka egyszerű vizsgálatát.

A megoldás irányítópultján állapottal kapcsolatos esemény látható, hogy egy hálózati kapcsolat nem kifogástalan. A probléma vizsgálatához válassza ki a **hálózati hivatkozásokat tartalmaz az éppen figyelt** csempére.

A részletezés oldal azt mutatja, hogy a **DMZ2-DMZ1** hálózati kapcsolat nem kifogástalan. Válassza ki **alhálózati hivatkozások megtekintése** a hálózati kapcsolathoz. 


A részletezés oldal bemutatja az alhálózati kapcsolatok a **DMZ2-DMZ1** hálózati kapcsolat. Mindkét alhálózati hivatkozások a várakozási ideje túllépte a küszöbértéket, ami lehetővé teszi a hálózati kapcsolat nem kifogástalan. Mindkét alhálózati kapcsolatok késés Trendy is láthatja. Használja a gráf összpontosíthat a szükséges időtartomány szabályozhatja az időbeállítást. Láthatja, hogy a késés elérésekor a maximális napi időpontot. Keresse meg a naplókat később ebben az időszakban a probléma kivizsgálására. Válassza ki **csomóponti hivatkozások megtekintése** további részletesen elemezheti. 
 
 ![Alhálózati hivatkozások lap](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Hasonló az előző oldalra, a részletezés oldal, az adott alhálózat-hivatkozás felsorolja a különböző csomóponti hivatkozások. Hasonló műveletek végezhetők el itt, ahogy az előző lépésben tette. Válassza ki **topológia megtekintése** a két csomópont közötti topológia megtekintéséhez. 
 
 ![Csomópont hivatkozások lap](media/network-performance-monitor-performance-monitor/node-links.png) 

A két kijelölt csomópont közötti összes elérési utat a topológia térkép is megjelennek. A Ugrás ugrásra felépülő topológiát a topológia-térképként két csomópont közötti útvonalak jelenítheti meg. Biztosít egy világos képet hány útvonalak léteznek között, illetve a két csomópont elérési utak adatcsomagok igénybe vehet. Hálózati teljesítmény szűk vörös színnel jelennek meg. Hibás hálózati kapcsolat vagy egy hibás hálózati eszköz megkereséséhez tekintse meg a piros elemeket a topológia-térképként. 

 ![A topológia topológiai irányítópult](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Az adatveszteség, késés és az egyes útvonalán ugrások számát, áttekintheti a **művelet** ablaktáblán. A görgetősáv használatával a nem jó elérési utak részleteinek megtekintéséhez. A szűrők segítségével válassza ki a nem megfelelő állapotú Ugrás az elérési utakat úgy, hogy csak a kiválasztott elérési utak topológiájának ábrázolja. Nagyítás vagy a topológia térkép kívül, az egér kereke használja. 

Az alábbi ábrán a problémás területek, az adott szakaszt az a hálózat kiváltó okának jelennek meg a piros elérési utak és az útválasztók ugrásainak. Válasszon ki egy csomópontot a topológia térképen, a csomópont, amely tartalmazza a teljes tartománynév és IP-cím tulajdonságainak megjelenítéséhez. Hop kiválasztásával tekinthet meg a következő ugrás IP-címét. 
 
![Topológia térkép a kijelölt csomópont tulajdonságai](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>További lépések
[Naplók keresése](../../azure-monitor/log-query/log-query-overview.md) részletes hálózati teljesítmény adatfelderítési rekordok megtekintéséhez.

---
title: Teljesítmény figyelése szolgáltatása hálózati Teljesítményfigyelő az Azure Naplóelemzés |} Microsoft Docs
description: A Teljesítményfigyelő funkció hálózati Teljesítményfigyelőben segítséget nyújt a hálózati kapcsolat figyelheti a hálózaton lévő különböző pontok között. -Alapú telepítések és a helyszíni helyeken, figyelheti több adatközpontok és a fiókirodák, és létfontosságú a többrétegű alkalmazások vagy mikroszolgáltatások létrehozására.
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
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 65497548d0b8066627be25520c28d39491918d09
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
ms.locfileid: "30241445"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Teljesítményfigyelő megoldás hálózati: teljesítményfigyelés

A Teljesítményfigyelő képesség [hálózati Teljesítményfigyelő](log-analytics-network-performance-monitor.md) segít nyomon, hogy hálózati kapcsolatra a hálózaton lévő különböző pontok között. -Alapú telepítések és a helyszíni helyeken, figyelheti több adatközpontok és a fiókirodák, és létfontosságú a többrétegű alkalmazások vagy mikroszolgáltatások létrehozására. A Teljesítményfigyelő képes észlelni a hálózati problémák, mielőtt a felhasználók panaszkodnak mert. Fő előnye, hogy a következők: 

- Veszteséget és késéseket figyelése különböző alhálózatokon keresztül, és állítson be riasztásokat.
- Figyelje az összes elérési utat (például a redundáns elérési utak) a hálózaton.
- Végezzen hibaelhárítást a átmeneti és időpontban hálózati problémák, amelyek nehezen replikálni.
- Határozza meg, hogy az adott szegmens a hálózaton, a teljesítmény csökkenését felelős.
- A hálózati SNMP szükségessége nélkül állapotának figyelésére.


![Hálózati teljesítményfigyelő](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguráció
A hálózati teljesítményt figyelő konfigurációs megnyitásához a [hálózati Teljesítményfigyelő megoldás](log-analytics-network-performance-monitor.md), és válassza ki **konfigurálása**.

![Konfigurálja a hálózati teljesítmény figyelése](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Új hálózatok létrehozása

Egy hálózatot a hálózati Teljesítményfigyelőben az alhálózatok logikai tárolója. A program segít a figyelést a hálózati infrastruktúra, szükség szerint rendezheti. Hozzon létre egy hálózati egy rövid nevet, és az üzleti logika szerint további alhálózatokat. Például London nevű hálózat létrehozása, és adja hozzá az összes alhálózata a londoni adatközpont. Létrehozhat egy nevű hálózati vagy *ContosoFrontEnd* és hozzáadása ehhez a hálózathoz minden az alhálózatok Contoso nevű átadott megszüntetni az alkalmazás. A megoldás automatikusan létrehoz egy alapértelmezett hálózattól, amely a környezetben felderített összes alhálózatot tartalmaz. 

Amikor létrehoz egy hálózatot, adjon hozzá egy alhálózatot rá. Alhálózat ezután eltávolítják az alapértelmezett hálózatról. Ha törli a hálózaton, az összes alhálózatot automatikusan visszatér az alapértelmezett hálózati. Az alapértelmezett hálózati úgy működik, mint bármely felhasználó által definiált hálózati nem szereplő összes alhálózat tárolója. Nem szerkeszthetők vagy az alapértelmezett hálózat törlése. Mindig marad a rendszerben. Szükség szerint tetszőleges számú egyéni hálózatokat hozhat létre. A legtöbb esetben a szervezet alhálózatok egynél több hálózati vannak rendezve. Hozzon létre egy vagy több hálózatot az üzleti logikát a alhálózatok csoportosításához.

Új hálózat létrehozásához:


1. Válassza ki a **hálózatok** fülre.
1. Válassza ki **Hozzáadás hálózati**, majd adja meg a hálózati nevét és leírását. 
2. Egy vagy több alhálózatból, majd válassza ki és **Hozzáadás**. 
3. Válassza ki **mentése** a konfiguráció mentéséhez. 


### <a name="create-monitoring-rules"></a>Figyelési szabályok létrehozása 

Teljesítményfigyelő állapotával kapcsolatos események, amikor a küszöbértéket, a teljesítmény a hálózati kapcsolatok vagy a két hálózat közötti két alhálózatai megsérül állít elő. A rendszer automatikusan e küszöbértékek is találhat. Egyéni küszöbökkel is biztosíthat. A rendszer automatikusan létrehozzon egy alapértelmezett szabály, amely olyan állapotesemény, amikor elvesztése vagy bármely két hálózati vagy alhálózat közötti késés hivatkozásait megszegése hoz létre a rendszer által ismert küszöbértéket. Ez a folyamat a figyelheti a hálózati infrastruktúra, amíg explicit módon nincsenek figyelési szabályok megoldás segítségével. Ha engedélyezve van az alapértelmezett szabályt, a csomópontok szintetikus tranzakciók küldeni a többi csomópontnak, hogy engedélyezve van, a figyelési. Az alapértelmezett szabályt akkor hasznos, kis hálózatokkal. Példa: egy olyan forgatókönyvet, ahol kis számú egy mikroszolgáltatási futtató kiszolgálók, és győződjön meg arról, hogy a kiszolgálók kapcsolódik egymáshoz szeretné.

>[!NOTE]
> Azt javasoljuk, hogy letiltja az alapértelmezett szabályt, és hozzon létre egyéni figyelési szabályokat, különösen olyan nagyobb hálózatok, ahol nagyszámú csomópontok használ a figyelést. Egyéni állapotfigyelési szabályainak csökkentheti a megoldás és a figyelést a hálózatra rendezheti súgó által generált forgalmat.

Az üzleti logika szerint figyelési szabályokat létrehozni. Például akkor, ha szeretné-e a hálózati kapcsolat a központtal két office webhelyek teljesítményének figyeléséhez. A hálózati O1 office hely1 összes alhálózata csoportban. Majd csoport office hely2 O2 hálózaton lévő összes alhálózata. Végezetül csoportban az központjában hálózati H. Hozzon létre két figyelési szabályokat--O1 és óra között egy, a másik O2 és H. közötti összes alhálózata 

Az egyéni ellenőrzési szabályokat létrehozni:

1. Válassza ki **szabály hozzáadása** a a **figyelő** fülre, és adja meg a szabály nevét és leírását.
2. Válassza ki a két hálózati vagy alhálózat hivatkozások figyelése a listából. 
3. Válassza ki a hálózatot, a hálózati legördülő listából válassza ki a kívánt átrendezés tartalmazó. Ezután válassza ki a megfelelő alhálózati legördülő listában az alhálózatokra. Ha egy hálózati kapcsolat minden alhálózatai figyelésére, jelölje be **összes alhálózatai**. Hasonlóképpen válassza ki a kívánt átrendezés. Zárja ki az adott alhálózat hivatkozások a kiválasztott beállítások figyelése, jelölje be **vegye fel kivételhiba**. 
4. ICMP és a TCP protokoll szintetikus tranzakciók végrehajtására választhat. 
5. Ha nem kívánja a cikkek állapotával kapcsolatos események létrehozása választotta, törölje a jelet **állapotfigyelő figyelés engedélyezése a hivatkozások a szabály által kezelt**. 
6. Válassza ki a feltételek figyelése. Az egyéni küszöbökkel beállítását a rendszerállapot-esemény létrehozása, adja meg a küszöbérték. A feltétel értéke meghaladja a megadott küszöbértéknél, a kijelölt hálózati vagy alhálózat pár, amikor a rendszerállapot esemény jön létre. 
7. Válassza ki **mentése** a konfiguráció mentéséhez. 

Figyelési szabály mentése után integrálható, hogy a szabály Riasztáskezelési kiválasztásával **riasztás létrehozása**. Riasztási szabály automatikusan hozza létre a keresési lekérdezés. Más paramétereket automatikusan kitölti. Riasztási szabály használatakor is fogadhatja az e-mail alapú értesítések mellett a meglévő hálózati Teljesítményfigyelő riasztásokra. Riasztások is elindítható a runbookok javító műveleteket, vagy azok integrálható a meglévő szolgáltatási megoldások webhookok használatával. Válassza ki **kezelése riasztás** a riasztási beállítások konfigurálása. 

Most több Teljesítményfigyelő szabályokat hozhat létre, vagy a funkció használatához helyezze át a megoldás irányítópultja.

### <a name="choose-the-protocol"></a>Válassza ki a protokoll

Hálózati teljesítmény figyelése szintetikus tranzakciók használja hálózati teljesítménymutatók, például a csomag és a kapcsolat késési kiszámításához. Jobban megértse a fogalom, fontolja meg egy hálózati Teljesítményfigyelő ügynök kapcsolódik a hálózati kapcsolat egyik végét. A hálózati Teljesítményfigyelő ügynök mintavételi csomagokat küld a második hálózati Teljesítményfigyelő ügynök kapcsolódik a hálózat egy másik végét. A második ügynök válaszok válasz csomagokkal. Ez a folyamat ismétlődik néhány alkalommal. Válaszok és az egyes válasz fogadásához szükséges idő mérésével az első hálózati Teljesítményfigyelő ügynök kapcsolat késési értékelésére, és az elveszett csomagokat. 

A formátum, méretének és ezek a csomagok sorozatát a választott megfigyelési szabályok létrehozásakor protokoll határozza meg. A csomagok protokollon alapuló, a közbenső hálózati eszközöket, például az útválasztók és kapcsolók, előfordulhat, hogy feldolgozni ezek a csomagok eltérő. Következésképpen a protokoll választott hatással van az eredmények pontosságának. A protokoll választott azt is meghatározza, hogy bármely manuális lépéseket a hálózati Teljesítményfigyelő megoldás telepítése után kell végrehajtania. 

Hálózati Teljesítményfigyelő biztosít a szintetikus tranzakciók végrehajtása ICMP és a TCP protokoll közötti választás. Ha ICMP szintetikus tranzakció szabály létrehozásakor, a hálózati teljesítmény-figyelő ügynökök ICMP ECHO üzenetek használatával kiszámítja a hálózati késés és a csomagveszteség. ICMP ECHO használja ugyanazt az üzenetet, amely a hagyományos ping eszköz által küldött. Ha TCP protokollt használja, hálózati Teljesítményfigyelő ügynökök TCP SZIN csomagok küldése a hálózaton keresztül. Ez a lépés a TCP-kézfogás befejezési követi, és RST csomagok használatával létesített kapcsolat törlődik. 

Mielőtt protokollt választja, vegye figyelembe a következőket: 

* **Több hálózati útvonal felderítése.** Akkor jelenik meg a TCP pontosabb, ha több útvonal felderítésére, és kevesebb ügynökök minden alhálózatban kell. Például egy vagy két olyan megbízottat TCP használó felderíthetők az alhálózatok közötti összes redundáns elérési utat. Több ügynököt hasonló eredmény elérése érdekében ICMP használó van szüksége. ICMP, ha két alhálózat között útvonalak számos használva szüksége több, mint a forrás vagy a cél alhálózaton 5N ügynökök.

* **Eredmények pontosságának.** Útválasztók és kapcsolók általában alacsonyabb prioritású hozzárendelése ICMP ECHO csomagok TCP csomagok képest. Bizonyos helyzetekben előfordulhat Ha a hálózati eszközök van terhelve, jobban TCP által gyűjtött adatok tükrözi az adatvesztéssel és alkalmazások által tapasztalt késést. Ennek oka az, hogy az alkalmazás forgalom a legtöbb TCP protokollon keresztül zajlik. Ilyen esetekben ICMP TCP képest kevésbé pontos eredményeket nyújt. 

* **Tűzfal-konfiguráció.** TCP protokollt igényel, hogy a TCP-csomagokat a célport kapnak. A hálózati Teljesítményfigyelő-ügynökök által használt alapértelmezett port 8084. A port módosítható ügynökök konfigurálásakor. Győződjön meg arról, hogy a hálózati tűzfalak vagy hálózati biztonsági csoport (NSG) (az Azure-ban) vonatkozó szabályok engedélyezik-e a port forgalmát. Szükség győződjön meg arról, hogy azokon a számítógépeken, amelyeken az ügynökök telepítve vannak a helyi tűzfal forgalmat engedélyezi ezt a portot a van konfigurálva. PowerShell-parancsfájlok segítségével tűzfalszabályok beállítása a Windows rendszerű számítógépeken, de a hálózati tűzfal manuálisan konfigurálnia kell. Ezzel szemben ICMP nem működik a porton keresztül. A legtöbb vállalati környezetben ICMP-forgalmat engedélyezett a tűzfalon keresztül hálózati diagnosztika eszközök, például a ping eszköz használatát teszik lehetővé. Ha egy gép másik pingelhető, a tűzfal kézi konfigurálása nélkül használhatja az ICMP protokollt.

>[!NOTE] 
> Néhány tűzfalak blokkolhatják ICMP, amely, amely jelentős számú eseményt eredményez a biztonsági információk és az esemény a felügyeleti rendszer újraküldési vezethet. Győződjön meg arról, hogy az Ön által a protokoll NSG vagy hálózati tűzfal nem blokkolja. Ellenkező esetben a hálózati teljesítmény figyelése nem figyelhető hálózati szegmenst. Azt javasoljuk, hogy használja-e TCP figyelésre. ICMP helyzetekben használhatja adott nem használható TCP, például amikor: 
>
> - A Windows ügyfélalapú csomópontok esetén használható, mert a nyers TCP-szoftvercsatornák a Windows-ügyfelek nem engedélyezett.
> - A hálózati tűzfal vagy NSG blokkol TCP.
> - Hogyan lehet váltani a protokoll nem ismeri.

Ha a telepítés során ICMP használatát választotta, átválthat TCP bármikor az alapértelmezett szabály figyelési szerkesztésével.

1. Ugrás a **hálózati teljesítményt** > **figyelő** > **konfigurálása**   >  **Figyelő**. Válassza ki **alapértelmezett szabály**. 
2. Görgessen a **protokoll** szakaszt, és válassza ki a használni kívánt protokollt. 
3. Válassza ki **mentése** a beállítást. 

Akkor is, ha az alapértelmezett szabályt egy adott protokollt használja, a másik protokollal létrehozhat új szabályokat. Hol néhány szabály ICMP használja, és egyéb TCP szabályok vegyesen is létrehozhat. 

## <a name="walkthrough"></a>Útmutatás 

Most egy egyszerű vizsgálat olyan állapotesemény esetében tekintse meg.

A megoldás irányítópultján olyan állapotesemény mutatja, hogy egy hálózati kapcsolat nem kifogástalan. Vizsgálja meg a problémát, válassza ki a **hálózati kapcsolatok figyelt** csempére.

A részletező lapon látható, melyet a **DMZ2-DMZ1** hálózati kapcsolat állapota nem megfelelő. Válassza ki **alhálózati hivatkozások** a hálózati kapcsolat. 


A részletező lapon látható az alhálózati kapcsolat a **DMZ2-DMZ1** hálózati kapcsolat. Mindkét alhálózati kapcsolat a késési küszöbértéket a küszöbérték, amely lehetővé teszi a hálózati kapcsolat nem kifogástalan. Mindkét alhálózati kapcsolat a késési trendjeit is látható. Használja a diagramot úgy, hogy a szükséges időtartomány összpontosítani lévő vezérlése az időbeállítást. A maximális késés elérésekor időpontja tekintheti meg. Keresse meg később a naplókat a probléma kivizsgálása ebben az időszakban. Válassza ki **csomóponti kapcsolat megtekintése** további részletezéshez. 
 
 ![Alhálózat hivatkozások lap](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Hasonló az előző lapra, a részletezés az adott alhálózat hivatkozás laplistákhoz a bennük foglalt csomóponti kapcsolat. Hasonló műveletek végezhetők itt, ahogyan az előző lépésben. Válassza ki **nézet topológia** megtekintéséhez a topológia a két csomópont között. 
 
 ![Csomópont hivatkozások lap](media/log-analytics-network-performance-monitor/node-links.png) 

A két kijelölt csomópontok közötti összes elérési utat ábrázolási a topológia a térképen. A topológia-térképként két csomópont között útvonalak a Ugrás-hop topológiája jelenítheti meg. Biztosít a hány útvonalak léteznek a két csomópontot, és milyen közötti világossá elérési utak az adatcsomagok igénybe vehet. Hálózati teljesítmény szűk piros láthatók. Hibás hálózati kapcsolat vagy egy hibás hálózati eszköz található, tekintse meg a piros elemeket a topológia-térképként. 

 ![A topológia térkép topológia irányítópult](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Az adatvesztés, a késés és az egyes útvonalán ugrások száma tekintse át a **művelet** ablaktáblán. A görgetősáv segítségével a nem megfelelő elérési utak részleteinek megtekintéséhez. A szűrők segítségével válassza ki a nem megfelelő állapotú Ugrás az elérési utakat, hogy csak a kiválasztott elérési utak topológiájának ábrázolja. Nagyítás vagy a topológia térkép kívül, az egér kerekének használja. 

Az alábbi ábrán az okozza az adott részt a a hálózaton a problémás területek jelennek meg a piros elérési útja és az útválasztók ugrásainak. Válasszon ki egy csomópontot a csomópontot, amely tartalmazza a teljes tartománynév és IP-cím tulajdonságainak megjelenítéséhez a topológia a térképen. Az IP-címét a hop kiválasztásával jeleníti meg. 
 
![A kijelölt csomópont tulajdonság topológia térkép](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>További lépések
[Naplók keresése](log-analytics-log-searches.md) részletes hálózati teljesítmény rekordok megtekintéséhez.

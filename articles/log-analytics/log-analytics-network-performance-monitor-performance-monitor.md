---
title: "Teljesítmény figyelése szolgáltatása hálózati Teljesítményfigyelő az Azure Naplóelemzés |} Microsoft Docs"
description: "A Teljesítményfigyelő funkció hálózati Teljesítményfigyelőben segít a figyelheti a hálózati kapcsolat a hálózaton, például a felhőben történő alkalmazáshoz és a helyszíni helyeken, több különböző adatközponthoz és a fiókirodákban létfontosságú különböző pontok között Többrétegű alkalmazások és micro-szolgáltatások."
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
ms.openlocfilehash: a90ab3bc857b704d9d94daf96d17611844abb1a6
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution---performance-monitoring"></a>Teljesítményfigyelő-megoldás – a hálózati teljesítmény figyelése

A Teljesítményfigyelő képesség [hálózati Teljesítményfigyelő](log-analytics-network-performance-monitor.md) különböző keresztül figyelheti a hálózati kapcsolat segítségével a hálózaton,-alapú telepítések és a helyszíni helyeken, például több különböző adatközponthoz mutat, és fiókirodák, kritikus kritikus Többrétegű alkalmazások és micro-szolgáltatások. A Teljesítményfigyelő képes észlelni a hálózati problémák, mielőtt a felhasználók panaszkodnak mert. Főbb előnyei a következők: 

- Veszteséget és késéseket figyelése különböző alhálózatokon és értesítések beállítása 
- Összes elérési utat (például a redundáns elérési utak) a hálózat figyelése 
- Átmeneti & időpontban hálózati problémák, amelyeket nehéz replikálása 
- Határozza meg a hálózaton, a teljesítmény csökkenését felelős az adott szegmens 
- A hálózati SNMP szükségessége nélkül állapotának figyelésére 


![Hálózati teljesítményfigyelő](media/log-analytics-network-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguráció
A hálózati teljesítményt figyelő konfigurációs megnyitásához a [hálózati Teljesítményfigyelő megoldás](log-analytics-network-performance-monitor.md) , és kattintson a **konfigurálása** gombra.

![Konfigurálja a hálózati teljesítmény figyelése](media/log-analytics-network-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Új hálózatok létrehozása

Egy hálózati NPM az alhálózatok logikai tárolója. A program segít a figyelést a hálózati infrastruktúra figyelési igényei szerint rendezheti. Hozzon létre egy hálózati egy rövid nevet, és az üzleti logika szerint további alhálózatokat. Például London nevű hálózat létrehozása, és adja hozzá az alhálózatok a londoni datacenter, vagy egy hálózati nevű *ContosoFrontEnd* , és adja hozzá az összes alhálózatot az ehhez a hálózathoz Contoso nevű alkalmazás előtérrendszerét szolgál. A megoldás automatikusan létrehoz egy alapértelmezett hálózaton található, amely a környezetben felderített összes alhálózatot tartalmaz. Hálózatot hoz létre, amikor egy alhálózat hozzáadása, és alhálózaton eltávolítják az alapértelmezett hálózatról. Ha törli a hálózaton, az összes alhálózatot automatikusan visszatér az alapértelmezett hálózati. Az alapértelmezett hálózati ebből kifolyólag minden olyan alhálózat nem található a felhasználó által definiált hálózati tárolója funkcionál. Nem szerkeszthetők vagy az alapértelmezett hálózat törlése. Mindig marad a rendszerben. Azonban létrehozhat annyi egyéni hálózatok szükség szerint. A legtöbb esetben a szervezet alhálózatok egynél több hálózati vannak rendezve, és hozzon létre egy vagy több hálózatot az üzleti logikát a alhálózatok csoportosításához.

Új hálózat létrehozásához:


1. Kattintson a **hálózat lap**.
1. Kattintson a **Hozzáadás hálózati** és írja be a hálózati nevét és leírását. 
2. Válassza ki egy vagy több alhálózatból, és kattintson a **Hozzáadás**. 
3. Kattintson a **mentése** a konfiguráció mentéséhez. 


### <a name="create-monitoring-rules"></a>Figyelési szabályok létrehozása 

Teljesítményfigyelő állapotával kapcsolatos események, amikor a küszöbértéket, a teljesítmény a hálózati kapcsolatok vagy a két hálózat közötti két alhálózatai megsérül állít elő. Ezeket a küszöbértékeket is ismernie kell automatikusan a rendszer, vagy megadhat egyéni küszöbértékeket. A rendszer automatikusan létrehozzon egy alapértelmezett szabály, amely olyan állapotesemény, amikor elvesztése vagy bármely két hálózati/alhálózat közötti késés hivatkozásait megszegése hoz létre a rendszer által ismert küszöbértéket. Ez segít a megoldás figyeléséhez a hálózati infrastruktúra, amíg explicit módon nincsenek ellenőrzési szabályok. Ha a **alapértelmezett szabály** van engedélyezve, a csomópontok küldeni a szintetikus tranzakciók a többi csomópontnak, hogy engedélyezte a figyelésre. Az alapértelmezett szabályt akkor hasznos, kis hálózatokhoz, például az olyan forgatókönyvekben, ahol kis számú egy mikroszolgáltatási futtató kiszolgálók, és győződjön meg arról, hogy a kiszolgálók kapcsolódik egymáshoz szeretné. 

>[!NOTE]
> Javasoljuk, hogy tiltsa le a **alapértelmezett szabály** , és hozzon létre egyéni figyelési szabályokat, különösen olyan nagyobb hálózatok esetén használata nagyszámú csomópontot a figyelés. Ez csökkenti a megoldás és a figyelést a hálózatra rendezheti súgó által generált forgalmat. 

Az üzleti logika szerint figyelési szabályokat létrehozni. Például ha azt szeretné, a hálózati kapcsolat két office helyek headquarter teljesítményének figyeléséhez, majd csoport office hely1 O1 hálózaton lévő összes alhálózata, office hely2 O2 hálózaton lévő összes alhálózata és a hálózati H. alkalmával a székhelyet mindig az összes alhálózata Hozzon létre szabályokat egy az egyhez O1 és H között a másik O2 és H. közötti figyelési 2 

Az egyéni ellenőrzési szabályokat létrehozni:

1. Kattintson a **szabály hozzáadása** a a **figyelő** lapra, és írja be a szabály nevét és leírását. 
2. Válassza ki a két hálózati vagy alhálózat hivatkozások figyelése a listából. 
3. Először a hálózati legördülő listából válassza ki azt a hálózatot, amelyben az első alhálózat/s érdeklő tartalmazza, és a megfelelő alhálózati legördülő listából válassza ki az alhálózat/s. Válassza ki **összes alhálózatai** Ha azt szeretné, hogy a hálózati kapcsolaton minden alhálózatai figyelésére. Hasonlóképpen válassza ki a másik alhálózat/s iránt. És kattinthat **vegye fel kivételhiba** kizárása az adott alhálózat hivatkozások a kijelölés végrehajtott figyelését. 
4. ICMP és TCP protokollok a szintetikus tranzakciók végrehajtása választhat. 
5. Ha nem kívánja a kijelölt, majd elemeinek állapotával kapcsolatos események törlése létrehozása **engedélyezése a hivatkozások a szabály által kezelt állapotfigyelés**. 
6. Válassza ki a feltételek figyelése. Adhatja meg egyéni küszöbökkel állapotát az eseménygenerálás írja be a küszöbértékekhez. A feltétel értéke a megadott küszöbértéknél, a kijelölt hálózati/alhálózat pár fölé megy, amikor a rendszerállapot esemény jön létre. 
7. Kattintson a **mentése** a konfiguráció mentéséhez. 

Figyelési szabály mentése után integrálható, hogy a szabály Riasztáskezelési kattintva **riasztás létrehozása**. Riasztási szabály automatikusan létrejön a keresési lekérdezés és más szükséges paramétereket automatikusan kitöltött-a. Riasztási szabály használatakor is fogadhatja az e-mail alapú értesítések mellett a meglévő riasztást NPM belül. Riasztások is elindítható a runbookok javító műveleteket, vagy azok integrálható a meglévő szolgáltatási felügyeleti megoldások webhookok használatával. Kattinthat **kezelése riasztás** a riasztási beállítások konfigurálása. 

Most hozzon létre több Teljesítményfigyelő szabályok vagy a funkció használatához helyezze át a megoldás irányítópultja 

### <a name="choose-the-protocol"></a>Válassza ki a protokoll

Hálózati teljesítmény figyelése (NPM) szintetikus tranzakciók használatával hálózati teljesítménymutatók, például a csomag és a kapcsolat késési kiszámításához. Ez jobb megértése, fontolja meg az NPM ügynök kapcsolódik a hálózati kapcsolat egyik végét. Az NPM ügynök mintavételi csomagokat küld egy második NPM ügynök kapcsolódik a hálózat egy másik végét. A második ügynök válaszok válasz csomagokkal. Ez a folyamat ismétlődik néhány alkalommal. Válaszok és minden válasz fogadásához szükséges idő mérésével az első NPM ügynök kapcsolat késési értékelésére, és az elveszett csomagokat. 

A formátum, méretének és ezek a csomagok sorozatát a választott megfigyelési szabályok létrehozásakor protokoll határozza meg. A csomagok protokollon alapuló, a köztes hálózati eszközt (útválasztók, kapcsolók stb.) lehet, hogy másképp feldolgozni ezeket a csomagokat. Következésképpen a protokoll választott hatással van az eredmények pontosságának. És a protokoll választott azt is meghatározza, hogy bármely manuális lépéseket az NPM-megoldás telepítése után kell végrehajtania. 

NPM biztosít a szintetikus tranzakciók végrehajtása ICMP és a TCP protokoll közötti választás. ICMP szintetikus tranzakció szabály létrehozásakor válassza ki, ha az NPM ügynökök ICMP ECHO üzenetek használatával kiszámítja a hálózati késés és a csomagveszteség. ICMP ECHO használja ugyanazt az üzenetet, amely a hagyományos Ping eszköz által küldött. Ha TCP protokollt használja, a NPM ügynökök TCP SZIN csomag elküldése a hálózaton keresztül. Ide kerül a TCP-kézfogás befejezési és majd a RST csomagok használata a kapcsolat eltávolítása. 

Mielőtt használandó protokollt választja, vegye figyelembe a következőket: 

**Több hálózati útvonal felderítéséhez.**  TCP pontosabb, ha több útvonal felderítéséhez és kevesebb ügynökök minden alhálózatban kell. Például egy vagy két ügynökök TCP használatával felderíthetők az alhálózatok közötti összes redundáns elérési utat. Azonban hogy az ICMP Protokollt használó hasonló eredmény elérése érdekében több ügynökök kell. ICMP, használatával, ha két alhálózat között útvonalak számos szüksége több, mint a forrás vagy a cél alhálózaton 5N ügynökök. 

**Eredmények pontosságának.** Útválasztók és kapcsolók általában alacsonyabb prioritású hozzárendelése ICMP ECHO csomagok TCP csomagok képest. Bizonyos helyzetekben előfordulhat Ha a hálózati eszközök van terhelve, jobban TCP által gyűjtött adatok tükrözi az adatvesztéssel és alkalmazások által tapasztalt késést. Ennek oka az, hogy az alkalmazás forgalom a legtöbb TCP protokollon keresztül zajlik. Ilyen esetekben ICMP TCP képest kevésbé pontos eredményeket nyújt. 

**Tűzfal-konfiguráció.** TCP protokollt igényel, hogy a TCP-csomagokat a célport kapnak. Az alapértelmezett port NPM-ügynökök által használt az 8084, de ez az ügynökök konfigurálásakor módosítható. Igen szükség győződjön meg róla, hogy a hálózati tűzfalak vagy az NSG-szabályok (az Azure-ban) forgalmat a porton keresztülhaladó. Szükség győződjön meg arról, hogy azokon a számítógépeken, amelyeken az ügynökök telepítve vannak a helyi tűzfal forgalmat engedélyezi ezt a portot a van konfigurálva. Tűzfalszabályok konfigurálása a Windows rendszert futtató számítógépeken, de kell manuálisan konfigurálnia a hálózati tűzfal használhatja a PowerShell-parancsfájlokat. Ezzel szemben ICMP nem működik a portot használja. A legtöbb vállalati környezetben ICMP-forgalmat engedélyezett a tűzfalon keresztül hálózati diagnosztika eszközök, például a Ping eszköz használatát teszik lehetővé. Így ha egy gép másik pingelhető, majd használhatja az ICMP protokoll tűzfal kézi konfigurálása nélkül. 

>[!NOTE] 
> Néhány tűzfalak blokkolhatják a ICMP, ami azt eredményezheti, hogy a biztonsági információk és az esemény a felügyeleti rendszer jelentős számú eseményt eredményezve újraküldési. Győződjön meg arról, hogy az Ön által nem blokkolva van a hálózati tűzfal/NSG, ellenkező esetben NPM nincs felügyelete hálózati szegmenst. Ebből kifolyólag javasoljuk TCP használhat a figyeléshez. Használjon ICMP forgatókönyvekben hol áll nem használhatják a TCP, például amikor: 
>
> - Windows-alapú ügyfél csomópontok, mivel TCP nyers sockets nem engedélyezettek a Windows ügyfél használ
> - A hálózati tűzfal/NSG blokkok TCP
> - Hogyan lehet váltani a protokoll 

Ha a telepítés során ICMP használatát választotta, átválthat TCP bármikor az alapértelmezett szabály figyelési szerkesztésével:

1. Navigáljon a **hálózati teljesítményt** > **figyelő** > **konfigurálása**   >  **Figyelő** majd **alapértelmezett szabály**. 
2. Görgessen a **protokoll** szakaszt, és válassza ki a használni kívánt protokollt. 
3. Kattintson a **mentése** a beállítást. 

Akkor is, ha az alapértelmezett szabályt egy adott protokollt használ, a másik protokollal létrehozhat új szabályokat. Ha egyes szabályok ICMP, illetve egy másik használja TCP szabályok vegyesen is létrehozhat. 

## <a name="walkthrough"></a>Útmutatás 

Most, hogy tekintse át a Teljesítményfigyelőben, egyszerű vizsgálatot a rendszerállapot-esemény alapvető okának vizsgáljuk meg.  

A megoldás irányítópult figyelje meg, hogy nincs olyan állapotesemény – egy hálózati kapcsolat állapota nem megfelelő. Úgy dönt, hogy vizsgálja meg a problémát, majd kattintson a **hálózati kapcsolatok figyelt** csempére.

A részletezési oldalon azt láthatja, hogy a **DMZ2-DMZ1** hálózati kapcsolat állapota nem megfelelő. Kattintson a a **alhálózati hivatkozások** hivatkozásra a hálózati kapcsolat. 


A részletező lapon látható az alhálózati kapcsolat **DMZ2-DMZ1** hálózati kapcsolat. Bizonyára észrevette, hogy mindkét az alhálózati kapcsolat a késési elért a küszöbértéket, így a hálózati kapcsolat nem kifogástalan. Az alhálózati kapcsolat a késési trendjeit is megtekinthető. Használhatja az időbeállítást-vezérlés a diagramot úgy, hogy a szükséges időtartomány összpontosítanak. Ha a késés elérte a maximális szakában tekintheti meg. A naplókat a probléma kivizsgálása ebben az időszakban később rá tud keresni. Kattintson a **csomóponti kapcsolat megtekintése** további részletezéshez. 
 
 ![Produkáló alhálózati kapcsolatok](media/log-analytics-network-performance-monitor/subnetwork-links.png) 

Hasonló az előző lapra, a részletezés az adott alhálózat hivatkozás laplistákhoz le a bennük foglalt csomóponti kapcsolat. Hasonló műveletek végezhetők itt, ahogyan az előző lépésben. Kattintson a **nézet topológia** megtekintéséhez a topológia a két csomópont között. 
 
 ![Csomóponti kapcsolat](media/log-analytics-network-performance-monitor/node-links.png) 

A két kijelölt csomópontok közötti összes elérési utat ábrázolási a topológia a térképen. A topológia-térképként két csomópont között útvonalak a Ugrás-hop topológiája jelenítheti meg. Biztosít a hány útvonalak léteznek a két csomópontot, és milyen közötti világossá elérési utak az adatcsomagok tart. Hálózati teljesítmény szűk piros színnel jelöli. Hibás hálózati kapcsolat vagy egy hibás hálózati eszköz is keresse meg a topológia-térképként piros színes elemek felmérésével. 

 ![Topológia irányítópult](media/log-analytics-network-performance-monitor/topology-dashboard.png) 

Az adatvesztés, a késés és az egyes elérési utakat az ugrások számát tekinthetők át a **művelet** ablaktáblán. A görgetősáv segítségével ezeket a nem megfelelő elérési utak részleteinek megtekintéséhez. A szűrők segítségével válassza ki a nem megfelelő állapotú Ugrás az elérési utakat, hogy csak a kiválasztott elérési utak topológiájának ábrázolja. Az egér kerekének segítségével nagyítása vagy kicsinyítése a topológia leképezés. 

Az a kép, alatt egyértelműen megtekintheti az adott részt a a hálózaton a problémás területek okozza az elérési útja és a piros szín ugrások megtekintésével. Kattintson a csomópont a topológia a térképen tárja fel a csomópont teljesen minősített Tartománynevét, beleértve a tulajdonságok és IP-címet. Az IP-címét a hop kattintva jeleníti meg. 
 
![Topológia irányítópult](media/log-analytics-network-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>További lépések
* [Naplók keresése](log-analytics-log-searches.md) részletes hálózati teljesítmény rekordok megtekintéséhez.

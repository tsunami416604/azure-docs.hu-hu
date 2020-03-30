---
title: Teljesítményfigyelő a Hálózati teljesítményfigyelőben
description: A Hálózati teljesítményfigyelő Teljesítményfigyelő funkciója segítségével a hálózat különböző pontjain is figyelheti a hálózati kapcsolatot. Figyelheti a felhőalapú üzembe helyezéseket és a helyszíni helyeket, több adatközpontot és fiókirodát, valamint a kritikus fontosságú többrétegű alkalmazásokat vagy mikroszolgáltatásokat.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: 126cca9d3606b378e59e4f4e1c5b52d985d19d94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055693"
---
# <a name="network-performance-monitor-solution-performance-monitoring"></a>Hálózati teljesítményfigyelő megoldás: Teljesítményfigyelés

A [Hálózati teljesítményfigyelő](network-performance-monitor.md) Teljesítményfigyelő funkciója segítségével a hálózat különböző pontjain is figyelheti a hálózati kapcsolatot. Figyelheti a felhőalapú üzembe helyezéseket és a helyszíni helyeket, több adatközpontot és fiókirodát, valamint a kritikus fontosságú többrétegű alkalmazásokat vagy mikroszolgáltatásokat. A Teljesítményfigyelő segítségével még a felhasználók panasza előtt észlelheti a hálózati problémákat. A legfontosabb előnye, hogy: 

- Figyelje a veszteséget és a késést a különböző alhálózatok között, és riasztásokat állítson be.
- A hálózat összes útvonalának (beleértve a redundáns elérési utakat is) figyelése.
- Elháríthatja az átmeneti és időponthoz elegendő, nehezen replikálható hálózati problémákat.
- Határozza meg a hálózat adott szegmensét, amely a csökkent teljesítményért felelős.
- Figyelje a hálózat állapotát, anélkül, hogy SNMP-re lenne szükség.


![Hálózati teljesítményfigyelő](media/network-performance-monitor-performance-monitor/npm-performance-monitor.png)

## <a name="configuration"></a>Konfiguráció
A Hálózati teljesítményfigyelő konfigurációjának megnyitásához nyissa meg a [Hálózati teljesítményfigyelő megoldást,](network-performance-monitor.md)és válassza a **Konfigurálás**lehetőséget.

![A Network Performance Monitor konfigurálása](media/network-performance-monitor-performance-monitor/npm-configure-button.png)

### <a name="create-new-networks"></a>Új hálózatok létrehozása

A Hálózati teljesítményfigyelőben lévő hálózat az alhálózatok logikai tárolója. Segít megszervezni a hálózati infrastruktúra figyelését az Ön igényeinek megfelelően. Létrehozhat egy rövid nevű hálózatot, és az üzleti logikának megfelelően alhálózatokat adhat hozzá. Létrehozhat például egy London nevű hálózatot, és hozzáadhatja az összes alhálózatot a londoni adatközpontban. Vagy létrehozhat egy *ContosoFrontEnd* nevű hálózatot, és hozzáadhatja a hálózathoz az összes Contoso nevű alhálózatot, amelyek az alkalmazás előterét szolgálják. A megoldás automatikusan létrehoz egy alapértelmezett hálózatot, amely tartalmazza a környezetben felderített összes alhálózatot. 

Amikor létrehoz egy hálózatot, alhálózatot ad hozzá. Ezután az alhálózat törlődik az alapértelmezett hálózatról. Ha töröl egy hálózatot, az összes alhálózata automatikusan visszakerül az alapértelmezett hálózatra. Az alapértelmezett hálózat tárolóként működik az összes olyan alhálózat számára, amely nem tartalmaz felhasználó által definiált hálózatot. Az alapértelmezett hálózat nem szerkeszthető és nem törölhető. Mindig a rendszerben marad. Annyi egyéni hálózatot hozhat létre, amennyire szüksége van. A legtöbb esetben a szervezet alhálózatai több hálózatba vannak rendezve. Hozzon létre egy vagy több hálózatot az alhálózatok csoportosításához az üzleti logikához.

Új hálózat létrehozása:


1. Válassza a **Hálózatok** lapot.
1. Válassza **a Hálózat hozzáadása**lehetőséget, majd írja be a hálózat nevét és leírását. 
2. Jelöljön ki egy vagy több alhálózatot, majd válassza **a Hozzáadás lehetőséget.** 
3. A konfiguráció mentéséhez válassza a **Mentés** gombot. 


### <a name="create-monitoring-rules"></a>Figyelési szabályok létrehozása 

A Teljesítményfigyelő akkor hoz létre állapoteseményeket, ha a két alhálózat vagy két hálózat közötti hálózati kapcsolatok teljesítményének küszöbértékét megsértik. A rendszer automatikusan megtanulhatja ezeket a küszöbértékeket. Egyéni küszöbértékeket is megadhat. A rendszer automatikusan létrehoz egy alapértelmezett szabályt, amely állapoteseményt hoz létre, ha bármely hálózati vagy alhálózati kapcsolat között elvesztés vagy késés sérti a rendszer által megtanult küszöbértéket. Ez a folyamat segít a megoldásnak a hálózati infrastruktúra figyelésében, amíg nem hozott létre kifejezetten figyelési szabályokat. Ha az alapértelmezett szabály engedélyezve van, az összes csomópont szintetikus tranzakciókat küld az összes többi csomópontnak, amelyet engedélyezbe tett a figyeléshez. Az alapértelmezett szabály kis hálózatok esetén hasznos. Egy példa egy olyan forgatókönyv, amelyben egy kis számú, mikroszolgáltatást futtató kiszolgálóval rendelkezik, és győződjön meg arról, hogy az összes kiszolgáló rendelkezik-e kapcsolattal egymással.

>[!NOTE]
> Azt javasoljuk, hogy tiltsa le az alapértelmezett szabályt, és hozzon létre egyéni figyelési szabályokat, különösen a nagy hálózatok, ahol nagy számú csomópontot használ a figyeléshez. Az egyéni figyelési szabályok csökkenthetik a megoldás által generált forgalmat, és segíthetnek a hálózat figyelésének rendszerezésében.

Figyelési szabályokat hozhat létre az üzleti logikának megfelelően. Egy példa, ha két irodai hely és a központ hálózati kapcsolatának teljesítményét szeretné figyelni. Csoportosítsa az összes alhálózatot az Office Site1-ben az O1 hálózatban. Ezután csoportosítsa az összes alhálózatot az office site2-ben az O2 hálózatban. Végül csoportosítsa az összes alhálózatot a h hálózatban. 

Egyéni figyelési szabályok létrehozása:

1. Válassza a Szabály **hozzáadása lehetőséget** a **Figyelő** lapon, és írja be a szabály nevét és leírását.
2. Válassza ki a listából figyelni kívánt hálózati vagy alhálózati kapcsolatok párját. 
3. Válassza ki a kívánt alhálózatokat tartalmazó hálózatot a hálózat legördülő listájából. Ezután válassza ki az alhálózatokat a megfelelő alhálózat legördülő listából. Ha egy hálózati kapcsolat összes alhálózatát figyelni szeretné, válassza az **Összes alhálózat**lehetőséget. Hasonlóképpen jelölje ki a többi alhálózatot. Ha ki szeretné zárni bizonyos alhálózati kapcsolatok figyelését a megadott beállításokból, válassza a **Kivétel hozzáadása**lehetőséget. 
4. Válasszon az ICMP és a TCP protokollok közül a szintetikus tranzakciók végrehajtásához. 
5. Ha nem szeretne állapoteseményeket létrehozni a kiválasztott elemekhez, törölje a minden jelzőeseményt **a szabály hatálya alá tartozó hivatkozásokon.** 
6. Válassza ki a figyelési feltételeket. Egyéni küszöbértékek beállítása az állapot-esemény generálása, adja meg a küszöbértékeket. Ha a feltétel értéke meghaladja a kiválasztott hálózati vagy alhálózati párra vonatkozó kijelölt küszöbértéket, a rendszer állapoteseményt hoz létre. 
7. A konfiguráció mentéséhez válassza a **Mentés** gombot. 

A figyelési szabály mentése után integrálhatja a szabályt a Riasztáskezelés szolgáltatással a **Riasztás létrehozása**lehetőséget választva. A rendszer automatikusan riasztási szabályt hoz létre a keresési lekérdezéssel. A rendszer automatikusan kitölti a többi szükséges paramétert. Egy riasztási szabály használatával a Hálózati teljesítményfigyelőn belül meglévő riasztásokon kívül e-mail alapú riasztásokat is kaphat. Riasztások is elindíthatja a kiigazító műveletek runbookok, vagy integrálhatók a meglévő szolgáltatáskezelési megoldások webhookok használatával. A **riasztási** beállítások szerkesztéséhez válassza a Riasztás kezelése lehetőséget. 

Most már létrehozhat több Teljesítményfigyelő-szabályt, vagy átléphet a megoldás irányítópultjára a képesség használatához.

### <a name="choose-the-protocol"></a>A protokoll kiválasztása

A Hálózati teljesítményfigyelő szintetikus tranzakciókat használ a hálózati teljesítménymérők, például a csomagvesztés és a kapcsolatkés kiszámításához. A koncepció jobb megértéséhez fontolja meg a hálózati kapcsolat egyik végéhez csatlakoztatott Hálózati teljesítményfigyelő-ügynök. Ez a Hálózati teljesítményfigyelő ügynök mintavételi csomagokat küld a hálózat egy másik végéhez csatlakoztatott második hálózati teljesítményfigyelő ügynöknek. A második ügynök válaszcsomagokkal válaszol. Ez a folyamat ismétlődik néhányszor. A válaszok számának és az egyes válaszok fogadásához szükséges idő nek a mérésével az első Hálózati teljesítményfigyelő ügynök felméri a kapcsolat késését és a csomagleadásokat. 

A csomagok formátumát, méretét és sorrendjét a figyelési szabályok létrehozásakor kiválasztott protokoll határozza meg. A csomagok protokollja alapján a köztes hálózati eszközök, például az útválasztók és a kapcsolók eltérően dolgozhatják fel ezeket a csomagokat. Ennek következtében a protokollválasztás befolyásolja az eredmények pontosságát. A protokollválasztás azt is meghatározza, hogy a Hálózati teljesítményfigyelő megoldás telepítése után kell-e manuális lépéseket tennie. 

A Hálózati teljesítményfigyelő felajánlja a szintetikus tranzakciók végrehajtásához az ICMP és a TCP protokollok közötti választást. Ha szintetikus tranzakciós szabály létrehozásakor az ICMP lehetőséget választja, a Hálózati teljesítményfigyelő ügynökei ICMP ECHO-üzeneteket használnak a hálózati késés és a csomagvesztés kiszámításához. Icmp ECHO használja ugyanazt az üzenetet, hogy a hagyományos ping segédprogram. Ha a TCP protokollt használja protokollként, a Hálózati teljesítményfigyelő ügynökök TCP SYN-csomagokat küldenek a hálózaton keresztül. Ezt a lépést tcp-kézfogás befejezése követi, és a kapcsolatot RST-csomagok segítségével távolítja el. 

A protokoll kiválasztása előtt vegye figyelembe az alábbi információkat: 

* **Több hálózati útvonal felderítése.** A TCP pontosabb, ha több útvonalat észlel, és kevesebb ügynökre van szüksége az egyes alhálózatokban. A TCP protokollt használó egy vagy két ügynök például felderítheti az alhálózatok közötti összes redundáns útvonalat. Több olyan ügynökre van szükség, amelyek az ICMP-t használják a hasonló eredmények eléréséhez. Az ICMP használatával, ha két alhálózat között több útvonal van, több mint 5N ügynökre van szüksége egy forrás- vagy célalhálózatban.

* **Az eredmények pontossága.** Az útválasztók és kapcsolók általában alacsonyabb prioritást rendelnek az ICMP ECHO-csomagokhoz, mint a TCP-csomagok. Bizonyos helyzetekben, amikor a hálózati eszközök erősen be van töltve, a TCP által kapott adatok jobban tükrözi késés az alkalmazások által tapasztalt veszteséget és késést. Ennek az az oka, hogy az alkalmazás forgalom nagy része TCP-n keresztül áramlik. Ilyen esetekben az ICMP a TCP-hez képest kevésbé pontos eredményeket ad. 

* **Tűzfal konfigurációja.** A TCP protokoll megköveteli, hogy a TCP-csomagokat a rendszer a célportra küldje. A Hálózati teljesítményfigyelő ügynökei által használt alapértelmezett port a 8084. Az ügynökök konfigurálásakor módosíthatja a portot. Győződjön meg arról, hogy a hálózati tűzfalak vagy a hálózati biztonsági csoport (NSG) szabályok (az Azure-ban) engedélyezik a porton a forgalmat. Azt is meg kell győződnie arról, hogy a helyi tűzfal a számítógépeken, ahol ügynökök vannak telepítve van beállítva, hogy a forgalom ezen a porton. A PowerShell-parancsfájlok segítségével konfigurálhatja a tűzfalszabályokat a Windows rendszert futtató számítógépeken, de manuálisan kell konfigurálnia a hálózati tűzfalat. Ezzel szemben az ICMP nem port használatával működik. A legtöbb vállalati forgatókönyvben az ICMP-forgalom a tűzfalakon keresztül engedélyezett, hogy lehetővé tegye a hálózati diagnosztikai eszközök, például a ping segédprogram használatát. Ha pingelhet egy gépet a másiktól, az ICMP protokollt a tűzfalak manuális konfigurálása nélkül is használhatja.

>[!NOTE] 
> Egyes tűzfalak blokkolhatják az ICMP-t, ami olyan továbbközvetítéshez vezethet, amely nagyszámú eseményt eredményez a biztonsági információk és az eseménykezelő rendszer részéről. Győződjön meg arról, hogy a választott protokollt nem blokkolja a hálózati tűzfal vagy az NSG. Ellenkező esetben a Hálózati teljesítményfigyelő nem tudja figyelni a hálózati szegmenst. Javasoljuk, hogy a TCP-t használja a figyeléshez. Az ICMP használata olyan esetekben, amikor nem használhatja a TCP-t, például amikor: 
>
> - Windows ügyfélalapú csomópontokat használ, mivel a TCP nyers szoftvercsatornák nem engedélyezettek a Windows-ügyfelekben.
> - A hálózati tűzfal vagy az NSG blokkolja a TCP-t.
> - Nem tudod, hogy kell átváltani a protokollt.

Ha az ICMP központi telepítés során történő használatát választotta, az alapértelmezett figyelési szabály módosításával bármikor átválthat a TCP-re.

1.  >Nyissa meg a > **Monitor** > **Hálózatteljesítmény-figyelő** **konfigurálása**figyelő t. **Network Performance** Ezután válassza **az Alapértelmezett szabály lehetőséget.** 
2. Görgessen a **Protokoll** szakaszhoz, és válassza ki a használni kívánt protokollt. 
3. A beállítás alkalmazásához válassza a **Mentés** gombot. 

Még ha az alapértelmezett szabály egy adott protokollt is használ, új szabályokat hozhat létre egy másik protokollal. Akár olyan szabályok at is létrehozhat, amelyekben egyes szabályok az ICMP-t, míg mások a TCP-t használják. 

## <a name="walkthrough"></a>Útmutatás 

Most nézd meg egy egyszerű vizsgálatot a kiváltó oka egy egészségügyi esemény.

A megoldás irányítópultján egy állapotesemény azt mutatja, hogy a hálózati kapcsolat nem megfelelő állapotú. A probléma kivizsgálásához jelölje ki a **figyelt hálózati kapcsolatok** csempét.

A leásás lapján látható, hogy a **DMZ2-DMZ1** hálózati kapcsolat nem kifogástalan. Válassza a Hálózati **kapcsolat alhálózati kapcsolatainak megtekintése** lehetőséget. 


A leásás lap a **DMZ2-DMZ1** hálózati kapcsolat összes alhálózati kapcsolatát mutatja. Mindkét alhálózati kapcsolat esetében a késés átlépte a küszöbértéket, ami a hálózati kapcsolatot nem megfelelő állapotba hozza. A két alhálózati kapcsolat késési trendjeit is láthatja. A diagram időválasztó vezérlőjének használatával a kívánt időtartományra összpontosíthat. Láthatja a napszakot, amikor a késés elérte a csúcsát. A probléma kivizsgálásához keresse meg később a naplókat ebben az időszakban. Válassza **a Csomópontcsatolások megtekintése** lehetőséget a további részletezéshez. 
 
 ![Alhálózati hivatkozások lap](media/network-performance-monitor-performance-monitor/subnetwork-links.png) 

Az előző oldalhoz hasonlóan az adott alhálózati kapcsolat leásási lapja is felsorolja az alkotó csomópontcsatolásokat. Itt is végrehajthat hasonló műveleteket, mint az előző lépésben. Válassza **a Topológia megtekintése** lehetőséget a két csomópont közötti topológia megtekintéséhez. 
 
 ![Csomóponthivatkozások lap](media/network-performance-monitor-performance-monitor/node-links.png) 

A két kijelölt csomópont közötti összes útvonal a topológiatérképen lesz ábrázolva. A topológiatérképen két csomópont közötti útvonalak ugrásról ugrásra történő topológiáját is megjelenítheti. Ez ad egy világos képet, hogy hány útvonal létezik a két csomópont között, és milyen útvonalakat az adatcsomagok venni. A hálózati teljesítmény szűk keresztmetszetei piros színnel jelennek meg. A hibás hálózati kapcsolat vagy hibás hálózati eszköz megkereséséhez tekintse meg a topológiatérkép piros elemeit. 

 ![Topológia irányítópult topológiai térképpel](media/network-performance-monitor-performance-monitor/topology-dashboard.png) 

Áttekintheti a veszteséget, a késést és az ugrások számát az egyes útvonalakon a **Művelet** ablaktáblán. A görgetősáv segítségével megtekintheti a nem megfelelő elérési utak részleteit. A szűrők segítségével jelölje ki a nem megfelelő állapotú ugrással rendelkező görbéket, hogy csak a kijelölt görbék topológiája legyen ábrázolva. A topológiatérkép nagyításához vagy kicsinyítéséhez használja az egérgörgőt. 

Az alábbi képen a hálózat adott szakaszának problémás területeinek kiváltó oka a piros elérési utakban és ugrásokban jelenik meg. Válasszon ki egy csomópontot a topológialeképezésben a csomópont tulajdonságainak felfedéséhez, amely tartalmazza a teljes tartománynn-t és az IP-címet. Az ugrás kiválasztása az ugrás IP-címét jeleníti meg. 
 
![Topológialeképezés kijelölt csomóponttulajdonságokkal](media/network-performance-monitor-performance-monitor/topology-dashboard-root-cause.png) 

## <a name="next-steps"></a>További lépések
A részletes hálózati teljesítményadatrekordok megtekintéséhez [keressen naplókat.](../../azure-monitor/log-query/log-query-overview.md)

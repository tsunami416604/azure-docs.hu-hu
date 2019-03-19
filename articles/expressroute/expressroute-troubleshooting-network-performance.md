---
title: 'Virtuálishálózat-teljesítményi hibák elhárítása: Azure | Microsoft Docs'
description: Ez az oldal nyújt az Azure hálózati kapcsolat teljesítmény tesztelése szabványosított módszerrel.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: 9ec310ffaa9d2bb297abde9341bf7b6c2dc763b4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995797"
---
# <a name="troubleshooting-network-performance"></a>A hálózati teljesítmény hibaelhárítása
## <a name="overview"></a>Áttekintés
Az Azure a helyszíni hálózat csatlakoztatása az Azure-stabil és gyors módon biztosítja. Módszerek, például Site-to-Site VPN és ExpressRoute sikeresen által használt ügyfelek kis és nagy arra az Azure-ban való futtatásához. De mi történik, ha a teljesítmény nem felel meg, az elvárás vagy a korábbi élmény? Ez a dokumentum segítségével szabványosíthatja a módon tesztelése és alapvető az adott környezethez.

Ez a dokumentum bemutatja, hogyan következetesen és egyszerűen tesztelheti hálózati késés és sávszélesség két gazdagép között. Ez a dokumentum tekintse meg az Azure-hálózatot, és segít elkülöníteni a probléma pontok néhány tanácsot is nyújt. A PowerShell-parancsfájlt és a tárgyalt eszközöket a hálózaton (mindkét végén a tesztelt hivatkozás) két gazdagépekre van szükségük. Egy gazdagépen kell lennie a Windows Server vagy az asztal, a másik lehet Windows vagy Linux. 

>[!NOTE]
>A módszer a hibaelhárítási, az eszközök és a használt módszerek olyan egyéni beállítások. Ez a dokumentum ismerteti, milyen gyakran igénybe megközelítés és eszközök. A módszer valószínűleg eltérőek lehetnek, semmit nem megfelelő az a probléma megoldására más megközelítést. Azonban ha nem rendelkezik egy bevált megoldást jelentenek, ez a dokumentum is elsajátíthatja létrehozása a saját módszereket, eszközök és beállítások a hálózati problémák elhárítását elérési útját.
>
>

## <a name="network-components"></a>Hálózati összetevők
Előtt hibaelhárítási, vizsgáljuk néhány gyakori használati és összetevőket. A hozzászólás biztosítja azt a teljes körű láncot, amely engedélyezi a kapcsolatot az Azure-ban az egyes összetevők áttérni.
[![1]][1]

A legmagasabb szinten I három fő hálózati útválasztási tartományok; leírása

- az Azure-hálózatot (a jobb oldali kék felhő)
- az internetről vagy WAN (a központban zöld felhő)
- a vállalati hálózaton (a bal oldali peach felhő)

A diagram megnézzük a jobbról balra, rövid időre az egyes összetevők vizsgáljuk:
 - **Virtuális gép** – lehet, hogy a több hálózati adapterrel, győződjön meg, hogy bármely statikus útvonalak, az alapértelmezett útvonalakat, és operációsrendszer-beállításokat küld és forgalmat fogadó úgy gondolja, hogy ez a módszer van. Minden egyes Virtuálisgép-Termékváltozat rendelkezik is, a sávszélesség-korlátozás. Kisebb Virtuálisgép-Termékváltozat használja, ha a forgalom korlátozott a sávszélesség elérhető a hálózati adaptert. Általában egy DS5v2 használata esetén a tesztelés (és majd törlése után végzett vizsgálat pénzt takaríthat meg), a virtuális gép elegendő sávszélesség biztosításához.
 - **Hálózati adapter** -tudnia a magánhálózati IP-cím, amely a hálózati adapter van rendelve.
 - **Hálózati adapter NSG** – hiba előfordulhat, hogy adott NSG-ket a hálózati adapterek szintjén a alkalmazni kell, győződjön meg arról, az NSG-szabálykészlet alkalmas az átadni kívánt forgalom. Ellenőrizze például, portok 5201 iPerf, az RDP a 3389-es, vagy a 22-es az SSH számára meg nyitva, hogy a teszt-forgalom áthaladását.
 - **Virtuális hálózat alhálózatához** – a hálózati Adaptert rendel egy adott alhálózatot, győződjön meg arról, hogy tudja, hogy melyik és a egy alhálózathoz társított.
 - **Alhálózati NSG** – ugyanúgy, mint a hálózati adapter, az NSG-k, az alhálózathoz is alkalmazhatók. Ellenőrizze, hogy az NSG-szabálykészlet az átadni kívánt forgalom megfelelő-e. (a forgalmat bejövő a hálózati adapterre, az alhálózati NSG vonatkozik, először, akkor a hálózati adapter NSG-t, ezzel szemben a virtuális gépről kimenő forgalmat a hálózati adapter NSG vonatkozik először és play kerül az alhálózati NSG-t).
 - **Alhálózat UDR** – felhasználó által megadott útvonalak irányíthatja a forgalmat (például egy tűzfal vagy terheléselosztó) egy köztes Ugrás. Ellenőrizze, hogy tudja, hogy van-e a forgalom és ha így ahol kerül, és mi a következő ugrás fog tenni a forgalmat egy udr-t. (például egy tűzfal sikerült átadni a forgalom egy része és más forgalom az azonos két gazdagép között).
 - **Átjáró-alhálózat és NSG / UDR** – csakúgy, mint a Virtuálisgép-alhálózatot, az átjáró-alhálózat NSG-ket és az udr-EK rendelkezhet. Győződjön meg arról, hogy tudja, ha azok vannak-e, illetve a forgalom a lettek milyen hatást.
 - **Virtuális hálózati átjáró (ExpressRoute)** -társviszony-létesítés (ExpressRoute) vagy a VPN engedélyezve van, ha nincsenek számos olyan beállítást, amelyek hatással lehetnek hogyan vagy, ha forgalmat útvonal. Ha több ExpressRoute-Kapcsolatcsoportok vagy csatlakoztatott virtuális hálózati átjáróhoz VPN-alagutat, ez a beállítás hatással van kapcsolat beállítást a kapcsolat súlyozási ismernie kell, és hatással van az elérési utat a forgalom vesz igénybe.
 - **Irányíthatja a szűrő** (nem látható) – egy útvonalszűrőhöz csak az expressroute-on Microsoft Peering vonatkozik, de fontos ellenőrizni, ha nem látja a várt útvonalakat a Microsoft-Peering. 

Ezen a ponton használ a hivatkozás WAN részét. Az útválasztási tartomány lehet a service provider, a vállalati WAN vagy az interneten. Számos útválasztók ugrásainak, technológiák és ezeket a hivatkozásokat az érintett vállalatok megnehezítheti némileg hibaelhárítása. Gyakran munka szabály ki az Azure és a vállalati hálózatok is először előtt lépésközt a vállalatok és az útválasztók ugrásainak a gyűjteményhez.

A fenti ábrán a bal szélen az a vállalati hálózathoz. A vállalat méretétől függően ez útválasztási tartomány lehet néhány hálózati eszközök között, és a WAN- vagy többrétegű a telephelyi/vállalati hálózatban.

Adja meg a járó összetettséget, ezek három különböző magas szintű hálózati környezetekben, gyakran optimális elindításához a szélén, és próbálja meg a jó teljesítmény ahol és amikor romlik valamely. Ez a megközelítés segíthet a három a probléma útválasztási tartományának azonosításához, és majd összpontosítson az adott környezetben a hibaelhárítást.

## <a name="tools"></a>Eszközök
A legtöbb hálózati probléma elemezhetők, és elkülönített alapvető eszközökkel, mint például a ping parancs és a traceroute használatával. Ritka, hogy meg kell, például a Wireshark-csomagjainak elemzése mélyen. Segítség a hibaelhárításhoz, hogy az Azure Connectivity Toolkit (AzureCT) célja a put egyes eszközök egy egyszerű csomag. Teljesítményteszteléshez szeretném, hogy iPerf és PSPing. iPerf egy gyakran használt eszköz, és a legtöbb operációs rendszeren futtatja. iPerf jó az alapszintű teljesítmény tesztek, és viszonylag egyszerűen használható. A PSPing SysInternals által fejlesztett ping-eszközt. A PSPing egyszerű módja egy is könnyen használható parancs végrehajtásához TCP és az ICMP pingelésre. Mindkét eszköz könnyen használható és "" egyszerűen által telepített a gazdagépen ahhoz a könyvtárhoz, a fájlok kiugrások.

Tudok már burkolt be mindezek eszközöket és módszereket, egy PowerShell modul (AzureCT), amely telepíthető és használható.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – az Azure Connectivity Toolkit
Két részből áll a AzureCT PowerShell-modul [rendelkezésre állási tesztelési] [ Availability Doc] és [Teljesítménytesztelés][Performance Doc]. Ez a dokumentum csak az érintett teljesítménytesztelésbe, így lehetővé teszi, hogy a két hivatkozás teljesítmény parancsokat a PowerShell-modult a hangsúlyt.

Ez az eszközkészlet teljesítményteszteléshez használandó három alapvető lépésből áll. 1.) telepítse a PowerShell-modult, 2.) telepítse az alkalmazásokat támogató iPerf és a PSPing 3) futtassa a teljesítményteszt.

1. A PowerShell-modul telepítése

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Ez a parancs letölti a PowerShell-modult, és helyileg telepíti azt.

2. A támogató alkalmazások telepítése
    ```powershell
    Install-LinkPerformance
    ```
    A AzureCT parancs telepíti egy új könyvtárban "C:\ACTTools" iPerf és a PSPing, a Windows tűzfal portjait, engedélyezi az ICMP Használatát, és a portot 5201 (iPerf) forgalmat is megnyílik.

3. A vizsgálat futtatása

    Először a távoli gazdagépen kell telepíteni és iPerf kiszolgáló módban fusson. A távoli kiszolgáló figyel vagy 3389 (RDP for Windows) vagy a 22-es (az SSH Linux rendszeren) és a forgalom engedélyezéséhez a portot a iPerf 5201 is biztosítják. Ha a távoli állomás windows, a AzureCT telepítse, és futtassa az Install-LinkPerformance parancsot iPerf és iPerf sikeresen kiszolgáló módban elindításához szükséges tűzfalszabályok beállítása. 
    
    Ha a távoli gép készen áll, a helyi számítógépen nyissa meg a Powershellt, és a teszt elindításához:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Ez a parancs futtatható egyidejű terheléselosztást és a sávszélesség-kapacitást becsült várakozási ideje tesztek egy sorozatát, és a hálózati kapcsolat késését.

4. Tekintse át a kimenetet a tesztek

    A PowerShell-kimeneti formátum hasonlóan néz ki:

    [![4]][4]

    Részletes eredményeit a iPerf és a PSPing tesztek vannak az egyes szöveges fájlok a AzureCT eszközök könyvtár: "C:\ACTTools."

## <a name="troubleshooting"></a>Hibaelhárítás
Ha nem lehetővé teszi a teljesítményteszt várt eredmény foglalkozhatunk azzal, hogy miért kell egy progresszív részletes folyamat. Az elérési utat az összetevők számát tekintve a rendszerezett megközelítést általában egy gyorsabb útvonalat biztosít megoldási lépésközt körül, és a potenciálisan feleslegesen végzett a azonos tesztelés többször, mint.

>[!NOTE]
>Itt a forgatókönyv egy teljesítményprobléma, nem kapcsolódási probléma. A lépések eltérő, ha a forgalom egyáltalán nem passing lenne.
>
>

Először is kihívást jelentenek az előfeltételek. Az az elvárás ésszerű? Például ha van egy 1 – GB/s ExpressRoute-kapcsolatcsoport és a késés 100 ms, várható el a forgalmat a TCP teljesítménybeli jellemzői az nagy késésű kapcsolatokon keresztül megadott teljes 1 GB/s sebességű. Tekintse meg a [szakasz hivatkozik](#references) a további, a teljesítmény feltételezéseket.

Ezután I javasoljuk a szélén útválasztási tartományok között, és próbálja meg a problémát az egyetlen jelentős útválasztási tartomány; elkülönítése a vállalati hálózathoz, a WAN hálózaton vagy az Azure-hálózatot. Emberek gyakran az elérési út "fekete dobozhoz" vétkesség közben a fekete dobozhoz blaming az egyszerű, jelentősen késhet megoldás különösen, ha a probléma ténylegesen területen, hogy rendelkezik-e képesség a módosításokat. Győződjön meg arról, hogy a szolgáltató vagy az Internetszolgáltatótól leadása előtt hajtsa végre a megfelelő gondossággal.

A fő útválasztási tartomány, amely úgy tűnik, hogy tartalmazzák a probléma azonosítása után létre kell hoznia egy diagram a terület az adott. Bemutató videó, a Jegyzettömb vagy Visio diagram formájában vagy egy tényleges "élesben térkép" biztosít, hogy egy további elkülönítése módszeres megközelítést a problémát. Tesztelési pontok tervezéséhez és frissítheti a térkép területére, vagy tájékozódjon részletesebben a tesztelési előrehaladtával törli.

Most, hogy egy diagram, elindítása érdekében osztani a hálózati szegmensek és a probléma szűkítéséhez. Ismerje meg, ahol működik, ahol nem támogatja. A tesztelési pontok le a problémát okozó összetevőt elkülönítése tartsa kerül át.

Emellett ne felejtse el más réteg az OSI-modell meg. Egyszerű összpontosítani, a hálózati és biztonsági 1 – 3 (fizikai, az adatok és a hálózati réteg), de a problémák is fel a 7. rétegbeli az alkalmazásréteg. Egy megnyitott szem előtt tartani, és ellenőrizze a feltételezéseket.

## <a name="advanced-expressroute-troubleshooting"></a>Speciális ExpressRoute-hibaelhárítási
Ha nem biztos abban, ahol a peremhálózaton, a felhő ténylegesen van, az Azure-összetevőket elkülönítése kihívást jelenthet. Az ExpressRoute használata esetén az Edge-ben a Microsoft vállalati peremhálózati (MSEE) nevű hálózati összetevő. **Ha az ExpressRoute-tal**, az MSEE, forduljon a Microsoft hálózatához, valamint az a Microsoft-hálózatot elhagyó utolsó Ugrás az első pontot. Egy kapcsolat objektumot hoz létre a virtuális hálózati átjáró és az ExpressRoute-kapcsolatcsoport között, ha ténylegesen hajt végre egy kapcsolatot a egyet msee-hez. Az MSEE FELISMERVE, az első vagy utolsó ugrás (attól függően, melyik irányba fog) elengedhetetlen az Azure hálózati problémák vagy elszigetelése igazolja, hogy a probléma van az Azure-ban, vagy további aktiválásához a WAN hálózaton vagy a vállalati hálózathoz. 

[![2]][2]

>[!NOTE]
> Figyelje meg, hogy az MSEE nem az Azure-felhőben. Az ExpressRoute valójában a peremhálózaton, a Microsoft network valójában nem az Azure-ban. Miután csatlakozott az expressroute használatával egy egyet msee-hez, a Microsoft hálózatához csatlakozik, onnan majd nyissa meg a felhőszolgáltatások, például az Office 365 (a Microsoft-Peering) vagy Azure (a privát és/vagy a Microsoft Peering) bármelyikét.
>
>

Ha két virtuális hálózat (virtuális hálózatok A és B a diagram) vannak csatlakoztatva a **ugyanazon** ExpressRoute-kapcsolatcsoport hajthat végre az teszteken elkülönítése a probléma az Azure-ban (vagy igazolnia nem szerepel az Azure)
 
### <a name="test-plan"></a>Tesztterv
1. Futtassa a Get-LinkPerformance tesztet a VM1, VM2 és között. Ez a vizsgálat bepillantást enged a, ha a probléma a helyi, akár nem. Ez a vizsgálat elfogadható késés és sávszélesség-eredmények állít elő, ha a helyi virtuális hálózatok közötti hálózati jelölheti meg, a megfelelő választás.
2. Feltéve, hogy a helyi virtuális hálózatok közötti forgalom mindig jó, futtassa a Get-LinkPerformance tesztet VM1 és vm3 virtuális gép között. Ez a vizsgálat gyakorolja a kapcsolatot az MSEE lefelé, és vissza az Azure-bA a Microsoft hálózatán keresztül. Ez a vizsgálat elfogadható késés és sávszélesség-eredmények állít elő, ha jelölheti meg az Azure-hálózatot, megfelelő választás.
3. Azure kiderül, hogy a rendszer, ha a vállalati hálózaton is hajtsa végre a tesztek hasonló sorrendben. Amely szintén teszt is, ha, ideje a szolgáltató vagy az Internetszolgáltató diagnosztizálhatja a WAN-kapcsolaton. Példa: Ez a vizsgálat futtatása a két fiókirodákban, vagy az ügyfélszolgálat és a egy adatközponti kiszolgáló között. Attól függően, mi tesztelt, keresse meg a végpont (kiszolgálók, számítógépek, stb.), amely kísérletezhet az elérési út.

>[!IMPORTANT]
> Rendkívül fontos, hogy minden teszt jelölje meg a teszt időpontja és az eredményt egy közös helyen (szeretném, a OneNote-ban vagy az Excel). Minden egyes tesztfuttatás azonos kimeneti kell rendelkeznie, így a létrejövő adatok összehasonlíthasson tesztek, és nem rendelkezik "lyuk" az adatok. Több teszt egységességének használom a AzureCT hibaelhárítási elsődleges oka. A Magic Quadrant nem a pontos terhelés esetekben a futtatási, hanem a *Magic Quadrant* jelenik meg, hogy van egy *konzisztens teszt- és kimeneti* minden tesztből. Az idő rögzítése, és egységes minden egy alkalommal kellene különösen akkor hasznos, ha később találja, hogy időnként-e a problémát. Az adatgyűjtés meghozni odafigyelést, és el kell kerülni a órányi időt (megtanultam rögzített így sok éve) ugyanezeket a forgatókönyveket.
>
>

## <a name="the-problem-is-isolated-now-what"></a>A problémát el különítve, hogyan tovább?
Minél többet különítheti el a problémát a megoldása érdekében azonban gyakran eléri a pont, ahol már nem válthat mélyebb vagy további a hibák elhárításában. Példa: az útválasztók ugrásainak Európa keresztül tart-szolgáltató között megjelenik a hivatkozás, de az elvárt elérési úton érhető el minden Ázsia. Ez a pont akkor, ha a, az elérhető segítséget. Függ az útválasztási tartomány, elkülönített, a problémát, vagy még jobb, ha adott összetevőt csak szűkítésére tudja, akik van.

Vállalati hálózati problémák esetén a belső informatikai osztályukhoz vagy a támogató (amely lehet a hardver gyártója) a hálózati szolgáltató előfordulhat az eszközkonfiguráció vagy hardver javítása érdekében.

A WAN-on a vizsgálati eredmények osztanak meg a szolgáltató vagy az Internetszolgáltató előfordulhat, hogy biztosíthatja azok elindult, és elkerülheti a kiterjedő néhány már tesztelt azonos platform számára. Azonban nem lehet offended szeretné magukat az eredmények ellenőrzése. "Megbízható, de ellenőrizze" esetén egy jó mottója hibaelhárítási mások által jelentett eredményei alapján.

Az Azure-ral, miután a problémát a lehető legtöbb részletet tudja, elkülönítve, ideje tekintse át a [Azure Network dokumentációja] [ Network Docs] majd ha továbbra is szükséges, és [nyisson egy támogatási jegyet][Ticket Link].

## <a name="references"></a>Referencia
### <a name="latencybandwidth-expectations"></a>Késés és sávszélesség verziójával kapcsolatos elvárások
>[!TIP]
> Földrajzi késés (mérföld vagy adja meg kilométerben) a tesztelt végpontok között az messze a legnagyobb komponens késés. Nincs érintett berendezések késleltetés (fizikai és virtuális összetevők, útválasztók ugrásainak, stb. száma), míg a földrajzi bizonyult a legnagyobb összetevője összesített késés WAN-kapcsolatok esetén. Is fontos tudni, hogy a távolságot a távolsága a fiber futtatása nem a lineáris vagy közúti térképes távolság. Ez a távolság rendkívül nehéz bármely pontosságú beolvasása. Ennek eredményeképpen I általában a város távolság Számológép használata az internetes és tudja, hogy ez a módszer egy nagymértékben pontatlan mérték, de elegendő beállításához egy általános elvárás.
>
>

Egy ExpressRoute-telepítő Seattle, Washington, az USA területén belül van. Az alábbi táblázatban láthatók a késés és sávszélesség böngészőbővítményének tesztelésen, különböző Azure-helyen. Tudok már becsült földrajzi minden teszt végéig a közötti távolság.

Beállítások ellenőrzése:
 - Egy fizikai kiszolgálón futó Windows Server 2016 a 10 GB/s sávszélességű hálózati Adapterhez, egy ExpressRoute-kapcsolatcsoporthoz csatlakozik.
 - Egy 10Gbps prémium szintű ExpressRoute-kapcsolatcsoport a privát társviszony-létesítés engedélyezve van az azonosított helyen.
 - Egy Azure Vnethez a az UltraPerformance átjáró a megadott régióban.
 - DS5v2 rendszerű virtuális gép Windows Server 2016-ban a virtuális hálózaton. A virtuális gép volt tartományon kívüli csatlakoztatott, sestaveno az alapértelmezett (nincs optimalizálási vagy a Testreszabás) az Azure lemezkép AzureCT telepítve.
 - Minden teszt volt a AzureCT Get-LinkPerformance parancsot használja egy 5 perces terhelési teszt a hat tesztelések mindegyikéhez. Példa:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Az adatok az adatfolyam minden teszt kellett a terhelés, a helyszíni fizikai kiszolgáló (iPerf ügyfél Budapesten található), akár az Azure virtuális gép (iPerf server felsorolt Azure-régióban) áramlanak.
 - A "Késési" oszlopadatok származik, a nem terhelési teszt (egy TCP késés vizsgálat futtatása iPerf nélkül).
 - A "Maximális sávszélesség" oszlopadatok származik, a 16 TCP folyamat terhelési teszt ablak 1 MB-os mérettel.

[![3]][3]

### <a name="latencybandwidth-results"></a>Késés és sávszélesség-eredmények
>[!IMPORTANT]
> Ezek a számok csak általános referenciaként szolgálnak. Számos tényező befolyásolja a késést, és ezeket az értékeket, amelyek közül általában konzisztens idővel feltételek Azure-ban vagy a szolgáltató hálózaton belül is küldhetnek forgalmat különböző elérési útján bármikor, így a késés és sávszélesség érintheti. Ezek a módosítások hatásainak általában jelentős eltérések nem eredményez.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Hely|Azure<br/>Régió|Becsült<br/>Távolságskála (km-re)|Késés|1 munkamenet<br/>Bandwidth|Maximum<br/>Bandwidth|
| Seattle | USA nyugati régiója, 2.        |    191 km |   5 ms | 262.0 MB/s |  3.74 Gbits/mp |
| Seattle | USA nyugati régiója          |  1,094 km-re |  18 ms |  82.3 MB/s |  3.70 Gbits/mp |
| Seattle | USA középső régiója       |  2,357 km-re |  40 ms |  38.8 MB/s |  2.55 Gbits/mp |
| Seattle | USA déli középső régiója |  2,877 km-re |  51 ms |  30.6 MB/s |  2.49 Gbits/mp |
| Seattle | USA északi középső régiója |  2,792 km-re |  55 ms |  27.7 MB/s |  2.19 Gbits/mp |
| Seattle | USA 2. keleti régiója        |  3,769 km-re |  73 ms |  21.3 MB/s |  1.79 Gbits/mp |
| Seattle | USA keleti régiója          |  3,699 km-re |  74 ms |  21.1 MB/s |  1.78 Gbits/mp |
| Seattle | Kelet-Japán       |  7,705 km-re | 106 ms |  14.6 MB/s |  1,22 Gbits/mp |
| Seattle | Az Egyesült Királyság déli régiója         |  7,708 km-re | 146 ms |  10.6 MB/s |   896 MB/s |
| Seattle | Nyugat-Európa      |  7,834 km-re | 153 ms |  10.2 MB/s |   761 MB/s |
| Seattle | Kelet-Ausztrália   | 12,484 km-re | 165 ms |   9.4 MB/s |   794 MB/s |
| Seattle | Délkelet-Ázsia   | 12,989 km-re | 170 ms |   9.2 MB/s |   756 MB/s |
| Seattle | Dél-Brazília *   | 10,930 km-re | 189 ms |   8.2 MB/s |   699 MB/s |
| Seattle | Dél-India      | 12,918 km-re | 202 ms |   7.7 MB/s |   634 MB/s |

\* A késés, Brazília jó példa, ahol lineáris távolság jelentősen eltér a fiber távolság futtassa. Szeretné, hogy a késés szerepelnie kell a hálózatok 160 MS, de ténylegesen 189 MS várt. Ez a különbség az elvárás elleni utalhat hálózati probléma valahol, de valószínű, hogy futnak-e a fiber Brazília egyenes halad és tartalmaz egy extra 1000 km-re vagy utazási hozhatja a Seattle, Brazília, így.

## <a name="next-steps"></a>További lépések
1. Az Azure Connectivity Toolkit letöltése a githubról [https://aka.ms/AzCT][ACT]
2. Kövesse az utasításokat [hivatkozásra a Teljesítménytesztelés][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "azure hálózati összetevők"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute-hibaelhárítási"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Teljesítményoptimalizált tesztkörnyezetben"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell kimenete"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT

---
title: "Hibaelhárítás az Azure-beli virtuális hálózat teljesítmény |} Microsoft Docs"
description: "Ezen a lapon Azure hálózati kapcsolat teljesítmény tesztelése szabványosított módszert biztosít."
services: expressroute
documentationcenter: na
author: tracsman
manager: rossort
editor: 
ms.assetid: 
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/20/2017
ms.author: jonor
ms.openlocfilehash: 56f011632a2aa3ef0632efd5ace472c0fc79a329
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="troubleshooting-network-performance"></a>A hálózati teljesítmény hibaelhárítása
## <a name="overview"></a>Áttekintés
Azure csatlakozni a helyi hálózatról Azure stabil és gyors módot biztosít. Módszerek, például telephelyek közötti VPN és ExpressRoute sikeresen használt ügyfelek nagy- és a vállalatok számára az Azure-ban. De mi történik, ha a teljesítmény nem felel meg a várt értéket vagy előzetes tapasztalata? Ez a dokumentum segítségével szabványosítására tesztelése módja és alapvető az adott környezethez.

Ez a dokumentum bemutatja, hogyan könnyen és következetesen tesztelheti hálózati késés és a sávszélesség két állomások között. Ez a dokumentum néhány tanácsot is tekintse meg az Azure-hálózatot, és segítenek elkülöníteni probléma pontok módszereket biztosít. A PowerShell-parancsfájlt és a tárgyalt eszközök igényel a két állomás a hálózaton (két végén a tesztelt hivatkozás). Egy olyan gazdagépet kell lennie a Windows Server vagy az asztal, a másik Windows vagy Linux lehet. 

>[!NOTE]
>Hibaelhárítási megközelítést, az eszközök és a használt módszerek olyan egyéni beállítások. Ez a dokumentum ismerteti a megközelítést és eszközök gyakran igénybe. A megközelítésre valószínűleg eltérőek, nincs szükség a probléma elhárításához különböző szempontok gond. Azonban ha nem rendelkezik egy meglévő módszert is, ez a dokumentum is megismerheti az útvonalban összeállítása a saját módszereket, eszközök és beállítások a hálózati problémák elhárítását.
>
>

## <a name="network-components"></a>Hálózati összetevők
A hibaelhárítás digging, mielőtt most tárgyalják néhány gyakori használati és összetevők. Az ismertető biztosítja azt a most gondolat a végpont lánc, amely lehetővé teszi, hogy a kapcsolat az Azure-ban minden egyes összetevő.
[![1]][1]

A legmagasabb szintjén I ismertetik három fő hálózati útválasztási tartományok;

- az Azure-hálózatot (a jobb oldali kék felhő)
- az internetről vagy WAN (közepén zöld felhő)
- a vállalati hálózaton (a bal oldali peach felhő)

Röviden minden egyes összetevő most megnézi a diagram jobbról balra, ismertetik:
 - **Virtuális gép** - lehet, hogy több hálózati adapter, győződjön meg arról, bármely statikus útvonal, az alapértelmezett útvonalak és operációsrendszer-beállításokat küld és forgalom fogadását úgy gondolja, hogy azt módja van. Minden virtuális gép SKU is, a sávszélesség-korlátozás van. Egy kisebb méretű SKU használata, a forgalom korlátozza a sávszélesség álljon rendelkezésre, a hálózati adapternek. Általában egy DS5v2 használni a teszteléshez (és majd egyszer végzett tesztelés kevesebbet költeni a Törlés) a virtuális gép a megfelelő sávszélesség biztosításához.
 - **A hálózati adapter** -tudnia, amely hozzá van rendelve a hálózati adapter magánhálózati IP-címe.
 - **A hálózati adapter NSG** – nem lehet, hogy adott NSG-ket a hálózati adapter szinten alkalmazott kell, győződjön meg arról az NSG-szabálykészlet megfelelő próbál továbbítani a forgalmat. Például győződjön meg róla portok 5201 iPerf, az RDP a 3389-es, vagy nyissa meg a teszt forgalom való 22-es az SSH.
 - **VNet Subnet** – a hálózati adapter van rendelve egy bizonyos alhálózat, győződjön meg arról, ismernie kell a szabályokat, és csak egyet kapcsolódó alhálózaton.
 - **Alhálózati NSG** – ugyanúgy, mint a hálózati adapter, az NSG-ket alkalmazni lehet, valamint az alhálózatot. Ellenőrizze, hogy az NSG-szabálykészlet megfelelő próbál továbbítani a forgalmat. (a forgalom bejövő a hálózati adapterhez, az alhálózatot, NSG először vonatkozik, akkor a hálózati adapter NSG, ezzel szemben a virtuális gép kimenő forgalmat a hálózati adapter NSG vonatkozik először és az alhálózati NSG kerülnek play).
 - **Alhálózati UDR** -felhasználó által megadott útvonalak forgalmat egy köztes Ugrás (például egy tűzfal vagy a terheléselosztó) lehet irányítani. Gondoskodjon arról, hogy tudja, hogy van-e a forgalom érvényes, és ha így irányától, és mi a következő ugrás fog tenni a forgalom egy UDR. (például egy tűzfal sikerült egyes csomagokat és a megtagadási más a azonos két gazdagépek között forgalmat).
 - **Átjáró alhálózati / NSG / UDR** -csakúgy, mint a Virtuálisgép-alhálózatot, az átjáró alhálózatának NSG-ket és udr-EK rendelkezhet. Győződjön meg arról, ha azok vannak-e, illetve a forgalom lettek milyen hatások tudja.
 - **VNet-átjáró (ExpressRoute)** -után társviszony-létesítés (ExpressRoute) vagy a VPN engedélyezve van, nincs-e számos olyan beállítást, amelyek hatással lehetnek hogyan vagy, ha forgalmi útvonalak. Ha több ExpressRoute-Kapcsolatcsoportok vagy VPN-alagutat az azonos virtuális hálózaton Gatewayhez csatlakozó, ez a beállítás hatással van a kapcsolat beállítást a kapcsolat súlyozási szabályokkal tisztában kell lennie, és hatással van az elérési út a forgalom időt vesz igénybe.
 - **Útvonal-szűrő** (nincs ábrázolva) - útvonal szűrő csak a ExpressRoute Microsoft Peering vonatkozik, de fontos ellenőrizni, ha is nem lát a várt útvonalakat a Microsoft Peering. 

Ezen a ponton még a a hivatkozás WAN részét. Az útválasztási tartomány lehet a szolgáltató, a vállalati WAN vagy az interneten. Sok ugrások, -technológiák és ezek a hivatkozások szerepet játszó vállalatok teheti némileg hibaelhárítás nehezen végezhető. Gyakran munka Azure és a vállalati hálózatok is szabály először vállalatok és az útválasztók ugrásainak a gyűjteménybe Ugrás előtt.

A fenti ábrán bal van a vállalati hálózathoz. A vállalat méretétől függően ez útválasztási tartomány lehet néhány olyan hálózati eszközt, és a WAN-KAPCSOLATON, vagy több réteg központ/vállalati hálózati eszközök közötti.

Megadott három különböző magas szintű hálózati környezetben a összetett szolgáltatásokkal, akkor gyakran optimális maradt a szélén elindításához és esetén a jó teljesítmény, és ahol csökkenti a megjelenítése. Ezt a módszert használja a három probléma útválasztási tartományának azonosításához, és ezután összpontosítson, az adott környezet hibaelhárítási segítséget.

## <a name="tools"></a>Eszközök
A legtöbb hálózati probléma elemzése és elkülönített, például a ping parancs és a traceroute alapvető eszközökkel. Ritka, hogy meg kell vizsgálni, Wireshark például egy csomagjainak elemzése mély. Hibaelhárítás érdekében az Azure-kapcsolat Toolkit (AzureCT), amelyre az egyes eszközök egy egyszerű csomag jött létre. Teljesítmény teszteléshez tetszik iPerf és PSPing használatát. iPerf egy gyakran használt eszköz, és a legtöbb operációs rendszeren futtatja. iPerf ideális alapvető teljesítményének vizsgálatokat, és viszonylag könnyen használható. A PSPing SysInternals által fejlesztett ping-eszközt. A PSPing egyszerű módja ICMP- és TCP pingelésre egy is könnyen használható parancs végrehajtásához. Mindkét eszköz könnyű és "" egyszerűen által telepített Másolás a fájlokat egy könyvtárat a gazdagépen.

Szeretnék már összes ezekkel az eszközökkel és módszerek törik a azokat egy PowerShell-modul (AzureCT) telepítése és használata.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – az Azure kapcsolat eszközkészlet
A AzureCT PowerShell modul két részből áll [elérhetőség tesztelési] [ Availability Doc] és [Teljesítménytesztelés][Performance Doc]. Ez a dokumentum csak az érintett teljesítményének tesztelése, így lehetővé teszi, hogy a két hivatkozás teljesítmény parancsokat a PowerShell modul helyezi a hangsúlyt.

Ez az eszközkészlet a teljesítmény teszteléshez használni három alapvető lépésből áll. 1.) telepítse a PowerShell-modult, 2.) telepítse a támogató alkalmazások iPerf és PSPing 3) futtatja.

1. A PowerShell-modul telepítése

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Ez a parancs letölti a PowerShell-modult, és helyileg telepíti azt.

2. A támogató alkalmazások telepítése
    ```powershell
    Install-LinkPerformance
    ```
    A AzureCT parancs telepíti az új könyvtár "C:\ACTTools" iPerf és PSPing, is nyílik meg a Windows tűzfal portjait, engedélyezi az ICMP Használatát, és porttal, 5201 (iPerf) forgalom.

3. A vizsgálat futtatása

    Először a távoli állomáson levő kell telepítése és futtatása iPerf kiszolgáló módban. Bizonyosodjon meg arról, a távoli kiszolgáló figyel a következőn vagy 3389-es (RDP for Windows) vagy a 22-(SSH Linux), valamint átengedi a forgalmat a portot a iPerf 5201. Ha a távoli állomás windows, a AzureCT telepítése, és futtassa az Install-LinkPerformance parancsot iPerf és indításához szükséges iPerf módban sikeresen tűzfalszabályok beállítása. 
    
    Ha a távoli gép készen áll, a helyi számítógépen nyissa meg a Powershellt, és a teszt elindításához:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Ez a parancs egyidejű betöltés és késés tesztek használatával megbecsülheti a sávszélesség-kapacitása és a hálózati kapcsolat a késési futtat.

4. Tekintse át a kimenetet a tesztet

    A PowerShell kimeneti formátum a következőhöz hasonló:

    [![4]][4]

    A részletes a iPerf és PSPing tesztek eredményei az egyes szövegfájlok a AzureCT eszközök könyvtár: "C:\ACTTools."

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a vizsgálat nem nem próbálkozik kívánt eredmény elérése érdekében, hogy miért tudja legyen a progresszív részletes folyamat. Az elérési út az összetevők számát tekintve a rendszeres megközelítés általában kapcsolatot biztosít gyorsabb megoldás mint Ugrás körül, valamint potenciálisan feleslegesen arról, hogy az azonos tesztelés többször.

>[!NOTE]
>A forgatókönyv itt teljesítményprobléma, nem egy hálózati probléma. A lépések eltérő, ha a forgalom nem minden lenne.
>
>

Első lépésként kihívást jelentenek a feltételezéseket. A várt eredmény ésszerű van? Például egy 1-Gbps ExpressRoute-kapcsolatcsoportot és a késés 100 ms esetén várható el a teljes 1 GB/s a megadott TCP teljesítményétől nagy késleltetésű kapcsolaton keresztül lebonyolított forgalom. Tekintse meg a [szakasz hivatkozik](#references) a további a teljesítmény feltételezéseket.

A következő I maradt a szélén útválasztási tartományok közötti kezdési javasoljuk, majd próbálja meg különítse el a problémát az egyetlen jelentős útválasztási tartomány; a vállalati hálózathoz, a WAN hálózaton vagy az Azure-hálózatot. Személyek gyakran vétkesség az elérési út "fekete mezőt" közben a fekete mezőt blaming áll, akkor előfordulhat, hogy jelentősen késleltetés feloldási különösen akkor, ha a probléma valóban területen, hogy rendelkezik-e, hogy módosításokat. Győződjön meg arról, hogy a szolgáltató vagy az Internetszolgáltató átadás előtt hajtsa végre a megfelelő gondossággal.

Amennyiben úgy tűnik, hogy tartalmazza a hiba jelentős útválasztási tartomány állapította meg, érdemes létrehozni egy diagram terület az adott. Vagy egy Faliújság, a Jegyzettömb vagy a Visio, a diagram a tényleges "túlélésért folyó map" nyújt további izolátumának módszeres megközelítése engedélyezi a problémát. Tesztelési pontok tervezéséhez, és frissíti a térkép törli a tesztelési előrehaladtával mélyebb dig vagy területeket.

Most, hogy egy diagramot, indítsa el a hálózati felosztani szegmensek és a probléma szűkítéséhez. Ahol működik, ahol nem megállapítása. A tesztelési pontok különítheti el a hibát okozó összetevő le tartsa áthelyezését.

Is ne felejtse el megnézni, más rétegeiből a OSI-modell. Könnyen arra utalnak, a hálózati és rétegek 1-3 (fizikai, az adatok és a hálózati réteg), de a problémák is lehet másolatot réteg 7 az alkalmazási rétegre. Egy nyitott szem előtt tartani, és ellenőrizze a feltételezéseket.

## <a name="advanced-expressroute-troubleshooting"></a>Speciális ExpressRoute-hibaelhárítási
Ha nem biztos abban, hogy a felhő széle ténylegesen esetén, az Azure összetevők elkülönítése bonyolult lehet. ExpressRoute használatánál, a peremhálózati a Microsoft vállalati peremhálózati (MSEE) hálózati összetevőt. **Ha ExpressRoute**, a MSEE a Microsoft hálózati és az utolsó ugrás, így a Microsoft hálózati kapcsolódási első pontot. Objektumot létrehozni a virtuális hálózat átjáró és az ExpressRoute-kapcsolatcsoport között, ha ténylegesen hajt végre a kapcsolat a MSEE. A MSEE ismer fel, mert az első vagy utolsó ugrás (attól függően, melyik irányba fog) különösen fontos vagy Azure hálózati problémák azonosítása az Azure-ban a probléma igazolni, vagy további után a WAN hálózaton vagy a vállalati hálózathoz. 

[![2]][2]

>[!NOTE]
> Figyelje meg, hogy a MSEE nem Azure felhőben. ExpressRoute valójában a Microsoft hálózati ténylegesen nem az Azure-ban a szélén. Az ExpressRoute egy MSEE való kapcsolódás, után csatlakozik a Microsoft hálózati, ott majd lépjen a felhőszolgáltatások, például az Office 365 (a Microsoft Peering) vagy (a saját és/vagy a Microsoft Peering) Azure egyikének sem.
>
>

Ha két Vnetek (Vnetek A és B a diagramban) kapcsolódnak a **azonos** ExpressRoute-kapcsolatcsoportot hajthat végre egy sor tesztet különítse el a problémát az Azure-ban (vagy igazolnia nincs Azure-ban)
 
### <a name="test-plan"></a>Tesztelési terv
1. Futtassa a Get-LinkPerformance tesztet VM1 és vm2 virtuális gépnek között. Ez a vizsgálat a betekintést nyújt, ha a probléma nem helyi, vagy nem. Ez a vizsgálat elfogadható késésétől és a sávszélesség eredmények hoz létre, ha a helyi virtuális hálózat hálózati jelölheti meg, jó.
2. Feltéve, hogy a helyi virtuális hálózat akkor kimenő forgalomról helyes, a Get-LinkPerformance teszt futtatása VM1 és VM3 között. Ez a vizsgálat gyakorolja a kapcsolatot a MSEE és vissza az Azure a Microsoft a hálózaton keresztül. Ha ez a vizsgálat elfogadható késésétől és a sávszélesség-eredmények, jelölheti meg az Azure-hálózatot, jó.
3. Azure kiderül, hogy a rendszer, ha a vállalati hálózaton végezheti el a teszteket hasonló sorrendben. Amely is teszteli is, ha az az idő a szolgáltató vagy az Internetszolgáltató a WAN-kapcsolaton keresztül diagnosztizálásához használható. Példa: A teszt futtatása, két fiókirodákban, vagy a segélyszolgálat és egy adatközponti kiszolgáló között. Attól függően, hogy mi tesztel, található a végpont (kiszolgálók, számítógépek, stb.), amely élhet arról.

>[!IMPORTANT]
> Nagyon fontos, hogy minden teszt jelölje meg a teszt futtatásának időpontját és az eredményt (tetszik OneNote vagy Excel) közös helyen. Minden teszt futtatásakor kell azonos kimeneti, hasonlítsa össze az eredményül kapott adatok között tesztelési futtatják, és nem rendelkezik "lyuk" az adatok. Több teszt egységességének használni a AzureCT hibaelhárítási miatt. A Bűvös nem szerepel a pontos terheléselosztási forgatókönyveket futtatni, hanem a *magic* van arra, hogy jelenik meg egy *konzisztens teszt- és kimeneti* minden tesztből. Az idő rögzítése, és hogy azonos adatokat minden egy alkalommal különösen akkor hasznos, ha később találja, hogy időnként-e a problémát. Előre az adatgyűjtés odafigyelést, és elkerülheti a megismételt vizsgálat az azonos forgatókönyvek (I megtanulta, rögzített így sok éve) óra lesz.
>
>

## <a name="the-problem-is-isolated-now-what"></a>A probléma az elkülönített, hogyan tovább?
Minél több különítheti el a problémát a későbbiekben is segít, de gyakran eléri a pont, ahol nem léphet mélyebb vagy további a hibaelhárítás. Példa: az útválasztók ugrásainak keresztül Európa véve-szolgáltató között megjelenik a hivatkozás, de a várt elérési út minden Ázsia. Ez egy kell érheti el a Súgó. A problémát a egymástól el vannak különítve az útválasztási tartomány függ, vagy még jobb, ha szűkítheti az adott összetevő le, akik van.

A vállalati hálózati problémák a belső IT-részlegtől vagy a hálózat (amely lehet, hogy a hardver gyártója) támogató szolgáltatótól esetleg segítséget az eszközök konfigurációját vagy a hardver javítása.

Az a WAN-KAPCSOLATON a vizsgálati eredmények osztja meg a szolgáltató vagy az Internetszolgáltató segíthet azokat elindult, és elkerülheti a lefedik az azonos alapoktól már tesztelt némelyike. Azonban nem lehet offended szeretné magukat az eredményeket. "Megbízható, de ellenőrizze" esetén egy jó mottó hibaelhárítási mások által jelentett eredmények alapján.

Az Azure-ral, miután a problémát a legnagyobb részletességgel elvégezheti, elkülönítve tekintse át az idő a [Azure hálózati dokumentáció] [ Network Docs] , és ezután Ha továbbra is szükséges [támogatásijegymegnyitása][Ticket Link].

## <a name="references"></a>Referencia
### <a name="latencybandwidth-expectations"></a>Késés/sávszélesség verziójával kapcsolatos elvárások
>[!TIP]
> Földrajzi késés (miles vagy kilométerben) a tesztelést végpontok között része messze a legnagyobb késést. Nincs érintett berendezések késleltetés (virtuális és fizikai összetevők, ugrások száma stb.), miközben geográfiai bizonyult a teljes késést legnagyobb összetevője a WAN-kapcsolaton meghatározásakor. Fontos továbbá vegye figyelembe, hogy a távolság a távolsága a részszálas futtatása nem a lineáris vagy ütemterv Distance lehet. Ez a távolság nagyon nehéz bármely pontossággal beolvasása. Ennek eredményeképpen I általában a város távolság Számológép használata az interneten és, hogy ez a módszer egy nagymértékben pontatlan mérték, de elegendő beállításához egy általános várt értéket.
>
>

Egy ExpressRoute-telepítő a budapesti, Washington, az USA is jelent. Az alábbi táblázatban láthatók a késés és sávszélesség I látott tesztelése az Azure különböző helyekre. Szeretnék már becsült közötti minden a vizsgálat befejezésekor a földrajzi távolságot.

Beállítások ellenőrzése:
 - Fizikai kiszolgáló Windows Server 2016 egy 10 GB/s NIC ExpressRoute-kapcsolatcsoportot csatlakozik.
 - Egy 10 GB/s prémium ExpressRoute-kapcsolatcsoportot a magánhálózati társviszony-létesítés engedélyezve van, egy helyen.
 - A megadott régión belül egy UltraPerformance átjáróval Azure VNet.
 - Egy DS5v2 futó virtuális gép Windows Server 2016 a virtuális hálózat. A virtuális gép volt a tartományhoz nem csatlakoztatott, az alapértelmezett kép: Azure (nincs optimalizálási vagy testreszabása)-val készült AzureCT telepítve.
 - Minden teszt volt a AzureCT Get-LinkPerformance parancsot használja egy 5 perces terheléstesztet az egyes a hat tesztelési futtatják. Példa:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Az adatok áramlását a tesztelések kellett áramlanak a helyszíni fizikai kiszolgáló (iPerf ügyfél budapesti), akár az Azure virtuális Géphez (iPerf server felsorolt Azure-régióban) betöltése.
 - Az "Késés" oszlop adattípusa nem terheléstesztet (a TCP késés nélküli teszt iPerf fut) származik.
 - A "Maximális sávszélesség" oszlop adattípusa a 16 TCP folyamat terheléstesztet 1 MB-os ablak méretű származik.

[![3]][3]

### <a name="latencybandwidth-results"></a>Késés/sávszélesség eredmények
>[!IMPORTANT]
> Ezeket a számokat csak általános referenciaként szolgálnak. Számos tényező befolyásolja a késés, és ezek az értékek megegyeznek általában adott idő alatt, amíg Azure vagy a szolgáltatók hálózaton belüli küldhet forgalom elérési útja eltér keresztül bármikor, így a késleltetés és a sávszélesség érintheti. Ezek a változások eredő általában jelentős eltérések nem oldják.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Hely|Azure<br/>Régió|Becsült<br/>Távolság (km)|Késés|1 munkamenet<br/>Bandwidth|Maximum<br/>Bandwidth|
| Seattle | USA nyugati régiója, 2.        |    191 km |   5 ms | 262.0 MB/s |  3.74 Gbits/mp | 21
| Seattle | USA nyugati régiója          |  1,094 km |  18 ms |  82.3 MB/s |  3.70 Gbits/mp | 20
| Seattle | USA középső régiója       |  2,357 km |  40 ms |  38.8 MB/s |  2.55 Gbits/mp | 17
| Seattle | USA déli középső régiója |  2,877 km |  51 ms |  30.6 MB/s |  2.49 Gbits/mp | 19
| Seattle | USA északi középső régiója |  2,792 km |  55 ms |  27.7 MB/s |  2.19 Gbits/mp | 18
| Seattle | USA 2. keleti régiója        |  3,769 km |  73 ms |  21.3 MB/s |  1.79 Gbits/mp | 16
| Seattle | USA keleti régiója          |  3,699 km |  74 ms |  21.1 MB/s |  1.78 Gbits/mp | 15
| Seattle | Kelet-Japán       |  7,705 km | 106 ms |  14.6 MB/s |  1,22 Gbits/mp | 28
| Seattle | Az Egyesült Királyság déli régiója         |  7,708 km | 146 ms |  10.6 MB/s |   896 MB/s | 24
| Seattle | Nyugat-Európa      |  7,834 km | 153 ms |  10.2 MB/s |   761 MB/s | 23
| Seattle | Kelet-Ausztrália   | 12,484 km | 165 ms |   9.4 MB/s |   794 MB/s | 26
| Seattle | Délkelet-Ázsia   | 12,989 km | 170 ms |   9.2 MB/s |   756 MB/s | 25
| Seattle | Dél-Brazília *   | 10,930 km | 189 ms |   8.2 MB/s |   699 MB/s | 22
| Seattle | Dél-India      | 12,918 km | 202 ms |   7.7 MB/s |   634 MB/s | 27

\*A várakozási Brazília jó példa, ha lineáris távolság jelentősen eltér a részszálas távolság futtassa. A várakozási 160 ms pontos lenne, de ténylegesen 189 MS volna várt. Ezt a különbséget a várt eredmény elleni oka lehet hálózati hiba valahol, de a legvalószínűbb ok az, hogy fusson-e a fiber nem Ugrás brazíliai egyenes és egy extra 1000 km-es vagy ezt az beszerzése Budapest brazíliai utazás.

## <a name="next-steps"></a>További lépések
1. A Githubon: Azure kapcsolat eszközkészlet letöltésére [http://aka.ms/AzCT][ACT]
2. Kövesse az utasításokat [hivatkozás teljesítményének tesztelése][Performance Doc]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "azure hálózati összetevők"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute-hibaelhárítási"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "telj tesztkörnyezetben"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell kimenete"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index#pivot=services&panel=network
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: http://aka.ms/AzCT












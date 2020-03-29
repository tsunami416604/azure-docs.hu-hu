---
title: 'Hálózati kapcsolat teljesítményének hibaelhárítása: Azure'
description: Ez a lap az Azure hálózati kapcsolat teljesítményének tesztelésére szabványosítási módszert biztosít.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: bb68919fba731caa32dcca3f4c991b8881afc6f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74869646"
---
# <a name="troubleshooting-network-performance"></a>A hálózati teljesítmény hibaelhárítása
## <a name="overview"></a>Áttekintés
Az Azure stabil és gyors megoldást kínál az Azure helyszíni hálózatból történő eléréséhez. Az olyan megoldások segítségével, mint a helyek közötti VPN és az ExpressRoute, kisebb és nagyobb szervezetek is sikerrel futtathatják üzleti folyamataikat az Azure-ban. De mi történik, ha a teljesítmény nem felel meg az elvárásainak vagy a korábbi tapasztalatoknak? Ez a dokumentum segíthet egységesíteni az adott környezet tesztelésének és alapkonfigurációjának szabványosítását.

Ez a dokumentum bemutatja, hogyan tesztelheti egyszerűen és következetesen a hálózati késést és a sávszélességet két állomás között. Ez a dokumentum is néhány tanácsot ad az Azure-hálózat áttekintésével és a problémapontok elkülönítésével. A powershell-parancsfájl és a tárgyalt eszközök két állomást igényelnek a hálózaton (a tesztelt kapcsolat mindkét végén). Az egyik állomásnak Windows Server vagy Desktop kiszolgálónak kell lennie, a másik lehet Windows vagy Linux. 

>[!NOTE]
>A hibaelhárítás, az eszközök és az alkalmazott módszerek személyes preferenciái. Ez a dokumentum leírja a megközelítést és eszközöket gyakran veszi. A megközelítés valószínűleg eltérő, nincs semmi baj a különböző megközelítések problémamegoldás. Ha azonban nem rendelkezik megalapozott megközelítéssel, ez a dokumentum a hálózati problémák elhárításához szükséges saját módszerek, eszközök és beállítások létrehozásának útjára léphet.
>
>

## <a name="network-components"></a>Hálózati összetevők
Mielőtt beleássa magát a hibaelhárításba, beszéljünk meg néhány gyakori kifejezést és összetevőt. Ez a vitafórum biztosítja, hogy a végpontok közötti lánc minden olyan összetevőjére gondoljunk, amely lehetővé teszi a kapcsolatot az Azure-ban.
![1][1]

A legmagasabb szinten három fő hálózati útválasztási tartományt írok le;

- az Azure-hálózat (kék felhő a jobb oldalon)
- az internet vagy a WAN (zöld felhő a központban)
- a vállalati hálózat (barackfelhő a bal oldalon)

Ha jobbról balra tekintjük a diagramot, beszéljünk röviden az egyes összetevőkről:
 - **Virtuális gép** – Előfordulhat, hogy a kiszolgáló több hálózati adapterrel rendelkezik, biztosítja a statikus útvonalakat, az alapértelmezett útvonalakat és az operációs rendszer beállításait, amelyek úgy küldik és fogadják a forgalmat, ahogy ön gondolja. Emellett minden virtuális gép termékváltozat a sávszélesség-korlátozás. Ha egy kisebb virtuális gép termékváltozat, a forgalmat korlátozza a hálózati adapter számára elérhető sávszélesség. Én általában egy DS5v2 tesztelésére (majd törölje, ha kész a tesztelés pénzt takarít meg), hogy biztosítsák a megfelelő sávszélességet a virtuális gép.
 - **NIC** – Győződjön meg arról, hogy ismeri a privát IP-cím, amely a szóban forgó hálózati adapterhez van rendelve.
 - **NIC NSG** – Előfordulhat, hogy a hálózati adapter szintjén speciális NSG-k vannak alkalmazva, győződjön meg arról, hogy az NSG-szabálykészlet megfelelő a átadni kívánt forgalomhoz. Például győződjön meg arról, hogy az 5201-es vagy az rdp-hez 3389 vagy az SSH 22-es portjai nyitottak, hogy a tesztforgalom áthaladjon.
 - **Virtuálishálózat-alhálózat** – A hálózati adapter egy adott alhálózathoz van rendelve, győződjön meg arról, hogy melyik és az alhálózathoz társított szabályok.
 - **Alhálózati NSG** – A hálózati adapterhez hasonlóan az NSG-k is alkalmazhatók az alhálózaton. Győződjön meg arról, hogy az NSG szabálykészlet megfelelő a forgalom próbál átadni. (a hálózati adapterbe érkező forgalom esetében először az NSG alhálózat, majd a hálózati hálózati nsg vonatkozik, fordítva a virtuális gépről kimenő forgalom esetében a hálózati adapter NSG-je először, majd az alhálózati NSG kerül a játékba).
 - **Subnet UDR** – A felhasználó által definiált útvonalak irányíthatják a forgalmat egy köztes ugrás (például egy tűzfal vagy a terheléselosztó). Győződjön meg róla, hogy tudja, ha van egy UDR a helyén a forgalom, és ha igen, ha megy, és mi a következő ugrás fog tenni, hogy a forgalom. (például egy tűzfal átadhat bizonyos forgalmat, és megtagadhatja a két állomás közötti egyéb forgalmat).
 - **Átjáró alhálózat / NSG / UDR** - Csakúgy, mint a virtuális gép alhálózat, az átjáró alhálózat rendelkezhet NSG-k és UDRs. Győződjön meg róla, hogy tudja, ha ott vannak, és milyen hatással vannak a forgalom.
 - **VNet Gateway (ExpressRoute)** – Miután a társviszony-létesítés (ExpressRoute) vagy a VPN engedélyezve van, nincs sok beállítás, amely befolyásolhatja, hogyan és ha a forgalmi útvonalak. Ha több ExpressRoute-kapcsolatvagy VPN-alagút csatlakozik ugyanahhoz a virtuális hálózat-átjáróhoz, tisztában kell lennie a kapcsolat súlybeállításaival, mivel ez a beállítás befolyásolja a kapcsolatpreferenciát, és befolyásolja a forgalom elérési útját.
 - **Útvonalszűrő** (nem látható) – Az útvonalszűrő csak az ExpressRoute-alapú Microsoft társviszony-létesítésre vonatkozik, de kritikus fontosságú annak ellenőrzése, hogy nem látja-e a Microsoft társviszony-létesítéskor várt útvonalakat. 

Ezen a ponton a kapcsolat WAN részén vagy. Ez az útválasztási tartomány lehet a szolgáltató, a vállalati WAN vagy az internet. Sok komló, technológia és vállalat, amely részt vesz ezekkel a hivatkozásokkal, megnehezítheti a hibaelhárítást. Gyakran dolgozik azon, hogy először kizárja az Azure-t és a vállalati hálózatokat, mielőtt beleugrik ebbe a vállalat- és komlógyűjteménybe.

Az előző ábrán a bal szélen található a vállalati hálózat. A vállalat méretétől függően ez az útválasztási tartomány lehet néhány hálózati eszköz Ön és a WAN között, vagy több rétegnyi eszköz egy campus/vállalati hálózatban.

Tekintettel a három különböző magas szintű hálózati környezet összetettségére, gyakran optimális a széleken kezdeni, és megpróbálni megmutatni, hogy hol jó a teljesítmény, és hol romlik. Ez a megközelítés segíthet azonosítani a probléma útválasztási tartománya a három, majd a hibaelhárítás az adott környezetben.

## <a name="tools"></a>Eszközök
A legtöbb hálózati probléma elemezhető és elkülöníthető olyan alapvető eszközökkel, mint a ping és a traceroute. Ritka, hogy olyan mélyre kell menned, mint egy csomagelemzés, mint a Wireshark. A hibaelhárítás érdekében az Azure Connectivity Toolkit (AzureCT) úgy lett kifejlesztve, hogy ezen eszközök némelyikét egy egyszerű csomagba helyezze. A teljesítmény tesztelés, szeretem használni iPerf és PSPing. Az iPerf egy általánosan használt eszköz, amely a legtöbb operációs rendszeren fut. iPerf jó az alapvető teljesítmény tesztek és meglehetősen könnyen használható. PSPing egy ping eszköz által kifejlesztett SysInternals. PSPing egy egyszerű módja annak, hogy végre ICMP és TCP ping egy is könnyen használható parancsot. Mindkettő -ból ezek szerszámok van könnyűsúly és van " beiktatott" egyszerűen mellett megbirkózik a fájlokat -hoz egy címtár -on házigazda.

Ezeket az eszközöket és módszereket egy PowerShell-modulba (AzureCT) csomagoltam, amelyet telepíthet és használhat.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – az Azure Connectivity Toolkit
Az AzureCT PowerShell modul két [összetevőrendelkezésre állási tesztelésés][Availability Doc] [teljesítménytesztelés.][Performance Doc] Ez a dokumentum csak a teljesítményteszteléssel foglalkozik, így lehetővé teszi, hogy a PowerShell-modul két teljesítménykapcsolati parancsára összpontosítson.

Az eszközkészlet et a teljesítményteszteléshez három alapvető lépésben használhatja. 1) Telepítse a PowerShell modult, 2) Telepítse a támogató alkalmazásokat iPerf és PSPing 3) Futtassa a teljesítménytesztet.

1. A PowerShell-modul telepítése

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Ez a parancs letölti a PowerShell-modult, és helyileg telepíti azt.

2. A támogató alkalmazások telepítése
    ```powershell
    Install-LinkPerformance
    ```
    Ez az AzureCT parancs telepíti az iPerf-et és a PSPing-et egy új "C:\ACTTools" könyvtárba, megnyitja a Windows tűzfal portjait az ICMP és az 5201-es (iPerf) forgalom engedélyezéséhez.

3. A teljesítményteszt futtatása

    Először is, a távoli állomáson telepítenie kell és futtatnia kell az iPerf-et szerver módban. Győződjön meg arról is, hogy a távoli állomás 3389 (RDP For Windows) vagy 22 (SSH for Linux) rendszeren figyel, és engedélyezi a forgalmat az 5201-es porton az iPerf számára. Ha a távoli állomás windows, telepítse az AzureCT-t, és futtassa az Install-LinkPerformance parancsot az iPerf és az iPerf kiszolgálói üzemmódban történő indításához szükséges tűzfalszabályok beállításához. 
    
    Miután a távoli számítógép készen áll, nyissa meg a PowerShellt a helyi számítógépen, és indítsa el a tesztet:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Ez a parancs egy sor egyidejű terhelési és késési tesztet futtat, hogy segítsen megbecsülni a hálózati kapcsolat sávszélesség-kapacitását és késését.

4. A tesztek teljesítményének áttekintése

    A PowerShell kimeneti formátuma a következőkhöz hasonlóan néz ki:

    ![4][4]

    Az összes iPerf- és PSPing-teszt részletes eredményei az AzureCT-eszközök könyvtárában, a "C:\ACTTools" címtárban találhatók.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a teljesítményteszt nem ad várt eredményt, kitalálni, hogy miért kell egy progresszív lépésről-lépésre folyamat. Tekintettel az útvonal összetevőinek számára, a szisztematikus megközelítés általában gyorsabb megoldást biztosít, mint az ugrás, és potenciálisan feleslegesen ugyanazt a tesztelést többször is elvégezzük.

>[!NOTE]
>A forgatókönyv itt egy teljesítményprobléma, nem kapcsolati probléma. A lépések más lenne, ha a forgalom nem halad el egyáltalán.
>
>

Először is, kérdőjelezd meg a feltételezéseidet. Ésszerű az elvárása? Például ha egy 1 Gbit/s-os ExpressRoute-kapcsolatés 100 ms késés ésszerűtlen, hogy számíthat a teljes 1 Gbps forgalom miatt a tcp teljesítményjellemzői a nagy késésű kapcsolatok. A [teljesítményfeltételezésekről](#references) a Referenciák című részben további információkért.

Ezután azt javaslom, hogy az útválasztási tartományok közötti szélektől induljon, és próbálja meg elkülöníteni a problémát egyetlen fő útválasztási tartományra; a vállalati hálózat, a WAN vagy az Azure Network. Az emberek gyakran hibáztatják a "fekete doboz" az úton, míg hibáztatja a fekete doboz könnyen megoldható, akkor jelentősen késleltetheti megoldás, különösen, ha a probléma valójában egy olyan területen, hogy megvan a képessége, hogy a változásokat. Győződjön meg róla, hogy nem a kellő gondossággal átadása előtt a szolgáltató vagy az ISP.

Miután azonosította a problémát tartalmazó fő útválasztási tartományt, hozzon létre egy diagramot a kérdéses területről. A rajztáblán, a jegyzettömbön vagy a Visio diagramként egy konkrét "harci térképet" biztosít, amely lehetővé teszi a módszeres megközelítést a probléma további elkülönítésére. Megtervezheti a tesztelési pontokat, és frissítheti a térképet, ahogy törli a területeket, vagy mélyebbre áshat a tesztelés előrehaladtával.

Most, hogy van egy diagramja, kezdje el szegmensekre osztani a hálózatot, és szűkítse le a problémát. Tudja meg, hol működik, és hol nem. Tartsa mozog a vizsgálati pontok elkülöníteni le a jogsértő komponens.

Is, ne felejtsük el, hogy nézd meg más rétegek az OSI modell. Könnyen összpontosíthat a hálózatra és az 1-3. Legyen nyitott, és ellenőrizze feltételezéseket.

## <a name="advanced-expressroute-troubleshooting"></a>Speciális ExpressRoute hibaelhárítás
Ha nem biztos abban, hogy a felhő széle valójában hol van, az Azure-összetevők elkülönítése kihívást jelent. ExpressRoute használat közben a peremél a Microsoft Enterprise Edge (MSEE) nevű hálózati összetevő. **Az ExpressRoute használatakor**az MSEE az első kapcsolattartási pont a Microsoft hálózatában, és az utolsó ugrás, amely elhagyja a Microsoft-hálózatot. Amikor létrehoz egy kapcsolatobjektumot a virtuális hálózat átjárója és az ExpressRoute-kapcsolat között, valójában kapcsolatot létesít az MSEE-vel. Az MSEE első vagy utolsó ugrásként való felismerése (attól függően, hogy melyik irányba halad) elengedhetetlen az Azure Network-problémák elkülönítéséhez, hogy bizonyítsa, hogy a probléma az Azure-ban, vagy a WAN-ban vagy a vállalati hálózatban lejjebb van. 

![2][2]

>[!NOTE]
> Figyelje meg, hogy az MSEE nem az Azure-felhőben. ExpressRoute valójában szélén a Microsoft-hálózat valójában nem az Azure-ban. Miután csatlakozott az ExpressRoute-hoz egy MSEE-hez, csatlakozik a Microsoft hálózatához, onnan bármelyik felhőalapú szolgáltatáshoz elérhet, például az Office 365-höz (a Microsoft Társviszony-létesítéshez) vagy az Azure-hoz (privát és/vagy Microsoft társviszony-létesítéssel).
>
>

Ha két virtuális hálózat (a diagramBan az A és B virtuális hálózatok) **ugyanahhoz** az ExpressRoute-kapcsolatcsoporthoz csatlakozik, tesztsorozatot hajthat végre a probléma elkülönítésére az Azure-ban (vagy bizonyíthatja, hogy nem az Azure-ban található)
 
### <a name="test-plan"></a>Vizsgálati terv
1. Futtassa a Get-LinkPerformance tesztet a VM1 és a VM2 között. Ez a teszt betekintést nyújt, hogy a probléma helyi-e vagy sem. Ha ez a teszt elfogadható késést és sávszélességet eredményez, megjelölheti a helyi virtuális hálózatot jóként.
2. Feltételezve, hogy a helyi virtuális hálózat forgalma jó, futtassa a Get-LinkPerformance teszt et a VM1 és a VM3 között. Ez a teszt gyakorolja a kapcsolatot a Microsoft-hálózaton keresztül le az MSEE-be, majd vissza az Azure-ba. Ha ez a teszt elfogadható késést és sávszélesség-eredményeket eredményez, megjelölheti az Azure-hálózatot jóként.
3. Ha az Azure ki van zárva, hasonló tesztsorozatot hajthat végre a vállalati hálózaton. Ha ez is jól tesztel, itt az ideje, hogy működjön együtt a szolgáltatóval vagy az isp diagnosztizálni a WAN-kapcsolat. Példa: Futtassa ezt a tesztet két fiókiroda között, vagy az íróasztal és az adatközpont-kiszolgáló között. Attól függően, hogy mit tesztel, keresse meg azokat a végpontokat (kiszolgálók, számítógépek stb.), amelyek gyakorolhatják ezt az elérési utat.

>[!IMPORTANT]
> Rendkívül fontos, hogy minden egyes tesztnél jelölje meg a teszt futtatásának idejét, és rögzítse az eredményeket egy közös helyen (szeretem a OneNote-ot vagy az Excel-t). Minden tesztfuttatásnak azonos kimenettel kell rendelkeznie, így összehasonlíthatja az eredményül eső adatokat a tesztfuttatások között, és nem rendelkezhet "lyukakkal" az adatokban. Több teszt közötti konzisztencia az elsődleges oka annak, hogy az AzureCT hibaelhárítási. A varázslat nem a pontos terhelési forgatókönyvek futok, hanem a *varázslat* az a tény, hogy kapok egy *egységes teszt és adatkimenet* minden egyes teszt. Az idő rögzítése és a konzisztens adatok minden egyes alkalommal különösen hasznos, ha később úgy találja, hogy a probléma szórványos. Legyen szorgalmas az adatgyűjtés előre, és akkor elkerülhető óra újratesztelése ugyanazokat a forgatókönyveket (megtanultam ezt a kemény utat sok évvel ezelőtt).
>
>

## <a name="the-problem-is-isolated-now-what"></a>A probléma elszigetelt, most mi lesz?
Minél könnyebben elkülönítheti a problémát, annál könnyebb a probléma megoldása, azonban gyakran eléri azt a pontot, ahol nem tud mélyebbre vagy tovább menni a hibaelhárítással. Példa: a szolgáltatón keresztül imitált link jelenik meg, amely európán keresztül viszi az ugrásokat, de a várt útvonal Ázsiában van. Ez az a pont, amikor segítséget kell kérned. Attól függ, hogy kitől az útválasztási tartomány, amelyre elkülönítette a problémát, vagy még jobb, ha le tudja szűkíteni egy adott összetevőre.

Vállalati hálózati problémák esetén a hálózatot támogató belső informatikai részleg vagy szolgáltató (amely lehet a hardver gyártója) segíthet az eszköz konfigurációjában vagy a hardver javításában.

A WAN esetében a tesztelési eredmények megosztása a szolgáltatóval vagy az internetszolgáltatóval segíthet a kezdésben, és elkerülheti, hogy ugyanazokat az alapokat fedje le, mint amelyeket már tesztelt. Ne sértődjön meg azonban, ha saját maga szeretné ellenőrizni az eredményeket. A "Bizalom, de ellenőrzés" jó mottó, ha mások által jelentett eredmények alapján hibaelhárítást alkalmazunk.

Az Azure-ral, miután a lehető legrészletesebben elkülöníti a problémát, ideje áttekinteni az [Azure Network dokumentációját,][Network Docs] majd ha még szükség van [egy támogatási jegy megnyitására.][Ticket Link]

## <a name="references"></a>Referencia
### <a name="latencybandwidth-expectations"></a>Késés/sávszélesség-elvárások
>[!TIP]
> A tesztelt végpontok közötti földrajzi késés (mérföld vagy kilométer) messze a késés legnagyobb összetevője. Bár van berendezés késés (fizikai és virtuális alkatrészek, komlók száma, stb) részt, földrajz bizonyult a legnagyobb összetevője az általános késés kezelése során WAN-kapcsolatok. Azt is fontos megjegyezni, hogy a távolság a távolság a szál távon nem egyenes vonal vagy útiterv távolság. Ezt a távolságot hihetetlenül nehéz bármilyen pontossággal megszerezni. Ennek eredményeként, én általában egy városi távolság kalkulátor az interneten, és tudom, hogy ez a módszer egy durván pontatlan intézkedés, de elég ahhoz, hogy egy általános elvárás.
>
>

Van egy ExpressRoute beállítás seattle-i, Washington az USA-ban. Az alábbi táblázat bemutatja a késést és a sávszélességet láttam tesztelés különböző Azure-helyeken. Megbecsültem a földrajzi távolságot a teszt végei között.

Teszt beállítása:
 - 10 Gb/s-os hálózati adapterrel rendelkező, 10 Gb/s sebességű Windows Server 2016 rendszert futtató fizikai kiszolgáló, amely ExpressRoute-kapcsolaton keresztül csatlakozik.
 - 10 Gbps Premium ExpressRoute-kapcsolat a privát társviszony-létesítés engedélyezve lévő helyen.
 - Egy Azure virtuális hálózat egy UltraPerformance átjáró a megadott régióban.
 - Windows Server 2016 rendszerű DS5v2 virtuális gép a virtuális hálózaton. A virtuális gép nem tartományhoz csatlakozott, az alapértelmezett Azure-lemezképből készült (nincs optimalizálás vagy testreszabás) az AzureCT telepítve.
 - Minden tesztelés az AzureCT Get-LinkPerformance parancsot használta egy 5 perces betöltési teszttel mind a hat tesztfuttatáshoz. Példa:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Az egyes tesztek adatfolyama a helyszíni fizikai kiszolgálóról (seattle-i iPerf-ügyfél) az Azure VM-ig (a felsorolt Azure-régióiban lévő iPerf-kiszolgálóig) áramlott.
 - A "Késés" oszlop adatai a No Load tesztből származnak (TCP-késleltetési teszt iPerf futtatás nélkül).
 - A "Maximális sávszélesség" oszlop adatai a 16 TCP áramlási terhelési tesztből származnak, 1 Mb-os ablakmérettel.

![3][3]

### <a name="latencybandwidth-results"></a>Késés/sávszélesség eredményei
>[!IMPORTANT]
> Ezek a számok csak általános tájékoztató jellegűek. Számos tényező befolyásolja a késést, és bár ezek az értékek általában konzisztensek az idő múlásával, az Azure-on vagy a Szolgáltatóhálózaton belüli feltételek bármikor küldhetnek forgalmat különböző útvonalakon keresztül, így a késés és a sávszélesség hatással lehet. Általában ezeknek a változásoknak a hatásai nem eredményeznek jelentős különbségeket.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>Hely|Azure<br/>Régió|Becsült<br/>Távolság (km)|Késés|1 munkamenet<br/>Sávszélesség|Maximum<br/>Sávszélesség|
| Seattle | USA nyugati régiója, 2.        |    191 km |   5 ms | 262,0 Mbit/mp |  3,74 Gbit/mp |
| Seattle | USA nyugati régiója          |  1094 km |  18 ezres |  82,3 Mbit/mp |  3,70 Gbit/mp |
| Seattle | USA középső régiója       |  2 357 km |  40 ms |  38,8 Mbit/mp |  2,55 Gbit/mp |
| Seattle | USA déli középső régiója |  2 877 km |  51 ms |  30,6 Mbit/mp |  2,49 Gbit/mp |
| Seattle | USA északi középső régiója |  2792 km |  55 ms |  27,7 Mbit/mp |  2,19 Gbit/mp |
| Seattle | USA 2. keleti régiója        |  3 769 km |  73 ezres |  21,3 Mbit/mp |  1,79 Gbit/mp |
| Seattle | USA keleti régiója          |  3 699 km |  74 ezres |  21,1 Mbit/mp |  1,78 Gbit/mp |
| Seattle | Kelet-Japán       |  7705 km | 106 ms |  14,6 Mbit/mp |  1,22 Gbit/mp |
| Seattle | Az Egyesült Királyság déli régiója         |  7708 km | 146 ms |  10,6 Mbit/mp |   896 Mbit/mp |
| Seattle | Nyugat-Európa      |  7 834 km | 153 ms |  10,2 Mbit/mp |   761 Mbit/mp |
| Seattle | Kelet-Ausztrália   | 12 484 km | 165 ms |   9,4 Mbit/mp |   794 Mbit/mp |
| Seattle | Délkelet-Ázsia   | 12 989 km | 170 ms |   9,2 Mbit/mp |   756 Mbit/mp |
| Seattle | Brazília Dél *   | 10 930 km | 189 ms |   8,2 Mbit/mp |   699 Mbit/mp |
| Seattle | Dél-India      | 12 918 km | 202 ezres |   7,7 Mbit/mp |   634 Mbit/mp |

\*A késés, hogy Brazília egy jó példa, ahol az egyenes vonal távolság jelentősen eltér a rost futási távolság. Arra számítanék, hogy a látencia lenne a környéken 160 ms, de valójában 189 ms. Ez a különbség az elvárásaimellen jelezheti a hálózati probléma valahol, de valószínűleg, hogy a rost fut nem megy Brazíliába egy egyenes vonal, és egy extra 1000 km-re, vagy ha az utazás, hogy Brazíliába Seattle-ből.

## <a name="next-steps"></a>További lépések
1. Töltse le az Azure Connectivity Toolkit-et a GitHubról a[https://aka.ms/AzCT][ACT]
2. Kövesse a [hivatkozás teljesítményének tesztelésére][Performance Doc] vonatkozó utasításokat

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure hálózati összetevők"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute hibaelhárítás"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Perf tesztkörnyezet"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-kimenet"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT

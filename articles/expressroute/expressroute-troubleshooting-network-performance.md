---
title: 'Hálózati kapcsolat teljesítményének megoldása: Azure'
description: Ez az oldal egy szabványosított módszert biztosít az Azure hálózati kapcsolatok teljesítményének tesztelésére.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: troubleshooting
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: e882035af3ac0a086c58b4886fd6999970712df1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86521666"
---
# <a name="troubleshooting-network-performance"></a>A hálózati teljesítmény hibaelhárítása
## <a name="overview"></a>Áttekintés
Az Azure stabil és gyors megoldást kínál az Azure helyszíni hálózatból történő eléréséhez. Az olyan megoldások segítségével, mint a helyek közötti VPN és az ExpressRoute, kisebb és nagyobb szervezetek is sikerrel futtathatják üzleti folyamataikat az Azure-ban. De mi történik, ha a teljesítmény nem felel meg a várt vagy a korábbi élménynek? A dokumentum segítségével szabványosíthatja az adott környezet tesztelésének és alapkonfigurációjának módját.

Ez a dokumentum bemutatja, hogyan lehet egyszerűen és következetesen tesztelni a hálózati késést és a sávszélességet két gazdagép között. Ez a dokumentum az Azure-hálózat megkeresésének módjairól is nyújt útmutatást, és segít a problémás pontok elkülönítésében. A megjelenő PowerShell-parancsfájlnak és-eszközöknek két gazdagépet kell megkövetelniük a hálózaton (a tesztelt hivatkozás mindkét végén). Az egyik gazdagépnek Windows-kiszolgálónak vagy-asztalnak kell lennie, a másik pedig Windows vagy Linux. 

>[!NOTE]
>A hibaelhárítási módszer, a használt eszközök és módszerek személyes beállítások. Ez a dokumentum a gyakran elvégezhető megközelítést és eszközöket ismerteti. Az Ön megközelítése valószínűleg eltérő lesz, és a problémamegoldás különböző módszereinek egyike sem okoz gondot. Ha azonban nem rendelkezik létrehozott megközelítéssel, a dokumentum elsajátíthatja a saját módszereinek, eszközeinek és preferenciáinak a hálózati problémák megoldásához való létrehozásához szükséges útvonalat.
>
>

## <a name="network-components"></a>Hálózati összetevők
A hibaelhárításhoz való kiásás előtt beszéljünk néhány gyakori kifejezésről és összetevőről. Ez a vita biztosítja, hogy a végpontok közötti lánc minden olyan összetevőjére gondoljon, amely lehetővé teszi a kapcsolódást az Azure-ban.
![1][1]

A legmagasabb szinten leírom a három fő hálózati útválasztási tartományt;

- Az Azure-hálózat (a jobb oldali kék felhő)
- az Internet vagy a WAN (zöld felhő a központban)
- a vállalati hálózat (Peach Cloud a bal oldalon)

A diagramot a jobbról balra haladva tárgyaljuk, hogy röviden lássuk az egyes összetevőket:
 - **Virtuális gép** – a kiszolgálónak több hálózati adaptere is lehet, a statikus útvonalak, az alapértelmezett útvonalak és az operációsrendszer-beállítások a forgalomnak a megfelelő módon történő küldését és fogadását biztosítják. Emellett az egyes virtuális gépekhez tartozó SKU sávszélesség-korlátozást is tartalmaz. Ha kisebb VM SKU-t használ, a forgalmat a hálózati adapter számára elérhető sávszélesség korlátozza. Általában DS5v2 használok a teszteléshez (és a törlés után töröljük a pénzt) a megfelelő sávszélesség biztosításához a virtuális gépen.
 - **NIC** – győződjön meg arról, hogy ismeri a kérdéses hálózati adapterhez rendelt magánhálózati IP-címet.
 - **NIC NSG** – lehetséges, hogy a hálózati adapter szintjén bizonyos NSG vannak alkalmazva, hogy a NSG-szabálykészlet megfelelő legyen az átadni kívánt forgalomhoz. Tegyük fel például, hogy a iPerf, a 3389 for RDP vagy az SSH esetében a 5201-es portok nyitva vannak, hogy lehetővé tegyék a tesztelési forgalom továbbítását.
 - **VNet alhálózat** – a hálózati adapter egy adott alhálózathoz van rendelve, ellenőrizze, hogy az adott alhálózathoz melyik és milyen szabályok vannak társítva.
 - **ALHÁLÓZAT NSG** – csakúgy, mint a hálózati adapter, a NSG is alkalmazható az alhálózaton. Győződjön meg arról, hogy az átadni kívánt forgalom megfelel a NSG-szabálynak. (a hálózati adapterhez bejövő forgalom esetén az alhálózat NSG először alkalmazza, majd a NIC-NSG a virtuális gépről kimenő forgalomhoz, a NIC NSG először alkalmazza, az alhálózati NSG pedig lejátszásra kerül.
 - **ALHÁLÓZAT UDR** – a felhasználó által megadott útvonalak átirányítják a forgalmat egy köztes ugrásba (például tűzfal vagy terheléselosztó). Győződjön meg arról, hogy van-e UDR a forgalomhoz, és ha igen, hol van, és mi a következő ugrás a forgalomra. (például egy tűzfal átadhat egy forgalmat, és megtagadhatja a többi forgalmat ugyanazon két gazdagép között).
 - **Gateway subnet/NSG/UDR** – akárcsak a virtuálisgép-alhálózathoz, az átjáró alhálózatának NSG és UDR is lehet. Győződjön meg arról, hogy tudja, van-e ott, és milyen hatással vannak a forgalomra.
 - **VNet-átjáró (ExpressRoute)** – ha a társítás (ExpressRoute) vagy a VPN engedélyezve van, nincs sok olyan beállítás, amely hatással lehet a forgalmi útvonalakra vagy azokra. Ha több ExpressRoute-áramkör vagy VPN-alagút kapcsolódik ugyanahhoz a VNet-átjáróhoz, vegye figyelembe a kapcsolat súlyozási beállításait, mivel ez a beállítás hatással van a kapcsolatra, és hatással van a forgalom elérési útjára.
 - **Útvonal-szűrő** (nem látható) – az útvonal-szűrő csak a ExpressRoute Microsoft-társára vonatkozik, de kritikus fontosságú annak ellenőrzéséhez, hogy nem látja-e a várt útvonalakat a Microsoft-társon. 

Ezen a ponton a hivatkozás WAN-részén van. Ez az útválasztási tartomány lehet a szolgáltató, a vállalati WAN vagy az Internet. Sok ugrás, technológia és a velük foglalkozó vállalatok némileg nehézkessé tehetik a hibakeresést. Gyakran az Azure-t és a vállalati hálózatokat is ki kell zárnia, mielőtt beugrott a vállalatok és a komló ezen gyűjteményéből.

Az előző ábrán a bal szélen a vállalati hálózat. A vállalat méretétől függően ez az útválasztási tartomány lehet néhány hálózati eszköz, amely az Ön és a WAN, illetve több, a kampusz/vállalati hálózatban lévő eszközök között található.

Ezeknek a három különböző magas szintű hálózati környezetnek a bonyolultsága miatt a rendszer gyakran optimálisan indul el a szélein, és megmutathatja, hol van a teljesítmény, és hogy hol romlik. Ezzel a módszerrel azonosíthatja a három problémás útválasztási tartományt, majd az adott környezetre koncentrálhat a hibaelhárításra.

## <a name="tools"></a>Eszközök
A legtöbb hálózati probléma az olyan alapszintű eszközökkel elemezhető és különíthető el, mint a ping és a traceroute. Ritka, hogy a csomagok elemzéséhez, például a Wireshark kell lépnie. A hibaelhárítással kapcsolatos segítség érdekében az Azure connectivity Toolkit (AzureCT) úgy lett kifejlesztve, hogy néhány ilyen eszközt egy egyszerű csomagban helyezzen el. A teljesítmény-teszteléshez szeretném használni a iPerf és a PSPing. a iPerf egy leggyakrabban használt eszköz, és a legtöbb operációs rendszeren fut. a iPerf kiválóan használható az alapszintű előadások teszteléséhez, és a használata meglehetősen egyszerű. A PSPing egy, a SysInternals által fejlesztett pingelési eszköz. A PSPing egyszerű módszer az ICMP és a TCP-pingelések elvégzésére is egy könnyen használható paranccsal. Mindkét eszköz könnyű, és a rendszer egyszerűen telepíti a fájlokat a gazdagép egy könyvtárába.

Ezeket az eszközöket és metódusokat becsomagoltam egy PowerShell-modulba (AzureCT), amelyet telepíthet és használhat.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT – az Azure connectivity Toolkit
A AzureCT PowerShell-modul két összetevő [rendelkezésre állásának tesztelését][Availability Doc] és a [teljesítmény tesztelését][Performance Doc]végzi. Ez a dokumentum csak a teljesítmény tesztelésével kapcsolatos, ezért a PowerShell-modulban a két hivatkozás teljesítményére vonatkozó parancsokra koncentrálhat.

Az eszközkészletet a teljesítmény-teszteléshez három alapvető lépéssel hajthatja végre. 1.) telepítse a PowerShell-modult, 2.) telepítse a támogató alkalmazásokat iPerf és PSPing 3) futtassa a teljesítménytesztet.

1. A PowerShell-modul telepítése

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    Ez a parancs letölti a PowerShell-modult, és helyileg telepíti.

2. A támogató alkalmazások telepítése
    ```powershell
    Install-LinkPerformance
    ```
    Ez a AzureCT parancs telepíti a iPerf és a PSPing egy új "C:\ACTTools" könyvtárba, és megnyitja a Windows tűzfal portjait is, hogy engedélyezze az ICMP és a port 5201 (iPerf) forgalmat.

3. A teljesítményteszt futtatása

    Először a távoli gazdagépen telepítenie és futtatnia kell a iPerf kiszolgálói módban. Győződjön meg arról is, hogy a távoli gazdagép a 3389-as (RDP for Windows) vagy a 22 (SSH Linux rendszerhez) vagy a 5201-es porton keresztüli adatforgalom iPerf való figyelését figyeli. Ha a távoli gazdagép Windows, telepítse a AzureCT, és futtassa az install-LinkPerformance parancsot a iPerf beállításához és a iPerf kiszolgáló módban való elindításához szükséges tűzfalszabályok létrehozásához. 
    
    Miután a távoli gép elkészült, nyissa meg a PowerShellt a helyi gépen, és indítsa el a tesztet:
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    Ez a parancs egyidejű terhelési és késési tesztek sorozatát futtatja a hálózati kapcsolat sávszélesség-kapacitásának és késésének becslése érdekében.

4. A tesztek kimenetének áttekintése

    A PowerShell kimeneti formátuma a következőhöz hasonlóan néz ki:

    ![4][4]

    Az összes iPerf-és PSPing-teszt részletes eredményeit a AzureCT-eszközök címtárának "C:\ACTTools." mappájában található egyedi szövegfájlokban található.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha a teljesítményteszt nem eredményezi a várt eredményeket, akkor érdemes kitalálni, hogy miért kellene egy lépésenkénti folyamatnak lennie. Az elérési úton található összetevők száma alapján a szisztematikus megközelítés általában gyorsabb elérési utat biztosít, mint a jumping Around, és potenciálisan feleslegesen ugyanazzal a teszteléssel többször is.

>[!NOTE]
>A forgatókönyv itt egy teljesítményproblémák, nem pedig kapcsolódási probléma. A lépések eltérőek lehetnek, ha a forgalom egyáltalán nem halad át.
>
>

Először is megkérdőjelezheti a feltételezéseket. Ésszerű a várakozás? Ha például egy 1 GB/s ExpressRoute áramkörből és 100 MS-ból késésből áll, akkor ésszerűtlen, hogy a TCP-t a nagy késésű kapcsolatokra mutató teljesítményi jellemzőkkel rendelkező teljes 1 GB/s-os adatforgalom miatt elvárják. A teljesítményre vonatkozó feltételezésekről a [referenciák című szakaszban](#references) talál további információt.

Következő lépésként azt javasoljuk, hogy kezdjen az útválasztási tartományok között, és próbálja meg elkülöníteni a problémát egyetlen jelentős útválasztási tartományba; a vállalati hálózat, a WAN vagy az Azure-hálózat. Az emberek gyakran az elérési úton lévő "fekete dobozt" hibáztatják, miközben a fekete dobozra való kihasználása könnyen elvégezhető, jelentősen késleltetheti a felbontást, különösen akkor, ha a probléma ténylegesen olyan területen van, ahol módosításokat végezhet. Győződjön meg arról, hogy a szolgáltató vagy ISP számára való átadás előtt gondoskodik a megfelelő gondosságról.

Miután azonosította a problémát tartalmazó fő útválasztási tartományt, létre kell hoznia egy diagramot a szóban forgó területről. A faliújságon, a Jegyzettömbben vagy a Visio-diagramon egy konkrét "csata-hozzárendelést" is biztosít, amely lehetővé teszi a módszert a probléma további elkülönítéséhez. Megtervezheti a tesztelési pontokat, és frissítheti a térképet úgy, hogy törli a területeket, vagy mélyebbre ássa a tesztelés folyamatát.

Most, hogy már rendelkezik egy diagrammal, megkezdheti a hálózat felosztását a szegmensekre, és leszűkítheti a problémát. Megtudhatja, hol működik és hol nem. Folytassa a tesztelési pontok áthelyezésével, hogy elkülönítse a jogsértő összetevőt.

Ne felejtse el megtekinteni az OSI-modell egyéb rétegeit is. A hálózatra és a 1-3-es rétegekre (fizikai, adatés hálózati rétegek) is könnyedén koncentrálhat, de a problémák az alkalmazási réteg 7. rétegében is felmerülhetnek. Tartsa meg a nyitott elmét, és ellenőrizze a feltételezéseket.

## <a name="advanced-expressroute-troubleshooting"></a>Speciális ExpressRoute-hibaelhárítás
Ha nem biztos abban, hogy a felhő széle valóban az, az Azure-összetevők elkülönítése kihívást jelenthet. A ExpressRoute használatakor a peremhálózat a Microsoft Enterprise Edge (MSEE) nevű hálózati összetevő. A **ExpressRoute használatakor**a MSEE az első kapcsolódási pont a Microsoft hálózatához, a legutóbbi ugrás pedig elhagyja a Microsoft hálózatát. Amikor létrehoz egy kapcsolati objektumot a VNet-átjáró és a ExpressRoute-áramkör között, valójában kapcsolatot létesít a MSEE. A MSEE első vagy utolsó ugrásként való felismerése (attól függően, hogy melyik irányt futtatja) elengedhetetlen az Azure hálózati problémák elkülönítéséhez, hogy a probléma az Azure-ban vagy a WAN-ban vagy a vállalati hálózaton található további alsóbb rétegben legyen. 

![2][2]

>[!NOTE]
> Figyelje meg, hogy a MSEE nem az Azure-felhőben található. A ExpressRoute valójában a Microsoft-hálózat peremén van, valójában nem az Azure-ban. Miután kapcsolódott a ExpressRoute-hez egy MSEE, a Microsoft hálózatához csatlakozik, innen bármelyik felhőalapú szolgáltatáshoz, például az Office 365-hoz (Microsoft-partneri kapcsolattal) vagy az Azure-hoz (magán-és/vagy Microsoft-partneri kapcsolattal) csatlakozhat.
>
>

Ha a diagram két virtuális hálózatok (a virtuális hálózatok A és B) **ugyanahhoz** a ExpressRoute-áramkörhöz csatlakozik, több tesztet is végrehajthat az Azure-beli probléma elkülönítéséhez (vagy annak bizonyításához, hogy az nem az Azure-ban van)
 
### <a name="test-plan"></a>Tesztelési terv
1. Futtassa a Get-LinkPerformance tesztet a VM1 és a VM2 között. Ez a teszt azt ismerteti, hogy a probléma helyi-e, vagy sem. Ha ez a teszt elfogadható késést és sávszélesség-eredményeket hoz létre, akkor a helyi VNet-hálózatot is érdemes lehet megjelölni.
2. Ha feltételezi, hogy a helyi VNet-forgalom jó, futtassa a Get-LinkPerformance tesztet a VM1 és a VM3 között. Ez a teszt a Microsoft hálózatán keresztül gyakorolja a kapcsolódást a MSEE, és visszatér az Azure-ba. Ha ez a teszt elfogadható késést és sávszélesség-eredményeket hoz létre, akkor az Azure-hálózatot is érdemes lehet megjelölni.
3. Ha az Azure ki van zárva, a teszteket hasonló módon hajthatja végre a vállalati hálózaton. Ha ez is jól működik, ideje, hogy együttműködjön a szolgáltatóval vagy az INTERNETSZOLGÁLTATÓval a WAN-kapcsolat diagnosztizálásához. Példa: futtassa ezt a tesztet két fiókirodában, illetve az íróasztal és egy adatközpont-kiszolgáló között. A teszteléstől függően megkeresheti a végpontokat (kiszolgálókat, számítógépeket stb.), amelyek ezt az elérési utat gyakorolhatják.

>[!IMPORTANT]
> Fontos, hogy minden egyes tesztnél megjelölje a teszt futtatásának időpontját, és az eredményeket egy közös helyen (például a OneNote vagy az Excel programban) jegyezze fel. Az egyes tesztelési műveleteknek azonos kimenettel kell rendelkezniük, így összehasonlíthatja az eredményül kapott adatokat a tesztek között, és az adatokat nem lehet "lyukakkal" ellátni. A több teszt közötti konzisztencia az elsődleges ok, amiért a AzureCT használja a hibaelhárításhoz. A Magic nem a jelenleg futtatott terhelési forgatókönyvek, hanem a *Magic* az a tény, hogy *egységes tesztet és adatkimenetet* kapok minden tesztből. Ha később azt tapasztalja, hogy a probléma szórványos, az idő rögzítése és az egységes adatkezelési idő is különösen hasznos. Legyen gondos az adatgyűjtés előtt, és elkerülheti a különböző forgatókönyvek újratesztelésének idejét (sok évvel ezelőtt megtanultam ezt a nehéz utat).
>
>

## <a name="the-problem-is-isolated-now-what"></a>A probléma el van különítve, most mi?
Minél többen is elkülönítheti a problémát, annál könnyebben javíthatja azt, de gyakran éri el azt a pontot, ahol nem mélyebbre vagy részletesebbre teheti a hibaelhárítást. Példa: láthatja, hogy az egész szolgáltatón keresztüli ugrások az Európa-on keresztül zajlanak, de a várt elérési út Ázsiában van. Ez a pont akkor érhető el, ha segítségre van szüksége. Az Ön által kért kérdés attól függ, hogy melyik útválasztási tartományhoz kapcsolódott a probléma, vagy még jobb, ha le tud szűkíteni egy adott összetevőre.

Vállalati hálózati problémák esetén a hálózatot támogató belső informatikai részleg vagy szolgáltató (amely lehet a hardver gyártója) segíthet az eszköz konfigurálásában vagy a hardveres javításban.

A WAN esetében a tesztelési eredmények megosztása a szolgáltatóval vagy az INTERNETSZOLGÁLTATÓval segíthet megkezdeni a használatot, és elkerülheti, hogy a már teszteltek egy részét. Ne sértse azonban, ha saját maga szeretné ellenőrizni az eredményeket. A "bizalmi kapcsolat, de az ellenőrzés" jó választás, ha más személy által jelentett eredmények alapján végez hibakeresést.

Az Azure-ban, ha a problémát a lehető legrészletesebben elkülöníti, itt az ideje, hogy áttekintse az [Azure hálózati dokumentációját][Network Docs] , és ha szükséges, [Nyisson meg egy támogatási jegyet][Ticket Link]is.

## <a name="references"></a>Hivatkozások
### <a name="latencybandwidth-expectations"></a>Késés/sávszélesség várakozási ideje
>[!TIP]
> A tesztelt végpontok közötti földrajzi késés (mérföld vagy kilométer) messze a késés legnagyobb összetevője. A berendezések késése (fizikai és virtuális összetevők, ugrások száma stb.) esetében a földrajz a WAN-kapcsolatok kezelése során a teljes késés legnagyobb részét bizonyította. Azt is fontos megjegyezni, hogy a távolság a szál távolsága, nem az egyenes vonal vagy a közúti Térkép távolsága. Ez a távolság hihetetlenül nehéz bármilyen pontossággal. Ennek eredményeképpen általában egy városi távolsági számológépet használok az interneten, és tudja, hogy ez a módszer egy durván pontatlan mérték, de elég egy általános elvárást beállítani.
>
>

ExpressRoute-telepítőt kaptam a Washington állambeli Seattle-ben. Az alábbi táblázat a különböző Azure-helyszínekre való tesztelési késést és sávszélességet mutatja be. A teszt végén a földrajzi távolságot becsültük.

Teszt beállítása:
 - Egy Windows Server 2016 rendszert futtató fizikai kiszolgáló 10 GB/s hálózati adapterrel, amely egy ExpressRoute-áramkörhöz csatlakozik.
 - A 10Gbps prémium szintű ExpressRoute áramkör a privát társak beállításával azonosított helyen.
 - Egy Azure-VNet egy UltraPerformance-átjáróval a megadott régióban.
 - Egy Windows Server 2016 rendszert futtató DS5v2 virtuális gép a VNet. A virtuális gép nem csatlakozik a tartományhoz, és az alapértelmezett Azure-rendszerképből (optimalizálás vagy testreszabás nélkül) készült, és a AzureCT telepítve van.
 - Minden teszt a AzureCT Get-LinkPerformance parancsot használta 5 percenkénti terhelési teszttel a hat teszt futtatásához. Például:

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - Az egyes tesztek adatforgalmának terhelése a helyszíni fizikai kiszolgálóról (iPerf-ügyfél, Seattle-ből) történt, akár az Azure-beli virtuális gépen (iPerf-kiszolgáló a felsorolt Azure-régióban).
 - A "késés" oszlop adatait a nincs betöltési teszt (a TCP-késési teszt a iPerf futtatása nélkül) értékből származik.
 - A "maximális sávszélesség" oszlop adatait a 16 TCP-flow terhelési tesztje egy 1 MB méretű ablakméret alapján.

![3][3]

### <a name="latencybandwidth-results"></a>Késés/sávszélesség eredményei
>[!IMPORTANT]
> Ezek a számok csak általános referenciák. Számos tényező befolyásolja a késést, és noha ezek az értékek általában konzisztensek az idő múlásával, az Azure-ban vagy a szolgáltatói hálózaton belüli feltételek bármikor elküldhetik a forgalmat különböző elérési utakon keresztül, így a késés és a sávszélesség is hatással lehet. A változások hatása általában nem eredményez jelentős különbségeket.
>
>

| ExpressRoute<br/>Hely|Azure<br/>Régió | Becsült<br/>Távolság (km) | Késés|1 munkamenet<br/>Sávszélesség | Maximum<br/>Sávszélesség |
| ------------------------------------------ | --------------------------- |  - | - | - | - |
| Seattle | USA 2. nyugati régiója        |    191 km |   5 MS | 262,0 MB/s |  3,74 Gbits/s |
| Seattle | USA nyugati régiója          |  1 094 km |  18 MS |  82,3 MB/s |  3,70 Gbits/s |
| Seattle | Central US       |  2 357 km |  40 MS |  38,8 MB/s |  2,55 Gbits/s |
| Seattle | USA déli középső régiója |  2 877 km |  51 MS |  30,6 MB/s |  2,49 Gbits/s |
| Seattle | USA északi középső régiója |  2 792 km |  55 MS |  27,7 MB/s |  2,19 Gbits/s |
| Seattle | USA 2. keleti régiója        |  3 769 km |  73 MS |  21,3 MB/s |  1,79 Gbits/s |
| Seattle | USA keleti régiója          |  3 699 km |  74 MS |  21,1 MB/s |  1,78 Gbits/s |
| Seattle | Kelet-Japán       |  7 705 km | 106 MS |  14,6 MB/s |  1,22 Gbits/s |
| Seattle | Az Egyesült Királyság déli régiója         |  7 708 km | 146 MS |  10,6 MB/s |   896 MB/s |
| Seattle | West Europe      |  7 834 km | 153 MS |  10,2 MB/s |   761 MB/s |
| Seattle | Kelet-Ausztrália   | 12 484 km | 165 MS |   9,4 MB/s |   794 MB/s |
| Seattle | Délkelet-Ázsia   | 12 989 km | 170 MS |   9,2 MB/s |   756 MB/s |
| Seattle | Dél-Brazília *   | 10 930 km | 189 MS |   8,2 MB/s |   699 MB/s |
| Seattle | Dél-India      | 12 918 km | 202 MS |   7,7 MB/s |   634 MB/s |

\*A Brazília-re vonatkozó késés jó példa arra, hogy az egyenes vonal távolsága jelentősen eltér a szál futási helyétől. Elvárom, hogy a késés a 160 MS környékén lenne, de valójában 189 MS. Ez a különbség a vártnál is jelezheti a hálózati problémát, de a nagy valószínűséggel, hogy a szál futása nem egy egyenes vonalban található Brazíliában, és egy további 1 000 km, vagy a Seattle-ből érkező Brazíliába utazik.

## <a name="next-steps"></a>Következő lépések
1. Töltse le az Azure-kapcsolati eszközkészletet a GitHubról[https://aka.ms/AzCT][ACT]
2. Kövesse a [kapcsolati teljesítmény tesztelésének][Performance Doc] utasításait

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure hálózati összetevők"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute-hibaelhárítás"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "Teljesítményfigyelő-tesztkörnyezet"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell-kimenet"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT

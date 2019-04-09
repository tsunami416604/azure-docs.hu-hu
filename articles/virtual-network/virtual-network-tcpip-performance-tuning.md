---
title: TCP/IP teljesítmény hangolása az Azure virtuális gépek |} A Microsoft Docs
description: Ismerje meg, különböző gyakran használt TCP/IP teljesítmény hangolási módszerek és azok Azure virtuális gépeken.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: 6d53b8fe46997f6b4f915bfb14a64d69b22cc5d4
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59057829"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Az Azure virtuális gépek TCP/IP-teljesítményhangolása

Ez a cikk az a célja, hogy a gyakran használt TCP/IP teljesítmény hangolási módszerek és a Microsoft Azure-on futó virtuális gépek szempontjai. Fontos, először a fogalmak alapvető ismeretekkel rendelkezik, és ezután ismertetjük, hogyan azok hangolásával.

## <a name="common-tcpip-tuning-techniques"></a>Gyakran használt TCP/IP-hangolási módszerek

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>MTU töredezettséget és nagy küldése Kiszervezését (LSO)

#### <a name="explanation-of-mtu"></a>Maximális átviteli egység leírását

A maximális átviteli egység (MTU) a legnagyobb mérete keret (csomag), (bájt), amelyeket el küldhet egy hálózati adapter keresztül megadott. Az MTU előre konfigurálható beállítást, és az Azure virtuális gépeken használt MTU alapértelmezett értéke, és az alapértelmezett beállítás a legtöbb hálózati eszközön globálisan, 1500 bájt.

#### <a name="explanation-of-fragmentation"></a>Töredezettség magyarázata

Töredezettség akkor fordul elő, amikor egy csomagot küld a rendszer, amely meghaladja a maximális átviteli egység egy hálózati adapter. A TCP/IP-verem megszakítja a csomagot, amelyek megfelelnek a felületek MTU kisebb darabokra (szilánkok). Töredezettség az IP-rétegben történik, és független a mögöttes protokollt (például a TCP). 2000 bájt méretű csomagot egy hálózati adaptert 1500 MTU-keresztül küldött, majd azt fogja kell bontani 1500 bájtos egy csomagot és egy 500 bájt méretű csomagot.

Hálózati eszközök az elérési út a forrás és cél között van csomagokat, amelyek túllépik a MTU, vagy a csomag töredékben kisebb tömbökre.

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>Az IP-csomag a "nem töredék (DF)" bit

A tördelést bit értéke azt a jelzőt, az IP-protokoll fejlécben. Ha a DF bit be van állítva, azt jelzi, hogy a közvetítő hálózati eszközök az elérési úton, a küldő és fogadó között nem kell töredékben a csomagot. Számos oka lehet, hogy miért lehet beállítani a bit (lásd például kapcsolatos alábbi Útvonalkeresés szakaszban). Ha egy hálózati eszköz megkapja a tördelést bittel csomagot és a csomagban túllépi az eszközök kapcsolat MTU, akkor a normál viselkedését dobja el a csomagot, és a egy "ICMP darabolás szükséges" csomag küldi vissza az eredeti forrásra, az eszköz a csomag.

#### <a name="performance-implications-of-fragmentation"></a>Töredezettség teljesítményre gyakorolt hatása

Töredezettség negatív teljesítményre gyakorolt hatása lehet. A fő oka a teljesítményt egyik töredezettsége CPU/memória hatását és a csomagok összeállítását. A hálózati eszköz van szüksége egy csomag töredékben, amikor azt kell töredezettsége végrehajtásához CPU/memória-erőforrásokat. Azonos kell fordulhat elő, ha a csomagot a rendszer újra. A hálózati eszköz összes töredék kell tárolnia, amíg nem érkezett, azt is, az eredeti csomaggal szétbontani. A töredezettség/összeállítási folyamatán késése miatt a töredezettség/összeállítási folyamat is eredményezheti.

Más lehetséges negatívan befolyásolta a teljesítményt Ennek következménye töredezettség, hogy töredezett csomagokat előfordulhat, hogy érkezik sorrendben. Üzemen kívüli csomagok okozhat az üzemen kívüli csomagokat - lesz majd szükség lesz küldve a teljes csomagot, amely a hálózati eszközök bizonyos típusú. Eldobni a szilánk jellemző forgatókönyvei közé tartozik például a tűzfalak biztonsági eszközök, vagy pufferek elfogytak, amikor egy hálózati eszköz a kapnak. Amikor egy hálózati eszköz megkapják a pufferek elfogytak, a hálózati eszköz szétbontani töredezett csomagokat próbált, de nem rendelkezik az erőforrások tárolásához, és a csomag reassume.

Töredezettség is felfogható negatív műveletet, de támogatja a töredezettség azért szükséges, hogy a különféle hálózatokhoz csatlakozó az interneten keresztül.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Előnyök és következményeinek MTU módosítása

Általánosságban elmondható növekvő MTU hozhat létre a hatékonyabb hálózaton. Minden csomag továbbított további fejléc-információkat adnak, akkor az eredeti csomaggal rendelkezik. További csomagok azt jelenti, hogy további fejléc többletterhelést okoz, és a hálózat kevésbé hatékony eredményeként.

Ha például Ethernet fejléc mérete 14 bájt és a egy 4 bájtos keret ellenőrizze feladatütemezési (FCS) keret konzisztencia biztosításához. Ha egy 2000 bájt méretű csomagot küld a rendszer, majd Ethernet terheléssel 18 bájt kerül a hálózaton. Ha a csomag töredezett 1500 bájt méretű csomagot és egy 500 bájt méretű csomagot, majd minden egyes csomag kell 18 bájt fejléc Ethernet - vagy 36 bájt. Mivel egy 2000 bájtos csomagban csak egy Ethernet-fejléc 18 bájt.

Fontos megjegyezni, hogy maga is MTU növelése nem feltétlenül hozza létre a hatékonyabb hálózaton. Ha egy alkalmazás csak 500 bájtos csomagokat küld, majd azonos fejléc többletterhelést útvonalon jön legyen MTU 1500 bájt vagy 9000 bájt. Ahhoz, hogy a hálózatban kell lennie a további hatékony, majd azt is használnia kell, amelyek MTU képest nagyobb csomagméret.

#### <a name="azure-and-vm-mtu"></a>Az Azure és a virtuális gép MTU

Az Azure virtuális gépek MTU alapértelmezés szerint 1500 bájt. A virtuális hálózat az Azure stack-töredékben 1400-as bájt, egy csomag megkísérli. Azonban a virtuális hálózat az Azure stack lehetővé teszi csomagok 2006 bájt akár Ha a "Tördelést" bit be van állítva az IP-fejléc.

Fontos megjegyezni, hogy a töredezettséget nem jelentik azt, hogy a virtuális hálózat az Azure stack nem természetüknél fogva hatékony, mivel töredékek csomagok 1400-as bájt, míg a virtuális gépek 1500 MTU. A valóságban, hogy a hálózati csomagok álló sokkal kisebb, mint az 1400-as bájt vagy 1500 bájt.

#### <a name="azure-and-fragmentation"></a>Az Azure és a töredezettség

Az Azure virtuális hálózati vermet ma van konfigurálva, dobja el a "Out of rendelés töredék" – azaz töredezett csomagokat eredményezhet, amely az eredeti töredezett sorrendben nem érkezik. Ezek a csomagok elsősorban bejelentett November 2018 FragmentStack nevű hálózati biztonsági rés miatt a rendszer elveti.

FragmentSmack a Linux kernel kezelése töredezett IPv4 és IPv6-csomagokat összeállítását hibának. A távoli támadó használhatja ezt a hibát a célrendszeren nagyobb CPU és a egy szolgáltatásmegtagadást okozhat az eseményindító költséges töredék újraépítési műveletekhez.

#### <a name="tune-the-mtu"></a>MTU hangolása

Az Azure virtuális gépek egy konfigurálható MTU ugyanúgy, mint bármely más operációs rendszer támogatja. Azonban a töredezettséget, amely Azure-ban történik, és a fenti részletes kell alkalmazni az MTU konfigurálásakor.

Azure ügyfelek számára, hogy növelje a virtuális gép MTU nem javasoljuk. A hozzászólás célja részletesen elmagyarázza, hogyan Azure MTU valósít meg és hajtja végre a töredezettség még ma.

> [!IMPORTANT]
>MTU növelése nem segített a teljesítmény javítása, és az alkalmazás teljesítményére negatív hatással lehet.
>
>

#### <a name="large-send-offload-lso"></a>Nagy küldemények kiszervezése (LSO)

Nagy küldése Kiszervezését (LSO) a hálózati teljesítmény javíthatja a csomagok és az Ethernet-adaptert a szegmentálási felé történő kiszervezésével. LSO engedélyezve van, az a TCP/IP-verem hozzon létre egy nagy méretű TCP-csomagot, és az Ethernet-adaptert, majd Szegmentálás továbbítása előtt küldött. LSO az az előnye, hogy a Processzor, a csomagok szegmentálja a csomag mérete, amely MTU megfelel, és az Ethernet-adapter, hol történik a hardver a feldolgozás kiszervezése az ingyenes is. LSO elérhető előnyökkel kapcsolatban további tudnivalókat található [dokumentáció a Microsoft hálózati Adapter teljesítményét](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Ha LSO engedélyezve van, az Azure-ügyfelek keret nagy méretek jelenhet meg ha teljesítő csomagrögzítéseket gyűjtsenek. A keret nagy méretek egyes ügyfelek feltételezi, hogy töredezettség vagy a Jumbo-keretek használatát MTU van használatban, ha az nem vezethet. LSO, az ethernet-adapterét egy nagyobb MSS a TCP/IP-vermet, meghirdethet egy nagyobb méretű TCP-csomag létrehozásához. A teljes nem szegmentált keret továbbítja az Ethernet-adaptert, és a virtuális gépen végrehajtott csomagrögzítés látható lesz. Azonban a csomagot fogja kell bontani sok kisebb keretek a Ethernet adapter az Ethernet-adaptert MTU megfelelően.

### <a name="tcpmss-window-scaling-and-pmtud"></a>TCP/MSS ablak méretezéssel és a PMTUD

#### <a name="explanation-of-tcp-mss"></a>TCP MSS ismertetése

TCP maximális szegmens mérete (MSS) a TCP-szegmens maximális méretének beállítása javasolt beállítás elkerülhető legyen a TCP-csomagokat töredezettsége. Operációs rendszerek általában be MSS, MSS MTU - = TCP-fejléc & IP-méret (20 bájt vagy teljes 40 bájt). Így egy olyan csatoló esetében egy MTU 1500 lesz egy 1460 az MSS. Az MSS azonban nem konfigurálható.

Ez a beállítás a háromutas TCP-kézfogás a egyetért abban, ha egy TCP-munkamenet be van állítva, a forrás és cél között. Mindkét fél küldése egy MSS értéket, és a TCP-kapcsolatot a két alacsonyabb szolgál.

A közvetítő hálózati eszközök, például a VPN-átjárók, beleértve az Azure VPN Gateway, módosíthatja az optimális hálózati teljesítmény biztosítása érdekében a forrás- és független MTU jogosultak. Tehát Megjegyzendő, hogy a forrás- és önálló MTU ne legyen-e a tényleges MSS érték egyetlen tényezők.

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>PMTU (PMTUD) ismertetése

MSS egyeztetve van, amíg azt nem utalhat, előfordulhat, hogy a tényleges MSS használható, mint a forrás és cél közötti elérési úton egyéb hálózati eszközöket, mint a forrás- és egy kisebb MTU-érték. Ebben az esetben az eszközt, amelynek MTU értéke kisebb, mint a csomagot fogja dobja el a csomagot, és annak MTU tartalmazó Internet Control Message Protocol (ICMP) töredezettsége szükséges (a típus 3, a kód 4) üzenetet küld vissza. Az ICMP-üzenet lehetővé teszi, hogy a forrás gazdagépen az elérési út MTU megfelelően csökkentése érdekében. A folyamat PMTU-felderítés nevezzük.

A folyamat PMTUD természetüknél fogva nem hatékony, valamint hálózati teljesítmény hatással van. Csomagok, amelyek meghaladják a hálózati elérési út MTU elküldése, majd ezeket a csomagokat kell lesz küldve, az alacsonyabb MSS. Ha a küldő nem kap a ICMP darabolás szükséges csomagot, az elérési út (gyakran nevezik PMTUD blackhole), a hálózati tűzfal miatt, majd a küldő nem tud az MSS csökkentheti, és folyamatosan kell küldenie a csomagot. Emiatt nem javasoljuk, hogy növelje az Azure virtuális gép MTU.

#### <a name="vpn-considerations-with-mtu"></a>MTU VPN megfontolások

Ügyfelek, amelyek a virtuális géppel, amelyek hajtsa végre a beágyazás (például IPSec virtuális magánhálózatok) lehet csomagméretet és MTU további következményeiről. VPN-eket adjon hozzá további fejlécek az eredeti csomagot, így csomagok méretének növelését és a egy kisebb MSS igénylő lehet hozzáadni.

Az aktuális ajánljuk az Azure-hoz, hogy TCP MSS korlátozását 1350 bájt és 1400-as MTU alagútkapcsolaton beállítása. További információ található a [VPN eszközök és IPSec/IKE-Paraméterek lap](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Késés, a üzenetváltási idő és a TCP-ablak méretezése

#### <a name="latency-and-round-trip-time"></a>Valós idejű adatelérést és üzenetváltási idő

Hálózati késés szabályozzák a fénysebességgel fiber száloptikás hálózaton keresztül. A valóságban ez hálózati átviteli sebessége a TCP is hatékonyan szabályozottabbá (gyakorlati méretkorlát) miatt az üzenetváltási időt (RTT) két hálózati eszköz között.

| | | | |
|-|-|-|-|
|Útválasztás|Távolság|Egyirányú idő|Üzenetváltási időt (RTT)|
|New York, San Francisco|4,148 km-re|21 ms|42 ms|
|New York-i London|5,585 km-re|28 ms|56 ms|
|Sydney New York-i|15,993 km-re|80 ms|160 ms|

Ez a táblázat bemutatja a lineáris távolság két helye között azonban hálózatokon, távolság általában hosszabb, mint a lineáris távolság. Egy egyszerű képlet minimális RTT kiszámításához a fénysebességgel szabályozzák: minimális RTT = 2 * (adja meg kilométerben Távolságskála / propagálás sebessége).

Propagálás sebessége egy standard értéke 200 is használható.-1 ezredmásodpercre az fogadja a távolságot a mérőszámok alacsony érték.

A példában New York, San Francisco célszerű a lineáris távolság 4,148-km-re. Minimális RTT = 2 * (4,148 200). A kimenet a egyenlet ezredmásodpercben lesz.

Mivel a két hely közötti fizikai távolság rögzített valósággá, ha a legnagyobb hálózati teljesítmény szükség, majd válassza ki a célhelyek között legkisebb távolságot, leginkább logikus lehetőség. Másodlagosan tervezési döntéseket a virtuális hálózaton belül lehet tenni elérési útjának forgalom optimalizálása, és a késés csökkentése érdekében. Ezek a virtuális hálózati megfontolások a hálózati kialakítási szempontok az alábbi szakasz ismerteti.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Valós idejű adatelérést és üzenetváltási idő hatások TCP

Üzenetváltási időt (RTT) TCP maximális átviteli közvetlen hatással van. A TCP protokoll ablakméret elvét használva állítják rendelkezik. Az ablak mérete előtt a küldő nyugtázása a fogadó kell kapnia a TCP-kapcsolaton keresztül elküldött forgalom maximális mennyisége. Ha a TCP MSS 1460 értékre van állítva, és a TCP-ablakméret van beállítva, és 65535 közötti majd a küldő is 45 csomagok küldése előtt a nyugtázás fogadjon kell bocsátani a fogadó. Ha nem érkezik nyugtázás a küldő megpróbálja újraküldeni. Ebben a példában a TCP-ablakméret / TCP MSS = küldött csomagok. Vagy 65535 / 1460 kerekíti a rendszer legfeljebb 45.

A "Várakozás az nyugtázási", hozhat létre az adatok, egy megbízható kézbesítést mechanizmusként állapota milyen hatékonyan hatására RTT TCP átviteli sebesség hatással lehet. Minél hosszabb a küldő megvárja, amíg nyugtázása a továbbiakban azt kell is várja meg, további adatok küldése előtt.

A képlet egyetlen TCP-kapcsolat maximális átviteli sebesség kiszámítása a következőképpen történik: Ablak mérete / (Körbejárási késés (MS) / 1000) = maximális bájt/s. Az alábbi táblázat (MB) az olvashatóság érdekében formázva van, és megjeleníti a maximális MB / s sebességet egyetlen TCP-kapcsolat.

| | | | |
|-|-|-|-|
|TCP-ablakméret (bájt)|Körbejárási késés<br/>Ennyi ezredmásodpercig tart|Maximum<br/>S sebességet (MB)|Maximum<br/> MB / s sebességet|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

Ha bármely csomagvesztés, majd, csökkenti a TCP-kapcsolat maximális átviteli sebesség közben a küldő újraküldi a már küldött adatokat.

#### <a name="explanation-of-tcp-window-scaling"></a>A TCP-ablak méretezés magyarázata

TCP ablak skálázás nem egy fogalom, amely dinamikusan növeli a TCP-ablakméret további adatok küldésének ennyi nyugtázást. Ebben a korábbi példában 45 csomagok lenne küldhető előtt nyugtázást volt szükség. Ha a nyugtázást van előtt küldött csomagok, majd a TCP maximális átviteli sebesség is nő, hogy hányszor nyugtázási vár a küldő csökkentésével.

TCP-átviteli mutatják be az alábbi egyszerű táblázat:

| | | | |
|-|-|-|-|
|TCP-ablakméret<br/>(bájt)|Körbejárási késés (MS)|Maximum<br/>S sebességet (MB)|Maximum<br/> MB / s sebességet|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

Azonban a TCP fejléc TCP-ablakméret értéke csak 2 bájt hosszúságú, ami azt jelenti, hogy a fogadó ablakban maximális értéke 65535. Annak érdekében, hogy a maximális méretének növeléséhez TCP ablak nagyságrendi tényezőt jelent meg.

A méretezési tényező beállítás is, amelyek az operációs rendszerben konfigurálhatók. A méretezési tényező használatával a TCP-ablakméret kiszámítására használt képlet a következőképpen történik: TCP-ablakméret TCP-ablakméret bájtban = \* (2 ^ tényező méretezése). Ha az ablak Mértéktényező 3 és 65535 közötti ablak mérete, a számítási következőképpen történik: 65535 \* (2 ^ 3) = 262,140 bájt. 14 nagyságrendi tényezőt eredményez a TCP-ablakméret 14 (a maximális engedélyezett eltolásnak), majd a TCP-ablakméret 1,073,725,440 bájtok (8,5 gigabitig) lesz.

#### <a name="support-for-tcp-window-scaling"></a>TCP-ablak méretezés támogatása

Windows, és a különböző méretezési tényező beállítani egy alapon történik a kapcsolat típusa – nincsenek kapcsolatok (datacenter, az internetes és így tovább) több osztályt. Az ablak méretezési kapcsolat besorolás a Get-NetTCPConnection powershell-paranccsal tekintheti meg.

```powershell
Get-NetTCPConnection
```

Az értékeket minden egyes osztály a Get-NetTCPSetting powershell-paranccsal tekintheti meg.

```powershell
Get-NetTCPSetting
```

A kezdeti TCP-ablakméret és TCP méretezési faktor állíthatók be a Windows a Set-NetTCPSetting powershell-parancs segítségével. További információ található a [Set-NetTCPSetting lap](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

AutoTuningLevel hatékony TCP beállításait az alábbiak szerint.

| | | | |
|-|-|-|-|
|AutoTuningLevel|Méretezési faktor|Skálázási szorzót|A képlet<br/>maximális méretének kiszámítása|
|Letiltva|None|None|Ablakméret|
|Korlátozott|4|2^4|Ablakméret * (2 ^ 4)|
|Erősen korlátozott|2|2^2|Ablakméret * (2 ^ 2)|
|Normál|8|2^8|Ablakméret * (2 ^ 8)|
|Kísérleti|14|2^14|Ablakméret * (2 ^ 14)|

Ezek a beállítások, amelyek közül a legnagyobb valószínűséggel TCP teljesítményét Megjegyzendő, hogy az interneten, az Azure-ban, a vezérlőn kívüli sok más tényező is befolyásolhatja a TCP teljesítményét.

#### <a name="increase-mtu-size"></a>Maximális átviteli egység méretének növelése

Egy logikai kérdést tehet fel az "MTU növelése növelheti a TCP teljesítményt, nagyobb MTU azt jelenti, hogy egy nagyobb MSS"? A válasz egyszerű – valószínűleg nem áll. Írtaknak, vannak előnyei és hátrányai a csomag mérete, amelyek csak a TCP-forgalom nem alkalmazható. Mint már említettük, a befolyásoló legfontosabb tényezők a TCP átviteli teljesítmény, a TCP-ablakméret, a csomagvesztés, valamint a RTT.

> [!IMPORTANT]
> Az Azure nem javasolja, hogy az Azure-ügyfelek módosítsa az alapértelmezett MTU-érték virtuális gépeken.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>A gyorsított hálózatkezelés és a Fogadóoldali skálázás

#### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Virtuális gép hálózati funkciók régebben lett CPU-igényes VM vendégként és a Hipervizorgazda is. Minden csomagot, amely a gazdagépen keresztül továbbítását a szoftver dolgozza fel a gazdagép Processzorának – beleértve az összes a virtuális hálózat beágyazás/Németország – capsulation. Így további áramlik a forgalom, amely a gazdagépen, majd a magasabb CPU-terhelés. És ha a gazdagép Processzorának foglalt egyéb műveleteket is, majd, amelyek is érinti hálózati átviteli sebességgel és késéssel. A probléma még foglalkoztak gyorsított hálózatkezelés keresztül.

Gyorsított hálózatkezelés az Azure belső fejlesztésű programozható hardver- és technológiák, például az SR-IOV keresztül konzisztens ultraalacsony hálózati késést biztosít. Váltással nagy része az Azure szoftveralapú hálózati verem a processzorok ki és be SmartNICs FPGA-alapú számítási ciklusokat, és kevesebb terhelést a virtuális gépen, a jitter és inkonzisztenciát csökkentésével a késés, a végfelhasználói alkalmazások visszaszerzi. Más szóval teljesítmény sokkal kiszámíthatóbb lehet.

Gyorsított hálózatkezelés javított teljesítménye azáltal, hogy a Vendég virtuális gép kerülni a gazdagépre, és létrehozza a közvetlenül a gazdagép SmartNIC egy datapath éri el. Gyorsított hálózatkezelés előnyei a következők:

- **Rövidebb válaszidőt / magasabb szintű csomag / másodperc (pps)**: A virtuális kapcsoló eltávolítása a datapath eltávolítja a csomagok töltött idő a gazdagép a csoportházirend feldolgozása, és növeli a dolgozhatók fel a virtuális gép csomagok száma.

- **Alacsonyabb jitter**: Virtuáliskapcsoló-feldolgozó a szabályzatot, amely a alkalmazni mennyiségét és a munkaterhelés, a CPU, amely a feldolgozási függ. A házirend betartatása hardverre tehermentesítést eltávolítja az adott változékonyságát azáltal, hogy a csomagok közvetlenül a virtuális gép eltávolítása a gazdagépet a virtuális gép kommunikációs és az összes szoftver megszakítások és környezeti kapcsolók a.

- **Csökkent a CPU-kihasználtság**: A virtuális kapcsoló a gazdagép megkerülésével kevesebb hálózati forgalom feldolgozása tekintetében CPU-kihasználtság vezet.

Gyorsított hálózatkezelés per VM alapon kifejezetten engedélyezni kell. A virtuális gép gyorsított hálózatkezelés engedélyezéséhez szükséges utasításokat esetén érhető el a [hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelés oldal](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling-rss"></a>Fogadó oldali skálázás (RSS)

Fogadóoldali skálázás a hálózati illesztőprogram technológia, amely hatékonyabban osztja el a hálózati forgalom fogadását osztja meg a fogadási feldolgozása több processzor között egy többprocesszoros rendszer. Egyszerűen fogalmazva a RSS lehetővé teszi, hogy a rendszer feldolgozni a kapott forgalmat egy nagyobb méretű, mert az egy helyett használja az összes rendelkezésre álló processzorok. Az RSS további technikai vita található a [oldal Fogadóoldali skálázás bemutatása](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

Az RSS a maximális teljesítmény érdekében, ha a virtuális gép gyorsított hálózatkezelés engedélyezett szükséges. Is lehetnek előnyei az RSS-nem engedélyezett gyorsított hálózatkezeléssel rendelkező virtuális gépeken. Ha RSS engedélyezve van, és azt engedélyezéséhez szükséges konfiguráció található meghatározása áttekintést a [hálózati átviteli sebesség optimalizálása az Azure virtual machines lapon](http://aka.ms/FastVM).

### <a name="tcp-time-wait-and-time-wait-assassination"></a>TCP várakozási idő és az idő várjon Assassination

Egy másik gyakori hiba, amely hatással van a hálózati és az alkalmazások teljesítményének a TCP idő várjon beállítása. Foglalt virtuális gépeken, amelyek nyitó és záró számos szoftvercsatornák, vagy egy ügyfél vagy kiszolgáló (IP:Source forrásport és célport IP:Destination), a TCP, normál működés során egy adott szoftvercsatorna is végül várakozási idő állapotban a jelentős mennyiségű időt. A "várakozási idő" állapotban, hogy a lezárás előtt szoftvercsatorna kézbesítendő további adatok az adott. Ezért TCP/IP-vermek általában a újbóli használatának tiltása a szoftvercsatorna csendes elvetésével az ügyfelek TCP külön csomagot.

Ennyi ideig szoftvercsatorna van állapotba konfigurálható, de 240 másodperc, 30 másodperces terjedhet. Sockets véges erőforrás, és a egy adott időpontban használható szoftvercsatornák számát konfigurálható (általában rejlik körülbelül 30 000 lehetséges szoftvercsatornák számát). Ha ez a szám kimerül, vagy az ügyfelek és kiszolgálók eltérő várakozási beállításai, és egy virtuális Gépet megpróbálja újból felhasználhatja a szoftvercsatorna várakozási idő állapotban, majd új kapcsolatok sikertelen lesz, mivel TCP külön csomagot csendes módban a rendszer elveti.

Kimenő szoftvercsatornák, valamint a TCP idő várjon beállítások és a szoftvercsatorna újbóli porttartomány értéke általában az operációs rendszer a TCP/IP-verem belül konfigurálható. Ezek a számok módosítása javíthatja a skálázhatóságot, de a helyzettől függően sikerült bevezetni együttműködési problémák és körültekintően kell módosítani.

Idő várjon Assassination elnevezésű képesség korlátozás skálázás ennek jelent. Idő várjon Assassination lehetővé teszi, hogy egy szoftvercsatorna alatt bizonyos helyzetekben, mint például amikor az IP-csomag az új kapcsolat feladatütemezési száma meghaladja az előző kapcsolat le a legutóbbi csomag sorszáma fel újra. Ebben az esetben az operációs rendszer lehetővé teszi az új kapcsolatot kíván létesíteni (fogadja el az új SZIN ACK) és kényszerítése zárjon be az előző kapcsolat, amely időben lett várakozási állapot. Ez a funkció a támogatott Windows virtuális gépek Azure-ban még ma, és támogatási belüli más virtuális gépek Azure-ügyfelei számára a megfelelő operációsrendszer-szállítók által kell vizsgálni.

Dokumentáció TCP idő várjon beállítások és Forrásporttartomány konfigurálásával érhető el: a [beállításokat, amelyeket a hálózati teljesítmény javításához lapon módosíthatók](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuális hálózat tényező befolyásolhatja a teljesítményt

### <a name="vm-maximum-outbound-throughput"></a>Virtuális gép maximális kimenő átviteli sebesség

Az Azure számos olyan Virtuálisgép-méretek és-típusok, minden egyes teljesítménybeli különböző vegyesen. Egy ilyen teljesítmény a funkció akkor a hálózati átviteli sebesség (vagy a sávszélesség) megadása megabit / másodperc (Mbps). Virtuális gépek megosztott hardveren található, mert a hálózati kapacitás a virtuális gépek ugyanazt a hardvert megosztás között viszonylag kell osztani. Nagyobb virtuális gépek viszonylag további sávszélesség-nál kisebb méretű virtuális gépek vannak lefoglalva.

A hálózati sávszélességet, minden virtuális gép számára lefoglalt kimenő forgalom (kimenő) forgalmat a virtuális gépről a forgalmi díjas. Minden hálózati forgalmat a virtuális gép elhagyása után kell fizetnie a lefoglalt korlátot, függetlenül a cél felé. Ha például egy virtuális gépet egy 1000 MB/s-korlát, akkor ezt a korlátot attól függetlenül érvényes, a kimenő forgalom az azonos virtuális hálózatba, vagy Azure-on kívül egy másik virtuális gép szánt.
Bejövő forgalom nem díjköteles, vagy közvetlenül korlátozott. Vannak azonban egyéb tényezők, például a Processzor- és tárterületi korlátozásai, ami hatással lehet a virtuális gépek képesek-e feldolgozni a beérkező adatokat.

Gyorsított hálózatkezelés funkciója, amelyekkel javítható a hálózati teljesítmény, beleértve a késés, átviteli sebesség és CPU-kihasználtság. Gyorsított hálózatkezelés javítja a virtuális gépek átviteli sebesség, miközben azt is megteheti csak a virtuális gép legfeljebb kiosztott sávszélességet.

Az Azure virtual machines rendelkeznie kell egy, hanem több, a hálózati adapterek csatlakoztatva. Egy virtuális gépnek kiosztott sávszélesség egyezik meg az összes kimenő forgalmat a virtuális géphez csatlakoztatott összes hálózati adapter esetében. Más szóval virtuális gépenként, hány hálózati adaptereket a virtuális géphez csatolt függetlenül a lefoglalt a sávszélesség.
 
Várt kimenő adatátviteli és az egyes virtuális gép mérete által támogatott hálózati adapterek száma részleteit itt. Maximális átviteli sebesség megtekintéséhez válassza ki a típusát, például az általános célú, majd válassza ki a méretet-sorozat az eredményül kapott oldalon, mint a Dv2 sorozat. Minden sorozatot a hálózatkezelés című, a hálózati adapterek max. száma az utolsó oszlopban specifikációk tartalmazó táblázat található / várt hálózati teljesítmény (Mbps).

Az átviteli sebesség korlát vonatkozik a virtuális géphez. Átviteli sebesség nem érinti a következő tényezőktől függ:

- **Hálózati adapterek száma**: A sávszélesség korlátja minden kimenő forgalom a virtuális gépből épül.

- **Gyorsított hálózatkezelés**: Bár a funkció akkor hasznosak, növelve a közzétett korlátot, nem változtatja meg a határértéket.

- **Forgalom cél**: Az összes destinations beleszámítanak a kimenő korlátot.

- **Protokoll**: Minden kimenő forgalmat az összes protokollhoz keresztül a határérték felé számolnak.

A [felkeresésével ezen a lapon található tábla a maximális sávszélesség a Virtuálisgép-típusonként](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) és a megfelelő Virtuálisgép-típusra kattint. Minden típusa lapon egy táblázat jelennek meg a maximális hálózati adapterek és a várható maximális hálózati sávszélességet.

További információ a virtuális gép hálózati sávszélesség található [virtuális gép hálózati sávszélesség](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Internetes teljesítménnyel kapcsolatos szempontok

Ez a cikk teljes írtaknak tényezők az interneten és az Azure a vezérlőn kívüli hatással lehet a hálózati teljesítmény. Ezek a tényezők a következők:

- **Késés**: Két cél közötti üzenetváltási idő hatással lehet a köztes hálózatokon, ne használjon "legrövidebb" távolság elérési út lehetséges és az optimálisnál rosszabb társviszony-létesítési útvonalakon forgalom problémák

- **Csomagveszteség**: Csomagveszteség okozhatja hálózati torlódás, a fizikai elérési út problémák és a hálózati eszközök mellett végrehajtása

- **Maximális átviteli egység mérete/töredezettsége**: A csomagok ugyanis ronthatják sorrendben, vagy az adatok érkezési késéseket, ami hatással lehet a csomagok kézbesítését az elérési úton elhelyezkedő töredezettsége vezethet

A traceroute mérésére hálózati teljesítményt nyújt (például a csomagvesztés és a késés) mentén minden hálózati elérési út a forrás- és eszköz közötti hatékony eszköz.

### <a name="network-design-considerations"></a>Hálózati kialakításával kapcsolatos szempontok

A fenti szempontok együtt egy virtuális hálózat a topológia hatással lehet a virtuális hálózati teljesítmény. Ha például egy küllős Tervező, hogy a forgalom backhauls globálisan egy egyetlen központi virtuális hálózaton vezeti be a hálózati késést, és így hatással az általános hálózati teljesítmény. Ehhez hasonlóan a hálózati adatforgalom azon hálózati eszközök számát is befolyásolhatja összesített késés. Például egy küllős kialakításában, ha továbbítja forgalmat egy küllős hálózati virtuális készüléket és a központ virtuális berendezés előtt áthaladó csatlakozik az internethez, majd késés is vezeti be a hálózati virtuális berendezések.

### <a name="azure-regions-virtual-networks-and-latency"></a>Az Azure-régiók, a virtuális hálózatok és a késés

Azure-régió általános földrajzi területét belül található több adatközpontban épülnek fel. Ezek az adatközpontok fizikai nem lehet egymás mellé, és bizonyos esetekben előfordulhat, hogy elválasztani kevesebb mint 10 alapján. A virtuális hálózat nem egy logikai területen felül a fizikai adatközpont-hálózatához az Azure és a egy virtuális hálózat nem jelenti azt, semmilyen konkrét hálózati topológia az adatközponton belül. Például virtuális gép és a virtuális gép B szerepelnek ugyanazon a virtuális hálózatot és alhálózatot, de a különböző állványokon, sorok, még akkor is, adatközpontok lehet. Előfordulhat, hogy megtalálják a helyüket fiber száloptikás kábelt vagy adja meg kilométerben fiber száloptikás kábelt elválasztani. Ez a valóságban vezethet be változó késés (néhány ezredmásodperc eltérést) különböző virtuális gépek között.

A földrajzi helyét, és így két virtuális gépet, a késés befolyásolhatja keresztül a rendelkezésre állási csoportok és a rendelkezésre állási zónák konfigurációját, de egy régióban adatközpontok közötti távolság régióspecifikus és túlnyomórészt által befolyásolt Adatközpont-topológia a régióban.

### <a name="source-nat-port-exhaustion"></a>Forrás NAT portfogyás

Központi telepítés az Azure-ban a nyilvános interneten és/vagy a nyilvános IP-címtér az Azure-on kívülről végpontok kommunikálhat. Amikor egy példányt a kimenő kapcsolatot kezdeményez, Azure dinamikusan rendeli a magánhálózati IP-cím nyilvános IP-címre. Ez a leképezés létrehozása után a kimenő származó folyamat a visszatérő forgalom is elérheti a magánhálózati IP-címet, adja meg a folyamat.

Minden kimenő kapcsolat az Azure Load Balancer kell tartania a bizonyos ideig ezt a hozzárendelést. Az Azure több-bérlős jellegét Ez a leképezés minden kimenő flow minden virtuális gép karbantartása lehet erőforrás-igényes. Ezért nincsenek korlátai, állítsa be és az Azure virtuális hálózat konfigurációja alapján. Vagy conditions stated above pontosabban – egy Azure virtuális gép csak tehet a kimenő kapcsolatok bizonyos számú egy adott időpontban. Ha ezek a korlátok elfogy, majd az Azure virtuális gép nem fognak működni bármilyen további kimenő kapcsolatokat.

Ez a viselkedés, azonban, konfigurálható. További információ [SNAT és SNAT portot Erőforrásfogyás], lásd: [Ez a cikk](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Mérték hálózati teljesítmény az Azure-ban

A teljesítmény kapacitástervezés ebben a cikkben számos kapcsolódó hálózati késést / üzenetváltási időt (RTT) két virtuális gép között. Ez a szakasz néhány javaslatot tesz a késési/RTT, valamint a TCP teljesítményét és a virtuális gép hálózati teljesítmény tesztelése. A TCP/IP- és hálózati értékeket a fent ismertetett hangolásával és teljesítményének tesztelése az alább ismertetett technikák. A fent felsorolt számítások késés, MTU, MSS és ablakméret értékeit is használható, és elméleti maximális értékei a tesztelés során észlelt tényleges értékek összehasonlíthatók.

### <a name="measure-round-trip-time-and-packet-loss"></a>Mérték üzenetváltási idő és a csomagveszteség

A TCP teljesítményének erősen támaszkodik a RTT és a csomagveszteség. A legegyszerűbben RTT és a csomagveszteség mértékegysége a ping segédprogram megtalálható a Windows és Linux rendszerű használ. Ping kimenete minimális/maximális és átlagos késés a forrás és cél, valamint csomagvesztés jelennek meg. Ping alapértelmezés szerint az ICMP protokollt használja. TCP RTT teszteléséhez, akkor a PsPing használhatók. További információt a PsPing [ezt a hivatkozást](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Mérték tényleges átviteli sebesség TCP-kapcsolat

NTttcp egy olyan eszköz, Linux vagy Windows virtuális gép TCP teljesítményének a tesztelésére szolgál. Különböző TCP-beállításokat is lehet tweaked, és az előnyök tesztelt NTttcp használatával. További információ a NTttcp található, az alábbi hivatkozásokat.

- [A sávszélesség/átviteli sebesség tesztelése (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp segédprogram](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Mérték tényleges sávszélességet a virtuális gép

A teljesítmény tesztelése a virtuális gépek különböző típusairól, gyorsított hálózatkezelés és így tovább, tesztelhető legyen Iperf, Linux és Windows rendszereken is elérhető nevű eszköz használatával. Iperf TCP vagy UDP tesztelésére használható teljes hálózati átviteli sebesség. TCP-átviteli tesztek Iperf használatával (késés RTT és így tovább) ebben a cikkben tárgyalt tényezők vannak hatással. Tehát UDP eredményezhet, egyszerűen csak tesztelési maximális átviteli sebesség jobb eredményeket.

További információ alatt található:

- [Az Expressroute hálózati teljesítmény hibaelhárítása](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Hogyan lehet egy virtuális hálózati VPN teljesítményének érvényesítése](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Nem elég hatékony TCP viselkedések észleléséhez

Az Azure-ügyfelek, amelyek a hálózati teljesítményproblémák csomagrögzítés jelenhet meg a TCP-csomagokat a TCP-jelzők (ZSÁK, Ismétlődő ACK, ÚJRAKÜLDÉSI és gyors TIMEOUTS). Ezek a csomagok kifejezetten hálózati hatékonysági eredményeként csomagvesztés jelzik. Csomagveszteség viszont nem feltétlenül Azure teljesítménnyel kapcsolatos problémák miatt. Teljesítménnyel kapcsolatos problémák az alkalmazást, operációs rendszer vagy más problémákat is előfordulhat, hogy nem közvetlenül kapcsolódik az Azure platform oka lehet. Is fontos megjegyezni, hogy néhány újraküldési vagy duplikált, a hálózat ACK normál – megbízható készült TCP protokollt. És a TCP-csomagokat a csomagrögzítés bizonyítékokat nem feltétlenül jelez rendszeres hálózati probléma, amennyiben azok túlzott nem.

Azonban, hogy meg kell adni egyértelműen, hogy ezek csomagtípusok-e, hogy TCP átviteli sebesség nem éri el a maximális teljesítmény – a többi szakasz az említett okokból jelzések.

## <a name="next-steps"></a>További lépések

Most, hogy már beszéltünk a TCP/IP teljesítmény hangolása az Azure virtuális gépek, a következő lépések tartalmazzák [más virtuális hálózat megtervezése](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) szempontok vagy [tudjon meg többet a csatlakozás és a virtuális hálózatok konfigurálása](https://docs.microsoft.com/en-us/azure/virtual-network/).
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
ms.openlocfilehash: 1e8605a41cbe610c971b891309b2149d221b8b27
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426443"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>Az Azure virtuális gépek TCP/IP-teljesítményhangolása

Ez a cikk ismerteti a gyakran használt TCP/IP teljesítmény hangolási módszerek és szempontokat kell figyelembe venni, ha azokat használja az Azure-ban futó virtuális gépek számára. Ez egy alapvető áttekintést nyújt a módszereket, és ismerje meg, hogy azok hangolásával.

## <a name="common-tcpip-tuning-techniques"></a>Gyakran használt TCP/IP-hangolási módszerek

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU töredezettséget és Nagy küldemények kiszervezése

#### <a name="mtu"></a>MTU

A maximális átviteli egység (MTU) a legnagyobb mérete keret (csomag), (bájt), amelyeket el küldhet egy hálózati adapter keresztül megadott. A maximális átviteli egység a konfigurálható beállítást. Az alapértelmezett MTU használt Azure-beli virtuális gépeken, és az alapértelmezett beállítás a legtöbb hálózati eszközön globálisan, 1500 bájt.

#### <a name="fragmentation"></a>Töredezettség

Töredezettség akkor fordul elő, amikor egy csomagot küld a rendszer, amely meghaladja a maximális átviteli egység egy hálózati adapter. A TCP/IP-verem megszakítja a csomagot, amelyek megfelelnek a felület MTU kisebb darabokra (szilánkok). Töredezettség az IP-rétegben történik, és független a mögöttes protokollt (például a TCP). 1500 MTU-egy hálózati kapcsolaton keresztül egy 2 000 bájtos csomag elküldésekor a csomag egy 1500 bájt méretű csomagot és egy 500 bájt méretű csomagot kell bontani.

Hálózati eszközök az elérési út a forrás és cél között is vagy drop csomagot, amely meghaladja a maximális átviteli egység vagy a csomag töredékben kisebb tömbökre.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>A tördelést bites az IP-csomag

A nem töredék (DF) bit értéke azt a jelzőt, az IP-protokoll fejlécben. A DF bit azt jelzi, hogy a hálózati eszközök az elérési úton, a küldő és fogadó között nem kell töredékben a csomagot. Sikerült beállítani a bit ennek számos oka lehet. (Például a jelen cikk "PMTU" című szakaszában talál.) A hálózati eszköz megkapja a tördelést bittel csomagot, és az eszköz felületén MTU meghaladja a csomagban, a standard szintű viselkedés esetén az eszköz dobja el a csomagot. Az eszköz az ICMP darabolás szükséges üzenetet küld a csomag eredeti forrásának.

#### <a name="performance-implications-of-fragmentation"></a>Töredezettség teljesítményre gyakorolt hatása

Töredezettség negatív teljesítményre gyakorolt hatása lehet. Az egyik fő oka a hatása a teljesítményre, a Processzor/memória hatását a töredezettséget és csomagok összeállítását. A hálózati eszköz van szüksége egy csomag töredékben, amikor azt kell töredezettsége végrehajtásához CPU/memória-erőforrásokat.

Ugyanezt a csomagot a rendszer újra történik. A hálózati eszköz tárolására a töredékek száma, amíg nem érkezett, azt is, az eredeti csomaggal szétbontani rendelkezik. Ez a folyamat töredezettséget és újraépítési késés is eredményezheti.

Más lehetséges negatívan befolyásolta a teljesítményt Ennek következménye töredezettség, hogy töredezett csomagokat sorrendben érkezik. Amikor csomagokat sorrendben érkeznek, bizonyos típusú hálózati eszközöket is el kell dobni ezeket. Ha ez történik, a teljes csomag van.

Töredékek általában dobja el a biztonsági eszközök, például a tűzfalak vagy elfogytak, amikor egy hálózati eszköz megkapják a pufferek. Amikor egy hálózati eszköz megkapják a pufferek elfogytak, a hálózati eszköz szétbontani töredezett csomagokat próbált, de nem rendelkezik az erőforrások tárolásához, és a csomag reassume.

Töredezettség látható egy negatív műveletet, de a támogatási töredezettsége van szükség, nagy hálózatok csatlakozáskor az interneten keresztül.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Előnyök és következményeinek MTU módosítása

Hatékonyabb hálózati általánosan fogalmazva, növelje a MTU hozhat létre. Minden csomag továbbított fejléc-információkat adnak, akkor az eredeti csomaggal rendelkezik. Töredezettség további csomagokat hoz létre, ha nincs terhelés több fejlécet, és, amely lehetővé teszi a hálózati kevésbé hatékony.

Íme egy példa. Ethernet fejléc mérete 14 bájt és a egy 4 bájtos keret ellenőrzés feladatütemezési keret konzisztencia biztosításához. Egy 2000 bájt méretű csomagot küld a rendszer, ha Ethernet terheléssel 18 bájt hozzáadódik a hálózaton. A csomag töredezett 1500 bájt méretű csomagot és egy 500 bájt méretű csomagot, egyes csomagok Ethernet-fejléc, összesen 36 bájt 18 bájtot fog rendelkezni.

Ne feledje, hogy növelje a MTU nem feltétlenül hatékonyabb-hálózat létrehozása. Egy alkalmazás csak 500 bájtos csomagokat küld, ha ugyanazon fejléc többletterhelést fog léteznek, hogy MTU 1500 bájt vagy 9000 bájt. A hálózati hatékonyabb csak akkor, ha az MTU által érintett csomagméret nagyobb lesz.

#### <a name="azure-and-vm-mtu"></a>Az Azure és a virtuális gép MTU

Az Azure virtuális gépek MTU alapértelmezés szerint 1500 bájt. A virtuális hálózat az Azure stack-töredékben 1,400 bájt, egy csomag megkísérli. De a virtuális hálózati vermet lehetővé teszi csomagok 2,006 bájt akár Ha a tördelést bit be van állítva az IP-fejléc.

Vegye figyelembe, hogy a virtuális hálózati verem nem természetüknél fogva nem elég hatékony, mert a töredékek csomagok 1,400 bájt, annak ellenére, hogy a virtuális gépek 1500 MTU-rendelkeznek. Nagy része a hálózati csomagok sokkal kisebb, mint 1,400 vagy 1500 bájt.

#### <a name="azure-and-fragmentation"></a>Az Azure és a töredezettség

Virtuális hálózati vermet dobja el az "üzemen kívüli töredék,", nem töredezett eredeti sorrendjében érkeznek töredezett csomagokat van beállítva. Ezek a csomagok elsősorban bejelentett November 2018 FragmentSmack nevű hálózati biztonsági rés miatt a rendszer elveti.

FragmentSmack a Linux kernel kezelése töredezett IPv4 és IPv6-csomagokat összeállítását hibának. A távoli támadó használhatja ezt a hibát a célrendszeren vezethet nagyobb CPU és a egy szolgáltatásmegtagadás eseményindító költséges töredék újraépítési műveletekhez.

#### <a name="tune-the-mtu"></a>MTU hangolása

Egy Azure virtuális gép MTU, mint bármely más operációs rendszerben konfigurálhatja. Vegye figyelembe a töredezettséget, amely az Azure-ban, a fent említett megy végbe, de amikor egy MTU konfigurálásakor.

Nem javasoljuk ügyfeleinknek, hogy virtuális gép MTU növelése érdekében. A hozzászólás hivatott azt ismertetik, hogyan Azure MTU valósít meg és hajtja végre a töredezettség részleteit.

> [!IMPORTANT]
>MTU növelése nem ismeri fel a teljesítmény javítása, és az alkalmazás teljesítményére negatív hatással lehet.
>
>

#### <a name="large-send-offload"></a>Nagy küldemények kiszervezése

Nagy küldemények kiszervezése (LSO) a hálózati teljesítmény javíthatja a csomagok és az Ethernet-adaptert a szegmentálási felé történő kiszervezésével. LSO engedélyezve van, ha a TCP/IP-verem egy nagy méretű TCP-csomagot hoz létre, és elküldi azokat az Ethernet-adaptert a szegmentálási továbbítása előtt. LSO előnye, hogy a Processzor, a csomagok alkalmazásfelhasználókkal által MTU megfelel, és az Ethernet-adapter, hol történik a hardver a feldolgozás kiszervezése az ingyenes is. LSO előnyeivel kapcsolatos további tudnivalókért lásd: [támogató Nagy küldemények kiszervezése](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Ha LSO engedélyezve van, az Azure-ügyfelek keret nagy méretek jelenhet meg, csomagrögzítés végrehajtásakor. A keret nagy méretek úgy gondolja, hogy töredezettség történik az egyes ügyfelek vagy az, hogy nagy MTU van jelenleg használva, ha az nem vezethet. LSO, az Ethernet-adapterét meghirdethet egy nagyobb maximális mérete (MSS) a TCP/IP-verem létrehozása egy nagyobb méretű TCP-csomagot. A teljes nem szegmentált keret továbbítja az Ethernet-adaptert, és a virtuális gépen végrehajtott csomagrögzítés látható lesz. Azonban a csomagot fogja bontásban sok kisebb keretet, az Ethernet-adaptert, az Ethernet-adaptert MTU megfelelően.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS ablak méretezéssel és a PMTUD

#### <a name="tcp-maximum-segment-size"></a>TCP-szegmens maximális méret

TCP maximális mérete (MSS) beállítás, amely korlátozza a TCP-szegmens, elkerülheti a töredezettség TCP csomagok méretét. Operációs rendszerek által általában használt képlet MSS beállításához:

`MSS = MTU - (IP header size + TCP header size)`

Az IP-fejrész, és a TCP-fejléc: 20 bájt vagy 40 bájtok száma összesen. Ezért 1500 MTU-felületet kap egy 1,460 az MSS. Azonban az MSS konfigurálható.

Ez a beállítás van beleegyezett a TCP háromutas kézfogás a Ha a TCP-munkamenet be van állítva, egy forrás és cél között. Mindkét fél küldeni egy MSS értéket, és a TCP-kapcsolatot a két alacsonyabb szolgál.

Ne feledje, hogy a forrás- és a MTU nem az egyetlen olyan tényezőt, amelyek az MSS érték meghatározásához. Közbenső hálózati eszközök, például a VPN-átjárók, beleértve az Azure VPN Gateway, függetlenül a forrás- és optimális hálózati teljesítmény biztosítása érdekében MTU módosíthatja.

#### <a name="path-mtu-discovery"></a>PMTU

MSS egyeztetve van, de nem jelezheti, a tényleges MSS használható. Ennek oka az, előfordulhat, hogy a forrás- és a cél közötti elérési úton egyéb hálózati eszközöket, mint a forrás- és egy kisebb MTU-érték. Ebben az esetben az eszköz, amelynek MTU értéke kisebb, mint a csomag eldobja a csomagot. Az eszközt vissza egy ICMP darabolás szükséges (a típus 3, a kód 4) üzenet, amely tartalmazza a MTU küld. Az ICMP-üzenet lehetővé teszi, hogy a forrás gazdagépen az elérési út MTU megfelelően csökkentése érdekében. Az elérési út MTU-felderítés (PMTUD) nevezzük.

A PMTUD folyamat nem hatékony, és hatással van a hálózati teljesítmény. Csomagok, amelyek meghaladják a hálózati elérési út MTU elküldése, a csomagok kell az alacsonyabb MSS lesz küldve. Ha a küldő nem az ICMP darabolás szükséges üzenetet kapja, talán az elérési utat a hálózati tűzfal miatt (más néven egy *PMTUD blackhole*), a küldő nem tud az MSS csökkentheti, és folyamatosan szükséges ismét a csomagot. Ezért nem javasoljuk, hogy növelje az Azure virtuális gép MTU.

#### <a name="vpn-and-mtu"></a>VPN- és a maximális átviteli egység

Hajtsa végre a beágyazás (például IPsec virtuális magánhálózatok) virtuális gépeket használ, van-e további megfontolásokat csomagméretet és MTU kapcsolatban. VPN-eket további fejlécek hozzáadása csomagok, ami növeli a csomag mérete és a egy kisebb MSS igényel.

Az Azure-hoz azt javasoljuk, hogy beállította a TCP MSS korlátozását 1350 bájtra, és alagút 1400 MTU felületet. További információkért lásd: a [VPN eszközök és IPSec/IKE-Paraméterek lap](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Késés, a üzenetváltási idő és a TCP-ablak méretezése

#### <a name="latency-and-round-trip-time"></a>Valós idejű adatelérést és üzenetváltási idő

Hálózati késés szabályozzák a fénysebességgel fiber száloptikás hálózaton keresztül. Az üzenetváltási időt (RTT) két hálózati eszközök közötti hálózati átviteli sebessége a TCP is szabályozzák.

| | | | |
|-|-|-|-|
|**útvonal**|**távolságskála**|**Egyirányú idő**|**RTT**|
|New York, San Francisco|4,148 km-re|21 ms|42 ms|
|New York-i London|5,585 km-re|28 ms|56 ms|
|Sydney New York-i|15,993 km-re|80 ms|160 ms|

Ez a táblázat a lineáris két hely közötti távolság. A hálózatok, a távolság általában hosszabb, mint a lineáris távolság. A következő minimális RTT kiszámításához a fénysebességgel szabályozzák egy egyszerű képletet:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

200-as propagálás sebességétől is használhat. Ez az a távolság, és az, hogy világos fogadja az 1 ezredmásodpercre.

Vegyük New York, San Francisco példaként. Lineáris távolság 4,148 km-re. Csatlakoztatás ezt az értéket, a egyenlőségi, lekérjük a következőket:

`Minimum RTT = 2 * (4,148 / 200)`

A egyenlőségi kimenete ezredmásodpercben.

Ha azt szeretné, hogy a legjobb hálózati teljesítmény, jelölje be a előállítja a legrövidebb közöttük destinations, logikai lehetőség. A virtuális hálózati elérési útját forgalom optimalizálása, és csökkentheti a késéseket is tervezzen. További információkért lásd a "Hálózati kialakításával kapcsolatos szempontok" című szakaszát.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Valós idejű adatelérést és üzenetváltási idő hatások TCP

Üzenetváltási idő maximális TCP átviteli közvetlen hatással van. A TCP protokoll *ablakméret* a maximális mennyisége, amelyeket el küldhet a TCP-kapcsolaton keresztül előtt a küldő kell úgy, hogy a fogadó nyugtázási fogadjon forgalmat. Ha a TCP MSS 1,460 értékre van állítva, és a TCP-ablakméret 65 535 közötti értékre van állítva, a küldő küldhet 45 csomagok előtt nyugtázási fogadjon a fogadó rendelkezik. Ha a küldő nyugtázási nem kap, azt fogja timeouts az adatokat. Itt látható a képlet:

`TCP window size / TCP MSS = packets sent`

Ebben a példában 65 535 / 1,460 kerekíti a rendszer legfeljebb 45.

A "Várakozás az nyugtázási", olyan mechanizmus biztosítása érdekében az adatokról, megbízható kézbesítést állapota mi okozza a TCP teljesítményt érintő RTT. Minél hosszabb a küldő megvárja, amíg nyugtázás, annál több adatot küldenek, várjon van szüksége.

Íme egy TCP-kapcsolat a maximális átviteli sebesség kiszámítása a képletet:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Ez a táblázat bemutatja a maximális MB / s sebességet egyetlen TCP-kapcsolat. (Az olvashatóság érdekében (MB) szolgál a mértékegység.)

| | | | |
|-|-|-|-|
|**TCP-ablakméret (bájt)**|**Körbejárási késés (ms)**|**Maximális MB/s átviteli sebesség**|**Maximális megabit/másodperc átviteli sebesség**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

A csomagok elvesznek, ha a maximális átviteli sebesség TCP-kapcsolat csökken közben a küldő újraküldi a már küldött adatokat.

#### <a name="tcp-window-scaling"></a>TCP-ablak méretezése

TCP-ablak méretezés olyan módszer, amely dinamikusan növeli a TCP-ablakméret további adatok nyugtázást ennyi küldésének engedélyezéséhez. Az előző példában 45 csomagok lenne küldhető előtt nyugtázást volt szükség. Ha növeli a csomagok, amelyeket el küldhet előtt nyugtázást szükséges számát, a küldő vár nyugtázása, ami növeli a TCP maximális átviteli sebesség hányszor van csökkentése.

A következő táblázat bemutatja ezen kapcsolatok:

| | | | |
|-|-|-|-|
|**TCP-ablakméret (bájt)**|**Körbejárási késés (ms)**|**Maximális MB/s átviteli sebesség**|**Maximális megabit/másodperc átviteli sebesség**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

De a TCP fejléc TCP-ablakméret értéke csak 2 bájt hosszúságú, ami azt jelenti, hogy a fogadó ablakban maximális értéke 65 535. A maximális méretének növeléséhez TCP ablak nagyságrendi tényezőt jelent meg.

A méretezési tényező is egy beállítást, amely az operációs rendszer lehet konfigurálni. Itt látható a képlet a TCP-ablakméret kiszámításához méret használatával:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

3. és a egy 65 535 ablak méretének kiszámítása ablak méretezési tényező a következő:

`65,535 \* (2^3) = 262,140 bytes`

14 nagyságrendi tényezőt eredményez a TCP-ablakméret 14 (az engedélyezett maximális eltolás). A TCP-ablakméret 1,073,725,440 bájtok (8,5 gigabitig) lesz.

#### <a name="support-for-tcp-window-scaling"></a>TCP-ablak méretezés támogatása

Windows a különböző kapcsolattípusok különböző méretezési tényező állíthatja be. (A kapcsolatok osztályok között datacenter, internet stb.) Használja a `Get-NetTCPConnection` megtekintéséhez a kapcsolattípus skálázás ablak PowerShell-parancsot:

```powershell
Get-NetTCPConnection
```

Használhatja a `Get-NetTCPSetting` tekintse meg az értékeket minden egyes osztály PowerShell-parancsot:

```powershell
Get-NetTCPSetting
```

Beállíthatja a kezdeti TCP-ablakméret és TCP méretezési tényező a Windows használatával a `Set-NetTCPSetting` PowerShell-parancsot. További információkért lásd: [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Ezek a hatékony TCP beállításait `AutoTuningLevel`:

| | | | |
|-|-|-|-|
|**AutoTuningLevel**|**Méretezési faktor**|**Skálázási szorzót**|**A képlet<br/>maximális méretének kiszámítása**|
|Letiltva|None|None|Ablakméret|
|Korlátozott|4|2^4|Ablakméret * (2 ^ 4)|
|Erősen korlátozott|2|2^2|Ablakméret * (2 ^ 2)|
|Normál|8|2^8|Ablakméret * (2 ^ 8)|
|Kísérleti|14|2^14|Ablakméret * (2 ^ 14)|

Ezek a beállítások a legnagyobb valószínűséggel TCP teljesítményét befolyásolja, de ne feledje, hogy az interneten, az Azure-ban, a vezérlőn kívüli sok más tényező is befolyásolhatja a TCP teljesítményét.

#### <a name="increase-mtu-size"></a>Maximális átviteli egység méretének növelése

Mivel egy nagyobb MTU nagyobb MSS azt jelenti, hogy vezetőnév e MTU növelése növelheti a TCP teljesítményét. Valószínűleg nem. Vannak, és a csomag mérete túl csak a TCP-forgalom hátrányai. Ahogy arra már korábban, a TCP-átviteli teljesítményét befolyásoló legfontosabb tényezők a TCP-ablakméret csomagvesztés és RTT.

> [!IMPORTANT]
> Nem ajánlott, hogy az Azure-ügyfelek módosíthatja az alapértelmezett MTU-érték virtuális gépeken.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>A gyorsított hálózatkezelés és a fogadóoldali skálázás

#### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Virtuális gép hálózati funkciók régebben lett Processzort, a Vendég virtuális Gépen és a hipervizorgazda is. Minden csomagot, amely a gazdagépen keresztül továbbítását a szoftver dolgozza fel a gazdagép CPU-használat, beleértve az összes virtuális hálózati beágyazás és kibontás. Így további áramlik a forgalom, amely a gazdagép, annál magasabb CPU betölteni. És ha a gazdagép Processzorának elfoglalva más műveletekkel, amely is hatással lesz hálózati átviteli sebességgel és késéssel. Az Azure gyorsított hálózatkezeléssel problémát orvosolja.

Gyorsított hálózatkezelés a belső fejlesztésű programozható hardver az Azure és technológiákkal, mint az SR-IOV-n keresztül konzisztens ultralow hálózati késést biztosít. Gyorsított hálózatkezelés nagy része az Azure szoftveralapú hálózati verem ki a processzorok és az FPGA-alapú SmartNICs helyezi át. A módosítás lehetővé teszi a végfelhasználói alkalmazások visszaigényléséhez számítási ciklusok, ami kevesebb terhelést helyezi a virtuális gépen, a késés jitter és inkonzisztenciát csökkentésével. Más szóval teljesítmény sokkal kiszámíthatóbb lehet.

Gyorsított hálózatkezelés javítja a teljesítményt azáltal, hogy a Vendég virtuális Gépen kerülni a gazdagépre, és létrehozza a közvetlenül a gazdagép SmartNIC egy datapath. Az alábbiakban néhány gyorsított hálózatkezelés előnyei:

- **Rövidebb válaszidőt / magasabb szintű csomag / másodperc (pps)**: A virtuális kapcsoló eltávolítása a datapath kiküszöböli a csomagokat használhat fel a gazdagép a házirend-feldolgozási idő, és növeli a dolgozhatók fel a virtuális gép csomagok száma.

- **Alacsonyabb jitter**: Virtuáliskapcsoló-feldolgozó a szabályzatot, amely a alkalmazni mennyiségét és a munkaterhelés, a CPU, amely a feldolgozási függ. A házirend betartatása hardverre tehermentesítést eltávolítja az adott változékonyságát azáltal, hogy a csomagok közvetlenül a virtuális Gépet, mivel a gazdagép Virtuálisgép kommunikáció és az összes szoftver megszakítások és a környezet kapcsolók a.

- **Csökkent a CPU-kihasználtság**: A virtuális kapcsoló a gazdagép megkerülésével kevesebb hálózati forgalom feldolgozása tekintetében CPU-kihasználtság vezet.

Gyorsított hálózatkezelés használata, meg kell explicit módon engedélyezi azt az egyes megfelelő virtuális Gépre. Lásd: [hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelésű](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) útmutatást.

#### <a name="receive-side-scaling"></a>Fogadóoldali skálázás

Méretezés (RSS), amely hatékonyabban osztja el a hálózati forgalom fogadását osztja meg a hálózati illesztőprogram technológia kap feldolgozása több processzor között olyan többprocesszoros rendszerek oldalán kaphat. Egyszerűen fogalmazva az RSS lehetővé teszi a kapott forgalmat több feldolgozni, mert az összes rendelkezésre álló processzorok egy helyett használja a rendszer. További technikai az RSS, lásd: [fogadóoldali skálázás bemutatása](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

A legjobb teljesítmény akkor kaphat, ha a virtuális gépen engedélyezve van a gyorsított hálózatkezelés, akkor engedélyeznie kell az RSS. Az RSS is lehetővé teszi a gyorsított hálózatkezelést előnyei nem használó virtuális gépeken. Annak megállapítása, hogy engedélyezve van-e az RSS- és engedélyezését a áttekintését lásd: [hálózati átviteli sebesség optimalizálása az Azure virtual machines](http://aka.ms/FastVM).

### <a name="tcp-timewait-and-timewait-assassination"></a>TCP TIME_WAIT és TIME_WAIT assassination

TCP TIME_WAIT egy másik gyakori beállítás, amely hatással van a hálózati és az alkalmazások teljesítményét. A foglalt virtuális gépeken, amelyek nyissa meg és zár be számos, az ügyfelek vagy kiszolgálók (IP:Source forrásport és célport IP:Destination), TCP a normál működés során egy adott szoftvercsatorna is megtörténhet TIME_WAIT állapotú hosszú ideje. A TIME_WAIT állapot célja, hogy mielőtt bezárná, kézbesítendő szoftvercsatorna további adatok. Így TCP/IP-vermek általában a újbóli használatának tiltása a szoftvercsatorna TCP külön csomagot az ügyfél csendes elvetésével.

Mennyi ideig TIME_WAIT van egy szoftvercsatorna nem konfigurálható. Ez terjedhet 30 másodperces 240 másodpercre. Sockets véges erőforrás, és a egy adott időpontban használható szoftvercsatornák számát konfigurálható. (Az elérhető szoftvercsatornák számát az általában körülbelül 30000.) Ha az elérhető szoftvercsatornák felhasználják, vagy ha az ügyfelek és kiszolgálók eltérő TIME_WAIT beállításait, és egy virtuális Gépet megpróbálja újból felhasználhatja a szoftvercsatorna TIME_WAIT állapotú, új kapcsolatok TCP külön csomagot csendes módban a rendszer elveti, sikertelen lesz.

Kimenő szoftvercsatorna-porttartomány értéke az operációs rendszer a TCP/IP-verem belül általában konfigurálható. Ugyanezt a TCP TIME_WAIT beállításait és a szoftvercsatorna újrafelhasználását. Ezek a számok módosítása esetleg javíthatja a skálázhatóságot. De a helyzettől függően ezek a változások együttműködési problémák okozhatják. Legyen óvatos, ha megváltoztatja ezeket az értékeket.

TIME_WAIT assassination használatával oldja meg a méretezési korlátozás. TIME_WAIT assassination lehetővé teszi, hogy szeretné újrafelhasználni bizonyos helyzetekben, például ha az új kapcsolat az IP-csomag sorszáma le az előző kapcsolat a legutóbbi csomag feladatütemezési száma meghaladja a szoftvercsatorna. Ebben az esetben az operációs rendszer lehetővé teszi az új kapcsolatot kíván létesíteni (akkor fogadja el az új SZIN ACK) és kényszerítése zárjon be az előző kapcsolat, amely TIME_WAIT állapotú volt. Ez a képesség az Azure-beli Windows virtuális gépeken támogatott. Támogatás a többi virtuális gép kapcsolatos további információkért ellenőrizze az operációs rendszer szállítójához.

TCP TIME_WAIT beállítások és Forrásporttartomány konfigurálásával kapcsolatos további információkért lásd: [beállításokat, amelyeket a hálózati teljesítmény javításához módosíthatók](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Virtuális hálózat olyan tényezőt, amelyek hatással lehetnek a teljesítmény

### <a name="vm-maximum-outbound-throughput"></a>Virtuális gép maximális kimenő átviteli sebesség

Az Azure biztosít a különböző Virtuálisgép-méretek és típusok, minden egyes teljesítménybeli különböző vegyesen. Ezek a képességek egyik hálózati átviteli sebesség (vagy a sávszélesség), amely megabit / másodperc (Mbps) mérjük. Virtuális gépek megosztott hardveren található, mert a hálózati kapacitás kell viszonylag megoszthatók a virtuális gépek ugyanazt a hardvert használja. Nagyobb virtuális gépek nagyobb sávszélesség-nál kisebb méretű virtuális gépek vannak lefoglalva.

A hálózati sávszélességet, minden virtuális gép számára lefoglalt kimenő forgalom (kimenő) forgalmat a virtuális gépről a forgalmi díjas. Minden hálózati forgalmat a virtuális gép elhagyása után kell fizetnie a lefoglalt korlátot, függetlenül a cél felé. Ha például egy virtuális gépet egy 1000 MB/s-korlát, akkor ezt a korlátot attól függetlenül érvényes, a kimenő forgalmat egy másik virtuális géphez ugyanazon a virtuális hálózaton vagy egy Azure-on kívül szánt.

Bejövő forgalom nem díjköteles, vagy közvetlenül korlátozott. De vannak más tényezők, például a Processzor- és tárterületi korlátozásai, amelyek hatással lehetnek a virtuális gépek képesek-e feldolgozni a beérkező adatokat.

Gyorsított hálózatkezelés javítható a hálózati teljesítmény, beleértve a késés, átviteli sebesség és CPU-kihasználtság célja. Gyorsított hálózatkezelés javítja a virtuális gépek átviteli sebesség, de azt csak a virtuális gép kiosztott sávszélesség legfeljebb teheti meg.

Azure-beli virtuális gépek legalább egy hálózati adapter csatlakoztatva van. Előfordulhat, hogy több rendelkeznek. A virtuális gép számára lefoglalt sávszélesség egyezik meg az összes kimenő forgalmat a géphez csatlakoztatott összes hálózati adapter esetében. Más szóval a sávszélességet egy virtuális gép alapon, függetlenül attól, hány hálózati adapter csatlakozik a gép.

Várt kimenő adatátviteli és az egyes virtuális gép mérete által támogatott hálózati adapterek számát részletezi [méretek a Windows virtuális gépek az Azure-ban](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Maximális átviteli sebesség megtekintéséhez válasszon egy típust, például **általános célú**, majd keresse meg a méretet sorozat az eredményül kapott (például "a Dv2 sorozat") oldalon olvashat. Az egyes sorozatok van egy tábla által biztosított hálózati előírásoknak, az utolsó oszlopban, amelynek címe: "hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps)."

Az átviteli sebesség korlát vonatkozik a virtuális géphez. Átviteli sebesség nem érinti a tényezők:

- **Hálózati adapterek száma**: Egyezik meg az összes kimenő forgalmat a virtuális gépről vonatkozik a sávszélességre vonatkozó korlátját.

- **Gyorsított hálózatkezelés**: Bár ez a funkció akkor hasznosak, a közzétett korlát elérése, a korlát nem módosul.

- **Forgalom cél**: Az összes destinations beleszámítanak a kimenő korlátot.

- **Protokoll**: Minden kimenő forgalmat az összes protokollhoz keresztül a határérték felé számolnak.

További információkért lásd: [virtuális gép hálózati sávszélesség](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Internetes teljesítménnyel kapcsolatos szempontok

Ez a cikk teljes írtaknak tényezők az interneten és az Azure a vezérlőn kívüli hatással lehet a hálózati teljesítmény. Íme néhány tényezők:

- **Késés**: Problémák köztes hálózatokon, forgalmat, amely a "legrövidebb" distance útvonal nem használ, és optimálisnál társviszony-létesítési útvonalakon által hatással lehet az üzenetváltási idő két célhelyek között.

- **Csomagveszteség**: Csomagveszteség okozhatja a hálózati torlódás, fizikai elérési út problémák és az alulteljesítő hálózati eszközöket.

- **Maximális átviteli egység mérete/töredezettsége**: Az elérési úton elhelyezkedő töredezettsége késések adatok beérkezés vagy a csomagok ugyanis ronthatják az üzemen kívüli, ami kihathat a csomagok kézbesítését vezethet.

A traceroute egy olyan jó eszköz méri, hálózati teljesítményt nyújt (például a csomagvesztés és a késés) mentén minden hálózati elérési útját a forráseszközt és a egy cél-eszköz között.

### <a name="network-design-considerations"></a>Hálózati kialakításával kapcsolatos szempontok

Az ebben a cikkben korábban ismertetett szempontok, valamint egy virtuális hálózat a topológia hatással lehet a hálózati teljesítmény. Ha például egy Központ-küllő-Tervező, hogy a backhauls globálisan single-hub virtuális hálózati forgalom hálózati késés, ami hatással lesz a teljes hálózati teljesítmény vezet be.

A hálózati adatforgalom azon hálózati eszközök számát is hatással lehet a teljes késése. Például a Központ-küllő tervezés, az adatforgalom áthalad egy küllős hálózati virtuális készüléket és a központ virtuális berendezés előtt áthaladó csatlakozik az internethez, ha a hálózati virtuális berendezések megjelentetni késés.

### <a name="azure-regions-virtual-networks-and-latency"></a>Az Azure-régiók, a virtuális hálózatok és a késés

Azure-régió általános földrajzi területét belül található több adatközpontot épülnek fel. Ezek az adatközpontok nem feltétlenül fizikailag egymás mellé. Bizonyos esetekben azok van elválasztva a kevesebb mint 10 alapján. A virtuális hálózat egy logikai területre az Azure-adatközpont fizikai hálózat felett. Virtuális hálózat nem jelenti azt, semmilyen konkrét hálózati topológia az adatközponton belül.

Két virtuális gépet az azonos virtuális hálózatot és alhálózatot lévő Előfordulhat például, a különböző állványokon, sorok vagy akár adatközpontokban. Ezek külön feet fiber száloptikás kábelt, vagy adja meg kilométerben fiber száloptikás kábelt kell. Ezt a különbséget sikerült vezeti be a változó késés (néhány ezredmásodperc eltérést) különböző virtuális gépek között.

Virtuális gép földrajzi helyét, és két virtuális gép között lehetséges késés befolyásolhatja a rendelkezésre állási csoportok és a rendelkezésre állási zónák konfigurációját. Azonban egy régióban adatközpontok közötti távolság régióspecifikus és elsősorban befolyásolt régióban adatközpont topológia szerint.

### <a name="source-nat-port-exhaustion"></a>Forrás NAT portfogyás

Központi telepítés az Azure-ban és a nyilvános interneten és/vagy a nyilvános IP-címtér az Azure-on kívüli végpontok közötti kommunikációhoz. Amikor egy példányt egy kimenő kapcsolatot kezdeményez, Azure dinamikusan rendeli a magánhálózati IP-cím nyilvános IP-címre. Után az Azure létrehozza ezt a hozzárendelést, a kimenő származó folyamat visszatérő forgalom is elérheti a magánhálózati IP-címet, adja meg a folyamatot.

Minden kimenő kapcsolat az Azure Load Balancer kell ezt a hozzárendelést bizonyos ideig karbantartása. Az Azure több-bérlős jellegét Ez a leképezés minden kimenő flow minden virtuális gép karbantartása lehet erőforrás-igényesek. Úgy állítsa be és az Azure virtuális hálózat konfigurációja alapján korlátozva van. Vagy tegyük fel, amelyek pontosabban, az Azure virtuális Gépekhez is csak győződjön meg arról, kimenő kapcsolatok bizonyos számú egy adott időpontban. Ezek a korlátok elérésekor a virtuális gép nem fog tudni több kimenő kapcsolatok.

De ez a viselkedés módosítható. További információ az SNAT és SNAT port Erőforrásfogyás, lásd: [Ez a cikk](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Mérték hálózati teljesítmény az Azure-ban

A hálózati késést a teljesítmény kapacitástervezés ebben a cikkben számos kapcsolódó / üzenetváltási időt (RTT) két virtuális gép között. Ez a szakasz néhány javaslatot tesz a késési/RTT tesztelése és tesztelése a TCP teljesítményét és a virtuális gép hálózati teljesítmény. Hangolhassa és teljesítményének tesztelése a TCP/IP- és hálózati értékek taglaltak szerint ebben a szakaszban leírt módszerek segítségével. Csatlakoztassa a késés, MTU, MSS és ablak mérete értékének a korábban megadott számításokat, és hasonlítsa össze a tényleges értékeknek, amely a tesztelés során megfigyelte elméleti maximális értékeket.

### <a name="measure-round-trip-time-and-packet-loss"></a>Mérték üzenetváltási idő és a csomagveszteség

TCP teljesítményét az erősen RTT és adatveszteség csomagot használ. A Windows és Linux elérhető PING segédprogram a legkönnyebb RTT vagy csomagot biztosít. PING kimenete jelennek meg a minimális, maximális és átlagos késés a forrás és cél között. Csomagveszteség is megjelenik. PING alapértelmezés szerint az ICMP protokollt használja. Használhatja a PsPing TCP RTT teszteléséhez. További információkért lásd: [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Mérték tényleges átviteli sebesség TCP-kapcsolat

NTttcp egy olyan eszköz, Linux vagy Windows virtuális gép TCP teljesítményének vizsgálatához. Különböző TCP beállításainak módosítása, és tesztelje a előnyöket NTttcp használatával. További információkért tekintse meg ezeket az erőforrásokat:

- [A sávszélesség/átviteli sebesség tesztelése (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp segédprogram](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Mérték tényleges sávszélességet a virtuális gép

A különböző típusú virtuális Gépet, a gyorsított hálózati teljesítmény és így tovább iPerf nevű eszköz használatával. iPerf Linux és Windows rendszereken is érhető el. iPerf TCP vagy UDP tesztelésére használható teljes hálózati átviteli sebesség. TCP-átviteli tesztek iPerf (például a késés és RTT) ebben a cikkben tárgyalt tényezők befolyásolják. Ezért UDP előfordulhat, hogy felfüggesztési jobb eredményeket, ha csak át szeretné maximális átviteli sebesség tesztelése.

További információval a következő cikkek szolgálnak:

- [Az Expressroute hálózati teljesítmény hibaelhárítása](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [VPN teljesítményének érvényesítése virtuális hálózaton](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Nem elég hatékony TCP viselkedések észleléséhez

Az Azure-ügyfelek csomagrögzítés, előfordulhat, hogy tekintse meg a TCP-csomagokat a TCP jelzővel (ZSÁK, Ismétlődő ACK, ÚJRAKÜLDÉSI és gyors TIMEOUTS), amely a hálózati teljesítmény problémákat jelezhet. Ezek a csomagok kifejezetten a csomagvesztés eredő hálózati hatékonysági jelzik. De csomagvesztés nem feltétlenül okozta Azure teljesítményproblémákat okozhat. Teljesítménybeli problémákat az alkalmazásokkal kapcsolatos problémák, operációs rendszer problémák vagy egyéb problémák, amelyek esetleg nem közvetlenül kapcsolódik az Azure platform oka lehet.

Ezenkívül vegye figyelembe, hogy bizonyos újraküldési és ismétlődő ACK nem rendellenes a hálózaton. A TCP protokollt megbízható készült. A TCP-csomagokat a csomagrögzítés bizonyítékokat nem feltétlenül jelent rendszeres hálózati hiba, csak akkor, ha túl nagy.

Továbbra is ezek csomagtípusok olyan TCP átviteli sebesség nem éri el a maximális teljesítmény érdekében a többi szakasz ebben a cikkben említett okokból jelzések.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a teljesítményhangolásról TCP/IP az Azure virtuális gépek, érdemes olvassa el az egyéb szempontok [virtuális hálózatok tervezési](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-vnet-plan-design-arm) vagy [tudjon meg többet a csatlakozás és a virtuális hálózatok konfigurálása ](https://docs.microsoft.com/en-us/azure/virtual-network/).

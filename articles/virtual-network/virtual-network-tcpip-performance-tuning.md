---
title: TCP/IP-teljesítményhangolás az Azure virtuális gépekhez | Microsoft dokumentumok
description: Ismerje meg a különböző gyakori TCP/IP-teljesítményhangolási technikákat és az Azure virtuális gépekkel való kapcsolatukat.
services: virtual-network
documentationcenter: na
author: rimayber
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2019
ms.author: rimayber
ms.reviewer: dgoddard, stegag, steveesp, minale, btalb, prachank
ms.openlocfilehash: bb23484903ac3ce129c6e7a7a27e0765c227fb1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68297786"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP-teljesítményhangolás az Azure virtuális gépekhez

Ez a cikk ismerteti a gyakori TCP/IP teljesítményhangolási technikákat, és néhány dolgot figyelembe kell venni, amikor azOkat az Azure-ban futó virtuális gépekhez használja. Ez biztosítja az alapvető áttekintést a technikákat, és fedezze fel, hogyan lehet őket hangolni.

## <a name="common-tcpip-tuning-techniques"></a>Gyakori TCP/IP hangolási technikák

### <a name="mtu-fragmentation-and-large-send-offload"></a>MTU, töredezettség és nagy küld tehermentesítés

#### <a name="mtu"></a>Mtu

A maximális átviteli egység (MTU) a bájtban megadott legnagyobb méretű keret (csomag), amely hálózati kapcsolaton keresztül küldhető. Az MTU egy konfigurálható beállítás. Az Azure virtuális gépeken használt alapértelmezett MTU, és a legtöbb hálózati eszközön világszerte az alapértelmezett beállítás 1500 bájt.

#### <a name="fragmentation"></a>Töredezettség

Töredezettség akkor következik be, amikor olyan csomagot küldenek, amely meghaladja a hálózati adapter MTU-ját. A TCP/IP-verem a csomagot kisebb darabokra (töredékekre) bontja, amelyek megfelelnek a kapcsolat MTU-jának. Töredezettség történik az IP-réteg, és független az alapul szolgáló protokoll (például a TCP). Ha egy 2000 bájtos csomagot 1500 MTU-val rendelkező hálózati kapcsolaton keresztül küldenek, a csomag egy 1500 bájtos csomagra és egy 500 bájtos csomagra lesz bontva.

A forrás és a cél közötti útvonalon lévő hálózati eszközök vagy eldobhatják az MTU-t meghaladó csomagokat, vagy kisebb darabokra törhetik a csomagot.

#### <a name="the-dont-fragment-bit-in-an-ip-packet"></a>Az IP-csomag nem tördelhető bitje

A Don't Fragment (DF) bit az IP-protokoll fejlécében lévő jelző. A DF bit azt jelzi, hogy a küldő és a fogadó közötti útvonalon lévő hálózati eszközök nem töredezhetik szét a csomagot. Ez a bit lehet beállítani több okból is. (Lásd a "Path MTU Discovery" szakaszban ezt a cikket egy példát.) Ha egy hálózati eszköz a Ne tördelés bitkészlettel rendelkező csomagot fogadjon, és az meghaladja az eszköz mtu-s felületét, a szokásos viselkedés az, ha az eszköz eldobja a csomagot. Az eszköz egy ICMP fragmentáció szükséges üzenetet küld vissza a csomag eredeti forrásának.

#### <a name="performance-implications-of-fragmentation"></a>A széttöredezettség teljesítménybeli következményei

A töredezettség negatív hatással lehet a teljesítményre. A teljesítményre gyakorolt hatás egyik fő oka a csomagok töredezettségének és újraösszeállításának CPU/memória hatása. Ha egy hálózati eszköznek szét kell tördelnie egy csomagot, a töredezettség végrehajtásához CPU-/memória-erőforrásokat kell lefoglalnia.

Ugyanez történik, ha a csomag újra össze. A hálózati eszköznek az összes töredéket tárolnia kell, amíg meg nem érkezik, hogy újra összerakhassa őket az eredeti csomagba. Ez a folyamat a töredezettség és újraösszeállítás is okozhat késést.

A töredezettség másik lehetséges negatív hatása az, hogy a töredezett csomagok nem megfelelően érkezhetnek meg. Ha a csomagok nem megfelelően érkeznek, bizonyos típusú hálózati eszközök eldobhatják azokat. Amikor ez megtörténik, az egész csomagot újra kell küldeni.

A töredékeket általában a biztonsági eszközök, például a hálózati tűzfalak, vagy a hálózati eszköz fogadási pufferei kimerítik. Ha egy hálózati eszköz fogadási pufferei kimerültek, a hálózati eszköz megpróbál újra összerakni egy töredezett csomagot, de nem rendelkezik a csomag tárolásához és újbóli átvételéhez szükséges erőforrásokkal.

Töredezettség lehet tekinteni, mint egy negatív művelet, de támogatja a töredezettség szükséges, ha az interneten keresztül különböző hálózatok at csatlakozik.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Az MTU módosításának előnyei és következményei

Általánosságban elmondható, hogy hatékonyabb hálózatot hozhat létre az MTU növelésével. Minden átküldött csomag fejlécadatokkal rendelkezik, amelyek hozzáadódnak az eredeti csomaghoz. Ha a töredezettség több csomagot hoz létre, több fejléc van a terhelés, és ez csökkenti a hálózat hatékonyságát.

Az alábbiakban erre láthat példát. Az Ethernet fejléc mérete 14 bájt plusz egy 4 bájtos keretellenőrzési sorrend a keret konzisztenciájának biztosítása érdekében. Ha egy 2000 bájtos csomagot küld, a hálózat 18 bájt Ethernet-terhelést ad hozzá. Ha a csomag 1500 bájtos és 500 bájtos csomagra van törve, minden csomag 18 bájt Ethernet fejléccel, összesen 36 bájttal rendelkezik.

Ne feledje, hogy az MTU növelése nem feltétlenül hoz létre hatékonyabb hálózatot. Ha egy alkalmazás csak 500 bájtos csomagokat küld, ugyanaz a fejlécterhelés akkor is megmarad, hogy az MTU 1500 bájt vagy 9000 bájt. A hálózat csak akkor lesz hatékonyabb, ha nagyobb csomagméretet használ, amelyet az MTU érint.

#### <a name="azure-and-vm-mtu"></a>Az Azure és a VM MTU

Az Azure-beli virtuális gépek alapértelmezett MTU-ja 1500 bájt. Az Azure virtuális hálózat verem megkísérli a csomag tördelése 1400 bájt.

Vegye figyelembe, hogy a virtuális hálózati verem nem eredendően nem hatékony, mert töredékek csomagok 1400 bájt, még akkor is, ha a virtuális gépek egy MTU 1500. A hálózati csomagok nagy százaléka sokkal kisebb, mint 1400 vagy 1500 bájt.

#### <a name="azure-and-fragmentation"></a>Az Azure és a töredezettség

A Virtuális hálózat verem úgy van beállítva, hogy "nem sorrendben lévő töredékeket" hagyjon el, azaz töredezett csomagokat, amelyek nem az eredeti töredezett sorrendben érkeznek. Ezeket a csomagokat elsősorban a 2018 novemberében bejelentett hálózati biztonsági rés miatt, a FragmentSmack miatt dobják el.

A FragmentSmack a töredezett IPv4- és IPv6-csomagok újraösszeállításának hibája. A távoli támadó ezt a hibát használhatja a drága töredék-újraszerelési műveletek indításához, ami megnövekedett processzorhoz és szolgáltatásmegtagadáshoz vezethet a célrendszeren.

#### <a name="tune-the-mtu"></a>Az MTU finomhangolása

Konfigurálhatja az Azure VM MTU, mint bármely más operációs rendszerben. De figyelembe kell vennie az Azure-ban előforduló töredezettség, a fent leírt, egy MTU konfigurálásakor.

Nem javasoljuk az ügyfeleknek, hogy növeljék a VM MT-k. Ez a vitafórum célja, hogy ismertesse az Azure megvalósításának és töredezettség végrehajtásának részleteit.

> [!IMPORTANT]
>Az MTU növelése nem ismert, hogy javítja a teljesítményt, és negatív hatással lehet az alkalmazás teljesítményére.
>
>

#### <a name="large-send-offload"></a>Nagy küldő tehermentesítés

A nagy küldési teher (LSO) javíthatja a hálózati teljesítményt azáltal, hogy a csomagok szegmentálását az Ethernet-adapterre rakja. Ha az LSO engedélyezve van, a TCP/IP-verem nagy TCP-csomagot hoz létre, és továbbítás előtt elküldi az Ethernet-adapternek szegmentálásra. Az LSO előnye, hogy megszabadítja a CPU-t a csomagok szegmentálásától az MTU-nak megfelelő méretekre, és a feldolgozást az Ethernet interfészre terheli, ahol hardverben végzik. Ha többet szeretne megtudni az LSO előnyeiről, olvassa el [a Nagy küldési tehermentesítés támogatása](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso)című témakört.

Ha az LSO engedélyezve van, az Azure-ügyfelek nagy képkockaméreteket láthatnak csomagrögzítések végrehajtásakor. Ezek a nagy képkockaméretek egyes ügyfelek azt gondolhatják, hogy töredezettség történik, vagy hogy egy nagy MTU-t használnak, amikor nem. Az LSO segítségével az Ethernet-adapter nagyobb maximális szegmensméretet (MSS) hirdethet a TCP/IP-veremben, hogy nagyobb TCP-csomagot hozzon létre. Ez a teljes nem szegmentált keret ezután továbbítja az Ethernet adapter, és látható lesz a csomag rögzítése a virtuális gép. De a csomag lesz bontani sok kisebb keretek az Ethernet adapter szerint az Ethernet adapter MTU.

### <a name="tcp-mss-window-scaling-and-pmtud"></a>TCP MSS ablakméretezés és PMTUD

#### <a name="tcp-maximum-segment-size"></a>TCP maximális szegmensmérete

A TCP maximális szegmensmérete (MSS) olyan beállítás, amely korlátozza a TCP-szegmensek méretét, így elkerülhető a TCP-csomagok töredezettsége. Az operációs rendszerek általában ezt a képletet használják az MSS beállításához:

`MSS = MTU - (IP header size + TCP header size)`

Az IP-fejléc és a TCP-fejléc egyenként 20 bájt, azaz összesen 40 bájt. Tehát egy 1500-as MTU-val rendelkező interfésznek 1460 MSS-e lesz. De az MSS konfigurálható.

Ezzel a beállítással a TCP háromutas kézfogásban állapodik meg, amikor egy TCP-munkamenet van beállítva egy forrás és egy cél között. Mindkét oldal küld egy MSS-értéket, és a kettő közül az alacsonyabb at használja a TCP-kapcsolat.

Ne feledje, hogy a forrás és a cél M.TUsa nem az egyetlen tényező, amely meghatározza az MSS értékét. A köztes hálózati eszközök, például a VPN-átjárók, beleértve az Azure VPN-átjárót is, a forrástól és a céltól függetlenül módosíthatják az MTU-t az optimális hálózati teljesítmény biztosítása érdekében.

#### <a name="path-mtu-discovery"></a>Elérési út MTU-felderítése

Az MSS egyeztetése megtörténik, de előfordulhat, hogy nem jelzi a ténylegesen használható MSS-t. Ennek az az oka, hogy a forrás és a cél közötti útvonalon lévő többi hálózati eszköz értéke alacsonyabb lehet, mint a forrás é és a cél. Ebben az esetben az az eszköz, amelynek MTU-ja kisebb, mint a csomag, eldobja a csomagot. Az eszköz visszaküld egy ICMP fragmentáció szükséges (Type 3, 4 kód) üzenetet, amely tartalmazza az MTU-t. Ez az ICMP-üzenet lehetővé teszi, hogy a forrásállomás megfelelően csökkentse az Elérési út MTU-ját. A folyamat neve Path MTU Discovery (PMTUD).

A PMTUD folyamat nem hatékony, és befolyásolja a hálózati teljesítményt. Ha olyan csomagokat küldenek, amelyek meghaladják a hálózati elérési út MTU-ját, a csomagokat alacsonyabb MSS-sel kell újratovábbítani. Ha a feladó nem kapja meg az ICMP töredezettség szükséges üzenetet, talán azért, mert a hálózati tűzfal az elérési úton (közkeletű nevén *a PMTUD blackhole),* a feladó nem tudja, hogy csökkentenie kell az MSS-t, és folyamatosan újraküldi a csomagot. Ezért nem javasoljuk az Azure VM MTU növelését.

#### <a name="vpn-and-mtu"></a>VPN és MTU

Ha olyan virtuális gépeket használ, amelyek beágyazást hajtanak végre (például IPsec VPN-eket), további szempontokat is figyelembe kell venni a csomagmérettel és az MTU-val kapcsolatban. A VPN-ek további fejléceket adnak a csomagokhoz, ami növeli a csomagméretet, és kisebb MSS-t igényel.

Az Azure esetében azt javasoljuk, hogy a TCP MSS-be- és 1350 bájtot állítsa be, az MTU bújtatási felületét pedig 1400-ra. További információt a [VPN-eszközök és az IPSec/IKE paraméterek lapon talál.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Késés, oda-vissza idő és TCP-ablakméretezés

#### <a name="latency-and-round-trip-time"></a>Késés és oda-vissza idő

A hálózati késleltetést a fénysebesség szabályozza egy optikai hálózaton keresztül. A TCP hálózati átviteli csatornáját hatékonyan szabályozza a két hálózati eszköz közötti oda-vissza idő (RTT).

| | | | |
|-|-|-|-|
|**Útválasztás**|**Távolság**|**Egyirányú idő**|**RTT**|
|New Yorkból San Franciscóba|4 148 km|21 ezres|42 ezres|
|New York-tól Londonig|5 585 km|28 ezres|56 ms|
|New York-sydney-i|15 993 km|80 ms|160 ms|

Ez a táblázat két hely közötti egyenes vonalat mutatja. A hálózatokban a távolság általában hosszabb, mint az egyenes vonalú távolság. Íme egy egyszerű képlet a minimális RTT kiszámításához a fénysebesség szerint:

`minimum RTT = 2 * (Distance in kilometers / Speed of propagation)`

A propagálás imátegyik sebességére 200 használható. Ez az a távolság méterben, ami 1 milliszekundum alatt halad.

Vegyük például New Yorkot San Franciscóba. Az egyenes vonaltávolság 4148 km. Dugulás, hogy az érték az egyenlet, kapunk a következő:

`Minimum RTT = 2 * (4,148 / 200)`

Az egyenlet kimenete ezredmásodpercben van megadva.

Ha a legjobb hálózati teljesítményt szeretné kihozni, a logikai lehetőség az, hogy a köztük lévő legrövidebb távolsággal rendelkező úti célokat válassza ki. A virtuális hálózatot is meg kell terveznie a forgalom útvonalának optimalizálásához és a késés csökkentéséhez. További információt a cikk "A hálózattervezéssel kapcsolatos szempontok" című részében talál.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Késés és oda-vissza időhatások a TCP-re

Az oda-vissza út közvetlen hatással van a maximális TCP-átviteli teljesítményre. A TCP protokollban az *ablak mérete* az a maximális forgalommennyiség, amely tcp-kapcsolaton keresztül küldhető, mielőtt a feladónak nyugtázást kellene kapnia a fogadótól. Ha a TCP MSS beállítása 1460, a TCP-ablak mérete pedig 65 535, a feladó 45 csomagot küldhet, mielőtt nyugtát kapna a címzetttől. Ha a feladó nem kap nyugtázást, újratovábbítja az adatokat. A képlet a következő:

`TCP window size / TCP MSS = packets sent`

Ebben a példában a 65 535 / 1460 45-re van felkerekítve.

Ez a "nyugtázásra váró" állapot, egy olyan mechanizmus, amely biztosítja az adatok megbízható továbbítását, az okozza az RTT-t a TCP átviteli hatásának befolyásolására. Minél tovább vár a feladó nyugtázásra, annál tovább kell várnia, mielőtt további adatokat küldene.

Egyetlen TCP-kapcsolat maximális átviteli teljesítményének kiszámítására szolgáló képlet:

`Window size / (RTT latency in milliseconds / 1,000) = maximum bytes/second`

Ez a táblázat egyetlen TCP-kapcsolat maximális megabájt/másodpercenkénti átviteli fót ad meg. (Az olvashatóság érdekében megabájtot használ a mértékegység.)

| | | | |
|-|-|-|-|
|**TCP-ablak mérete (bájt)**|**RTT késés (ms)**|**Maximális megabájt/másodperc átviteli teljesítmény**|**Maximális megabit/másodperc átviteli teljesítmény**|
|65,535|1|65.54|524.29|
|65,535|30|2.18|17.48|
|65,535|60|1.09|8.74|
|65,535|90|.73|5.83|
|65,535|120|.55|4.37|

Ha a csomagok elvesznek, a TCP-kapcsolat maximális átviteli forgalma csökken, miközben a feladó újraküldi a már elküldött adatokat.

#### <a name="tcp-window-scaling"></a>TCP-ablak méretezése

A TCP-ablak méretezése olyan technika, amely dinamikusan növeli a TCP-ablak méretét, hogy több adatot küldhet a nyugtázás előtt. Az előző példában 45 csomagot küldene a rendszer, mielőtt nyugtázásra lenne szükség. Ha növeli a nyugtázás előtt elküldhető csomagok számát, csökkenti, hogy a feladó hányszor vár nyugtázásra, ami növeli a TCP maximális átviteli áteresztőszámát.

Ez a táblázat a következőket mutatja be:

| | | | |
|-|-|-|-|
|**TCP-ablak mérete (bájt)**|**RTT késés (ms)**|**Maximális megabájt/másodperc átviteli teljesítmény**|**Maximális megabit/másodperc átviteli teljesítmény**|
|65,535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

A TCP ablak méretének TCP fejlécértéke azonban csak 2 bájt hosszú, ami azt jelenti, hogy a fogadási ablak maximális értéke 65 535. A maximális ablakméret növeléséhez tcp-ablakméretezési tényezőkerült bevezetésre.

A léptéktényező olyan beállítás is, amelyet az operációs rendszerben konfigurálhat. A TCP-ablak méretének léptéktényezőkkel történő kiszámításának képlete:

`TCP window size = TCP window size in bytes \* (2^scale factor)`

Itt van a 3-as ablaklépték-tényező számítása és a 65 535-ös ablakméret:

`65,535 \* (2^3) = 262,140 bytes`

A 14-es léptéktényező 14-es TCP-ablakméretet eredményez (a megengedett maximális eltolás). A TCP-ablak mérete 1 073 725 440 bájt (8,5 gigabit).

#### <a name="support-for-tcp-window-scaling"></a>TCP-ablakméretezés támogatása

A Windows különböző méretezési tényezőket állíthat be a különböző kapcsolattípusokhoz. (A kapcsolatok osztályai közé tartozik az adatközpont, az internet és így tovább.) A `Get-NetTCPConnection` PowerShell paranccsal megtekintheti az ablakméretezési kapcsolat típusát:

```powershell
Get-NetTCPConnection
```

A `Get-NetTCPSetting` PowerShell paranccsal megtekintheti az egyes osztályok értékeit:

```powershell
Get-NetTCPSetting
```

A PowerShell paranccsal beállíthatja a kezdeti TCP-ablakméretet és a TCP méretezési tényezőt a `Set-NetTCPSetting` Windows rendszerben. További információ: [Set-NetTCPSetting](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps).

```powershell
Set-NetTCPSetting
```

Ezek a hatékony `AutoTuningLevel`TCP-beállítások:

| | | | |
|-|-|-|-|
|**Autotuninglevel**|**Méretezési tényező**|**Méretezési szorzó**|**Az<br/>ablak maximális méretének kiszámításához tő: képlet**|
|Letiltva|None|None|Ablak mérete|
|Korlátozott hozzáférésű|4|2^4|Ablak mérete * (2^4)|
|Erősen korlátozott|2|2^2|Ablak mérete * (2^2)|
|Normál|8|2^8|Ablak mérete * (2^8)|
|Kísérleti|14|2^14|Ablak mérete * (2^14)|

Ezek a beállítások a legnagyobb valószínűséggel befolyásolják a TCP teljesítményét, de ne feledje, hogy az interneten, az Azure-on kívül számos más tényező is befolyásolhatja a TCP teljesítményét.

#### <a name="increase-mtu-size"></a>Az MTU méretének növelése

Mivel a nagyobb MTU nagyobb MSS-t jelent, talán felmerül a kérdés, hogy az MTU növelése növelheti-e a TCP teljesítményét. Valószínűleg nem. Vannak előnyei és hátrányai, hogy a csomag mérete túl csak TCP forgalmat. Ahogy azt korábban már tárgyaltuk, a TCP átviteli teljesítményét befolyásoló legfontosabb tényezők a TCP-ablak mérete, a csomagvesztés és az RTT.

> [!IMPORTANT]
> Nem javasoljuk, hogy az Azure-ügyfelek módosítsák az alapértelmezett MTU-értéket a virtuális gépeken.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Gyorsított hálózati és fogadási oldalméretezés

#### <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Virtuálisgép hálózati funkciók történelmileg processzorigényes mind a vendég virtuális gép és a hipervizor/gazdagép. Minden csomagot, amely áthalad az állomáson, a gazdaprocesszor szoftverben dolgoz fel, beleértve az összes virtuális hálózati beágyazást és decapsulációt. Tehát minél nagyobb a forgalom, hogy megy keresztül a fogadó, annál nagyobb a CPU terhelés. És ha a gazdagép CPU más műveletekkel van elfoglalva, ez hatással lesz a hálózati átviteli és késési. Az Azure gyorsított hálózatkezeléssel orvosolja ezt a problémát.

A gyorsított hálózati kapcsolat konzisztens ultraalacsony hálózati késést biztosít az Azure és az SR-IOV technológiák házon belüli programozható hardverein keresztül. A gyorsított hálózatkezelés az Azure szoftveresen definiált hálózati verem nagy részét a processzorokon kívülre helyezi át az FPGA-alapú SmartNIC-okba. Ez a módosítás lehetővé teszi a végfelhasználói alkalmazások számára a számítási ciklusok visszaszerzését, ami kisebb terhelést jelent a virtuális gépre, csökkentve a jittert és a késés inkonzisztenciáját. Más szóval, a teljesítmény lehet determinisztikusabb.

A gyorsított hálózatkezelés javítja a teljesítményt azáltal, hogy lehetővé teszi a vendég virtuális gép számára, hogy megkerülje a gazdagép, és hozzon létre egy datapath közvetlenül a gazdagép SmartNIC. Íme néhány előnye a gyorsított hálózatépítés:

- **Alacsonyabb késés / magasabb csomagok másodpercenként (pps)**: A virtuális kapcsoló eltávolítása a datapath kiküszöböli a csomagok időt töltenek a gazdagép a házirend-feldolgozás, és növeli a csomagok száma, amely feldolgozható a virtuális gép.

- **Csökkentett vibrálás:** A virtuális kapcsoló feldolgozása az alkalmazandó házirend mennyiségétől és a feldolgozást végző processzor terhelésétől függ. A házirend-kényszerítés hardverre való kiszervezése eltávolítja ezt a változékonyságot azáltal, hogy a csomagokat közvetlenül a virtuális gépnek kézbesíti, kiküszöbölve az állomás-virtuális gép kommunikációt és az összes szoftvermegszakítást és környezetkapcsolót.

- **Csökkent CPU-kihasználtság:** A virtuális kapcsoló megkerülése a gazdagépben kevesebb processzorkihasználtságot eredményez a hálózati forgalom feldolgozásához.

A gyorsított hálózatkezelés használatához explicit módon engedélyeznie kell azt minden egyes megfelelő virtuális számítógépen. Az utasításokért [lásd: Linuxos virtuális gép létrehozása gyorsított hálózattal.](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

#### <a name="receive-side-scaling"></a>Oldalméretezés fogadása

A fogadási oldali skálázás (RSS) egy olyan hálózati illesztőprogram-technológia, amely hatékonyabban osztja el a hálózati forgalom fogadását azáltal, hogy több processzoros rendszerben több processzoron osztja el a fogadási feldolgozást. Egyszerűen fogalmazva, RSS lehetővé teszi a rendszer feldolgozni több fogadott forgalmat, mert használja az összes rendelkezésre álló CPU helyett csak egy. Az RSS technikai bb megvitatásáról az [Oldalméretezés – Bevezetés](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling)című témakörben van.

To get the best performance when accelerated networking is enabled on a VM, you need to enable RSS. Az RSS olyan virtuális gépek számára is biztosít előnyöket, amelyek nem használnak gyorsított hálózatkezelést. Ha áttekintést szeretne kapni arról, hogy az RSS engedélyezve van-e, és hogyan engedélyezheti azt, olvassa el [a Hálózati átviteli hálózat optimalizálása az Azure virtuális gépekhez című témakört.](https://aka.ms/FastVM)

### <a name="tcp-time_wait-and-time_wait-assassination"></a>TCP TIME_WAIT és TIME_WAIT merénylet

A TCP TIME_WAIT egy másik gyakori beállítás, amely hatással van a hálózati és az alkalmazás teljesítményére. A tcp normál működése során a sok szoftvercsatornát megnyitó és záró foglalt virtuális gépeken , akár ügyfélként, akár kiszolgálóként (Forrás IP:Source Port + Destination IP:Destination Port), az adott szoftvercsatorna hosszú ideig TIME_WAIT állapotba kerülhet. A TIME_WAIT állapot célja, hogy lehetővé tegye a további adatok szoftvercsatornán való kézbesítését a bezárása előtt. Így a TCP/IP-kötegek általában megakadályozzák a szoftvercsatorna újrafelhasználását azáltal, hogy csendben eldobják az ügyfél TCP SYN csomagját.

A szoftvercsatorna TIME_WAIT konfigurálható. 30 és 240 másodperc között lehet. A szoftvercsatornák véges erőforrást jelentenek, és az adott időpontban használható szoftvercsatornák száma konfigurálható. (A rendelkezésre álló foglalatok száma általában körülbelül 30 000.) Ha a rendelkezésre álló szoftvercsatornákat felhasználják, vagy ha az ügyfelek és a kiszolgálók nem egyeznek TIME_WAIT beállításaival, és a virtuális gép TIME_WAIT állapotban próbál meg újra felhasználni egy szoftvercsatornát, az új kapcsolatok sikertelenek lesznek, mivel a TCP SYN-csomagok csendesen eldobódnak.

A kimenő szoftvercsatornák porttartományának értéke általában az operációs rendszer TCP/IP-vermében konfigurálható. Ugyanez igaz a TCP TIME_WAIT beállításokra és a szoftvercsatorna újrafelhasználására. Ezeknek a számoknak a módosítása potenciálisan javíthatja a méretezhetőséget. A helyzettől függően azonban ezek a változások interoperabilitási problémákat okozhatnak. Legyen óvatos, ha módosítja ezeket az értékeket.

A TIME_WAIT merénylet segítségével orvosolhatja ezt a méretezési korlátozást. TIME_WAIT merénylet lehetővé teszi, hogy a szoftvercsatorna bizonyos helyzetekben újra felhasználható legyen, például amikor az új kapcsolat IP-csomagjában lévő sorszám meghaladja az előző kapcsolat utolsó csomagjának sorszámát. Ebben az esetben az operációs rendszer lehetővé teszi az új kapcsolat létrejöttét (elfogadja az új SYN/ACK-t), és kényszeríti az előző, TIME_WAIT állapotban lévő kapcsolat bezárását. Ezt a funkciót az Azure-ban lévő Windows virtuális gépek is támogatják. Ha többet szeretne tudni a többi virtuális gép támogatásáról, érdeklődjön az operációs rendszer forgalmazójával.

A TCP TIME_WAIT beállítások és a forrásporttartomány konfigurálásáról a [Hálózati teljesítmény javítása érdekében módosítható beállítások című](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance)témakörben olvashat.

## <a name="virtual-network-factors-that-can-affect-performance"></a>A teljesítményt befolyásoló virtuális hálózati tényezők

### <a name="vm-maximum-outbound-throughput"></a>Virtuális gép maximális kimenő átviteli

Az Azure különböző virtuálisgép-méreteket és -típusokat biztosít, amelyek mindegyike különböző teljesítményképességekkel rendelkezik. Az egyik ilyen képesség a hálózati átviteli sebesség (vagy sávszélesség), amelyet megabit per másodpercben (Mbps) mérnek. Mivel a virtuális gépek megosztott hardveren vannak tárolva, a hálózati kapacitást igazságosan meg kell osztani az azonos hardvert használó virtuális gépek között. A nagyobb virtuális gépek nagyobb sávszélességet kapnak, mint a kisebb virtuális gépek.

Az egyes virtuális gépek számára lefoglalt hálózati sávszélesség et a virtuális gépről kimenő (kimenő) forgalom alapján méri a kapcsolat. A virtuális gépet elhagyó összes hálózati forgalom a lefoglalt korlátba számít, a céltól függetlenül. Ha például egy virtuális gép 1000 Mb/s-os korláttal rendelkezik, ez a korlát akkor érvényes, ha a kimenő forgalom ugyanazon virtuális hálózategy másik virtuális gépre vagy az Azure-on kívüli reked.

A be- és visszalépés nem mért vagy közvetlenül korlátozott. De vannak más tényezők, például a CPU és a tárolási korlátok, amelyek befolyásolhatják a virtuális gép azon képességét, hogy feldolgozza a bejövő adatokat.

A gyorsított hálózatkezelés célja a hálózati teljesítmény javítása, beleértve a késést, az átviteli teljesítményt és a processzorkihasználtságot. A gyorsított hálózatkezelés javíthatja a virtuális gépek átviteli állapotát, de ezt csak a virtuális gép lefoglalt sávszélességének növekedésével teheti meg.

Az Azure virtuális gépekhez legalább egy hálózati adapter van csatolva. Lehet, hogy több is van nekik. A virtuális gépszámára lefoglalt sávszélesség a géphez csatlakoztatott összes hálózati csatoló összes kimenő forgalmának összege. Más szóval a sávszélesség virtuális gépenként történik, függetlenül attól, hogy hány hálózati adapter van csatlakoztatva a géphez.

A várható kimenő átviteli és az egyes virtuális gépek mérete által támogatott hálózati csatolók száma az [Azure-beli Windows-alapú virtuális gépek méretei részletesen elérhető.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) A maximális átviteli teljesítmény megtekintéséhez válasszon ki egy típust, **például**az Általános célú t, majd keresse meg a méretsorozatra vonatkozó szakaszt az eredményül kapott oldalon (például "Dv2-sorozat"). Minden sorozathoz van egy táblázat, amely hálózati specifikációkat tartalmaz az utolsó oszlopban, amelynek címe "Max NIC / Expected network bandwidth (Mbps)."

Az átviteli korlát a virtuális gépre vonatkozik. Az átviteli átatettet nem befolyásolják ezek a tényezők:

- **Hálózati csatolók száma**: A sávszélesség-korlát a virtuális gépről érkező összes kimenő forgalom összegére vonatkozik.

- **Gyorsított hálózatkezelés**: Bár ez a funkció hasznos lehet a közzétett korlát elérésében, nem módosítja a korlátot.

- **Forgalmi cél:** Minden cél beleszámít a kimenő korlátba.

- **Protokoll**: Az összes protokollon keresztül immár összes kimenő forgalom beleszámít a korlátba.

További információ: [Virtual machine network bandwidth](https://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Az internetes teljesítménnyel kapcsolatos szempontok

A jelen cikkben tárgyalt, az Azure-on kívül és az Azure-on kívül is tényezők befolyásolhatják a hálózati teljesítményt. Íme néhány ilyen tényező:

- **Késés:** A két úti cél közötti oda-vissza utat a köztes hálózatokon felmerülő problémák, a "legrövidebb" távolsági útvonalat nem haladó forgalom és az optimálistól elmaradó társviszony-létesítési útvonalak befolyásolhatják.

- **Csomagvesztés**: A csomagvesztést a hálózati torlódások, a fizikai elérési út problémái és a hálózati eszközök alulteljesítő teljesítménye okozhatja.

- **MTU-méret/töredezettség:** Az útvonal mentén történő töredezettség késedelmet okozhat az adatok érkezésében vagy a nem sorrendben érkező csomagokban, ami befolyásolhatja a csomagok kézbesítését.

Traceroute egy jó eszköz mérésére hálózati teljesítmény jellemzői (mint a csomagvesztés és a késés) mentén minden hálózati útvonal között a forráseszköz és a céleszköz.

### <a name="network-design-considerations"></a>A hálózat tervezésével kapcsolatos szempontok

A cikkben korábban tárgyalt szempontok mellett a virtuális hálózat topológiája is befolyásolhatja a hálózat teljesítményét. Például egy hub-and-spoke kialakítás, amely backhauls forgalom globálisan egy egyhubos virtuális hálózat vezet be a hálózati késés, amely hatással lesz a teljes hálózati teljesítmény.

A hálózati forgalom által átszelő hálózati eszközök száma szintén befolyásolhatja a teljes késést. Például egy hub-and-küllős kialakítás, ha a forgalom áthalad egy küllős hálózati virtuális készülék és egy központi virtuális berendezés, mielőtt az internetre, a hálózati virtuális készülékek vezethet késés.

### <a name="azure-regions-virtual-networks-and-latency"></a>Azure-régiók, virtuális hálózatok és késés

Az Azure-régiók több adatközpontból állnak, amelyek egy általános földrajzi területen belül léteznek. Előfordulhat, hogy ezek az adatközpontok fizikailag nem egymás mellett vannak. Egyes esetekben akár 10 kilométeres körzetben is elvannak választva egymástól. A virtuális hálózat egy logikai átfedés az Azure fizikai adatközpont-hálózat tetején. A virtuális hálózat nem jelent semmilyen konkrét hálózati topológiát az adatközponton belül.

Például két virtuális gép, amelyek ugyanabban a virtuális hálózatban és alhálózatban lehetnek különböző állványok, sorok, vagy akár adatközpontok. Ezek lehet elválasztani láb optikai kábel vagy kilométeres optikai kábel. Ez a változat hozhat nak be változó késés (néhány ezredmásodperc különbség) a különböző virtuális gépek között.

A virtuális gépek földrajzi elhelyezését és a két virtuális gép közötti lehetséges késést a rendelkezésre állási csoportok és a rendelkezésre állási zónák konfigurációja befolyásolhatja. De az adatközpontok közötti távolság egy régióban régió-specifikus, és elsősorban a régió adatközpont-topológia befolyásolja.

### <a name="source-nat-port-exhaustion"></a>Forrás NAT port kimerülése

Az Azure-ban üzembe helyezés az Azure-on kívüli végpontokkal kommunikálhat a nyilvános interneten és/vagy a nyilvános IP-térben. Amikor egy példány kimenő kapcsolatot kezdeményez, az Azure dinamikusan leképezi a privát IP-címet egy nyilvános IP-címre. Miután az Azure létrehozza ezt a leképezést, a kimenő származó folyamat visszáruforgalmat is elérheti a magán-IP-címet, ahonnan a folyamat származik.

Minden kimenő kapcsolat, az Azure Load Balancer kell fenntartani ezt a leképezést egy ideig. Az Azure több-bérlős jellegével ez a leképezés minden kimenő folyamat minden virtuális gép erőforrás-igényes lehet. Így vannak korlátok, amelyek az Azure virtuális hálózat konfigurációja alapján vannak beállítva. Vagy pontosabban egy Azure-beli virtuális gép csak egy adott időpontban hozhat létre kimenő kapcsolatokat. Ha ezek a korlátok elérésekor a virtuális gép nem lesz képes több kimenő kapcsolatot létesíteni.

De ez a viselkedés konfigurálható. Az SNAT és az SNAT-portok kimerüléséről a cikkben olvashat [bővebben.](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections)

## <a name="measure-network-performance-on-azure"></a>A hálózati teljesítmény mérése az Azure-ban

Ebben a cikkben a teljesítménymaximumok száma a hálózati késés / oda-vissza idő (RTT) két virtuális gép közötti kapcsolatban. Ez a szakasz néhány javaslatot tartalmaz a késés/RTT tesztelésére, valamint a TCP-teljesítmény és a virtuális gép hálózati teljesítményének tesztelésére. Az ebben a szakaszban ismertetett technikák segítségével hangolhatja be és tesztelheti a korábban tárgyalt TCP/IP- és hálózati értékeket. A késés, az MTU, az MSS és az ablakméret értékeit csatlakoztathatja a korábban megadott számításokhoz, és összehasonlíthatja az elméleti maximumokat a tesztelés során megfigyelt tényleges értékekkel.

### <a name="measure-round-trip-time-and-packet-loss"></a>Oda-vissza út idejének és csomagvesztésének mérése

A TCP-teljesítmény nagymértékben függ az RTT-től és a csomagvesztéstől. A Windows és Linux rendszerben elérhető PING segédprogram biztosítja az RTT és a csomagvesztés mérésének legegyszerűbb módját. A PING kimenete a forrás és a cél közötti minimális/maximális/átlagos késleltetést mutatja. Azt is mutatja, csomagvesztés. A PING alapértelmezés szerint az ICMP protokollt használja. A PsPing segítségével tesztelheti a TCP RTT-t. További információ: [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>TCP-kapcsolat tényleges átviteli idejének mérése

Az NTTtcp egy Linux vagy Windows virtuális gép TCP-teljesítményének tesztelésére szolgáló eszköz. Módosíthatja a különböző TCP-beállításokat, majd tesztelheti az előnyöket az NTTtcp használatával. További információt az alábbi forrásokban talál:

- [Sávszélesség-/átviteli vizsgálat (NTttcp)](https://aka.ms/TestNetworkThroughput)

- [NTttcp segédprogram](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>A virtuális gép tényleges sávszélességének mérése

Az iPerf nevű eszköz használatával tesztelheti a különböző virtuálisgép-típusok teljesítményét, felgyorsított a hálózatkezelést és így tovább. IPerf linuxon és Windowson is elérhető. Az iPerf tcp vagy UDP használatával tesztelheti a teljes hálózati átviteli csatornát. Az iPerf TCP átviteli tesztjeit az ebben a cikkben tárgyalt tényezők (például a késés és az RTT) befolyásolják. Így udp lehet, hogy jobb eredményeket, ha csak azt szeretné, hogy tesztelje a maximális átviteli teljesítmény.

További információval a következő cikkek szolgálnak:

- [Az Expressroute hálózati teljesítményének hibaelhárítása](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [VPN teljesítményének érvényesítése virtuális hálózaton](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Nem hatékony TCP-viselkedések észlelése

A csomagrögzítésekben az Azure-ügyfelek tcp-jelzőkkel (SACK, DUP ACK, RETRANSMIT és FAST RETRANSMIT) rendelkező TCP-csomagokat láthatnak, amelyek hálózati teljesítményproblémákra utalhatnak. Ezek a csomagok kifejezetten jelzik a hálózat nem hatékony, hogy a csomagvesztés. A csomagvesztést azonban nem feltétlenül az Azure teljesítményproblémái okozzák. A teljesítményproblémák az alkalmazásproblémák, az operációs rendszer problémái vagy más olyan problémák eredménye lehet, amelyek nem kapcsolódnak közvetlenül az Azure platformhoz.

Ne feledje továbbá, hogy egyes továbbközvetítések és duplikált ACK-k normálisak a hálózaton. A TCP protokollok megbízhatóak. A csomagrögzítésben lévő TCP-csomagok bizonyítékai nem feltétlenül utalnak rendszerszintű hálózati problémára, kivéve, ha túlzottak.

Ezek a csomagtípusok mégis azt jelzik, hogy a TCP átviteli teljesítménye nem éri el a maximális teljesítményt, a cikk más szakaszaiban tárgyalt okok miatt.

## <a name="next-steps"></a>További lépések

Most, hogy értesült az Azure virtuális gépek TCP/IP-teljesítményének hangolásáról, érdemes elolvasnia a [virtuális hálózatok tervezésével](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm) kapcsolatos egyéb szempontokat, vagy [többet a virtuális hálózatok csatlakoztatásáról és konfigurálásáról.](https://docs.microsoft.com/azure/virtual-network/)

---
title: Linux teljesítményeszközök
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan háríthatja el és oldhatja meg a gyakori problémákat az Azure Kubernetes Szolgáltatás (AKS) használata kor
services: container-service
author: alexeldeib
ms.service: container-service
ms.topic: troubleshooting
ms.date: 02/10/2020
ms.author: aleldeib
ms.openlocfilehash: eb6b126b4d1794adf0380432040190b91a17a675
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925604"
---
# <a name="linux-performance-troubleshooting"></a>Linux teljesítményhibaelhárítás

A Linux gépekerőforrás-kimerülése gyakori probléma, és a tünetek széles skáláján keresztül nyilvánulhat meg. Ez a dokumentum magas szintű áttekintést nyújt az ilyen problémák diagnosztizálására rendelkezésre álló eszközökről.

Ezen eszközök közül sok elfogadja azt az intervallumot, amelyen a gördülő kimenet előállításához. Ez a kimeneti formátum általában sokkal könnyebbé teszi a pecsételési mintákat. Ha elfogadják, a példa `[interval]`meghívás a következőt is tartalmazza.

Sok ilyen eszközök kiterjedt története és széles körű konfigurációs lehetőségeket. Ez a lap a meghívások nak csak egy egyszerű részhalmazát tartalmazza a gyakori problémák kiemeléséhez. A kanonikus információforrás mindig az egyes eszközök referenciadokumentációja. Ez a dokumentáció sokkal alaposabb lesz, mint az itt.

## <a name="guidance"></a>Útmutatás

Legyen szisztematikus a teljesítményproblémák kivizsgálásának megközelítésében. Két gyakori megközelítés a USE (használat, telítettség, hibák) és a PIROS (sebesség, hibák, időtartam). A RED általában a szolgáltatások keretében használatos a kérelemalapú figyeléshez. A USE általában erőforrások figyelésére szolgál: a gép minden egyes erőforrásához, a kihasználtság figyeléséhez, a telítettséghez és a hibákhoz. A négy fő típusa az erőforrások minden gép cpu, memória, lemez, és a hálózat. Ezen erőforrások magas kihasználtsága, telítettsége vagy hibaaránya a rendszer rel kapcsolatos lehetséges problémára utal. Ha probléma áll fenn, vizsgálja meg a kiváltó oka: miért lemez IO késés magas? A lemezek vagy a virtuális gép termékváltozata szabályozott? Milyen folyamatok írnak az eszközökre, és milyen fájlokra?

Néhány példa a gyakori problémákra és mutatókra a diagnosztizálásukhoz:
- IOPS-szabályozás: használja a jostat ot az eszközönkénti IOPS mérésére. Győződjön meg arról, hogy egyetlen különálló lemez sem haladja meg a korlátot, és az összes lemez összege kisebb, mint a virtuális gépre vonatkozó korlát.
- Sávszélesség-szabályozás: használja a jostatot az IOPS-hoz, de az olvasási/írási átviteli teljesítmény mérése. Győződjön meg arról, hogy mind az eszközönkénti, mind az összesített átviteli igény a sávszélesség-korlátok alatt van.
- SNAT-kimerültség: ez a SAR-ban magas aktív (kimenő) kapcsolatokként nyilvánulhat meg. 
- Csomagvesztés: ez a TCP-újraküldési számon keresztüli proxyval mérhető az elküldött/fogadott számhoz képest. `sar` Mindkettő, `netstat` és meg tudja jelenmutatni ezt az információt.

## <a name="general"></a>Általános kérdések

Ezek az eszközök általános célokat szolgálnak, és alapvető rendszerinformációkat fednek le. Jó kiindulópontot jelentenek a további vizsgálatokhoz.

### <a name="uptime"></a>Uptime

```
$ uptime
 19:32:33 up 17 days, 12:36,  0 users,  load average: 0.21, 0.77, 0.69
```

az uptime biztosítja a rendszer uptime-ját és az 1, 5 és 15 perces terhelési átlagokat. Ezek a terhelési átlagok nagyjából megfelelnek a munkát végzett vagy megszakítás nélküli munkára váró szálaknak. Abszolút ezek a számok nehéz lehet értelmezni, de mérik az idő múlásával tudnak mondani hasznos információkat:

- 1 perces átlagos > 5 perces átlag azt jelenti, terhelés növekszik.
- 1 perces átlagos < 5 perces átlag azt jelenti, terhelés csökken.

az állásidő azt is megvilágíthatja, hogy miért nem állnak rendelkezésre információ: előfordulhat, hogy a probléma önmagában vagy újraindítással megoldódott, mielőtt a felhasználó hozzáférhetne a számítógéphez.

A rendelkezésre álló CPU-szálak nál magasabb terhelési átlagok egy adott munkaterhelés teljesítménybeli problémát jelezhetnek.

### <a name="dmesg"></a>Dmesg

```
$ dmesg | tail 
$ dmesg --level=err | tail
```

A dmesg kidobja a kernelpuffert. Az OOMKill-hez hasonló események egy bejegyzést adnak hozzá a kernelpufferhez. Az OOMKill vagy más erőforrás-kimerülési üzenetek keresése a dmesg naplókban a probléma erős mutatója.

### <a name="top"></a>felül

```
$ top
Tasks: 249 total,   1 running, 158 sleeping,   0 stopped,   0 zombie
%Cpu(s):  2.2 us,  1.3 sy,  0.0 ni, 95.4 id,  1.0 wa,  0.0 hi,  0.2 si,  0.0 st
KiB Mem : 65949064 total, 43415136 free,  2349328 used, 20184600 buff/cache
KiB Swap:        0 total,        0 free,        0 used. 62739060 avail Mem

   PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
116004 root      20   0  144400  41124  27028 S  11.8  0.1 248:45.45 coredns
  4503 root      20   0 1677980 167964  89464 S   5.9  0.3   1326:25 kubelet
     1 root      20   0  120212   6404   4044 S   0.0  0.0  48:20.38 systemd
     ...
```

`top`széles körű áttekintést nyújt a jelenlegi rendszerállapotról. A fejlécek néhány hasznos összesített információt nyújtanak:

- a feladatok állapota: futás, alvás, leállítva.
- CPU-kihasználtság, ebben az esetben többnyire mutatja tétlen idő.
- teljes, szabad és használt rendszermemória.

`top`hiányolhatják a rövid távú folyamatokat; alternatívák, `htop` `atop` mint, és hasonló interfészek rögzítése mellett néhány ilyen hiányosságokat.

## <a name="cpu"></a>CPU

Ezek az eszközök szolgáltat CPU kihasználtsági információkat. Ez különösen hasznos a gördülő kimenet, ahol a minták könnyen helyszínen.

### <a name="mpstat"></a>mpstat

```
$ mpstat -P ALL [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

19:49:03     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
19:49:04     all    1.01    0.00    0.63    2.14    0.00    0.13    0.00    0.00    0.00   96.11
19:49:04       0    1.01    0.00    1.01   17.17    0.00    0.00    0.00    0.00    0.00   80.81
19:49:04       1    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
19:49:04       2    1.01    0.00    0.00    0.00    0.00    1.01    0.00    0.00    0.00   97.98
19:49:04       3    0.00    0.00    0.99    0.00    0.00    0.99    0.00    0.00    0.00   98.02
19:49:04       4    1.98    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   96.04
19:49:04       5    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       6    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00    0.00   98.00
19:49:04       7    1.98    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   97.03
```

`mpstat`hasonló CPU-adatokat nyomtat a tetejére, de cpu-szálak szerinti bontásban. Az összes mag egyszerre való látása hasznos lehet a nagy mértékben kiegyensúlyozatlan CPU-használat észleléséhez, például ha egyetlen szálas alkalmazás 100%-os kihasználtság mellett egy magot használ. Ezt a problémát nehezebb lehet észrevenni, ha a rendszer összes processzorára összesítik.

### <a name="vmstat"></a>vmstat

```
$ vmstat [interval]
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 43300372 545716 19691456    0    0     3    50    3    3  2  1 95  1  0
```

`vmstat`hasonló információkat `mpstat` `top`és , a CPU-n (r oszlop) várakozó folyamatok számát, a memóriastatisztikát és az egyes munkaállapotokban eltöltött processzoridő százalékát adja meg.

## <a name="memory"></a>Memory (Memória)

Memória egy nagyon fontos, és szerencsére könnyű, erőforrás nyomon követni. Egyes eszközök jelenthetik a PROCESSZOR `vmstat`ÉS a memória, mint a . De szerszámok `free` szeret május csendes lenni hasznos részére gyors hibakeresés.

### <a name="free"></a>Ingyenes

```
$ free -m
              total        used        free      shared  buff/cache   available
Mem:          64403        2338       42485           1       19579       61223
Swap:             0           0           0
```

`free`alapvető információkat jelenít meg a teljes memóriáról, valamint a felhasznált és a szabad memóriáról. `vmstat`hasznosabb lehet még az alapvető memória elemzés miatt képes biztosítani gördülő kimenet.

## <a name="disk"></a>Lemez

Ezek az eszközök a lemez IOPS-át, a várakozási várólistákat és a teljes átviteli mertét mérik. 

### <a name="iostat"></a>iostat

```
$ iostat -xy [interval] [count]
$ iostat -xy 1 1
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           3.42    0.00    2.92    1.90    0.00   91.76

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await r_await w_await  svctm  %util
loop0             0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sdb               0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
sda               0.00    56.00    0.00   65.00     0.00   504.00    15.51     0.01    3.02    0.00    3.02   0.12   0.80
scd0              0.00     0.00    0.00    0.00     0.00     0.00     0.00     0.00    0.00    0.00    0.00   0.00   0.00
```

`iostat`betekintést nyújt a lemezkihasználtságba. Ez a meghívás `-x` megfelel a `-y` kiterjesztett statisztikák, hogy kihagyja a kezdeti `1 1` kimeneti nyomtatási rendszer átlagok indítása óta, és adja meg szeretnénk 1 másodperces intervallum, végződő után egy blokk kimenet. 

`iostat`számos hasznos statisztikát tár fel:

- `r/s`és `w/s` olvasásmásodpercenként, és írások másodpercenként. Ezeknek az értékeknek az összege IOPS.
- `rkB/s`és `wkB/s` kilobájtok olvasása/írása másodpercenként. Ezeknek az értékeknek az összege átviteli.
- `await`a várólistára helyezett kérelmek átlagos iowait ideje ezredmásodpercben.
- `avgqu-sz`a megadott intervallumon felüli átlagos várólistaméret.

Egy Azure virtuális gép:

- az egyes `r/s` `w/s` blokkeszközök összege és az adott blokkeszköz esetében nem haladhatja meg a lemez Termékváltozatra vonatkozó korlátait.
- az egyes `rkB/s` `wkB/s` blokkeszközök összege és az adott blokkeszköz esetében nem haladhatja meg a lemez Termékváltozatra vonatkozó korlátait
- az összeg `r/s` `w/s` és az összes blokk eszközök nem haladhatja meg a virtuális gép Termékváltozat.
- az összeg `rkB/s` és a "wkB/s az összes blokk eszközök nem haladhatja meg a határértékeket a VM SKU.

Vegye figyelembe, hogy az operációs rendszer lemeze a kapacitásának megfelelő legkisebb termékváltozat felügyelt lemezének számít. Egy 1024 GB-os operációs rendszer lemeze például egy P30-lemeznek felel meg. Az ideiglenes operációsrendszer-lemezek és az ideiglenes lemezek nem rendelkeznek egyedi lemezkorlátokkal; csak a teljes virtuálisgép-korlátok korlátozzák őket.

Nem nulla értékek await vagy avgqu-sz is jó mutatók IO viszálykodás.

## <a name="network"></a>Network (Hálózat)

Ezek az eszközök olyan hálózati statisztikákat mérnek, mint az átviteli teljesítmény, az átviteli hibák és a kihasználtság. A mélyebb elemzés részletes TCP-statisztikákat tehet ki a torlódásokról és az eldobott csomagokról.

### <a name="sar"></a>Sar

```
$ sar -n DEV [interval]
22:36:57        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
22:36:58      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azv604be75d832      1.00      9.00      0.06      1.04      0.00      0.00      0.00      0.00
22:36:58       azure0     68.00     79.00     27.79     52.79      0.00      0.00      0.00      0.00
22:36:58    azv4a8e7704a5b    202.00    207.00     37.51     21.86      0.00      0.00      0.00      0.00
22:36:58    azve83c28f6d1c     21.00     30.00     24.12      4.11      0.00      0.00      0.00      0.00
22:36:58         eth0    314.00    321.00     70.87    163.28      0.00      0.00      0.00      0.00
22:36:58    azva3128390bff     12.00     20.00      1.14      2.29      0.00      0.00      0.00      0.00
22:36:58    azvf46c95ddea3     10.00     18.00     31.47      1.36      0.00      0.00      0.00      0.00
22:36:58       enP1s1     74.00    374.00     29.36    166.94      0.00      0.00      0.00      0.00
22:36:58           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
22:36:58    azvdbf16b0b2fc      9.00     19.00      3.36      1.18      0.00      0.00      0.00      0.00
```

`sar`egy hatékony eszköz a széles körű elemzés. Bár ez a példa a hálózati statisztikák mérésére használja, ugyanolyan erős a CPU és a memóriafelhasználás mérésére. Ez a `sar` példa `-n` jelzővel `DEV` hívja meg a (hálózati eszköz) kulcsszót, amely eszközszerint jeleníti meg a hálózati átviteli értéket.

- Egy adott `rxKb/s` `txKb/s` eszköz teljes átviteli valószínűsítésének összege. Ha ez az érték meghaladja a kiosztott Azure NIC korlátját, a számítógépen lévő számítási feladatok nagyobb hálózati késést tapasztalnak.
- `%ifutil`egy adott eszköz kihasználtságát méri. Mivel ez az érték megközelíti a 100%-ot, a számítási feladatok nagyobb hálózati késést tapasztalnak.

```
$ sar -n TCP,ETCP [interval]
Linux 4.15.0-1064-azure (aks-main-10212767-vmss000001)  02/10/20        _x86_64_        (8 CPU)

22:50:08     active/s passive/s    iseg/s    oseg/s
22:50:09         2.00      0.00     19.00     24.00

22:50:08     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
22:50:09         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         2.00      0.00     19.00     24.00

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Ez a `sar` meghívás `TCP,ETCP` a kulcsszavakat használja a TCP-kapcsolatok vizsgálatára. Az utolsó sor harmadik oszlopa, a "retrans", a TCP-újraküldések másodpercenkénti száma. A mező magas értékei megbízhatatlan hálózati kapcsolatot jeleznek. Az első és a harmadik sorban az "aktív" a helyi eszközről származó kapcsolatot jelenti, míg a "távoli" bejövő kapcsolatot jelöl.  Az Azure-ban gyakori probléma az SNAT-port kimerülése, amely `sar` segíthet észlelni. Az SNAT-port oka a magas "aktív" értékek, mivel a problémát a kimenő, helyileg kezdeményezett TCP-kapcsolatok magas aránya okoz.

Időközhöz `sar` vezet, és kinyomtatja a gördülő kimenetet, majd kinyomtatja a végső kimenetsorokat, amelyek a meghívás átlagos eredményeit tartalmazzák.

### <a name="netstat"></a>Netstat

```
$ netstat -s
Ip:
    71046295 total packets received
    78 forwarded
    0 incoming packets discarded
    71046066 incoming packets delivered
    83774622 requests sent out
    40 outgoing packets dropped
Icmp:
    103 ICMP messages received
    0 input ICMP message failed.
    ICMP input histogram:
        destination unreachable: 103
    412802 ICMP messages sent
    0 ICMP messages failed
    ICMP output histogram:
        destination unreachable: 412802
IcmpMsg:
        InType3: 103
        OutType3: 412802
Tcp:
    11487089 active connections openings
    592 passive connection openings
    1137 failed connection attempts
    404 connection resets received
    17 connections established
    70880911 segments received
    95242567 segments send out
    176658 segments retransmited
    3 bad segments received.
    163295 resets sent
Udp:
    164968 packets received
    84 packets to unknown port received.
    0 packet receive errors
    165082 packets sent
UdpLite:
TcpExt:
    5 resets received for embryonic SYN_RECV sockets
    1670559 TCP sockets finished time wait in fast timer
    95 packets rejects in established connections because of timestamp
    756870 delayed acks sent
    2236 delayed acks further delayed because of locked socket
    Quick ack mode was activated 479 times
    11983969 packet headers predicted
    25061447 acknowledgments not containing data payload received
    5596263 predicted acknowledgments
    19 times recovered from packet loss by selective acknowledgements
    Detected reordering 114 times using SACK
    Detected reordering 4 times using time stamp
    5 congestion windows fully recovered without slow start
    1 congestion windows partially recovered using Hoe heuristic
    5 congestion windows recovered without slow start by DSACK
    111 congestion windows recovered without slow start after partial ack
    73 fast retransmits
    26 retransmits in slow start
    311 other TCP timeouts
    TCPLossProbes: 198845
    TCPLossProbeRecovery: 147
    480 DSACKs sent for old packets
    175310 DSACKs received
    316 connections reset due to unexpected data
    272 connections reset due to early user close
    5 connections aborted due to timeout
    TCPDSACKIgnoredNoUndo: 8498
    TCPSpuriousRTOs: 1
    TCPSackShifted: 3
    TCPSackMerged: 9
    TCPSackShiftFallback: 177
    IPReversePathFilter: 4
    TCPRcvCoalesce: 1501457
    TCPOFOQueue: 9898
    TCPChallengeACK: 342
    TCPSYNChallenge: 3
    TCPSpuriousRtxHostQueues: 17
    TCPAutoCorking: 2315642
    TCPFromZeroWindowAdv: 483
    TCPToZeroWindowAdv: 483
    TCPWantZeroWindowAdv: 115
    TCPSynRetrans: 885
    TCPOrigDataSent: 51140171
    TCPHystartTrainDetect: 349
    TCPHystartTrainCwnd: 7045
    TCPHystartDelayDetect: 26
    TCPHystartDelayCwnd: 862
    TCPACKSkippedPAWS: 3
    TCPACKSkippedSeq: 4
    TCPKeepAlive: 62517
IpExt:
    InOctets: 36416951539
    OutOctets: 41520580596
    InNoECTPkts: 86631440
    InECT0Pkts: 14
```

`netstat`lehet befelé néző sokféle hálózati statisztika, itt hivatkozott összefoglaló kimenet. A problémától függően számos hasznos mező létezik. A TCP szakasz egyik hasznos mezője a "sikertelen csatlakozási kísérletek". Ez az SNAT-port kimerülésének vagy a kimenő kapcsolatok egyéb problémáinak jelzése lehet. Az újraküldött szegmensek nagy aránya (szintén a TCP szakaszban) jelezheti a csomagkézbesítéssel kapcsolatos problémákat. 

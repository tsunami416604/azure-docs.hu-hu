---
title: Azure-Virtual Machines hálózati késés tesztelése Azure-Virtual Networkokban | Microsoft Docs
description: Megtudhatja, hogyan tesztelheti a hálózati késést egy virtuális hálózat Azure-beli virtuális gépei között
services: virtual-network
documentationcenter: na
author: steveesp
manager: Marina Lipshteyn
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: steveesp
ms.openlocfilehash: 760a181b4459db28d3a6eee5022cc0f984c4bee0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588277"
---
# <a name="testing-network-latency"></a>Hálózati késés tesztelése

A hálózati késés mérése a feladathoz tervezett eszközzel a legpontosabb eredményt adja. A nyilvánosan elérhető eszközök, például a SockPerf (Linux) és a latte (for Windows) olyan eszközök, amelyek elkülönítik és mérik a hálózati késéseket, és nem számítanak más típusú késéseket, például az alkalmazások késését. Ezek az eszközök az alkalmazások teljesítményét, azaz a TCP-t és az UDP-t érintő hálózati forgalomra összpontosítanak. Más gyakori csatlakozási eszközök, például a pingek időnként a késés méréséhez használhatók, de ezek az eredmények nem lehetnek a valós munkaterhelésekben használt hálózati forgalomra jellemzőek. Ennek oka, hogy a legtöbb ilyen eszköz az ICMP protokollt használja, amely eltérően kezelhető az alkalmazások forgalmával, és előfordulhat, hogy az eredmények nem alkalmazhatók a TCP-t és az UDP-t használó munkaterhelésekre. A legtöbb alkalmazás által használt protokollok pontos hálózati késésének teszteléséhez a SockPerf (Linux esetében) és a latte (for Windows) a legfontosabb eredményeket hozza létre. Ez a dokumentum mindkét eszközt magában foglalja.

## <a name="overview"></a>Áttekintés

Két virtuális gép, az egyik feladó és egy fogadó, kétirányú kommunikációs csatorna jön létre a csomagok küldésére és fogadására mindkét irányban, hogy mérje a kerekítési időt (RTT).

Ezekkel a lépésekkel mérhető a hálózati késés két Virtual Machines (VM) vagy akár két fizikai számítógép között. A késési mérések a következő esetekben lehetnek hasznosak:

- Teljesítményteszt létrehozása a központilag telepített virtuális gépek közötti hálózati késéshez
- A hálózati késésben történt változások hatásainak összehasonlítása a kapcsolódó módosítások után:
  - Operációs rendszer vagy hálózati verem szoftver, beleértve a konfigurációs módosításokat
  - Virtuálisgép-telepítési módszer, például zónák vagy PPG üzembe helyezése
  - A virtuális gép tulajdonságai, például a gyorsított hálózatkezelés vagy a méret módosítása
  - Virtuális hálózat, például útválasztási vagy szűrési változások

### <a name="tools-for-testing"></a>Tesztelési eszközök
A késés méréséhez két különböző lehetőség közül választhatunk, egyet a Windows-alapú rendszerekhez, egyet pedig a Linux-alapú rendszerekhez.

Windows esetén: latte. exe (Windows) [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Linux esetén: SockPerf (Linux) [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

Ezeknek az eszközöknek a használatával biztosítható, hogy csak a TCP-vagy UDP-adatátviteli időt mérjük, és ne ICMP (ping) vagy más, az alkalmazások által nem használt csomagok, és ne befolyásolják a teljesítményüket.

### <a name="tips-for-an-optimal-vm-configuration"></a>Tippek a virtuális gépek optimális konfigurálásához:

- A Windows vagy Linux legújabb verziójának használata
- Gyorsított hálózatkezelés engedélyezése a legjobb eredmények érdekében
- Virtuális gépek üzembe helyezése az [Azure Proximity elhelyezési csoporttal](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- A nagyobb méretű virtuális gépek általában jobb teljesítményt biztosítanak a kisebb virtuális gépeknél

### <a name="tips-for-analysis"></a>Elemzési tippek

- Az üzembe helyezés, a konfiguráció és az optimalizálások befejezése után korán létre kell hoznia egy alaptervet
- Mindig hasonlítsa össze az új eredményeket egy alapkonfigurációval, vagy más módon az egyik tesztből egy másikba az ellenőrzött módosításokkal.
- Tesztek ismétlése minden változás megfigyelt vagy tervezett állapotában


## <a name="testing-vms-running-windows"></a>Windows rendszerű virtuális gépek tesztelése

## <a name="get-latteexe-onto-the-vms"></a>A latte. exe beolvasása a virtuális gépekre

Töltse le a legújabb verziót: [https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Fontolja meg a latte. exe külön mappában való elhelyezését, például a c:\Tools

## <a name="allow-latteexe-through-the-windows-firewall"></a>A latte. exe engedélyezése a Windows tűzfalon keresztül

A FOGADÓn hozzon létre egy engedélyezési szabályt a Windows tűzfalon, hogy engedélyezze a latte. exe-forgalom érkezését. A legkönnyebben lehetővé teszi a teljes latte. exe program nevét, és nem az adott TCP-portok bejövő engedélyezését.

Engedélyezze a latte. exe programot a Windows tűzfalon keresztül, például a következő módon:

netsh advfirewall Firewall Add Rule program =\<PATH\>\\latte. exe név =&quot;latte&quot; Protocol = bármely dir = in Action = Allow Enable = Yes Profile = ANY


Ha például a latte. exe fájlt a &quot;c:\\Tools&quot; mappába másolta, a parancs a következő lesz:

```cmd
netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

## <a name="running-latency-tests"></a>Késési tesztek futtatása

A latte. exe elindítása a FOGADÓn (Futtatás a CMD-ből, nem a PowerShellből):

latte-a &lt;fogadó IP-címe&gt;:&lt;port&gt;-i &lt;iterációk&gt;

A 65k-iterációk nagyjából elég hosszúak a reprezentatív eredmények visszaküldéséhez.

Minden elérhető portszám rendben van.

Ha a virtuális gép IP-címe 10.0.0.4, a következőképpen fog kinézni:

```cmd
latte -a 10.0.0.4:5005 -i 65100
```

A latte. exe elindítása a KÜLDŐn (Futtatás a CMD-ből, nem a PowerShellből):

latte-c-a \<fogadó IP-címe\>:\<port\>-i \<iterációk\>


Az eredményül kapott parancs megegyezik a fogadóval, kivéve a &quot;-c&quot; hozzáadásával azt, hogy ez a &quot;ügyfél&quot; vagy feladó:

```cmd
latte -c -a 10.0.0.4:5005 -i 65100
```

Várjon az eredményekre. Attól függően, hogy a virtuális gépek milyen távol vannak, néhány percet is igénybe vehet. A már elvégzett tesztek futtatása előtt érdemes lehet kevesebb iterációt kezdenie a sikeres teszteléshez.



## <a name="testing-vms-running-linux"></a>Linux rendszerű virtuális gépek tesztelése

SockPerf használata. Elérhető [https://github.com/mellanox/sockperf](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>A SockPerf telepítése a virtuális gépeken

A Linux rendszerű virtuális gépeken (a küldő és a fogadó is) futtassa ezeket a parancsokat a virtuális gépek SockPerf előkészítéséhez. A fő disztribúciók parancsai vannak megadva.

#### <a name="for-rhel--centos-follow-these-steps"></a>A RHEL/CentOS esetében kövesse az alábbi lépéseket:
```bash
#CentOS / RHEL - Install GIT and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu-follow-these-steps"></a>Ubuntu esetén kövesse az alábbi lépéseket:
```bash
#Ubuntu - Install GIT and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros-copy-compile-and-install-sockperf-according-to-the-following-steps"></a>Az összes disztribúció esetében másolja, fordítsa le és telepítse a SockPerf az alábbi lépések szerint:
```bash
#Bash - all distros

#From bash command line (assumes git is installed)
git clone https://github.com/mellanox/sockperf
cd sockperf/
./autogen.sh
./configure --prefix=

#make is slower, may take several minutes
make

#make install is fast
sudo make install
```

### <a name="run-sockperf-on-the-vms"></a>SockPerf futtatása a virtuális gépeken

A SockPerf telepítésének befejeztével a virtuális gépek készen állnak a késési tesztek futtatására. 

Először indítsa el a SockPerf a FOGADÓn.

Minden elérhető portszám rendben van. Ebben a példában a 12345-es portot használjuk:
```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```
Most, hogy a kiszolgáló figyel, az ügyfél megkezdheti a csomagok küldését arra a portra, amelyen a figyel, 12345 ebben az esetben.

Körülbelül 100 másodperc elég hosszú a reprezentatív eredmények visszaküldéséhez, ahogy az az alábbi példában is látható:
```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Várjon az eredményekre. Attól függően, hogy a virtuális gépek milyen távolságban vannak, az ismétlések száma eltérő lesz. A hosszabb tesztek végrehajtása előtt érdemes lehet 5 másodpercnél rövidebb teszteket kezdeni.

Ez a SockPerf-példa egy 350 bájtos üzenet méretét használja, mivel ez egy tipikus átlagos méretű csomag. Az üzenetek mérete magasabb vagy alacsonyabb értékre állítható, így a virtuális gépeken futó számítási feladatok pontosabban reprezentálják az eredményeket.


## <a name="next-steps"></a>További lépések
* Növelje a késést az [Azure Proximity elhelyezési csoporttal](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
* Útmutató a [virtuális gépek hálózatkezelésének optimalizálásához](../virtual-network/virtual-network-optimize-network-bandwidth.md) a forgatókönyvhöz.
* További információ arról [, hogyan vannak lefoglalva a sávszélesség a virtuális gépekhez](../virtual-network/virtual-machine-network-throughput.md)
* További információ az [Azure Virtual Network gyakori kérdéseiről (GYIK)](../virtual-network/virtual-networks-faq.md)
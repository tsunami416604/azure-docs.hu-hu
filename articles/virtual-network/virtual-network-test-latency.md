---
title: Tesztelje az Azure virtuálisgép-hálózat késését egy Azure virtuális hálózatban | Microsoft dokumentumok
description: Ismerje meg, hogyan tesztelhet hálózati késést az Azure virtuális gépei között egy virtuális hálózaton
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
ms.openlocfilehash: 00efc2754948d53d4f80a6261dbd4041b358185b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896360"
---
# <a name="test-vm-network-latency"></a>Virtuális gép hálózati késleltetésének tesztelése

A legpontosabb eredmények elérése érdekében mérje az Azure virtuális gép (VM) hálózati késését egy feladathoz tervezett eszközzel. A nyilvánosan elérhető eszközök, például a SockPerf (Linux) és a latte.exe (Windows esetén) elkülöníthetik és mérhetik a hálózati késést, miközben kizárják az egyéb késéstípusokat, például az alkalmazáskésést. Ezek az eszközök az alkalmazás teljesítményét befolyásoló hálózati forgalom típusára összpontosítanak (nevezetesen a Transmission Control Protocol [TCP] és a User Datagram Protocol [UDP] forgalom). 

Más gyakori kapcsolódási eszközök, például a Ping, mérhetik a késést, de az eredmények nem feltétlenül tükrözik a valós munkaterhelésekben használt hálózati forgalmat. Ennek az az oka, hogy a legtöbb ilyen eszköz az Internet Control Message Protocol (ICMP) protokollt alkalmazza, amely az alkalmazásforgalomtól eltérően kezelhető, és amelynek eredményei nem feltétlenül vonatkoznak a TCP- és UDP-t használó számítási feladatokra. 

A legtöbb alkalmazás által használt protokollok pontos hálózati késéstesztelése érdekében a SockPerf (Linux) és a latte.exe (Windows) a legrelevánsabb eredményeket hozza. Ez a cikk mindkét eszközt ismerteti.

## <a name="overview"></a>Áttekintés

Két virtuális gép használatával, az egyik küldőként, a másik fogadóként, kétirányú kommunikációs csatornát hoz létre. Ezzel a megközelítéssel mindkét irányban küldhet és fogadhat csomagokat, és mérheti az oda-vissza út idejét (RTT).

Ezzel a módszersel mérheti a hálózati késést két virtuális gép vagy akár két fizikai számítógép között. A késésmérésai a következő esetekben lehetnek hasznosak:

- Hozzon létre egy viszonyítási alap a hálózati késés az üzembe helyezett virtuális gépek között.
- Hasonlítsa össze a hálózati késés változásainak hatásait a kapcsolódó módosítások után:
  - Operációs rendszer (OPERÁCIÓS rendszer) vagy hálózati veremszoftver, beleértve a konfigurációs módosításokat is.
  - Virtuális gép központi telepítési módszer, például egy rendelkezésre állási zóna vagy a közelség elhelyezési csoport (PPG) üzembe helyezése.
  - Virtuális gép tulajdonságai, például a gyorsított hálózatkezelés vagy a méret módosítása.
  - Virtuális hálózat, például útválasztás vagy szűrés módosítása.

### <a name="tools-for-testing"></a>Tesztelési eszközök
A késés méréséhez két különböző eszközbeállítás közül választhat:

* Windows alapú rendszerekesetén: [latte.exe (Windows)](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)
* Linux alapú rendszerekhez: [SockPerf (Linux)](https://github.com/mellanox/sockperf)

Ezekkel az eszközökkel biztosíthatja, hogy csak a TCP vagy UDP hasznos kézbesítési idejét mérjük, és ne az ICMP (Ping) vagy más olyan csomagtípusokat, amelyeket az alkalmazások nem használnak, és amelyek nem befolyásolják a teljesítményüket.

### <a name="tips-for-creating-an-optimal-vm-configuration"></a>Tippek az optimális virtuálisgép-konfiguráció létrehozásához

A virtuális gép konfigurációjának létrehozásakor tartsa szem előtt az alábbi javaslatokat:
- Használja a Windows vagy a Linux legújabb verzióját.
- A legjobb eredmény érdekében engedélyezze a gyorsított hálózatkezelést.
- Virtuális gépek üzembe helyezése egy [Azure közelségi elhelyezési csoporttal.](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)
- A nagyobb virtuális gépek általában jobban teljesítenek, mint a kisebb virtuális gépek.

### <a name="tips-for-analysis"></a>Tippek az elemzéshez

A teszteredmények elemzése során tartsa szem előtt az alábbi javaslatokat:

- Hozzon létre egy alapkonfigurációt korán, amint a központi telepítés, a konfiguráció és az optimalizálás befejeződött.
- Mindig hasonlítsa össze az új eredményeket egy alapértékkel, vagy ellenkező esetben az egyik tesztről a másikra ellenőrzött változásokkal.
- Ismételje meg a vizsgálatokat, ha változásokat észlelnek vagy terveznek.


## <a name="test-vms-that-are-running-windows"></a>Windows rendszert futtató virtuális gépek tesztelése

### <a name="get-latteexe-onto-the-vms"></a>A latte.exe beszereznie a virtuális gépekre

Töltse le a [latte.exe legújabb verzióját.](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b)

Fontolja meg a latte.exe fájl külön mappába helyezését, például *a c:\tools mappába.*

### <a name="allow-latteexe-through-windows-defender-firewall"></a>Latte.exe engedélyezése a Windows Defender tűzfalon keresztül

A *fogadóban*hozzon létre egy Engedélyezési szabályt a Windows Defender tűzfalon, hogy a latte.exe forgalom megérkezzen. Ez a legegyszerűbb, hogy a teljes latte.exe program név szerint, nem pedig, hogy bizonyos TCP portok bejövő.

A latte.exe engedélyezésének engedélyezése a Windows Defender tűzfalon keresztül a következő parancs futtatásával:

```cmd
netsh advfirewall firewall add rule program=<path>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY
```

Ha például a latte.exe fájlt a *c:\tools* mappába másolta, ez lesz a következő parancs:

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Késési tesztek futtatása

* A *vevőegységen*indítsa el a latte.exe parancsot (futtassa a CMD ablakból, ne a PowerShellből):

    ```cmd
    latte -a <Receiver IP address>:<port> -i <iterations>
    ```

    Körülbelül 65 000 iteráció elég hosszú ahhoz, hogy reprezentatív eredményeket ad vissza.

    Minden rendelkezésre álló port szám rendben van.

    Ha a virtuális gép IP-címe 10.0.0.4, a parancs így néz ki:

    `latte -a 10.0.0.4:5005 -i 65100`

* A *feladón*indítsa el a latte.exe-t (futtassa a CMD ablakból, ne a PowerShellből):

    ```cmd
    latte -c -a <Receiver IP address>:<port> -i <iterations>
    ```

    Az eredményül kapott parancs megegyezik a fogadóval, kivéve a&nbsp;*-c* hozzáadásával, amely jelzi, hogy ez az *ügyfél*vagy *a feladó:*

    `latte -c -a 10.0.0.4:5005 -i 65100`

Várjuk meg az eredményeket. Attól függően, hogy milyen messze vannak egymástól a virtuális gépek, a teszt eltarthat néhány percig. Fontolja meg a kevesebb iterációval való indítást a sikeres teszteléshez, mielőtt hosszabb teszteket futtatna.

## <a name="test-vms-that-are-running-linux"></a>Linuxot futtató virtuális gépek tesztelése

A Linuxot futtató virtuális gépek teszteléséhez használja a [SockPerf függvényt.](https://github.com/mellanox/sockperf)

### <a name="install-sockperf-on-the-vms"></a>SockPerf telepítése a virtuális gépekre

A Linux virtuális gépeken a *küldő* és a *fogadó*is futtatja a következő parancsokat a SockPerf előkészítéséhez a virtuális gépeken. A főbb dististikhoz parancsokat adnak.

#### <a name="for-red-hat-enterprise-linux-rhelcentos"></a>Red Hat Enterprise Linux (RHEL)/CentOS esetén

Futtassa az alábbi parancsot:

```bash
#RHEL/CentOS - Install Git and other helpful tools
    sudo yum install gcc -y -q
    sudo yum install git -y -q
    sudo yum install gcc-c++ -y
    sudo yum install ncurses-devel -y
    sudo yum install -y automake
    sudo yum install -y autoconf
```

#### <a name="for-ubuntu"></a>Ubuntu esetén

Futtassa az alábbi parancsot:

```bash
#Ubuntu - Install Git and other helpful tools
    sudo apt-get install build-essential -y
    sudo apt-get install git -y -q
    sudo apt-get install -y autotools-dev
    sudo apt-get install -y automake
    sudo apt-get install -y autoconf
```

#### <a name="for-all-distros"></a>Minden distros

Másolja, fordítsa le és telepítse a SockPerf-et a következő lépések nek megfelelően:

```bash
#Bash - all distros

#From bash command line (assumes Git is installed)
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

A SockPerf telepítése befejezése után a virtuális gépek készen állnak a késési tesztek futtatására. 

Először indítsa el a SockPerf-et a *vevőn*.

Minden rendelkezésre álló port szám rendben van. Ebben a példában az 12345-ös portot használjuk:

```bash
#Server/Receiver - assumes server's IP is 10.0.0.4:
sudo sockperf sr --tcp -i 10.0.0.4 -p 12345
```

Most, hogy a kiszolgáló figyel, az ügyfél megkezdheti a csomagok küldését a kiszolgálóra azon a porton, amelyen figyel (ebben az esetben 12345).

Körülbelül 100 másodperc elég hosszú ahhoz, hogy reprezentatív eredményeket ad vissza, amint az a következő példában látható:

```bash
#Client/Sender - assumes server's IP is 10.0.0.4:
sockperf ping-pong -i 10.0.0.4 --tcp -m 350 -t 101 -p 12345  --full-rtt
```

Várjuk meg az eredményeket. Attól függően, hogy milyen messze vannak egymástól a virtuális gépek, az ismétlések száma változik. A hosszabb tesztek futtatása előtt a sikeres teszteléshez érdemes lehet körülbelül 5 másodperces rövidebb tesztekkel kezdeni.

Ez a SockPerf-példa 350 bájtos üzenetméretet használ, amely egy átlagos csomagra jellemző. Módosíthatja a méretet magasabb vagy alacsonyabb eredmények elérése érdekében, amelyek pontosabban képviselik a virtuális gépeken futó számítási feladatokat.


## <a name="next-steps"></a>További lépések
* Az [Azure közelségi elhelyezési csoportjával](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)javíthatja a késést.
* Ismerje meg, hogyan [optimalizálhatja a hálózati hálózatok at virtuális gépek](../virtual-network/virtual-network-optimize-network-bandwidth.md) a forgatókönyvhöz.
* További információ [arról, hogy a sávszélesség hogyan van lefoglalva a virtuális gépek számára.](../virtual-network/virtual-machine-network-throughput.md)
* További információ: [Azure Virtual Network GYAKORI KÉRDÉSEK](../virtual-network/virtual-networks-faq.md).

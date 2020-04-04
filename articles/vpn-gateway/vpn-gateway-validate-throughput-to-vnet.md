---
title: Vpn-átviteli hálózat ellenőrzése Microsoft Azure virtuális hálózatszámára
description: Ez a dokumentum célja, hogy segítsen a felhasználónak érvényesíteni a hálózati átviteli a helyszíni erőforrások egy Azure virtuális gép.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
manager: dcscontentpm
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 05/29/2019
ms.author: radwiv
ms.reviewer: chadmat;genli
ms.openlocfilehash: dcf86deda32069bf9711dbeb733dc9361e22a771
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631772"
---
# <a name="how-to-validate-vpn-throughput-to-a-virtual-network"></a>VPN teljesítményének érvényesítése virtuális hálózaton

A VPN-átjárókapcsolat lehetővé teszi, hogy biztonságos, több helyiségen át vezető kapcsolatot hozzon létre az Azure-on belüli virtuális hálózat és a helyszíni informatikai infrastruktúra között.

Ez a cikk bemutatja, hogyan érvényesítheti a hálózati átviteli a helyszíni erőforrások egy Azure virtuális gép (VM).

> [!NOTE]
> Ez a cikk a gyakori problémák diagnosztizálásának és javításának elősegítésére szolgál. Ha nem tudja megoldani a problémát az alábbi információk használatával, forduljon az [ügyfélszolgálathoz.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="overview"></a>Áttekintés

A VPN-átjárókapcsolat a következő összetevőket foglalja magában:

* Helyszíni VPN-eszköz (Az ellenőrzött [VPN-eszközök](vpn-gateway-about-vpn-devices.md#devicetable)listájának megtekintése.)
* Nyilvános internet
* Azure VPN-átjáró
* Azure VM

Az alábbi ábrán egy helyszíni hálózat és egy Azure virtuális hálózat vpn-en keresztül imitátrál.

![Az ügyfélhálózat és az MSFT-hálózat logikai kapcsolata VPN használatával](./media/vpn-gateway-validate-throughput-to-vnet/VPNPerf.png)

## <a name="calculate-the-maximum-expected-ingressegress"></a>A várható maximális be- és kilépés kiszámítása

1. Határozza meg az alkalmazás alapszintű átviteli követelményeit.
1. Határozza meg az Azure VPN-átjáró átviteli korlátjait. A [VPN-átjáró – A VPN-átjáró – "Átjáró](vpn-gateway-about-vpngateways.md#gwsku)termékkészlet" című szakaszában talál segítséget.
1. Határozza meg az [Azure virtuális gép átviteli útmutatóját](../virtual-machines/virtual-machines-windows-sizes.md) a virtuális gép méretéhez.
1. Határozza meg az internetszolgáltató (ISP) sávszélességét.
1. Számítsa ki a várható átviteli úgy, hogy a virtuális gép, a VPN-átjáró vagy az ISP legkisebb sávszélességét veszi igénybe; amelyet megabit/másodpercben (/) mérnek, osztva nyolc (8) értékre.

Ha a számított átviteli érték nem felel meg az alkalmazás alapszintű átviteli követelményeinek, növelnie kell a szűk keresztmetszetként azonosított erőforrás sávszélességét. Az Azure VPN-átjáró átméretezéséhez olvassa el [az átjáró termékváltozatának módosítása.](vpn-gateway-about-vpn-gateway-settings.md#gwsku) Virtuális gép átméretezéséhez olvassa el [a Virtuális gép átméretezése.](../virtual-machines/virtual-machines-windows-resize-vm.md) Ha nem tapasztalja a várt internetes sávszélességet, felveheti a kapcsolatot az internetszolgáltatóval is.

> [!NOTE]
> A VPN-átjáró átviteli valószínűleg az összes hely közötti\VNET-vNET vagy pont-hely kapcsolat összesítése.

## <a name="validate-network-throughput-by-using-performance-tools"></a>A hálózati átviteli teljesítmény ellenőrzése teljesítményeszközökkel

Ezt az ellenőrzést nem csúcsidőben kell elvégezni, mivel a VPN-alagút átviteli telítettsége a tesztelés során nem ad pontos eredményeket.

Az eszköz általunk használt ez a teszt iPerf, ami működik mind a Windows és a Linux, és mind az ügyfél-és szerver mód. Windows virtuális gépek esetén 3 Gbps-os sebességre van korlátozva.

Ez az eszköz nem hajt végre olvasási/írási műveleteket a lemezre. Kizárólag saját maga által generált TCP-forgalmat hoz létre az egyik végétől a másikig. Az ügyfél- és kiszolgálócsomópontok között rendelkezésre álló sávszélességet mérő kísérletezésen alapuló statisztikákat hoz létre. Két csomópont közötti tesztelés során az egyik csomópont kiszolgálóként, a másik csomópont pedig ügyfélként működik. A teszt befejezése után azt javasoljuk, hogy fordítsa meg a csomópontok szerepköreit a feltöltési és letöltési átviteli szint teszteléséhez mindkét csomóponton.

### <a name="download-iperf"></a>Letöltés iPerf

Letöltés [iPerf](https://iperf.fr/download/iperf_3.1/iperf-3.1.2-win64.zip). További részletek az [iPerf dokumentációjában](https://iperf.fr/iperf-doc.php)találhatók.

 > [!NOTE]
 > A cikkben tárgyalt, harmadik féltől származó termékeket a Microsofttól független vállalatok gyártják. A Microsoft sem hallgatólagosan, sem más módon nem vállal garanciát a termékek teljesítményére vagy megbízhatóságára.

### <a name="run-iperf-iperf3exe"></a>Az iPerf futtatása (iperf3.exe)

1. Engedélyezze az NSG/ACL szabályt, amely engedélyezi a forgalmat (nyilvános IP-cím teszteléséhez az Azure Virtuális gépen).

1. Mindkét csomóponton engedélyezze az 5001-es port tűzfalkivételét.

   **Windows:** Futtassa a következő parancsot rendszergazdaként:

   ```CMD
   netsh advfirewall firewall add rule name="Open Port 5001" dir=in action=allow protocol=TCP localport=5001
   ```

   Ha a tesztelés befejezésekor el szeretné távolítani a szabályt, futtassa a következő parancsot:

   ```CMD
   netsh advfirewall firewall delete rule name="Open Port 5001" protocol=TCP localport=5001
   ```

   **Azure Linux:** Az Azure Linux-rendszerképek megengedő tűzfalakkal rendelkeznek. Ha egy alkalmazás figyel egy porton, a forgalom átmehet. A biztonságos egyéni lemezképeket explicit módon meg kell nyitni. A linuxos operációs rendszer `iptables` `ufw`rétegű `firewalld`gyakori tűzfalak közé tartozik a , vagy a .

1. A kiszolgálócsomóponton váltson arra a könyvtárra, ahol az iperf3.exe kibontása történik. Ezután futtassa az iPerf-et kiszolgálómódban, és állítsa be, hogy az 5001-es porton figyelje a következő parancsokat:

   ```CMD
   cd c:\iperf-3.1.2-win65

   iperf3.exe -s -p 5001
   ```

   > [!Note]
   > Az 5001-es port testreszabható, hogy figyelembe vegye a környezetében lévő egyes tűzfalkorlátozásokat.

1. Az ügyfélcsomóponton váltson arra a könyvtárra, ahol az iperf eszközt kibontja, majd futtassa a következő parancsot:

   ```CMD
   iperf3.exe -c <IP of the iperf Server> -t 30 -p 5001 -P 32
   ```

   Az ügyfél 30 másodperces forgalmat irányít az 5001-es porton a kiszolgálóra. A "-P" jelző azt jelzi, hogy 32 egyidejű kapcsolatot létesítünk a kiszolgálócsomóddal.

   A következő képernyőn látható a kimenet ebben a példában:

   ![Kimenet](./media/vpn-gateway-validate-throughput-to-vnet/06theoutput.png)

1. (NEM KÖTELEZŐ) A tesztelési eredmények megőrzéséhez futtassa a következő parancsot:

   ```CMD
   iperf3.exe -c IPofTheServerToReach -t 30 -p 5001 -P 32  >> output.txt
   ```

1. Az előző lépések végrehajtása után hajtsa végre ugyanazokat a lépéseket a szerepkörök sztornírozásával, hogy a kiszolgálócsomópont mostantól az ügyfélcsomópont legyen, és fordítva.

> [!Note]
> Iperf nem az egyetlen eszköz. [Az NTTTCP egy alternatív megoldás a teszteléshez.](https://docs.microsoft.com/azure/virtual-network/virtual-network-bandwidth-testing)

## <a name="test-vms-running-windows"></a>Windows rendszert futtató virtuális gépek tesztelése

### <a name="load-latteexe-onto-the-vms"></a>A Latte.exe betöltése a virtuális gépekre

A [Latte.exe](https://gallery.technet.microsoft.com/Latte-The-Windows-tool-for-ac33093b) legújabb verziójának letöltése

Fontolja meg a Latte.exe külön mappába helyezését, például`c:\tools`

### <a name="allow-latteexe-through-the-windows-firewall"></a>Latte.exe engedélyezése a Windows tűzfalon keresztül

A fogadóban hozzon létre egy Engedélyezési szabályt a Windows tűzfalon, hogy a Latte.exe forgalom megérkezzen. A legegyszerűbb, ha a teljes Latte.exe programot név szerint engedélyezi, nem pedig bizonyos TCP-portok bejövő hívását.

### <a name="allow-latteexe-through-the-windows-firewall-like-this"></a>A Latte.exe engedélyezése a Windows tűzfalon keresztül, így

`netsh advfirewall firewall add rule program=<PATH>\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

Ha például a latte.exe fájlt a "c:\tools" mappába másolta, ez lesz a parancs

`netsh advfirewall firewall add rule program=c:\tools\latte.exe name="Latte" protocol=any dir=in action=allow enable=yes profile=ANY`

### <a name="run-latency-tests"></a>Késési tesztek futtatása

Indítsa el a latte.exe-t a RECEIVER-en (a CMD-ből, ne a PowerShellből fusson):

`latte -a <Receiver IP address>:<port> -i <iterations>`

Körülbelül 65k iterációk elég hosszú ahhoz, hogy visszatérjen reprezentatív eredményeket.

Minden rendelkezésre álló port szám rendben van.

Ha a virtuális gép IP-címe 10.0.0.4, akkor így fog kinézni:

`latte -c -a 10.0.0.4:5005 -i 65100`

Indítsa el a latte.exe-t a SENDER-en (cmd-ből, ne powershellből fusson)

`latte -c -a <Receiver IP address>:<port> -i <iterations>`

Az eredményül kapott parancs ugyanaz, mint a fogadó, kivéve a "-c" hozzáadásával, amely azt jelzi, hogy ez az "ügyfél" vagy a feladó

`latte -c -a 10.0.0.4:5005 -i 65100`

Várjuk meg az eredményeket. Attól függően, hogy a virtuális gépek milyen messze vannak egymástól, néhány percet is igénybe vehet. Fontolja meg a kevesebb iterációval való indítást a sikeres teszteléshez, mielőtt hosszabb teszteket futtatna.

## <a name="test-vms-running-linux"></a>Linuxot futtató virtuális gépek tesztelése

A [SockPerf](https://github.com/mellanox/sockperf) segítségével tesztelje a virtuális gépeket.

### <a name="install-sockperf-on-the-vms"></a>SockPerf telepítése a virtuális gépekre

A Linux virtuális gépeken (mind a SENDER, mind a RECEIVER) futtassa ezeket a parancsokat a SockPerf előkészítéséhez a virtuális gépeken:

#### <a name="centos--rhel---install-git-and-other-helpful-tools"></a>CentOS / RHEL - Telepítse git és egyéb hasznos eszközök

`sudo yum install gcc -y -q`
`sudo yum install git -y -q`
`sudo yum install gcc-c++ -y`
`sudo yum install ncurses-devel -y`
`sudo yum install -y automake`

#### <a name="ubuntu---install-git-and-other-helpful-tools"></a>Ubuntu - Telepítse a GIT-et és más hasznos eszközöket

`sudo apt-get install build-essential -y`
`sudo apt-get install git -y -q`
`sudo apt-get install -y autotools-dev`
`sudo apt-get install -y automake`

#### <a name="bash---all"></a>Bash - minden

A bash parancssorból (feltételezi, hogy a git telepítve van)

`git clone https://github.com/mellanox/sockperf`
`cd sockperf/`
`./autogen.sh`
`./configure --prefix=`

A gyártmány lassabb, több percig is eltarthat

`make`

Make telepítés gyors

`sudo make install`

### <a name="run-sockperf-on-the-vms"></a>SockPerf futtatása a virtuális gépeken

#### <a name="sample-commands-after-installation-serverreceiver---assumes-servers-ip-is-10004"></a>Mintaparancsok a telepítés után. Kiszolgáló/fogadó - feltételezi, hogy a kiszolgáló IP-címe 10.0.0.4

`sudo sockperf sr --tcp -i 10.0.0.4 -p 12345 --full-rtt`

#### <a name="client---assumes-servers-ip-is-10004"></a>Ügyfél - feltételezi, hogy a kiszolgáló IP-címe 10.0.0.4

`sockperf ping-pong -i 10.0.0.4 --tcp -m 1400 -t 101 -p 12345  --full-rtt`

> [!Note]
> Győződjön meg arról, hogy nincsenek köztes ugrások (pl. virtuális berendezés) a virtuális gép és az átjáró közötti átviteli tesztelés során.
> Ha a fenti iPERF/NTTTCP tesztek rossz eredményeket hoznak (a teljes átviteli jelátviteli rendszer tekintetében), kérjük, olvassa el a következő cikket a probléma lehetséges kiváltó okainak megértéséhez:https://docs.microsoft.com/azure/virtual-network/virtual-network-tcpip-performance-tuning

Különösen az ügyféltől és a kiszolgálótól párhuzamosan gyűjtött csomagrögzítési nyomok (Wireshark/Network Monitor) elemzése segít a rossz teljesítmény értékelésében. Ezek a nyomkövetések közé tartozik a csomagvesztés, a nagy késés, MTU-méret. töredezettség, TCP 0 ablak, Rendelésen kívüli töredékek és így tovább.

## <a name="address-slow-file-copy-issues"></a>A lassú fájlmásolással kapcsolatos problémák kezelése

Még akkor is, ha az előző lépésekkel (iPERF/NTTTCP/etc.) értékelt teljes átviteli érték jó volt, lassú fájlkezelést tapasztalhat, amikor a Windows Intézővel vagy egy RDP-munkameneten keresztül húzza. Ez a probléma általában az alábbi tényezők egyikének vagy mindkettőnek köszönhető:

* A fájlmásolási alkalmazások, például a Windows Intéző és az RDP fájlok másolásakor nem használnak több szálat. A jobb teljesítmény érdekében használjon többszálas fájlmásolási alkalmazást, például [a Richcopy alkalmazást](https://technet.microsoft.com/magazine/2009.04.utilityspotlight.aspx) a fájlok 16 vagy 32 szál használatával történő másolásához. A Richcopy fájlmásolat-számának módosításához kattintson a **Műveletmásolás** > beállításai**Fájlmásolás parancsra.****Copy options** > 

   ![Lassú fájlmásolási problémák](./media/vpn-gateway-validate-throughput-to-vnet/Richcopy.png)<br>

   > [!Note]
   > Nem minden alkalmazás dolgozik ugyanaz, és nem minden alkalmazás/ folyamat használ minden a fonál. Ha futtatja a tesztet, előfordulhat, hogy néhány szál üres, és nem nyújt pontos átviteli eredményeket.
   > Az alkalmazás fájlátviteli teljesítményének ellenőrzéséhez használja a többszálas szálat a szál sorozatban történő számának növelésével vagy az alkalmazás vagy a fájlátvitel optimális átviteli képességének meghatározásával.

* A virtuális gép lemezolvasási/-írási sebessége nem megfelelő. További információ: [Azure Storage Troubleshooting](../storage/common/storage-e2e-troubleshooting.md).

## <a name="on-premises-device-external-facing-interface"></a>Helyszíni eszköz külső felülete

Említette a helyszíni tartományok alhálózatait, amelyeket az Azure-nak VPN-en keresztül a helyi hálózati átjárón keresztül el kell érnie. Ezzel egyidejűleg határozza meg a virtuális hálózat címterét az Azure-ban a helyszíni eszközhöz.

* **Útvonalalapú átjáró:** Az útvonalalapú VPN-ek házirend- vagy forgalomválasztója bármely-mindenhez (vagy helyettesítő karakterhez) van konfigurálva.

* **Házirendalapú átjáró:** A házirend-alapú VPN-ek titkosítják és irányítják a csomagokat az IPsec-alagutakon keresztül a helyszíni hálózat és az Azure virtuális hálózat közötti címelőtagok kombinációi alapján. A házirend (vagy forgalomválasztó) általában egy hozzáférési listaként van megadva a VPN-konfigurációban.

* **UsePolicyTrafficSelector** kapcsolatok: ("UsePolicyBasedTrafficSelectors" $True a kapcsolat konfigurálja az Azure VPN-átjáró, hogy csatlakozzon a házirend-alapú VPN-tűzfal a helyszínen. Ha engedélyezi a PolicyBasedTrafficSelectors, meg kell győződnie arról, hogy a VPN-eszköz rendelkezik a megfelelő forgalom választók a helyszíni hálózati (helyi hálózati átjáró) előtagok összes kombinációját az Azure virtuális hálózati előtagok helyett minden.If you enable PolicyBasedTrafficSelectors, you need to ensure your VPN device is the matching traffic selectors defined with all combinations of your on-premises network (local network gateway) prefixesek to and from the Azure virtual network prefixes, instead of any-to-any.

Nem megfelelő konfiguráció vezethet gyakori bontja az alagúton belül, csomag csepp, rossz átviteli és késés.

## <a name="check-latency"></a>Késés ellenőrzése

A késést a következő eszközökkel ellenőrizheti:

* WinMTR között
* TCPTraceroute
* `ping`és `psping` (Ezek az eszközök jó becslést nyújtanak az RTT-ről, de nem használhatók minden esetben.)

![Késés ellenőrzése](./media/vpn-gateway-validate-throughput-to-vnet/08checkinglatency.png)

Ha az MS Network gerinchálózatába való belépés előtt az ugrások bármelyikén nagy késleltetésű kiugrást észlel, érdemes lehet további vizsgálatokat végezni az internetszolgáltatónál.

Ha egy nagy, szokatlan késleltetés tüske figyelhető meg a komló belül "msn.net", kérjük, forduljon MS támogatás további vizsgálatok.

## <a name="next-steps"></a>További lépések

További információkért vagy segítségért tekintse meg az alábbi linket:

* [Microsoft támogatási szolgálat](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

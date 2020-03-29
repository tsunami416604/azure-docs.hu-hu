---
title: Az Azure virtuális gép hálózati átviteli csatornájának tesztelése
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan tesztelhet azure-beli virtuálisgép-hálózati átviteli.
services: virtual-network
documentationcenter: na
author: steveesp
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 80e8a5e5de1da2098d895e09b36fb209050743a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60743077"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Sávszélesség/átviteli adatvizsgálat (NTTTCP)

Amikor teszteli a hálózati átviteli teljesítmény az Azure-ban, a legjobb, ha egy olyan eszközt, amely a hálózattesztelésre célozza a hálózatot, és minimálisra csökkenti a teljesítményt befolyásoló egyéb erőforrások használatát. NTTTCP használata ajánlott.

Másolja az eszközt két azonos méretű Azure-gépre. Az egyik virtuális gép SENDER, a másik pedig VEVŐKÉNT működik.

#### <a name="deploying-vms-for-testing"></a>Virtuális gépek telepítése tesztelésre
Ennek a tesztnek a céljaira a két virtuális gépnek ugyanabban a felhőszolgáltatásban vagy ugyanabban a rendelkezésre állási csoportban kell lennie, hogy használhassuk a belső IP-jüket, és kizárhassuk a terheléselosztókat a tesztből. Lehetőség van a VIP-vel való tesztelésre, de ez a fajta tesztelés kívül esik a jelen dokumentum hatókörén.

Jegyezze fel a VEVŐ EGYSÉG IP-címét. Nevezzük ezt az IP-t "a.b.c.r"-nek.

Jegyezze fel a virtuális gép magok számát. Nevezzük ezt\#"num\_core"-nak.

Futtassa az NTTTCP-tesztet 300 másodpercig (vagy 5 percig) a küldő virtuális gépen és fogadó virtuális gépen.

Tipp: A teszt első beállításakor megpróbálhat egy rövidebb tesztidőszakot, hogy hamarabb visszajelzést kapjon. Ha a szerszám a várt módon működik, a legpontosabb eredmények érdekében hosszabbítsa meg a vizsgálati időszakot 300 másodpercre.

> [!NOTE]
> A feladónak **és** a fogadónak **ugyanazt a** vizsgálati időtartam paramétert (-t) kell megadnia.

Egyetlen TCP-adat 10 másodpercig való tesztelése:

Vevő paraméterek: ntttcp -r -t 10 -P 1

Feladó paraméterei: ntttcp -s10.27.33.7 -t 10 -n 1 -P 1

> [!NOTE]
> Az előző minta csak a konfiguráció megerősítésére használható. A vizsgálat érvényes példáit a dokumentum későbbi részében mutatjuk be.

## <a name="testing-vms-running-windows"></a>Windows rendszerű virtuális gépek tesztelése:

#### <a name="get-ntttcp-onto-the-vms"></a>Az NTTTCP beolvasása a virtuális gépekre.

Töltse le a legújabb verziót:<https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Vagy keresse meg, <https://www.bing.com/search?q=ntttcp+download> \< ha költözött: -- kell az első találatot

Fontolja meg az NTTTCP külön\\mappába helyezését, például c: eszközök

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>NTTTCP engedélyezése a Windows tűzfalon keresztül
A RECEIVER-en hozzon létre egy Engedélyezési szabályt a Windows tűzfalon, amely lehetővé teszi az NTTTCP-forgalom megérkezését. A legegyszerűbb, ha a teljes NTTTCP-programot név szerint engedélyezi, nem pedig bizonyos TCP-portok bejövő hívását.

Az ntttcp engedélyezése a Windows tűzfalon keresztül a következőknek megfelelően:

netsh advfirewall firewall add\<\>\\rule program= PATH ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

Ha például az ntttcp.exe fájlt a\\"c: tools" mappába másolta, ez a következő parancs lesz: 

netsh advfirewall firewall add rule\\\\program=c: tools ntttcp.exe name="ntttcp" protocol=any dir=in action=allow enable=yes profile=ANY

#### <a name="running-ntttcp-tests"></a>NTTTCP-tesztek futtatása

NTTTCP indítása a RECEIVER-en **(cmd-ből,** nem PowerShellből futva):

ntttcp -r –m\*\#\_[2 num\*cores], ,a.b.c.r -t 300

Ha a virtuális gép négy maggal és 10.0.0.4-es IP-címmel rendelkezik, az a következőkre néz ki:

ntttcp -r –m\*8, ,10.0.0.4 -t 300


NTTTCP indítása a SENDER-en (**cmd-ből futva,** nem PowerShellből):

ntttcp -s -m\*8, ,10.0.0.4 -t 300 

Várjuk meg az eredményeket.


## <a name="testing-vms-running-linux"></a>Linuxot futtató virtuális gépek tesztelése:

Használja az nttcp-for-linux. Elérhető a<https://github.com/Microsoft/ntttcp-for-linux>

A Linux virtuális gépeken (mind a SENDER, mind a RECEIVER) futtassa ezeket a parancsokat az ntttcp-for-linux előkészítéséhez a virtuális gépeken:

CentOS - Git telepítése:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - Git telepítése:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Gyártmány és telepítés mindkét esetben:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Mint a Windows példában, feltételezzük, hogy a Linux RECEIVER IP-címe 10.0.0.4

Indítsa el az NTTTCP-for-Linux-ot a RECEIVER-en:

``` bash
ntttcp -r -t 300
```

És a SENDER, fuss:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
A teszt hossza alapértelmezés szerint 60 másodperc, ha nincs megadva időparaméter

## <a name="testing-between-vms-running-windows-and-linux"></a>Tesztelés Windows és LINUX rendszerű virtuális gépek között:

Ebben a forgatókönyvben engedélyezni kell a no-sync módban, így a teszt futtatható. Ez a **-N jelző** linuxos és **-ns-jelzővel** történik a Windows rendszerhez.

#### <a name="from-linux-to-windows"></a>Linuxról Windowsra:

Windows \<> fogadója:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Feladó \<Linux>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>Windows-ról Linuxra:

Linux \<> fogadó:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Feladó \<Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Felhőszolgáltatás-példányok tesztelése:
A következő szakaszt hozzá kell adnia a ServiceDefinition.csdef
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>További lépések
* Az eredményektől függően előfordulhat, hogy van hely [a hálózati átviteli gépek optimalizálása](virtual-network-optimize-network-bandwidth.md) a forgatókönyvhöz.
* További információ [arról, hogy a virtuális gépek mennyi sávszélességet osztanak ki](virtual-machine-network-throughput.md)
* További információ az [Azure Virtual Network gyakori kérdéseivel (GYIK)](virtual-networks-faq.md)

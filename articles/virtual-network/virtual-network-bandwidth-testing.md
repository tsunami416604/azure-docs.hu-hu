---
title: "Tesztelés Azure virtuális hálózat átbocsátóképességét |} Microsoft Docs"
description: "Megtudhatja, hogyan tesztelheti az Azure virtuális gép hálózati teljesítményt."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 8afb0075eba0093aba8841db1731fb64afb7f6a5
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="bandwidththroughput-testing-ntttcp"></a>Sávszélesség/átvitel (NTTTCP) tesztelése

Hálózat teljesítménye szempontjából tesztelése az Azure-ban, célszerű olyan eszköz, amely a tesztelési hálózati célozza, és minimálisra csökkenti a teljesítményt befolyásoló egyéb erőforrások használatára. NTTTCP ajánlott.

Másolja az eszköz két Azure virtuális gépek azonos méretűnek. Egy virtuális gép úgy működik, mint a KÜLDŐ és a többi fogadó.

#### <a name="deploying-vms-for-testing"></a>Virtuális gépek telepítése teszteléshez
Ez a vizsgálat céljából a két virtuális gépek ugyanazt a felhőalapú szolgáltatást vagy a rendelkezésre állási csoportban kell lennie, hogy azt a belső IP-címet használja, és zárja ki azokat a terheléselosztókat a vizsgálat a. Lehetséges tesztelése a virtuális IP-címre, de az ilyen tesztelés van ez a dokumentum nem terjed.
 
Jegyezze fel a címzett IP-címe. Most hívható meg, hogy IP "a.b.c.r"

Jegyezze fel a magok száma a virtuális Gépen. Most hívása a "\#num\_magok"
 
A NTTTCP teszt futtatása 300 másodperc (azaz 5 perc) a virtuális gép küldő és fogadó virtuális gép.

Tipp: Ez a teszt az első alkalommal történő beállításakor próbálja meg egy rövidebb tesztelési időszakra visszajelzés hamarabb eléréséhez. Miután az eszköz a várt módon működik, terjessze ki a tesztelési időszakra 300 másodperc, a legpontosabb eredmény elérése érdekében.

> [!NOTE]
> A küldő **és** adjon meg fogadó **azonos** időtartam paraméter tesztelése (-t).

Ennek teszteléséhez egyetlen TCP-folyam 10 másodperc:

Fogadó paraméterek: ntttcp - r -t 10 - P 1

Küldő paraméterek: ntttcp-s10.27.33.7 -t 10 - n 1 -P 1

> [!NOTE]
> Az előző példa csak használatával ellenőrizze a konfigurációját. Érvényes példák a vizsgálat a dokumentum későbbi szakaszában tartoznak.

## <a name="testing-vms-running-windows"></a>WINDOWS rendszerű virtuális gépek ellenőrzési:

#### <a name="get-ntttcp-onto-the-vms"></a>A virtuális gépek alakzatot NTTTCP beolvasása.

A legújabb verzió letöltéséhez: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Vagy keresse meg, ha át: <https://www.bing.com/search?q=ntttcp+download> \< --először találati kell lennie

Vegye figyelembe a külön mappába, például a c: NTTTCP üzembe\\eszközök

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>A Windows tűzfalon keresztül NTTTCP engedélyezése
A fogadó hozzon létre egy olyan engedélyezési szabály engedélyezi a érkezésére NTTTCP forgalmat a Windows tűzfalon. A legegyszerűbb engedélyezése a teljes NTTTCP program neve, ahelyett, hogy az adott TCP-portok bejövő.

Ehhez hasonló a Windows tűzfalon ntttcp engedélyezése:

netsh advfirewall tűzfal program szabály hozzáadása =\<ELÉRÉSI\>\\ntttcp.exe neve = "ntttcp" protokoll bármely dir = művelet = = enable engedélyezése = yes profil = ANY

Például, ha a ntttcp.exe másolta a "c:\\eszközök" mappa, a parancs lenne: 

netsh advfirewall tűzfal hozzáadása szabály program = c:\\eszközök\\ntttcp.exe neve = "ntttcp" protokoll bármely dir = művelet = = enable engedélyezése = yes profil = bármely

#### <a name="running-ntttcp-tests"></a>Tesztek futtatása NTTTCP

Indítsa el a fogadó NTTTCP (**CMD-ről futtatva**, nem a PowerShell):

ntttcp - r-m [2\*\#num\_magok],\*, a.b.c.r -t 300

Ha a virtuális gép négy maggal és 10.0.0.4 IP-címét, azt néznek ki:

ntttcp - r – m 8,\*, 10.0.0.4 -t 300


Indítsa el a KÜLDŐ NTTTCP (**CMD-ről futtatva**, nem a PowerShell):

ntttcp -s – m 8,\*, 10.0.0.4 -t 300 

Várjon, amíg az eredményeket.


## <a name="testing-vms-running-linux"></a>Linuxos virtuális gépek ellenőrzési:

Nttcp a linux használja. Az elérhető <https://github.com/Microsoft/ntttcp-for-linux>

A Linux virtuális gépeken (KÜLDŐ és fogadó), futtassa az alábbi parancsokat ntttcp-az-linux a virtuális gépeken előkészítése:

CentOS - telepítés Git:
``` bash
  yum install gcc -y  
  yum install git -y
```
Ubuntu - telepítés Git:
``` bash
 apt-get -y install build-essential  
 apt-get -y install git
```
Ellenőrizze, és mindkét telepítéséhez:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

Hasonlóan a Windows a példában feltételezzük, hogy a Linux fogadó IP-cím 10.0.0.4

A fogadó NTTTCP a Linux Kezdés:

``` bash
ntttcp -r -t 300
```

És a feladó futtassa:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Teszt hossza az alapértelmezett érték 60 másodperc, ha a paraméter nem kap

## <a name="testing-between-vms-running-windows-and-linux"></a>Az ellenőrzési Windows és LINUX rendszerű virtuális gépek között:

Ez a forgatókönyv nem kell engedélyezni a a nem szinkron módban, a vizsgálat futtatható számára. Ehhez használja a **-N jelző** Linux, és **-ns jelző** Windows.

#### <a name="from-linux-to-windows"></a>A Linux a Windowsba:

Fogadó <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Küldő <Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>A Windows, Linux –:

Fogadó <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Küldő <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```

## <a name="next-steps"></a>Következő lépések
* Attól függően, hogy az eredmények lehet hely [optimalizálhatja a hálózati átviteli gépek](virtual-network-optimize-network-bandwidth.md) a forgatókönyvéhez.
* Ismerje meg [Azure Virtual Network gyakori kérdések (GYIK)](virtual-networks-faq.md)

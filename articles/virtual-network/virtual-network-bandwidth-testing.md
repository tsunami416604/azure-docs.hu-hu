---
title: Tesztelési Azure-beli Virtuálisgép-hálózati átviteli sebesség |} A Microsoft Docs
description: Ismerje meg, hogyan tesztelheti az Azure virtuális gépek hálózati átviteli sebessége.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/21/2017
ms.author: steveesp
ms.openlocfilehash: 45efaebb9539c4c0e2542966df6ab890b64d12ee
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50023822"
---
# <a name="bandwidththroughput-testing-ntttcp"></a>A sávszélesség/átviteli sebesség tesztelése (NTTTCP)

Hálózat teljesítménye szempontjából tesztelése az Azure-ban, esetén érdemes választani, olyan eszköz, amely a tesztelési hálózati célozza, és minimálisra csökkenti más erőforrások, amelyek hatással lehetnek a teljesítmény használatát. NTTTCP ajánlott.

Másolja az eszköz két Azure virtuális gépek azonos méretű. Egy virtuális gép KÜLDŐ és a másik fogadóként működik.

#### <a name="deploying-vms-for-testing"></a>Tesztelési virtuális gépek üzembe helyezéséhez
Ez a vizsgálat céljából a két virtuális gép ugyanazon a Felhőszolgáltatáson vagy a rendelkezésre állási csoportban kell lennie, hogy azt használja a belső IP-címek és Terheléselosztók kizárása a teszt. Lehetséges a virtuális IP-CÍMEK tesztelése, de az ilyen tesztelést nem ez a dokumentum foglalkozik.
 
Jegyezze fel a fogadó IP-cím. Adjuk a "a.b.c.r" IP-Címre

Jegyezze fel a magok számát a virtuális gépen. Adjuk a "\#num\_magok"  
Futtassa a 300 másodperc (azaz 5 perc) NTTTCP tesztet a virtuális gép küldő és fogadó virtuális gép.

Tipp: A teszt az első alkalommal beállításakor próbálja meg egy rövidebb tesztelési időszakra hamarabb kaphat a visszajelzést. Az eszköz a várt módon működik, ha a legpontosabb eredmények 300 másodperc terjed ki a tesztelési időszakra.

> [!NOTE]
> A küldő **és** adjon meg fogadó **azonos** tesztelése időtartama paraméter (-t).

Egyetlen TCP-folyamhoz tesztelése 10 másodpercig:

Fogadó paraméterek: - r -t ntttcp 10 - P 1

Küldő paraméterek: ntttcp-s10.27.33.7 -t 10 - n 1 -P 1

> [!NOTE]
> Ellenőrizze a konfigurációt az előző példa csak használható. A jelen dokumentum érvényes példái a tesztelési terjed ki.

## <a name="testing-vms-running-windows"></a>Az ellenőrzési WINDOWS rendszerű virtuális gépek:

#### <a name="get-ntttcp-onto-the-vms"></a>A virtuális gépek alakzatot NTTTCP beolvasása.

A legújabb verzió letöltéséhez: <https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769>

Vagy keresse meg, ha azok más: <https://www.bing.com/search?q=ntttcp+download> \< – először találati kell lennie

Érdemes lehet külön mappába, például: c: NTTTCP elhelyezése\\eszközök

#### <a name="allow-ntttcp-through-the-windows-firewall"></a>A Windows tűzfalon keresztül NTTTCP engedélyezése
A fogadó hozzon létre egy olyan engedélyezési szabály a NTTTCP forgalom érkezik, a Windows tűzfalon. A legegyszerűbb lehetővé teszik a teljes NTTTCP program neve szerint ahelyett, hogy adott TCP-portok bejövő.

A Windows tűzfalon, ehhez hasonló ntttcp engedélyezése:

netsh advfirewall firewall szabály program hozzáadása =\<elérési ÚTJA\>\\ntttcp.exe neve "ntttcp" protocol = bármely dir = működés = = engedélyezése engedélyezése = yes profile = ANY

Például, ha a ntttcp.exe kimásolt a "c:\\eszközök" mappába, ez lenne a parancsot: 

netsh advfirewall firewall szabály program hozzáadása = c:\\eszközök\\ntttcp.exe neve "ntttcp" protocol = bármely dir = működés = = engedélyezése engedélyezése = yes profile = ANY

#### <a name="running-ntttcp-tests"></a>Futó NTTTCP tesztek

Indítsa el a fogadó NTTTCP (**futtassa a CMD**, nem a PowerShell):

az r - ntttcp – m [2\*\#num\_magok],\*, a.b.c.r -t 300

Ha a virtuális gépen, négy magot és a egy IP-címe 10.0.0.4, azt kellene kinéznie:

m – 8., ntttcp - r\*, 10.0.0.4 -t 300


Indítsa el a KÜLDŐ NTTTCP (**futtassa a CMD**, nem a PowerShell):

ntttcp -s – m 8\*, 10.0.0.4 -t 300 

Várjon, amíg az eredmények.


## <a name="testing-vms-running-linux"></a>Az ellenőrzési linuxos virtuális gépek:

Nttcp a linux használja. Érhető el <https://github.com/Microsoft/ntttcp-for-linux>

A Linux rendszerű virtuális gépeken (KÜLDŐ és fogadó), futtassa a következő parancsokat ntttcp-az-linux rendszer a virtuális gépek előkészítése:

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
Győződjön meg arról, és telepítse a is:
``` bash
 git clone https://github.com/Microsoft/ntttcp-for-linux
 cd ntttcp-for-linux/src
 make && make install
```

A Windows példában feltételezzük, a Linux-fogadó IP-címe 10.0.0.4

A fogadó NTTTCP a Linux Kezdés:

``` bash
ntttcp -r -t 300
```

És a KÜLDŐ futtassa:

``` bash
ntttcp -s10.0.0.4 -t 300
```
 
Teszt hossza alapértelmezett értéke 60 másodperc, ha nincs ideje paraméter van megadva.

## <a name="testing-between-vms-running-windows-and-linux"></a>Az ellenőrzési Windows és LINUX rendszerű virtuális gépek között:

A forgatókönyvek azt kell engedélyezi a nem szinkron módban, így a teszt futtatásával. Ennek segítségével történik a **-N jelző** Linux rendszeren és **-ns jelző** for Windows.

#### <a name="from-linux-to-windows"></a>A Windows, Linux:

Fogadó <Windows>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Windows server IP>
```

Küldő <Linux> :

``` bash
ntttcp -s -m <2 x nr cores>,*,<Windows server IP> -N -t 300
```

#### <a name="from-windows-to-linux"></a>A Windows, Linux:

Fogadó <Linux>:

``` bash
ntttcp -r -m <2 x nr cores>,*,<Linux server IP>
```

Küldő <Windows>:

``` bash
ntttcp -s -m <2 x nr cores>,*,<Linux  server IP> -ns -t 300
```
## <a name="testing-cloud-service-instances"></a>Az ellenőrzési Cloud Service-példány:
A ServiceDefinition.csdef, adja hozzá az alábbi szakaszban kell
```xml
<Endpoints>
  <InternalEndpoint name="Endpoint3" protocol="any" />
</Endpoints> 
```

## <a name="next-steps"></a>További lépések
* Eredmények mintától függően előfordulhat, hogy lehet hely [optimalizálása a hálózati átviteli sebesség gépek](virtual-network-optimize-network-bandwidth.md) a forgatókönyvhöz.
* Megtudhatja, hogyan lehet [sávszélességet a virtuális gépek](virtual-machine-network-throughput.md)
* További tudnivalók a [Azure Virtual Network – gyakori kérdések (GYIK)](virtual-networks-faq.md)

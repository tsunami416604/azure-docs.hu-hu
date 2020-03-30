---
title: Csomagellenőrzés az Azure Network Watcher rel | Microsoft dokumentumok
description: Ez a cikk azt ismerteti, hogy a Network Watcher használatával hogyan végezhet mélyreható csomagellenőrzést a virtuális gépekről
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7d32043ca73e9cf810b3eab5e65cb4b42b599d18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152924"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Csomagellenőrzés az Azure Network Watcher rel

A Network Watcher csomagrögzítési szolgáltatásával rögzítheti és kezelheti az Azure virtuális gépeken lévő rögzítési munkameneteket a portálról, a PowerShellből, a CLI-ből és programozott módon az SDK és a REST API-n keresztül. A csomagrögzítés lehetővé teszi a csomagszintű adatokat igénylő forgatókönyvek kezelését azáltal, hogy az információkat könnyen használható formátumban adja meg. Kihasználva a szabadon elérhető eszközöket az adatok vizsgálatához, megvizsgálhatja a virtuális gépekre küldött és onnan érkező kommunikációt, és betekintést nyerhet a hálózati forgalomba. A csomagrögzítési adatok néhány példája: a hálózati vagy alkalmazásproblémák kivizsgálása, a hálózati visszaélések és a behatolási kísérletek észlelése, vagy a jogszabályi megfelelőség fenntartása. Ebben a cikkben bemutatjuk, hogyan lehet megnyitni a Network Watcher által biztosított csomagrögzítő fájlt egy népszerű nyílt forráskódú eszközzel. Példákat is bemutatunk, amelyek bemutatják a kapcsolat késésének kiszámítását, a rendellenes forgalom azonosítását és a hálózati statisztikák vizsgálatát.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk néhány előre konfigurált forgatókönyvön megy keresztül egy korábban futtatott csomagrögzítésen. Ezek a forgatókönyvek olyan képességeket mutatnak be, amelyek a csomagrögzítés áttekintésével érhetők el. Ez a forgatókönyv a [WireShark](https://www.wireshark.org/) segítségével vizsgálja meg a csomagrögzítést.

Ebben a forgatókönyvben azt feltételezi, hogy már futtatott egy csomagrögzítést egy virtuális gépen. A csomagrögzítés létrehozásáról a [Csomagrögzítés ek kezelése a portállal](network-watcher-packet-capture-manage-portal.md) vagy a REST alkalmazással című webhelyen a [Csomagrögzítések kezelése REST API-val](network-watcher-packet-capture-manage-rest.md)című webhelyen című ellátogat.

## <a name="scenario"></a>Forgatókönyv

Ebben az esetben:

* Csomagrögzítés áttekintése

## <a name="calculate-network-latency"></a>Hálózati késés számítása

Ebben a forgatókönyvben bemutatjuk, hogyan tekintheti meg a TCP-beszélgetés kezdeti oda-vissza (RTT) beszélgetését, amely két végpont között történik.

TCP-kapcsolat létrehozásakor a kapcsolatban küldött első három csomag a hármas kézfogásnak nevezett mintát követi. A kézfogásban küldött első két csomag, az ügyfél kezdeti kérése és a kiszolgáló válasza alapján kiszámíthatjuk a késést, amikor ez a kapcsolat létrejött. Ezt a késést oda-vissza útnak (RTT) nevezik. A TCP protokollról és a háromutas kézfogásról a következő forrásról talál további információt. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>1. lépés

WireShark indítása

### <a name="step-2"></a>2. lépés

Töltse be a **.cap** fájlt a csomagrögzítésből. Ez a fájl megtalálható a blob ban mentette a mi helyileg a virtuális gépen, attól függően, hogyan konfigurálta azt.

### <a name="step-3"></a>3. lépés

A TCP-beszélgetések kezdeti oda-vissza utazásának (RTT) megtekintéséhez csak a TCP kézfogásban részt vevő első két csomagot fogjuk megtekinteni. A háromutas kézfogás első két csomagját fogjuk használni, amelyek a [SYN], [SYN, ACK] csomagok. A TCP-fejlécben beállított jelzőkről vannak elnevezve. Ebben az esetben a kézfogás utolsó csomagja, az [ACK] csomag nem lesz használva. A [SYN] csomagot az ügyfél küldi el. Miután megkapta a kiszolgáló, elküldi az [ACK] csomagot, hogy nyugtázza a SYN fogadását az ügyféltől. Kihasználva azt a tényt, hogy a szerver válasza nagyon kevés többletterhelést igényel, kiszámítjuk az RTT-t, kivonva az [SYN, ACK] csomag ügyfél általi fogadásának idejét, mire az ügyfél a [SYN] csomagot elküldte.

A WireShark használatával ezt az értéket kiszámítjuk számunkra.

Ahhoz, hogy könnyebben megtekinthesse az első két csomagot a TCP háromutas kézfogásban, a WireShark által biztosított szűrési képességet használjuk.

A szűrő WireShark alkalmazásban való alkalmazásához bontsa ki a rögzítésben lévő [SYN] csomag "Átviteli vezérlési protokoll" szegmensét, és vizsgálja meg a TCP fejlécben beállított jelzőket.

Mivel az összes [SYN] és [SYN, ACK] csomagra szeretnénk szűrni, a zászlók alatt megerősítik, hogy a Syn bit 1-re van állítva, majd kattintson a jobb gombbal a Syn bit -> Apply as Filter -> Selected.

![7. ábra][7]

### <a name="step-4"></a>4. lépés

Most, hogy szűrte az ablakot, hogy csak a [SYN] bitbeállítással rendelkező csomagokat láthassa, könnyedén kiválaszthatja az okat, hogy megtekinthesse a kezdeti RTT-t. Egy egyszerű módja annak, hogy megtekinthesse az RTT wireshark egyszerűen kattintson a legördülő jelölt "SEQ / ACK" elemzés. Ekkor megjelenik az RTT. Ebben az esetben az RTT 0,0022114 másodperc, azaz 2,211 ms volt.

![8. ábra][8]

## <a name="unwanted-protocols"></a>Nem kívánt protokollok

Az Azure-ban üzembe helyezett virtuálisgép-példányon számos alkalmazás futtatható. Sok ilyen alkalmazások kommunikálni a hálózaton keresztül, talán az Ön kifejezett engedélye nélkül. A hálózati kommunikáció tárolására a csomagrögzítés segítségével megvizsgálhatjuk, hogyan beszélnek az alkalmazások a hálózaton, és megkereshetjük a problémákat.

Ebben a példában áttekintjük a korábbi futtatott csomagrögzítést a nem kívánt protokollok miatt, amelyek a számítógépen futó alkalmazástól származó jogosulatlan kommunikációt jelezhetik.

### <a name="step-1"></a>1. lépés

Ugyanannak a rögzítésnek az előző esetben történő használata a **Statisztikai** > **protokollhierarchia elemre**

![protokollhierarchia menü][2]

Megjelenik a protokollhierarchia ablaka. Ez a nézet a rögzítési munkamenet során használt összes protokoll, valamint a protokollok használatával továbbított és fogadott csomagok számát tartalmazza. Ez a nézet hasznos lehet a nem kívánt hálózati forgalom megkereséséhez a virtuális gépeken vagy a hálózaton.

![protokollhierarchia megnyitva][3]

Mint látható a következő képernyő elfog, nem volt forgalom a BitTorrent protokoll, amelyet a peer-to-peer fájlmegosztás. Rendszergazdaként nem számít a BitTorrent forgalomra ezen a virtuális gépen. Most, hogy tisztában van ezzel a forgalommal, eltávolíthatja a virtuális gépen telepített társ-egyenrangú szoftvert, vagy blokkolhatja a forgalmat a hálózati biztonsági csoportok vagy a tűzfal használatával. Emellett dönthet úgy, hogy a csomagrögzítéseket ütemezés szerint futtatja, így rendszeresen áttekintheti a protokoll használatát a virtuális gépeken. Ha például automatizálhatja a hálózati feladatokat az azure-ban, látogasson el [a Hálózati erőforrások figyelése az azure-automatizálással](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>A legnépszerűbb úti célok és portok keresése

A forgalom típusainak, a végpontok és a portok közlésének megértése fontos a hálózaton lévő alkalmazások és erőforrások figyelésekor és hibaelhárításakor. A csomagrögzítő fájl felülről történő felhasználásával gyorsan megtudhatjuk, hogy a virtuális gépünk legnépszerűbb úti céljaival kommunikál, és a portokat is használjuk.

### <a name="step-1"></a>1. lépés

Az előző forgatókönyvben ugyanezzel a rögzítéssel kattintson az > **IPv4-statisztika** > **cél- és portjainak statisztikája elemre.** **Statistics**

![csomagrögzítési ablak][4]

### <a name="step-2"></a>2. lépés

Ahogy átnézzük az eredményeket, egy vonal kiemelkedik, több kapcsolat volt a 111-es porton. A leggyakrabban használt port a 3389 volt, amely távoli asztal, a többi pedig az RPC dinamikus portjai.

Bár ez a forgalom nem jelent semmit, ez egy port, amelyet sok kapcsolathoz használtak, és a rendszergazda számára ismeretlen.

![5. ábra][5]

### <a name="step-3"></a>3. lépés

Most, hogy meghatároztunk egy nem megfelelő portot, kiszűrhetjük a rögzítésünket a port alapján.

A szűrő ebben a forgatókönyvben a következő lenne:

```
tcp.port == 111
```

A szűrő szövegét felülről adjuk meg a szűrő szövegdobozába, és beírjuk az Entert.

![6. ábra][6]

Az eredményekből láthatjuk, hogy az összes forgalom egy helyi virtuális gépről érkezik ugyanazon az alhálózaton. Ha még mindig nem értjük, hogy miért történik ez a forgalom, tovább vizsgálhatjuk a csomagokat, hogy megállapítsuk, miért kezdeményezi ezeket a hívásokat a 111-es porton. Ezzel az információval megtesszük a megfelelő lépéseket.

## <a name="next-steps"></a>További lépések

Ismerje meg a Network Watcher egyéb diagnosztikai funkcióit az [Azure hálózatfigyelés áttekintésének ellátogatásával](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png














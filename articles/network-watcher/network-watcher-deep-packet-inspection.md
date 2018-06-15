---
title: Az Azure hálózati figyelőt Csomagvizsgálat |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan mély Csomagvizsgálat begyűjti a virtuális gép hálózati figyelőt segítségével
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 1ad6ca4abe73336ce9ce3539fdaf2a9d7dd23fa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23863971"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Az Azure hálózati figyelőt Csomagvizsgálat

Csomag rögzítése az szolgáltatásával hálózati figyelőt, kezdeményezzen, és az Azure virtuális gépeken a portálról, PowerShell parancssori felület, és az SDK-t és a REST API-n keresztül szoftveresen rögzítésekre munkameneteket kezelhessen. Csomagrögzítéssel lehetővé teszi az információk könnyen használható formátumban szintű csomagadatok igénylő forgatókönyvek kezeléséhez. Kihasználva ingyenesen elérhető eszközök az adatok vizsgálata, és a virtuális gépek küldött kommunikációs vizsgálja meg, és betekintést nyerhet a hálózati forgalom. Néhány példa-használati csomag rögzítési adatok többek között: hálózati vagy alkalmazás problémákat vizsgálja, hálózati visszaélés és a behatolás kísérletek észlelése vagy előírásoknak való megfelelés karbantartása. Ebben a cikkben megmutatjuk, hogyan kattintva megnyithatja a csomag rögzítési hálózati figyelő által biztosított népszerű nyílt forráskódú eszköz használatával. Példa bemutatja, hogyan kapcsolat késleltetése kiszámításához, azonosítani a rendellenes forgalom, és vizsgálja meg a hálózati statisztikákat is lesz elérhető.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a korábban futtatott egy csomagrögzítéssel végighalad az néhány előre konfigurált forgatókönyvet. Ezek a forgatókönyvek a csomagrögzítéssel megtekintésével elérhető képességek mutatják be. Ez a forgatókönyv használ [WireShark](https://www.wireshark.org/) a csomagrögzítéssel vizsgálata.

Ez a forgatókönyv azt feltételezi, hogy a virtuális gépen már futott egy csomagrögzítéssel. Megtudhatja, hogyan hozzon létre egy csomagot rögzítési látogasson el a [kezelése csomagrögzítéseket a portál](network-watcher-packet-capture-manage-portal.md) vagy látogasson el a többi [kezelése Csomagrögzítéseket REST API](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben azt:

* A csomagrögzítéssel áttekintése

## <a name="calculate-network-latency"></a>Kiszámítja a hálózati késés

Ebben a forgatókönyvben megmutatjuk, hogyan kell megtekinteni a Transmission Control Protocol (TCP) témakör két végpontok közötti előforduló kezdeti oda-vissza időt (RTT).

Ha a TCP-kapcsolat létrejött, az első három az kapcsolatban küldött csomagok járjon el egy mintát, más néven a háromutas kézfogás. Az első két a kézfogás, az eredeti kérést az ügyfél és a kiszolgáló válaszára küldött csomagok megvizsgálásával azt is kiszámíthatja a késés, ha létrejött a kapcsolat. Ez a késés hivatkozik, az oda-vissza időt (RTT). A TCP protokoll és a három kézfogás további információt talál a következő erőforrás. https://support.microsoft.com/en-us/help/172983/Explanation-of-the-three-Way-Handshake-via-TCP-IP

### <a name="step-1"></a>1. lépés

Indítsa el a WireShark

### <a name="step-2"></a>2. lépés

Betöltési a **.cap** a csomagrögzítéssel a fájlt. Ez a fájl megtalálható a blob mentik el a helyileg a virtuális gépen, attól függően, hogyan konfigurálta.

### <a name="step-3"></a>3. lépés

A kezdeti oda-vissza időt (RTT) megtekintéséhez TCP témák, azt fogja csak megtekintik a TCP-kézfogás részt vevő első két csomagok. Azt fogja használni a három kézfogás, az első két csomagok amelyek [SZIN], [SZIN, Nyugtázási] csomagok. A TCP-fejlécében beállítású néven szerepelnek. A kézfogás [ACK]-csomagját, az utolsó csomag nem használható ebben a forgatókönyvben. A [SZIN] csomagot az ügyfél által küldött. Miután érkezik a kiszolgáló küldi [Nyugtázási] az ügyféltől a SZIN befogadására jóváhagyva. Kihasználva a tényt, hogy a kiszolgáló válaszát igényel csekély terhelést, azt kiszámítása a RTT idő kivonja a [SZIN, Nyugtázási] csomag érkezett az ügyfél által a [SZIN] csomagot az ügyfél által küldött.

WireShark használatával Ez az érték kiszámítása nekünk.

Könnyebb megtekintéséhez az első két csomagokat a TCP háromutas kézfogás, WireShark által biztosított szűrési képesség felhasznál azt.

A szűrni WireShark, bontsa ki a "Transmission Control Protocol" szegmens egy [SZIN] csomag, a rögzítés, és vizsgálja meg a beállítású, az TCP-fejlécben.

Mivel azt szeretnék szűrhet minden [SZIN] és [SZIN, Nyugtázási] csomagok jelzők a cofirm, hogy a szinkronizálás a mi bit értéke 1, majd kattintson a jobb gombbal a szin bites, a szűrő -> kijelölt -> alkalmaz.

![7. ábra][7]

### <a name="step-4"></a>4. lépés

Most, hogy a ablakot, csak a [SZIN] bittel csomagok szűrését, egyszerűen kiválaszthatja beszélgetések érdekli a kezdeti RTT megtekintéséhez. Egyszerűen tekintse meg a RTT WireShark egyszerűen kattintson a legördülő listában megjelölt "SEQ/Nyugtázási" elemzés. Ekkor megjelenik a RTT jelenik meg. Ebben az esetben a RTT 0.0022114 másodperc, vagy 2.211 ms volt.

![8. ábra][8]

## <a name="unwanted-protocols"></a>Nem kívánt protokollok

Akkor is számos olyan alkalmazás, az Azure-ban telepített virtuálisgép-példányon futnak. Ezek az alkalmazások számos kommunikálni a hálózaton keresztül, akár Ön engedélye nélkül. Csomagrögzítéssel tárolásához a hálózati kommunikációt használ, azt is vizsgálja ki hogyan alkalmazás vannak a hálózaton van szó, és keresse meg az esetleges problémákat.

Ebben a példában egy korábbi tanulmányozzák csomagrögzítéssel nemkívánatos protokollok, amelyek nem hitelesített kommunikáció a számítógépen futó valamelyik alkalmazás futott.

### <a name="step-1"></a>1. lépés

Kattintson az előző példában a azonos rögzítési használatával **statisztika** > **protokoll hierarchia**

![protokoll hierarchia menü][2]

A protokoll hierarchia ablak. Ez a nézet a rögzítési munkamenet és a protokollal fogadott és küldött csomagok száma során használt összes protokollok listáját jeleníti meg. Ez a nézet a nem kívánt hálózati forgalmat a virtuális gépek vagy a hálózaton kereséshez hasznos lehet.

![protokoll hierarchia megnyitása][3]

Mint az alábbi képernyőfelvételen látható, hiba történt a forgalmat a társ-társ fájlmegosztás használt BitTorrent protokoll segítségével. Rendszergazdaként nem várható, hogy BitTorrent forgalom az adott virtuális gépeken. Most már tisztában legyen a forgalom, akkor eltávolíthatja a társ-társ szoftver, a virtuális gépen telepített, vagy az adatforgalmat, a hálózati biztonsági csoportok vagy egy tűzfal. Emellett előfordulhat, hogy en csomag rögzítésekre fussanak ütemezés szerint, ezért tekintse át a protokoll használatát a virtuális gépeken rendszeresen. Az azure-ban a hálózati feladatok automatizálása a például a Microsoft [az azure Automation szolgáltatásban, a hálózati erőforrások figyelése](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Felső a célok és portok keresése

A típusú forgalom, a végpontok és a portok keresztül érkezett megértése, egy fontos figyelés vagy hibaelhárítási alkalmazások és a hálózati erőforrásokhoz. A fenti csomag rögzítési fájlt használ, azt is gyorsan ismerje meg a virtuális gép kommunikál a felső célhelyekre és a portok kihasználtságának.

### <a name="step-1"></a>1. lépés

Kattintson az előző példában a azonos rögzítési használatával **statisztika** > **IPv4 statisztika** > **a célok és portok**

![csomag rögzítési ablak][4]

### <a name="step-2"></a>2. lépés

Reméljük, az eredményeket egy sor feladat keresztül, mivel nincsenek 111 porton több kapcsolatot. A leggyakrabban használt port lett 3389-es, vagyis a távoli asztal, és a fennmaradó RPC dinamikus portok.

Ez az adatforgalom azt jelentheti, semmi sem, de napjainkban a portot, amelyet a sok kapcsolattal lett megadva, és a rendszergazda számára ismeretlen.

![5. ábra][5]

### <a name="step-3"></a>3. lépés

Most, hogy azt szűrheti, hogy a port alapján a rögzítési hely port kívüli azt észlelte.

A szűrő ebben a forgatókönyvben a következő lesz:

```
tcp.port == 111
```

Azt adja meg a fenti szűrő szöveget, a szűrő szövegmezőben, majd nyomja le adjon meg.

![6. ábra][6]

A találatokban láthatja az összes forgalom érkezik a helyi virtuális gép ugyanazon az alhálózaton. Ha még nem tudjuk miért jelentkezik a forgalmat, azt további vizsgálhatja meg a csomagok és meghatározni, miért azt, hogy így hívásokat a 111 porton. Az információ azt is elvégezheti a szükséges műveleteket.

## <a name="next-steps"></a>Következő lépések

Látogasson el a hálózati figyelőt más diagnosztikai szolgáltatásainak bemutatása [Azure hálózatfigyelési áttekintése](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png














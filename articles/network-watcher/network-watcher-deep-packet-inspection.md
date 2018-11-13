---
title: Az Azure Network Watcher csomagvizsgálatot |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan használhatja a Network Watcher mély Csomagvizsgálat gyűjtött egy virtuális gépről
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
ms.openlocfilehash: 7af14143e8ce4924c17a41c6bb1ff33954f4b583
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568726"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>A csomagok vizsgálata az Azure Network Watcher

Az a Network Watcher packet capture funkciójának használatával, kezdeményezni, és az Azure virtuális gépeken a portálról, PowerShell, CLI-t, és az SDK-t és a REST API-val programozott módon való rögzítését munkameneteket kezelhessen. A csomagrögzítés teszi lehetővé azáltal, hogy az adatokat, azonnal használható formátumban szintű csomagadatok igénylő forgatókönyvek. Vizsgálhatja meg az adatok szabadon elérhető eszközök használatával, vizsgálja meg a kommunikációt a virtuális gépek küldött, és betekintést nyerhet a hálózati forgalmat. Rögzítési csomagadatok néhány példa használati módjai többek között: hálózati vagy alkalmazás kapcsolatos problémák kivizsgálása, hálózati való visszaélés és a behatolás kísérletek észlelésekor vagy jogszabályoknak való megfelelőség fenntartásával. Ebben a cikkben bemutatjuk, hogyan biztosítják a Network Watcher packet rögzítési fájlt megnyitni egy népszerű, nyílt forráskódú eszköz segítségével. Egy kapcsolat késési kiszámításához, rendellenes forgalom azonosítására és vizsgálja meg a hálózati statisztika példákat is biztosít.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk a korábban futtatott csomagrögzítés végighalad néhány előre konfigurált forgatókönyvet. Ezek a forgatókönyvek bemutatják, csomagrögzítés áttekintésével elérhető funkciókat. Ebben a forgatókönyvben használt [WireShark](https://www.wireshark.org/) a csomagrögzítés vizsgálhatja meg.

Ebben a forgatókönyvben azt feltételezi, hogy a virtuális gépen már próbálkozott csomagrögzítés. Megtudhatja, hogyan hozhat létre packet capture meglátogatása [kezelés csomagrögzítéseket gyűjtsenek a portál](network-watcher-packet-capture-manage-portal.md) vagy funkcionáló REST-tel [Csomagrögzítéseket gyűjtsenek kezelése REST API-val](network-watcher-packet-capture-manage-rest.md).

## <a name="scenario"></a>Forgatókönyv

Ebben a forgatókönyvben azt:

* Csomagrögzítés áttekintése

## <a name="calculate-network-latency"></a>Hálózati késés kiszámítása

Ebben a forgatókönyvben bemutatjuk, hogyan a Transmission Control Protocol (TCP) témakör két végpontja között előforduló kezdeti Adatváltási időt (RTT) megtekintéséhez.

Ha a TCP-kapcsolat létrejött, az első három, a kapcsolat küldött csomagok gyakran Keresőmezőnek nevezzük a háromutas kézfogás mintát követi. Az első két csomagok küldi el a kézfogás, az eredeti kérést az ügyfél és a kiszolgáló válaszára megvizsgálásával hogy azt is megmutassa a késést, ha létrejött a kapcsolat. Ez a késés a neve, a Adatváltási ideje (Ezredmásodperc). További információ a TCP protokoll és a háromutas kézfogás tekintse meg a következő erőforrás. https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip

### <a name="step-1"></a>1. lépés

Indítsa el a WireShark

### <a name="step-2"></a>2. lépés

Betöltés a **.cap** a csomagrögzítés fájlt. Ez a fájl megtalálható a blob mentik el a helyileg a virtuális gépen, attól függően, hogyan konfigurálta.

### <a name="step-3"></a>3. lépés

Szeretné megtekinteni a kezdeti Adatváltási időt (RTT) TCP-beszélgetések, azt fogja csak megtekintik a TCP-kézfogás részt vevő első két csomagokat. Hogy fogják használni a háromutas kézfogás meg az első két csomagok, amelyek [SZIN], [SZIN, Nyugtázási] csomagokat. A TCP-fejlécében beállítású néven szerepelnek. A legutóbbi csomagot a kézfogás, a [ACK] csomagot, az ebben a forgatókönyvben nem használható. A [SZIN] csomagot küld a rendszer az ügyfél által. Után érkezik, a kiszolgáló elküldi a [ACK] csomagot a szinkronizálás a mi fogadása az ügyfél jóváhagyva. Kihasználva a tény, hogy a kiszolgáló válasza nagyon kevés többletterhelést igényel-e, kiszámítása a RTT az idő értékének a [SZIN, Nyugtázási] csomag érkezett az ügyfél a következő időpontig: [SZIN] csomagot az ügyfél által küldött.

Használatával a WireShark az érték számítása számunkra.

Hogy könnyebben megtekinthesse az első két csomagokat a TCP háromutas kézfogás, azt a WireShark szűrési képesség felhasznál.

A szűrő WireShark a alkalmazni, bontsa ki a "Transmission Control Protocol" szegmens a rögzítés [SZIN] csomag, és vizsgálja meg a TCP-fejléc beállítása a jelzők.

Mivel tudjuk szeretne szűrjön az összes [SZIN] és [SZIN, Nyugtázási] csomagok területen jelzők cofirm, hogy a szinkronizálás a mi bit értéke 1, majd a jobb gombbal kattintva elérhető a szinkronizálás a mi-bit -> alkalmaz, a szűrő -> kijelölt.

![7. ábra][7]

### <a name="step-4"></a>4. lépés

Most, hogy az ablakban csak az [SZIN] bittel csomagok szűrését, beszélgetések is érdeklik a kezdeti RTT megtekintéséhez egyszerűen kiválaszthatja. Egy egyszerű módja az RTT megtekintése a WireShark egyszerűen kattintson a legördülő listában megjelölt "SEQ/ACK" elemzése. Ekkor megjelenik a RTT jelenik meg. Ebben az esetben a RTT 0.0022114 másodperc vagy 2.211 ms volt.

![8. ábra][8]

## <a name="unwanted-protocols"></a>A nem kívánt protokollok

Az Azure-ban telepített virtuálisgép-példányt futtató számos alkalmazásokat is használhat. Ezek az alkalmazások számos kommunikálni a hálózaton keresztül, akár Ön engedélye nélkül. Csomagrögzítés használatával tárolja a hálózati kommunikációt, hogy segítségével megvizsgálhatja, hogyan alkalmazás beszél a hálózaton, és keresse meg az esetleges problémákat.

Ebben a példában egy korábbi tanulmányozzák csomagrögzítés nemkívánatos, amelyek azt jelzik a gépen futó alkalmazásból való jogosulatlan kommunikációs protokollok futott.

### <a name="step-1"></a>1. lépés

Kattintson az előző forgatókönyvben ugyanazt a rögzítési használatával **statisztika** > **protokoll – hierarchiája**

![protokoll hierarchia menü][2]

A protokoll hierarchia ablak. Ez a nézet a során a rögzítési munkamenet és protokollok használatával a fogadott és küldött csomagok száma használatban lévő protokollok listáját tartalmazza. Ez a nézet lehet hasznos, ha nemkívánatos hálózati forgalmat a virtuális gépek vagy a hálózaton lévő keresése.

![megnyitott protokoll hierarchia][3]

Ahogy az alábbi képernyőfelvételen látható, hiba történt a forgalmat a társ-társ fájlmegosztás használt BitTorrent protokollon keresztül. Rendszergazdaként nem látja a keresett BitTorrent forgalmat az adott virtuális gépeken. Mostantól figyelembe ezt a forgalmat, akkor eltávolíthatja a társ-társ szoftver, amely a virtuális gépen telepítve, vagy letiltja a forgalmat a hálózati biztonsági csoportok vagy tűzfal. Ezenkívül előfordulhat, hogy használatba vétele csomagrögzítés futtatása ütemezés szerint, így megtekintheti a protokoll használatát rendszeresen a virtuális gépeken. Egy példa az Azure-beli hálózati feladatok automatizálása a Microsoft [hálózati erőforrások az azure automation figyelése](network-watcher-monitor-with-azure-automation.md)

## <a name="finding-top-destinations-and-ports"></a>Leggyakoribb célhelyek és portok keresése

A típusú forgalom, a végpontok és a portok keresztül továbbítani megértése fontos egy figyelése és hibaelhárítása, alkalmazásokat és a hálózati erőforrásokhoz. A fenti csomagok rögzítési fájl használatával, azt segítségével gyorsan megismerkedhet a virtuális gépnél kommunikáló leggyakoribb célhelyek és kihasználtságának portokat.

### <a name="step-1"></a>1. lépés

Kattintson az előző forgatókönyvben ugyanazt a rögzítési használatával **statisztika** > **IPv4 statisztika** > **célhelyek és portok**

![csomagok rögzítése ablak][4]

### <a name="step-2"></a>2. lépés

Az eredmények között, egy sor vesz fel, hogy történt több kapcsolat 111 porton. A leggyakrabban használt port volt 3389-es, amely a távoli asztal, és a fennmaradó RPC dinamikus portok.

Amíg ez a forgalom jelentheti semmit, egy portot, amelyet számos kapcsolatokhoz használt és az rendszergazdájának ismeretlen.

![5. ábra][5]

### <a name="step-3"></a>3. lépés

Most, hogy eldöntöttük, kívüli szűrheti a port alapján a rögzítési hely port.

A szűrő ebben a forgatókönyvben a következő lesz:

```
tcp.port == 111
```

Azt adja meg a fenti szűrő szöveget, a szűrő szövegmezőbe, és az enter.

![6. ábra.][6]

Az eredmények között látható összes forgalom érkezik egy helyi virtuális gép ugyanazon az alhálózaton. Még mindig nem jól tudjuk miért Ez a forgalom történik, ha azt további vizsgálhatja meg a csomagok segítségével állapítja meg, miért azt, hogy, így ezeket a hívásokat a porton 111. Ezen adatok igénybe a megfelelő műveletet.

## <a name="next-steps"></a>További lépések

További információ a Network Watcher diagnosztikai többi funkciója funkcionáló [Azure hálózati Figyelés áttekintése](network-watcher-monitoring-overview.md)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png














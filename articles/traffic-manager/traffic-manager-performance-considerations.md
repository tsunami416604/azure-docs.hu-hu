---
title: Az Azure Traffic Manager teljesítményével kapcsolatos szempontok | Microsoft dokumentumok
description: A Traffic Manager teljesítményének és a webhely teljesítményének tesztelésének módjának megismerése a Traffic Manager használata során
services: traffic-manager
documentationcenter: ''
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 84367a00643c48e7fe2fb7f907bab64589193b2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938540"
---
# <a name="performance-considerations-for-traffic-manager"></a>Teljesítménnyel kapcsolatos megfontolások a Traffic Manager esetében

Ez a lap ismerteti a Traffic Manager használatával kapcsolatos teljesítménybeli szempontokat. Vegyük példaként a következő esetet:

Webhelye a WestUS és az EastAsia régióban található. Az egyik példány nem felel meg a traffic manager-mintavétel állapot-ellenőrzés. Az alkalmazásforgalom a kifogástalan állapotú régióba irányul. Ez a feladatátvétel várható, de a teljesítmény problémát okozhat a forgalom késése alapján, amely most egy távoli régióba utazik.

## <a name="performance-considerations-for-traffic-manager"></a>Teljesítménnyel kapcsolatos megfontolások a Traffic Manager esetében

A Traffic Manager egyetlen teljesítménybeli hatása a webhelyen a kezdeti DNS-keresése. A Traffic Manager-profil nevére vonatkozó DNS-kérelmet a trafficmanager.net zónát tartalmazó Microsoft DNS-gyökérkiszolgáló kezeli. A Traffic Manager feltölti és rendszeresen frissíti a Microsoft DNS-gyökérkiszolgálóit a Traffic Manager házirend és a mintavétel eredményei alapján. Így még a kezdeti DNS-keresése során sem küld DNS-lekérdezéseket a Traffic Manager.

A Traffic Manager több összetevőből áll: DNS-névkiszolgálókból, egy API-szolgáltatásból, a tárolási rétegből és egy végpontfigyelési szolgáltatásból. Ha egy Traffic Manager szolgáltatásösszetevő meghibásodik, nincs hatással a Traffic Manager-profilhoz társított DNS-névre. A Microsoft DNS-kiszolgálók rekordjai változatlanok maradnak. A végpontfigyelés és a DNS-frissítés azonban nem történik meg. Ezért a Traffic Manager nem tudja frissíteni a DNS-t, hogy a feladatátvételi helyre mutasson, amikor az elsődleges hely leáll.

A DNS-névfeloldás gyors, és az eredmények gyorsítótárba kerülnek. A kezdeti DNS-keresési sebesség attól függ, hogy az ügyfél az ügyfél által a névfeloldáshoz használt DNS-kiszolgálókat használja-e. Az ügyfelek általában ~50 ms-on belül végezhetnek DNS-keresési műveletet. A keresés eredményei a DNS élettartama (TTL) idejére kerülnek a gyorsítótárba. A Traffic Manager alapértelmezett TTL-je 300 másodperc.

A forgalom nem áramlik a Traffic Manageren keresztül. A DNS-keresése befejezése után az ügyfél rendelkezik a webhely egy példányának IP-címével. Az ügyfél közvetlenül csatlakozik az adott címhez, és nem halad át a Traffic Manageren. A kiválasztott Traffic Manager-házirend nincs hatással a DNS teljesítményére. A Teljesítmény-útválasztási módszer azonban negatívan befolyásolhatja az alkalmazás élményét. Ha például a házirend az Észak-Amerikából egy Ázsiában üzemeltetett példányra irányítja át a forgalmat, az adott munkamenetek hálózati késése teljesítménybeli problémát jelenthet.

## <a name="measuring-traffic-manager-performance"></a>A Traffic Manager teljesítményének mérése

Számos webhely et használhat a Traffic Manager-profilok teljesítményének és viselkedésének megértéséhez. Sok ilyen oldalak ingyenesek, de lehetnek korlátai. Egyes webhelyek díjellenében kínálnak továbbfejlesztett figyelést és jelentést.

Az ezeken a helyeken található eszközök mérik a DNS-késéseket, és megjelenítik a világ szerte a világ on-fi ügyfélhelyeihez tartozó feloldott IP-címeket. A legtöbb ilyen eszköz nem gyorsítótárazza a DNS-eredményeket. Ezért az eszközök a teljes DNS-keresési adatokat jelenítik meg minden egyes teszt futtatásakor. Ha a saját ügyfélről tesztel, a teljes DNS-keresési teljesítményt csak egyszer tapasztalja meg a TTL időtartama alatt.

## <a name="sample-tools-to-measure-dns-performance"></a>Mintaeszközök a DNS-teljesítmény mérésére

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS kínál számos teljesítmény eszközök. A DNS-összehasonlító eszköz megmutatja, hogy mennyi ideig tart a DNS-név feloldása, és hogy ez hogyan viszonyul a többi DNS-szolgáltatóhoz.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Az egyik legegyszerűbb eszköz a WebSitePulse. Adja meg az URL-címet a DNS-feloldási idő, az Első bájt, az Utolsó bájt és más teljesítménystatisztikák megtekintéséhez. Három különböző teszthely közül választhat. Ebben a példában azt láthatja, hogy az első végrehajtás azt mutatja, hogy a DNS-keresési 0,204 másodperc.

    ![impulzus1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Mivel az eredmények gyorsítótárazva vannak, ugyanannak a Traffic Manager-végpontnak a második tesztje a DNS-keresés 0,002 másodpercet vesz igénybe.

    ![impulzus2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [CA alkalmazás szintetikus monitora](https://asm.ca.com/en/checkit.php)

    Korábbi nevén a Watch-mouse Check Website eszköz, ezen az oldalon megmutatja a DNS-felbontás ideje több földrajzi régiók ban egyszerre. Adja meg az URL-címet, hogy több földrajzi helyről láthassa a DNS-feloldási időt, a kapcsolat idejét és sebességét. Ezzel a teszttel megtekintheti, hogy melyik üzemeltetett szolgáltatást adja vissza a világ különböző pontjain.

    ![impulzus1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Fütyülés](https://tools.pingdom.com/)

    Ez az eszköz teljesítménystatisztikákat biztosít a weboldal minden eleméhez. A Lapelemzés lapon látható a DNS-keresésekre fordított idő hány százaléka.

* [Mi a saját DNS-em?](https://www.whatsmydns.net/)

    Ez az oldal 20 különböző helyről végez DNS-kereséssel, és megjeleníti az eredményeket a térképen.

* [Dig webes felület](https://www.digwebinterface.com)

    Ez az oldal részletesebb DNS-információkat, köztük CNAMEs és A rekordokat jelenít meg. Győződjön meg róla, hogy ellenőrizze a "Colorize kimenet" és a "Statisztika" a lehetőségek alatt, és válassza a "Minden" a Nameservers.

## <a name="next-steps"></a>Következő lépések

[A Traffic Manager forgalomirányítási módszerei](traffic-manager-routing-methods.md)

[Traffic Manager-beállítások tesztelése](traffic-manager-testing-settings.md)

[Operations on Traffic Manager (A Traffic Managerrel végezhető műveletek) (REST API-referencia)](https://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager parancsmagok](https://docs.microsoft.com/powershell/module/az.trafficmanager)


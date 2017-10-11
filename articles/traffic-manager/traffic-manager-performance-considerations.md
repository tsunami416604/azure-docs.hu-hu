---
title: "Teljesítménnyel kapcsolatos szempontok az Azure Traffic Managerben |} Microsoft Docs"
description: "A Traffic Manager és a webhelye teljesítményét tesztelése a Traffic Manager használata esetén a teljesítmény ismertetése"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 3ba5dfa1-2922-43f1-9a23-d06969c4a516
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: f686685138625a53971f1fc5fc754fd22c9d67b2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="performance-considerations-for-traffic-manager"></a>Teljesítménnyel kapcsolatos megfontolások a Traffic Manager esetében

Ezen a lapon a teljesítménnyel kapcsolatos szempontok a Traffic Managerrel ismerteti. Vegye figyelembe a következő forgatókönyvet:

A WestUS és EastAsia régiók a webhely példányai vannak. Egy példányt a traffic manager mintavétel állapot-ellenőrzés sikertelen. Alkalmazás-forgalmat a megfelelő régió van átirányítva. Ennél a feladatátvételnél várt, de a teljesítmény a forgalmat egy távoli régió most utazik késését alapján problémát okozhat.

## <a name="performance-considerations-for-traffic-manager"></a>Teljesítménnyel kapcsolatos megfontolások a Traffic Manager esetében

Csak teljesítményére gyakorolt hatást, amely a Traffic Manager is rendelkezik a webhelyen a kezdeti DNS-ben. A Traffic Manager-profil nevét a DNS-kérelmet a Microsoft DNS-gyökérkiszolgáló a trafficmanager.net zónát üzemeltető kezeli. A TRAFFIC Manager tölti fel, és rendszeresen frissíti, a Microsoft DNS gyökérkiszolgálók a Traffic Manager-házirend és a mintavételi eredmények alapján. A kezdeti DNS-címkeresést, során úgy is nincs DNS-lekérdezések felvétele a Traffic Manager küldése.

A TRAFFIC Manager számos összetevőt épül fel: DNS-kiszolgálók, az API-szolgáltatás, a tárolási réteg és szolgáltatás figyelése a végpont névhez. Ha a Traffic Manager szolgáltatás valamelyik összetevője nem sikerül, akkor nincs hatással a Traffic Manager-profilhoz tartozó DNS-nevét. A Microsoft DNS-kiszolgálókon a rekordok változatlanok maradnak. Azonban végpontmonitoring kijelző és a DNS-frissítését nem fordulhat elő. A Traffic Manager ezért nem tudja frissíteni a DNS a feladatátvételi helyen mutassanak, amikor az elsődleges hely leáll.

DNS-névfeloldás gyorsan elvégezhető, és az eredmény tárolódik. A kezdeti DNS-címkeresés sebességétől függ, a DNS-kiszolgálók, az ügyfél használ a névfeloldáshoz. Általában egy ügyfél egy DNS-címkeresés belül ~ 50 ms hajthatja végre. A keresés eredményeinek gyorsítótárba kerüljenek-e a DNS idő élettartamát (TTL) időtartama. Élettartam a Traffic Manager alapértelmezés szerint 300 másodperc.

Forgalom Traffic Manager használatával nem terjeszthetők. Miután a DNS-keresés befejeződött, az ügyfél rendelkezik egy példányát a webhely IP-címet. Az ügyfél közvetlenül kapcsolódik, hogy a cím, és nem felel meg a Traffic Manager használatával. A Traffic Manager-házirend úgy dönt, a DNS teljesítményét nincs hatással van. Azonban teljesítmény útválasztás-metódus negatív hatással lehet az alkalmazások használhatóságát. Például a házirend átirányítja a felhasználókat Ázsiában található példány Észak-Amerika-forgalom, a hálózati késés, ezek a munkamenetek a teljesítménycsökkenés oka lehet.

## <a name="measuring-traffic-manager-performance"></a>A Traffic Manager teljesítményének mérése

A teljesítmény és a Traffic Manager-profil viselkedésének megértése segítségével több webhelye van. Ezeken a webhelyeken számos ingyenes azonban előfordulhat, hogy korlátozott. Bizonyos webhelyek nyújtanak a bővített figyelési és jelentéskészítési díjköteles.

Az eszközök a helyek mérték DNS késések és megjelenítheti a a feloldani az IP-címeket a világszerte elhelyezkedő ügyfelek helyének. A legtöbb ezek az eszközök nem gyorsítótárazzák a DNS-találatokat. Ezért az eszközök megjelenítése a teljes DNS-címkeresés teszt minden egyes futtatásakor. A saját ügyfélről tesztelésekor csak egyszer a TTL időszakra teljes DNS-keresési teljesítményét felhasználói élmény.

## <a name="sample-tools-to-measure-dns-performance"></a>DNS-teljesítményének méréséhez minta eszközök

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS teljesítmény számos eszközt kínál. A DNS-összehasonlítás eszköz tekintheti meg, mennyi időbe telik feloldani a DNS-nevet, és hogyan, amely összehasonlítja a más DNS-szolgáltató.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    A legegyszerűbb eszközök egyik WebSitePulse. Adja meg az URL-címet a DNS-feloldási idejét, az első bájtig eltelt, az utolsó bájtig eltelt és egyéb teljesítménystatisztikáit. Különböző teszt három hely közül választhat. Ebben a példában látható, hogy az első végrehajtása jeleníti meg, hogy a DNS-címkeresés 0.204 mp vesz igénybe.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Az eredmény tárolódik, mert a második vizsgálat a Traffic Manager-végpontot a DNS-címkeresés szükséges 0,002 mp.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Hitelesítésszolgáltató App szintetikus figyelője](https://asm.ca.com/en/checkit.php)

    Korábbi nevén a Watchmouse ellenőrzés webhely eszköz, a hely jelzik, hogy a DNS-feloldási idejét a több földrajzi régióban elhelyezkedő egyidejűleg. Adja meg az URL-címet a DNS-feloldási idejét, a kapcsolati időt és a több földrajzi helyről sebessége. Ez a vizsgálat segítségével megtekintheti, melyik üzemeltetett szolgáltatás visszaküldött a világ különböző helyeken.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    Ez az eszköz biztosít minden elem egy weblap teljesítménystatisztikáit. A lap elemzés lapon láthatók a DNS-címkeresés töltött idő százalékos aránya.

* [Újdonságok a DNS?](http://www.whatsmydns.net/)

    Ezen a helyen nem a DNS-címkeresést 20 különböző helyekről, és megjeleníti az eredményeket a térképen.

* [Webes felület Dig](http://www.digwebinterface.com)

    Ezen a helyen látható, a DNS-adatok, beleértve a CNAME és A rekordok részletes. Győződjön meg arról, hogy a "Kifestés egyszínűre output" és "Statisztikák" Ellenőrizze a beállítások, és válassza az "All" Nameservers alatt.

## <a name="next-steps"></a>Következő lépések

[A Traffic Manager forgalom-útválasztási módszerei](traffic-manager-routing-methods.md)

[A Traffic Manager-beállítások tesztelésére](traffic-manager-testing-settings.md)

[A Traffic Manager műveletei (REST API-referencia)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager-parancsmagok](http://go.microsoft.com/fwlink/p/?LinkId=400769)


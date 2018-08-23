---
title: Teljesítménnyel kapcsolatos megfontolások az Azure Traffic Manager |} A Microsoft Docs
description: A Traffic Manager és a webhely teljesítményének tesztelése a Traffic Manager használata esetén teljesítményének ismertetése
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 3ba5dfa1-2922-43f1-9a23-d06969c4a516
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 43bb407730594498cfe9c78810c4e9dfb17e4af4
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42054692"
---
# <a name="performance-considerations-for-traffic-manager"></a>Teljesítménnyel kapcsolatos megfontolások a Traffic Manager esetében

Jelen lap bemutatja a teljesítménnyel kapcsolatos megfontolások a Traffic Manager használatával. Vegye figyelembe az alábbi forgatókönyvet:

A webhely példánya a WestUS és EastAsia régióban van. A traffic manager mintavétel az állapot-ellenőrzés sikertelen egy példányt. Alkalmazás forgalmat a kifogástalan állapotú régió van átirányítva. A feladatátvételi várt, de a teljesítmény most már egy távoli régióba továbbított forgalmat a késéssel problémát jelenthetnek.

## <a name="performance-considerations-for-traffic-manager"></a>Teljesítménnyel kapcsolatos megfontolások a Traffic Manager esetében

A Traffic Manager rendelkezhet a webhelyén csak teljesítményre gyakorolt a kezdeti DNS-címkeresés. A Traffic Manager-profil neve DNS-kérelmet a Microsoft DNS-gyökérkiszolgáló, amelyen a trafficmanager.net zónában kezeli. A TRAFFIC Manager tölti fel, és rendszeresen frissíti, a Microsoft DNS gyökérkiszolgálók a Traffic Manager-házirend és a vizsgálati eredmények alapján. Az első DNS-keresés során tehát még nincs DNS küldi el a Traffic Manager.

A TRAFFIC Manager több összetevőből épül fel: DNS-kiszolgálók, API-szolgáltatás, a tárolási réteg és a egy endpoint monitoring szolgáltatás névhez. A Traffic Manager szolgáltatás valamelyik összetevője nem sikerül, ha nem nincs hatással a Traffic Manager-profil társított DNS-nevét. A Microsoft DNS-kiszolgálók rekordjait változatlan marad. Azonban végpont-monitorozás és a DNS-frissítése sem történik. Ezért nem sikerül frissíteni a DNS átirányítása a feladatátvételi helyet, ha az elsődleges hely leáll a Traffic Manager.

DNS-névfeloldás gyors, és az eredmények lettek gyorsítótárazva. A kezdeti DNS-címkeresés sebessége attól függ, hogy a DNS-kiszolgálókat, az ügyfél használ a névfeloldáshoz. Általában egy ügyfél egy DNS-címkeresés belül ~ 50 ms hajthatja végre. A keresési eredményeit a DNS idő-az-élettartam (TTL) részére lettek gyorsítótárazva. Élettartam a Traffic Manager alapértelmezés szerint 300 másodpercig.

Adatforgalom nem Traffic Manageren keresztül. Ha a DNS-lekérdezés befejeződött, az ügyfél rendelkezik egy példányát a webhely IP-címet. Az ügyfél közvetlenül csatlakozik a címet, és nem felel meg a Traffic Manageren keresztül. A Traffic Manager-szabályzatban lehet kiválasztani nincs semmilyen hatással a DNS teljesítményét. Azonban teljesítmény útválasztás-metódus negatív hatással lehet az alkalmazás felhasználói élményt. Például a szabályzat átirányítja a forgalmat Észak-Amerikában, Ázsiában lévő üzemeltetett példányhoz, a hálózati késést, ezek a munkamenetek teljesítménycsökkenés lehet.

## <a name="measuring-traffic-manager-performance"></a>A Traffic Manager teljesítményének mérése

Nincsenek segítségével megismerheti a teljesítmény és a egy Traffic Manager-profil viselkedését több webhelyet. Ezek a helyek számos ingyenes, de előfordulhat, hogy korlátozásokat. Bizonyos webhelyek fejlett figyelési és jelentéskészítési díj ellenében is nyújtanak.

Az eszközök ezeket a helyek mérték DNS késéseket és megjelenítési a feloldott IP-címei a világ különböző pontjain található ügyfelek helyei. Ezek az eszközök a legtöbb nem gyorsítótárazzák a DNS-találatokat. Ezért az eszközök megjelenítése a teljes DNS-címkeresés egy teszt minden egyes futtatásakor. Ha a saját ügyfélről, a teljes DNS-keresési teljesítményét a TTL időszakra egyszer csak tapasztalható.

## <a name="sample-tools-to-measure-dns-performance"></a>DNS-teljesítmény mérésére mintául szolgáló eszközök

* [SolveDNS](http://www.solvedns.com/dns-comparison/)

    SolveDNS teljesítmény számos eszközt kínál. A DNS-összehasonlító eszköz jeleníti meg, mennyi ideig tart feloldani a DNS-nevet, és hogyan, amely összehasonlítja a más DNS-szolgáltatók.

* [WebSitePulse](http://www.websitepulse.com/help/tools.php)

    A legegyszerűbb eszközök egyik WebSitePulse. Adja meg az URL-cím DNS-feloldási idő, az első bájtig eltelt, az utolsó bájtig eltelt és a más teljesítménystatisztikáit megtekintéséhez. Három különböző teszthelyszín közül választhat. Ebben a példában láthatja, hogy az első végrehajtás jeleníti meg, hogy a DNS-címkeresés szükséges 0.204 mp-ben.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Az eredményeket a rendszer gyorsítótárazza, mert a Traffic Manager-végpontot a második vizsgálat a DNS-címkeresés veszi 0,002 mp-ben.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Hitelesítésszolgáltató alkalmazás szintetikus figyelője](https://asm.ca.com/en/checkit.php)

    Korábbi nevén a Watchmouse ellenőrzés webhely eszköz, ezen a helyen bemutatják, a DNS-feloldási idő a több földrajzi régióban egyszerre. Adja meg az URL-cím DNS-feloldási idő, a kapcsolat ideje és a több földrajzi helyről sebessége. Ez a teszt segítségével megtekintheti, mely üzemeltetett szolgáltatás számára a világ különböző pontjain különböző helyeken adja vissza.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](http://tools.pingdom.com/)

    Ez az eszköz egy weblap egyes elemeinek teljesítménystatisztikáit biztosít. A lap elemzési lapon láthatók a DNS-címkeresés töltött időt.

* [Mi a DNS?](http://www.whatsmydns.net/)

    Ez a hely DNS-címkeresés nem 20 különböző helyekről, és megjeleníti az eredményeket a térképen.

* [Webes felület megismerésére](http://www.digwebinterface.com)

    Ezen a helyen jeleníti meg, beleértve a CNAME-rekordokat és a egy a rekordot a DNS-adatok részletesebb. Ellenőrizze a "Kifestés egyszínűre kimenet" és "Statisztikák" Ellenőrizze a beállítások, és válassza ki az "All" alatt delegálásához.

## <a name="next-steps"></a>További lépések

[Tudnivalók a Traffic Manager útválasztási módszerei](traffic-manager-routing-methods.md)

[A Traffic Manager beállításainak tesztelése](traffic-manager-testing-settings.md)

[A Traffic Manager műveletei (REST API-referencia)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Az Azure Traffic Manager parancsmagjai](https://docs.microsoft.com/powershell/module/azurerm.trafficmanager)


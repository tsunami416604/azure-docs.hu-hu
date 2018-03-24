---
title: Terheléselosztó egyéni mintavételt segítségével állapot figyelése |} Microsoft Docs
description: Ismerje meg, hogyan használható az Azure Load Balancer egyéni mintavételt terheléselosztó mögött példányok figyelése céljából
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/8/2018
ms.author: kumud
ms.openlocfilehash: 0aab72fdf48589a72707ae87f90af11f65f35088
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="understand-load-balancer-probes"></a>Terheléselosztó mintavételt ismertetése

Az Azure Load Balancer állapotteljesítmény alapján határozza meg, melyik háttér címkészletet példány új adatfolyamok kell kapniuk. Ha nem sikerül egy állapotmintáihoz, terheléselosztó leállítja az új forgalom küldése a megfelelő nem megfelelő állapotú példány, és meglévő forgalom azon a példányon nem érinti.  Háttér címkészletet példányainak mintavételi le, ha az összes meglévő forgalom lesz háttérkészlethez összes példánya túllépi az időkorlátot.

Felhőszolgáltatás szerepköreit (feldolgozói szerepköröket és webes szerepkörök) használja a vendégügynök mintavételi figyelésre. TCP- vagy HTTP egyéni állapotfigyelő mintavételt terheléselosztó mögötti virtuális gépek használatakor be kell állítani.

## <a name="understand-probe-count-and-timeout"></a>Mintavételi száma és időtúllépés

Mintavételi viselkedés attól függ:

* A sikeres mintavételek menüpontban, amelyek lehetővé teszik egy példányt kell címkével száma állapotúként.
* Sikertelen mintavételek menüpontban, amelyek egy példányt kell címkével száma szerint lefelé.

SuccessFailCount időkorlát és a gyakoriság értéke határozza meg, hogy példány megerősítik fut vagy nem működik. Az Azure-portálon az időtúllépés értéke a gyakoriság értékének kétszer.

A mintavétel konfigurációs terhelésű szoftverpéldányok a végpont (Ez azt jelenti, hogy egy elosztott terhelésű készlet) azonosnak kell lennie. Ha nem egy adott végpont kombinációjához azonos üzemeltetett szolgáltatás minden egyes szerepkör példánya vagy a virtuális gép különböző mintavételi konfigurációval rendelkezik. Például minden példány kell rendelkeznie, azonos helyi portok és időtúllépéseket okoz.

> [!IMPORTANT]
> Terheléselosztói mintavétel 168.63.129.16 IP-címét használja. A nyilvános IP-cím elősegíti a kerüljön-a-saját-IP-cím az Azure virtuális hálózatot belső platform erőforrásokhoz való kommunikációt. A virtuális nyilvános IP-cím 168.63.129.16 minden régióban szolgál, és nem módosítja. Azt javasoljuk, hogy lehetővé tegye az IP-címet a helyi tűzfal házirendekben. Ez nem tekinthető biztonsági kockázatot jelent, mert csak a belső Azure platformon is forrás-, hogy a cím az üzenet. Ha nem engedélyezi az IP-cím a tűzfalházirendeket a váratlan viselkedés különféle forgatókönyvekhez, amik történik. A viselkedés 168.63.129.16 azonos IP-címtartomány konfigurálását foglalja magában, és duplikálásakor IP-címek.

## <a name="learn-about-the-types-of-probes"></a>További tudnivalók a mintavételt típusú

### <a name="guest-agent-probe"></a>Vendég ügynök mintavétele

A Vendég ügynök mintavétel csak esetén Azure Cloud Services érhető el. Terheléselosztó használja a vendégügynököt a virtuális Gépen belül. Majd figyeli és reagál HTTP 200 OK válasz csak akkor, ha a példány a kész állapotban van. (Más állapotokat elfoglalt, újrahasznosítása vagy leállást.)

További információkért lásd: [a szolgáltatásdefiníciós fájlban (csdef) konfigurálása állapotteljesítmény](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [hozzon létre egy nyilvános terheléselosztó a felhőszolgáltatások Ismerkedés](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>A Vendég ügynök mintavétele jelölje példánya sérültként hasznossá?

Ha a vendégügynök nem válaszol a HTTP 200 OK, a terheléselosztó nem válaszol, a példány jelöli meg. Majd leállítja az adott példányhoz olyan adatforgalmat küldenie. A load balancer továbbra is fennáll, Pingelje meg a példány. Ha a vendégügynök válaszol egy HTTP 200, a terheléselosztó küld forgalom példánynak újra.

A webes szerepkör használata esetén a webhely kód általában futtat w3wp.exe, amelyen nem figyelik a Azure fabric vagy Vendég ügynök. A vendégügynök nem jelentett w3wp.exe (például 500-as HTTP-válaszok) hibáinak. Ezért a terheléselosztó kívül Elforgatás példánynak nem veszi.

### <a name="http-custom-probe"></a>Egyéni HTTP-vizsgálatot

Az egyéni HTTP-vizsgálatot felülbírálja az alapértelmezett Vendég ügynök mintavétel. Létrehozhat saját egyéni logika a szerepkör-példány állapotának meghatározására. A terheléselosztó-vizsgálat a végpont 15 másodpercenként alapértelmezés szerint. A példány lehet a load balancer elforgatási, ha az időkorláton belül válaszol a HTTP 200-as tekinthető. Az időkorlát értéke alapértelmezés szerint 31 másodperc.

Egyéni HTTP-vizsgálatot akkor lehet hasznos, ha azt szeretné, hogy a saját logikát példányt eltávolítja, a load balancer elforgatás végrehajtásához. Dönthet például úgy végezheti el egy példányát, ha a fenti 90 % CPU, és nem 200 állapotot ad vissza. Ha webes szerepkörök w3wp.exe, is kap automatikus webhelyének figyelését. Hibák a webhely-kódban nem 200 állapota a terheléselosztói mintavétel adnak vissza.

> [!NOTE]
> Az egyéni HTTP-vizsgálatot relatív elérési utakat, és csak a HTTP protokollt támogatja. HTTPS nem támogatott.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Az egyéni HTTP-vizsgálatot, jelölje meg, hogy megsérült példány hasznossá?

* A HTTP-alkalmazás eltérő (például a 403-as, 404 vagy 500) 200-as HTTP-válaszkód adja vissza. A pozitív visszajelzés riasztást küld, azonnal érvénybe az alkalmazáspéldány nem működik.
* A HTTP-kiszolgáló nem válaszol a határidő után. Attól függően, hogy az időtúllépés értéke, amely be van állítva, több mintavételi kérések mehet nem érkezik válasz előtt a mintavétel beolvasása jelölésű nem fut (Ez azt jelenti, hogy mielőtt SuccessFailCount mintavételt megkapja).
* A kiszolgáló megszünteti a kapcsolatot a TCP alaphelyzetbe állítása keresztül.

### <a name="tcp-custom-probe"></a>Egyéni TCP-Hálózatfigyelővel

TCP egyéni mintavételt kezdeményeznek kapcsolatot a megadott port egy háromutas kézfogás elvégzésével.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Egy egyéni TCP-Hálózatfigyelővel, jelölje meg, hogy megsérült példány hasznossá?

* A TCP-kiszolgáló az engedélyezett időtartam után egyáltalán nem válaszol. Ha a mintavételi van megjelölve, nem fut attól függ, hogy nem fut a mintavétel való megjelölése előtt nem érkezik válasz go konfigurált sikertelen mintavételi kérések száma.
* A mintavétel kap egy alaphelyzetbe állítja a szerepkörpéldány TCP.

Egy HTTP állapotmintáihoz vagy a TCP-Hálózatfigyelővel konfigurálásával kapcsolatos további információkért lásd: [PowerShell használatával az erőforrás-kezelő egy nyilvános terheléselosztó létrehozásához](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>Adja hozzá újra üzembe a load balancer elforgatási megfelelő példányok

TCP- és HTTP mintavételt kifogástalan minősülnek, és jelölje meg a megfelelő when szerepkörpéldányon:

* A load balancer lekérdezi egy pozitív mintavételt először a virtuális gép elindul.
* A számot (lásd a korábbiakban) SuccessFailCount sikeres mintavételek menüpontban, jelölje meg a megfelelő szerepkörpéldányon szükséges értéke határozza meg. Ha a szerepkör példánya el lett távolítva, a sikeres, egymást követő mintavételek menüpontban száma kell egyenlő vagy megjelölni a szerepkörpéldányon futó SuccessFailCount értéke meghaladja.

> [!NOTE]
> Ha a szerepkör példánya állapotának ingadozik, a terheléselosztó megvárja-e már előtt a kifogástalan állapotban helyezi el a szerepkör példánya. A felesleges várakozási idő védi a felhasználó és az infrastruktúra és egy szándékos házirend.

## <a name="use-log-analytics-for-a-load-balancer"></a>A naplóelemzési használni a terheléselosztóhoz

Használhat [analytics jelentkezzen](load-balancer-monitor-log.md) betöltése terheléselosztói mintavétel állapotfigyelő állapotát, és a mintavételi száma. Naplózás segítségével a Power bi-ban vagy az Azure Operational Insights adja meg a load balancer állapot statisztikája.

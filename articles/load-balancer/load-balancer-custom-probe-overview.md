---
title: "Terheléselosztó egyéni mintavételt be, és a figyelési állapot |} Microsoft Docs"
description: "Ismerje meg, hogyan használható az Azure Load Balancer egyéni mintavételt terheléselosztó mögött példányok figyelése céljából"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 102c07ff0994b3b411f2a13d7a43c5398d5dfd42
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="understand-load-balancer-probes"></a>A Load Balancer vizsgálatok ismertetése

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Az Azure Load Balancer lehetőséget nyújt a figyelheti a kiszolgálópéldányok állapotát mintavételt használatával. Egy mintavételt nem válaszol, a terheléselosztó leállítja, új kapcsolatok küld a nem megfelelő állapotú példány. Nem érinti a meglévő kapcsolatokat, és új kapcsolatok küldött megfelelő példányok.

Felhőszolgáltatás szerepköreit (feldolgozói szerepköröket és webes szerepkörök) használja a vendégügynök mintavételi figyelésre. TCP- vagy HTTP egyéni mintavételt terheléselosztó mögött a virtuális gépek használatakor be kell állítani.

## <a name="understand-probe-count-and-timeout"></a>Mintavételi száma és időtúllépés

Mintavételi viselkedés attól függ:

* A sikeres mintavételek menüpontban, amelyek lehetővé teszik egy példányt kell címkével száma állapotúként.
* Sikertelen mintavételek menüpontban, amelyek egy példányt kell címkével száma szerint lefelé.

SuccessFailCount időkorlát és a gyakoriság értéke határozza meg, hogy példány megerősítik fut vagy nem működik. Az Azure-portálon az időtúllépés értéke a gyakoriság értékének kétszer.

A mintavétel konfigurációs terhelésű szoftverpéldányok a végpont (Ez azt jelenti, hogy egy elosztott terhelésű készlet) azonosnak kell lennie. Ez azt jelenti, hogy nem kell minden szerepkörpéldányt vagy a virtuális gép egy másik mintavételi konfigurációs azonos üzemeltetett szolgáltatás egy adott végpont kombinációjához. Például minden példány kell rendelkeznie, azonos helyi portok és időtúllépéseket okoz.

> [!IMPORTANT]
> A Load Balancer mintavételi 168.63.129.16 IP-címét használja. A nyilvános IP-cím elősegíti a kerüljön-a-saját-IP-cím az Azure virtuális hálózatot belső platform erőforrásokhoz való kommunikációt. A virtuális nyilvános IP-cím 168.63.129.16 minden régióban szolgál, és nem változik. Azt javasoljuk, hogy lehetővé tegye az IP-címet a helyi tűzfal házirendekben. Ez nem tekinthető biztonsági kockázatot jelent, mert csak a belső Azure platformon is forrás-, hogy a cím az üzenet. Ha ezt nem teszi meg, a különböző helyzetekben, például az azonos IP-címtartomány 168.63.129.16, és hogy ismétlődő IP-címek konfigurálása váratlan viselkedést lesz.

## <a name="learn-about-the-types-of-probes"></a>További tudnivalók a mintavételt típusú

### <a name="guest-agent-probe"></a>Vendég ügynök mintavétele

Ez a Hálózatfigyelő csak akkor használható Azure Cloud Services. Terheléselosztó használja a virtuális gépen lévő vendégügynök figyeli és reagál HTTP 200 OK válasz csak akkor, ha a példány a kész állapotban van (Ez azt jelenti, hogy nem szerepel egy másik állapot például elfoglalt, újrahasznosítása vagy leállítása).

További információkért lásd: [a szolgáltatásdefiníciós fájlban (csdef) konfigurálása állapotteljesítmény](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [létrehozásához, egy internetre irányuló terheléselosztót a felhőszolgáltatások](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>A Vendég ügynök mintavétele jelölje példánya sérültként hasznossá?

Ha a vendégügynök nem válaszol a HTTP 200 OK, a terheléselosztó jelöli meg a példány válaszol, és leállítja a forgalom küldése annak a példánynak. A load balancer továbbra is fennáll, Pingelje meg a példány. Ha a vendégügynök válaszol egy HTTP 200, a terheléselosztó küld forgalom példánynak újra.

A webes szerepkör használata esetén a webhely kód általában lefut a w3wp.exe, nem figyelt az Azure fabric vagy Vendég ügynök. Ez azt jelenti, hogy a vendégügynök nem jelentett hibák w3wp.exe (például 500-as HTTP-válaszok), és a terheléselosztó nem lépnek kívül Elforgatás példányhoz.

### <a name="http-custom-probe"></a>Egyéni HTTP-vizsgálatot

Az egyéni HTTP terheléselosztó mintavétel felülbírálja az alapértelmezett Vendég ügynök mintavétel, ami azt jelenti, hogy a szerepkör-példány állapotának meghatározására a saját egyéni logika hozhat létre. A terheléselosztó-vizsgálat a végpont 15 másodpercenként alapértelmezés szerint. A példány load balancer elforgatási találhatók, ha az időkorlát (alapértelmezés szerint 31 másodperc) belül az válaszol egy HTTP 200 tekinthető meg.

Ez akkor lehet hasznos, ha azt szeretné, hogy a saját logikát példányt eltávolítja, a load balancer elforgatás végrehajtásához. Például sikertelen eldöntheti, egy példányát eltávolítja, ha meghaladja a 90 % CPU, és nem 200 állapotot ad vissza. Ha webes szerepkörök w3wp.exe, ez azt is jelenti automatikus kap a webhely megfigyeléséhez, mivel a webhely kódban hibák nem 200 állapot visszatér a terheléselosztói mintavétel.

> [!NOTE]
> Az egyéni HTTP-vizsgálatot relatív elérési utakat, és csak a HTTP protokollt támogatja. HTTPS nem támogatott.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Az egyéni HTTP-vizsgálatot, jelölje meg, hogy megsérült példány hasznossá?

* A HTTP-alkalmazás eltérő (például a 403-as, 404 vagy 500) 200-as HTTP-válaszkód adja vissza. Ez az a pozitív visszaigazolás, amely az alkalmazáspéldány azonnal kell tenni nem működik.
* A HTTP-kiszolgáló válaszol a határidő után. Attól függően, hogy az időtúllépés értéke, amely be van állítva, ez azt jelentheti, hogy több mintavételi kérések nyissa meg nem érkezik válasz, mielőtt a mintavétel beolvasása jelölésű nem fut (Ez azt jelenti, hogy mielőtt SuccessFailCount mintavételt megkapja).
* A kiszolgáló megszünteti a kapcsolatot a TCP alaphelyzetbe állítása keresztül.

### <a name="tcp-custom-probe"></a>Egyéni TCP-Hálózatfigyelővel

TCP mintavételt kezdeményeznek kapcsolatot a megadott port egy háromutas kézfogás elvégzésével.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Egy egyéni TCP-Hálózatfigyelővel, jelölje meg, hogy megsérült példány hasznossá?

* A TCP-kiszolgáló válaszol a határidő után. Ha a mintavételi van megjelölve, nem fut attól függ, hogy nem fut a mintavétel való megjelölése előtt nem érkezik válasz go konfigurált sikertelen mintavételi kérések száma.
* A mintavétel kap egy alaphelyzetbe állítja a szerepkörpéldány TCP.

Egy HTTP állapotmintáihoz vagy a TCP-Hálózatfigyelővel konfigurálásával kapcsolatos további információkért lásd: [létrehozásához egy internetre irányuló terheléselosztót a Resource Manager PowerShell-lel](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-load-balancer-rotation"></a>Adja hozzá a megfelelő példányok újra üzembe a load balancer Elforgatás

TCP- és HTTP mintavételt kifogástalan minősülnek, és jelölje meg a megfelelő when szerepkörpéldányon:

* A load balancer lekérdezi egy pozitív mintavételt először a virtuális gép elindul.
* A szám SuccessFailCount (lásd a korábbiakban) sikeres mintavételek menüpontban, jelölje meg a megfelelő szerepkörpéldányon szükséges értékének meghatározása. Ha a szerepkör példánya el lett távolítva, a sikeres, egymást követő mintavételek menüpontban száma kell egyenlő vagy megjelölni a szerepkörpéldányon futó SuccessFailCount értéke meghaladja.

> [!NOTE]
> Ha egy szerepkörpéldányt állapotának ingadozó van, a terheléselosztó már a kifogástalan állapotban a szerepkörpéldányt helyezése előtt vár. Ez a felhasználó és az infrastruktúra védelme érdekében házirend használatával történik.

## <a name="use-log-analytics-for-load-balancer"></a>Naplóelemzési használ a Load Balancer

Használhat [analytics keresse meg a terheléselosztó](load-balancer-monitor-log.md) ellenőrizheti a mintavételi állapotát és a mintavételi száma. Naplózás segítségével a Power bi-ban vagy az Azure Operational Insights adja meg a terheléselosztó állapot statisztikája.

---
title: Load Balancer az egyéni minták használatával állapot figyelése |} A Microsoft Docs
description: Az egyéni minták az Azure Load Balancer használata a terheléselosztó mögött példányok figyelése céljából
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
ms.date: 07/13/2018
ms.author: kumud
ms.openlocfilehash: 741b32f394ca2ce447826f7207f7fd9cbede9c51
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070860"
---
# <a name="understand-load-balancer-probes"></a>Load Balancer vizsgálatok ismertetése

Az Azure Load Balancer állapot-mintavételei alapján határozza meg, melyik háttérkészletpéldányt új folyamatok kell kapnia.   Állapot-mintavételei használatával észleli a hibát egy alkalmazás egy háttér-példányon.  A health mintavételi választ az alkalmazásból használhatja, hogy jelezze a Load Balancer-e a folytatáshoz küldjön új folyamatok vagy leállítani a új folyamatok kezeléséhez a terhelés és a tervezett leállás háttér-példányra is.

Állapot-mintavételei szabályozzák, hogy új folyamatok megfelelően működő háttér-példányok létrehozása történik. Ha az állapotfigyelő mintavételező nem sikerül, a terheléselosztó nem irányít, több új folyamatok a megfelelő nem megfelelő állapotú példányhoz.  Fennálló TCP-kapcsolatok állapot-mintavételi hiba után továbbra is.  Meglévő UDP-forgalom áthelyezi a háttérkészlet egy másik példányhoz a nem megfelelő állapotú példányból.

Minden háttérkészlet-mintavételt sikertelen lesz, ha alapszintű Load Balancer Terheléselosztók megszűnik az összes meglévő TCP-adatfolyamok a háttérkészlethez, míg a Standard Load balancer lehetővé teszik a meghatározott folytatásához; TCP-adatfolyamok nincsenek új folyamatok küld a háttérkészlethez.  Az összes meglévő UDP-forgalom az alapszintű és standard szintű terheléselosztóhoz leáll, amikor egy háttér-mintavételt összes tárolókészlet sikertelen.

A felhőszolgáltatásokhoz tartozó szerepkörök (a feldolgozói szerepkörök és a webes szerepkörök) használni egy vendégügynök mintavétel. TCP- vagy HTTP egyéni állapotmintákat úgy kell konfigurálni, ha Cloud Services használata az IaaS virtuális gépet terheléselosztó mögé.

## <a name="understand-probe-count-and-timeout"></a>Mintavétel száma és az időtúllépési ismertetése

Állapotminta viselkedésére függ:

* Sikeres, amelyek lehetővé teszik egy példány lehet feliratú mintavételek száma szerint, akár.
* Egy példány lehet feliratú okozó sikertelen mintavételek száma leállítottnak.

SuccessFailCount időkorlátja és a gyakoriság értéke határozza meg, e-példány fut vagy nem fut ellenőrzése. Az Azure Portalon az időtúllépés értéke a gyakoriság értékének kétszer.

A mintavételi konfigurációban a végpont (azt jelenti, egy elosztott terhelésű készlet) minden elosztott terhelésű példányok azonosnak kell lennie. Egy másik mintavételi konfigurációt egy adott végpont kombináció azonos üzemeltetett szolgáltatásban található minden egyes szerepkör-példány vagy virtuális gép nem rendelkezik. Minden példány például az azonos helyi portok és időtúllépéseket kell rendelkeznie.

> [!IMPORTANT]
> Terheléselosztói mintavételezők 168.63.129.16 IP-címet használja. A nyilvános IP-cím belső platform erőforrások bring-your-saját-IP-címhez Azure-beli virtuális hálózat forgatókönyv kommunikációt létesít. A virtuális nyilvános IP-címet 168.63.129.16 használja minden olyan régióban, és nem módosul. Azt javasoljuk, hogy lehetővé tegye az IP-címet a bármilyen helyi tűzfal-házirendek. Azt nem tekinthető biztonsági kockázatot jelent, mivel csak a belső Azure platform egy adott címről üzenetet is forrás. Ha a tűzfal-házirendek IP-cím nem engedélyezett, nem várt viselkedést számos célra történik. A viselkedés ugyanazokat a 168.63.129.16 azonos IP-címtartomány és duplikálásához IP-címek.

## <a name="learn-about-the-types-of-probes"></a>Ismerje meg a mintavétel típusú

### <a name="guest-agent-probe"></a>Vendég ügynök mintavétele

Vendég ügynök mintavétel csak az Azure Cloud Services érhető el. Load Balancer a vendégügynököt a virtuális gép használja. Ezután figyeli, és válaszol egy HTTP 200 OK válasz csak akkor, ha a példány a kész állapotban van. (A többi állapotot foglalt, újrahasznosítás vagy leállítása is.)

További információkért lásd: [a szolgáltatásdefiníciós fájlt (csdef) konfigurálja az állapotmintákat](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [első lépésként hozza létre a cloud services nyilvános load balancer](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

### <a name="what-makes-a-guest-agent-probe-mark-an-instance-as-unhealthy"></a>Jelölje meg a példány állapota nem megfelelőként Vendég ügynök mintavétel Miből?

Ha a vendégügynök nem válaszol a HTTP 200 OK, a terheléselosztó nem válaszol, a példány jelöli meg. Majd leállítja az forgalmat küld annak a példánynak. A load balancer pingelni a példány továbbra is. Ha a vendégügynök válaszol egy HTTP 200-as, a terheléselosztó forgalmat küld példányhoz újra.

Webes szerepkör használata esetén a webhely kód általában lefut az w3wp.exe, amely az Azure által nem felügyelt hálóhoz vagy Vendég ügynök. A vendégügynök nem jelentett hibák a w3wp.exe (például a HTTP 500-as választ). Ennek következtében a terheléselosztó nem használ annak a példánynak a rotációból.

### <a name="http-custom-probe"></a>Egyéni HTTP-mintavétel

Egyéni HTTP-mintavétel felülbírálja az alapértelmezett Vendég ügynök mintavétel. Létrehozhat saját egyéni logikát meghatározni a szerepkörpéldány állapotát. A load balancer 15 másodpercenként mintavételek a végpont alapértelmezés szerint. A példány jelenik meg a load balancer elforgatás, ha az időkorláton belül válaszol egy HTTP 200-minősül. Az időkorlát értéke alapértelmezés szerint 31 másodperc.

Egyéni HTTP-mintavétel akkor lehet hasznos, ha azt szeretné megvalósítani a saját logikai példányt eltávolítja a load balancer rotációból. Például dönthet, hogy távolítsa el egy példányt, ha meghaladja a 90 %-ot, és a egy nem 200 állapotát adja vissza. Ha a w3wp.exe használó webes szerepkörök, érhet el az automatikus figyelési webhelyét. Hibák a webhely kódban nem 200 állapot térjen vissza a terheléselosztói mintavételezők.

> [!NOTE]
> Egyéni HTTP-mintavétel relatív útvonalakat, és csak a HTTP protokollt támogatja. HTTPS nem támogatott.

### <a name="what-makes-an-http-custom-probe-mark-an-instance-as-unhealthy"></a>Az egyéni HTTP-mintavétel, jelölje meg a példány állapota nem megfelelőként Miből?

* A HTTP-kérelem egy HTTP-válaszkód, 200-as (például a 403-as, 404-es vagy 500-as) eltérő adja vissza. A pozitív visszajelzés riasztást küld, azonnal érvénybe az alkalmazáspéldány nem működik.
* A HTTP-kiszolgáló egyáltalán nem válaszol az időkorláton után. Az időtúllépési érték, amely be van állítva, attól függően több mintavételi kérések mehet megválaszolatlan előtt nem fut, a mintavétel megjelölve (azaz előtt SuccessFailCount mintavételek érkeznek).
* A kiszolgáló lezárja a kapcsolatot a TCP alaphelyzetbe állítása keresztül.

### <a name="tcp-custom-probe"></a>Egyéni TCP-mintavétel

Az egyéni minták TCP kezdeményeznek kapcsolatot a meghatározott portot egy háromutas kézfogást hajt végre.

### <a name="what-makes-a-tcp-custom-probe-mark-an-instance-as-unhealthy"></a>Egy egyéni TCP-mintavétel, jelölje meg a példány állapota nem megfelelőként Miből?

* A TCP-kiszolgáló egyáltalán nem válaszol az időkorláton után. Ha meg van jelölve a mintavétel nem fut, attól függ, hogy nem fut, a mintavétel megjelölése előtti megválaszolatlan go konfigurált meghiúsult mintavételi kérések száma.
* A mintavétel egy TCP alaphelyzetbe állítani a szerepkörpéldány kap.

A HTTP-állapot-mintavétel vagy TCP-mintavételt konfigurálásával kapcsolatos további információkért lásd: [Ismerkedés létrehozása egy nyilvános terheléselosztó a Resource Managerben PowerShell használatával](load-balancer-get-started-internet-arm-ps.md).

## <a name="add-healthy-instances-back-into-the-load-balancer-rotation"></a>A load balancer Elforgatás visszaimportálni megfelelő példányok hozzáadása

TCP- és HTTP-vizsgálatok tekinti a kifogástalan állapotú, és jelölje meg a szerepkörpéldány kifogástalan amennyiben:

* A load balancer lekérdezi az első alkalommal a virtuális gép elindul egy pozitív mintavételt.
* (Lásd a korábbiakban) SuccessFailCount számát határozza meg a szerepkörpéldány kifogástalan állapotúként kell megjelölnie sikeres mintavételezők értékét. Ha egy szerepkörpéldány el lett távolítva, sikeres, egymást követő mintavételek száma egyenlő vagy kell megjelölni a szerepkörpéldány futtatásával SuccessFailCount értéke meghaladja.

> [!NOTE]
> Ha egy szerepkörpéldány állapotát ingadozik, a terheléselosztó vár már előtt a szerepkörpéldány helyezi el a kifogástalan állapotban. Az extra várakozási idő a felhasználó és az infrastruktúra védi, és olyan szándékos szabályzat.

## <a name="use-log-analytics-for-a-load-balancer"></a>A terheléselosztó a log analytics használata

Használhat [log analytics](load-balancer-monitor-log.md) ellenőrizheti a load balancer mintavételi állapotinformációit és a mintavételi száma. Adja meg a load balancer állapot statisztikája naplózás használható a Power bi-ban vagy az Azure Operational Insights.

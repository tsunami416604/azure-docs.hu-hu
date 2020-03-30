---
title: Rugalmasság és vészhelyreállítás az Azure SignalR szolgáltatásban
description: Több SignalR szolgáltatáspéldány beállításának áttekintése a rugalmasság és a vészhelyreállítás elérése érdekében
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: cf0f345b0fbf9fea2512f72c1996c9a1597cc0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747651"
---
# <a name="resiliency-and-disaster-recovery"></a>Rugalmasság és vészhelyreállítás

A rugalmasság és a katasztrófa utáni helyreállítás gyakori igénye az online rendszerekre. Az Azure SignalR szolgáltatás már garantálja a 99,9%-os rendelkezésre állást, de még mindig regionális szolgáltatás.
A szolgáltatáspéldány mindig fut az egyik régióban, és nem adja át egy másik régióba, ha van egy régiószintű kimaradás.

Ehelyett szolgáltatásunk SDK-nk olyan funkciót biztosít, amely több SignalR szolgáltatáspéldányt támogat, és automatikusan más példányokra vált, ha némelyikük nem érhető el.
Ezzel a funkcióval, akkor képes lesz arra, hogy visszaszerezze, ha egy katasztrófa történik, de be kell állítania a megfelelő rendszer topológiát egyedül. Ebben a dokumentumban megtudhatja, hogyan teheti meg.

## <a name="high-available-architecture-for-signalr-service"></a>Magas rendelkezésre álló architektúra a SignalR szolgáltatáshoz

Annak érdekében, hogy a SignalR szolgáltatás régióközi rugalmasságát, több szolgáltatáspéldányt kell beállítania a különböző régiókban. Így ha az egyik régió nem, a többi biztonsági mentésként is használható.
Ha több szolgáltatáspéldányt csatlakoztat az alkalmazáskiszolgálóhoz, két szerepkör van, elsődleges és másodlagos.
Elsődleges egy olyan példány, aki vesz online forgalom és a másodlagos egy teljesen működőképes, de biztonsági mentési példány elsődleges.
Az SDK-implementációban az egyeztetés csak az elsődleges végpontokat adja vissza, így normál esetben az ügyfelek csak elsődleges végpontokhoz csatlakoznak.
De ha az elsődleges példány nem, az egyeztetés másodlagos végpontokat ad vissza, így az ügyfél továbbra is kapcsolatokat hozhat létre.
Az elsődleges példány és az alkalmazáskiszolgáló normál kiszolgálókapcsolatokon keresztül csatlakozik, de a másodlagos példány és az alkalmazáskiszolgáló egy speciális típusú kapcsolaton keresztül, az úgynevezett gyenge kapcsolaton keresztül.
A gyenge kapcsolat fő különbsége az, hogy nem fogadja el az ügyfélkapcsolat-útválasztást, mivel a másodlagos példány egy másik régióban található. Az ügyfél másik régióba történő átirányítása nem optimális választás (növeli a késést).

Egy szolgáltatáspéldány különböző szerepköröket rendelkezhet, ha több alkalmazáskiszolgálóhoz csatlakozik.
A régiók közötti forgatókönyv egyik tipikus beállítása, hogy két (vagy több) SignalR szolgáltatáspéldány- és alkalmazáskiszolgáló-párja van.
Belül minden pár alkalmazás szerver és SignalR szolgáltatás található ugyanabban a régióban, és signalr szolgáltatás csatlakozik az alkalmazáskiszolgálóelsődleges szerepkörként.
Az egyes párok között az alkalmazáskiszolgáló és a SignalR szolgáltatás is csatlakozik, de a SignalR másodlagossá válik, amikor egy másik régióban lévő kiszolgálóhoz csatlakozik.

Ezzel a topológiával az egyik kiszolgálóról érkező üzenetek továbbra is kézbesíthetők az összes ügyfélnek, mivel az összes alkalmazáskiszolgáló és SignalR szolgáltatáspéldány össze van kapcsolva.
De ha egy ügyfél csatlakozik, mindig az alkalmazáskiszolgálóugyanabban a régióban optimális hálózati késés elérése érdekében.

Az alábbi ábrán látható az ilyen topológia:

![topology](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Alkalmazáskiszolgálók konfigurálása több SignalR szolgáltatáspéldánysal

Miután az egyes régiókban létrehozott SignalR-szolgáltatás- és alkalmazáskiszolgálókat létrehozott, beállíthatja, hogy az alkalmazáskiszolgálók az összes SignalR szolgáltatáspéldányhoz csatlakozzanak.

Ezt kétféleképpen teheti meg:

### <a name="through-config"></a>A config

Már tudja, hogyan állíthatja be a SignalR szolgáltatás kapcsolati karakterláncát a környezeti változókon/alkalmazásbeállításokon/web.cofig-on keresztül, egy konfigurációs `Azure:SignalR:ConnectionString`bejegyzésben.
Ha több végpontja van, több konfigurációs bejegyzésben is beállíthat, mindegyik a következő formátumban:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Itt `<name>` van a végpont neve, és `<role>` a szerepe (elsődleges vagy másodlagos).
A név nem kötelező, de hasznos lesz, ha tovább szeretné szabni az útválasztási viselkedést több végpont között.

### <a name="through-code"></a>Kódon keresztül

Ha a kapcsolati karakterláncokat máshol szeretné tárolni, akkor a kódban is elolvashatja őket, és paraméterekként használhatja őket híváskor `AddAzureSignalR()` (ASP.NET Core-ban) vagy `MapAzureSignalR()` (ASP.NET).

Itt van a minta kód:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

Több elsődleges vagy másodlagos példány is konfigurálható. Ha több elsődleges és/vagy másodlagos példány van, az egyeztetés a következő sorrendben ad vissza egy végpontot:

1. Ha legalább egy elsődleges példány online állapotban van, adja vissza egy véletlenszerű en-online példányt.
2. Ha az összes elsődleges példány nem működik, adja vissza a véletlenszerű másodlagos online példány.

## <a name="failover-sequence-and-best-practice"></a>Feladatátvételi sorrend és ajánlott eljárás

Most már a megfelelő rendszer topológia beállítás. Amikor egy SignalR szolgáltatáspéldány nem működik, az online forgalom más példányokba lesz irányítva.
Itt történik, ha egy elsődleges példány nem található (és egy idő után helyreáll):

1. Az elsődleges szolgáltatáspéldány nem működik, a példány összes kiszolgálói kapcsolata megszakad.
2. A példányhoz csatlakoztatott összes kiszolgáló kapcsolat nélküliként jelöli meg, és az egyeztetés leállítja a végpont visszaadását, és elindítja a másodlagos végpont ot.
3. A példány összes ügyfélkapcsolata is le lesz zárva, az ügyfelek újra csatlakoznak. Mivel az alkalmazáskiszolgálók most másodlagos végpontot adnak vissza, az ügyfelek másodlagos példányhoz fognak csatlakozni.
4. Most másodlagos példány veszi az összes online forgalmat. A kiszolgálóról az ügyfelekre érkező összes üzenet továbbra is kézbesíthető, mivel másodlagosan csatlakozik az összes alkalmazáskiszolgálóhoz. Az ügyfél-kiszolgáló üzenetek azonban csak ugyanabban a régióban vannak átirányítva az alkalmazáskiszolgálóra.
5. Az elsődleges példány helyreállítása és online állapotba kerülése után az alkalmazáskiszolgáló újra létrehozza a kapcsolatot, és onlineként jelöli meg. A Negotiate most ismét visszaadja az elsődleges végpontot, így az új ügyfelek újra csatlakoznak az elsődlegeshez. De a meglévő ügyfelek nem lesz nekik, és továbbra is átirányítja a másodlagos, amíg nem bontják magukat.

Az alábbi ábrák bemutatják, hogyan történik a feladatátvétel a SignalR szolgáltatásban:

A feladatátvétel ![előtt.](media/signalr-concept-disaster-recovery/before-failover.png)

A feladatátvétel után ![a feladatátvétel után](media/signalr-concept-disaster-recovery/after-failover.png)

Rövid idő vel az elsődleges ![helyreállítás után Rövid idő vel az elsődleges visszanyerés után](media/signalr-concept-disaster-recovery/after-recover.png)

Normál esetben csak az elsődleges alkalmazáskiszolgáló és a SignalR szolgáltatás rendelkezik online forgalommal (kék színnel).
Feladatátvétel után a másodlagos alkalmazáskiszolgáló és a SignalR-szolgáltatás is aktívvá válik.
Miután az elsődleges SignalR szolgáltatás újra online állapotba került, az új ügyfelek csatlakoznak az elsődleges SignalR-hez. De a meglévő ügyfelek továbbra is másodlagos, így mindkét példány forgalmat.
Miután az összes meglévő ügyfél bontja a kapcsolatot, a rendszer visszatér a normális kerékvágásba (1. ábra).

Két fő minták végrehajtására régióközi magas rendelkezésre álló architektúra:

1. Az első az, hogy egy pár app szerver és SignalR szolgáltatás példány vesz minden online forgalmat, és egy másik pár, mint egy biztonsági mentés (úgynevezett aktív / passzív, illusztrált Ábra 1). 
2. A másik az, hogy két (vagy több) pár alkalmazásszerverek és SignalR szolgáltatás példányok, mindegyik részt vesz az online forgalom és szolgál biztonsági másolatot más párok (úgynevezett aktív / aktív, hasonló a 3. ábra).

A SignalR szolgáltatás mindkét mintát támogatja, a fő különbség az, hogy hogyan valósítja meg az alkalmazáskiszolgálókat.
Ha az alkalmazáskiszolgálók aktívak/passzívak, a SignalR szolgáltatás is aktív/passzív lesz (mivel az elsődleges alkalmazáskiszolgáló csak az elsődleges SignalR szolgáltatáspéldányt adja vissza).
Ha az alkalmazáskiszolgálók aktívak/aktívak, a SignalR szolgáltatás is aktív/aktív lesz (mivel az összes alkalmazáskiszolgáló saját elsődleges SignalR-példányait adja vissza, így mindegyikük forgalmat kaphat).

Meg kell jegyezni, függetlenül attól, hogy milyen mintákat úgy dönt, hogy használja, akkor kell csatlakoztatni az egyes SignalR szolgáltatáspéldányok egy alkalmazáskiszolgáló elsődleges.

A SignalR-kapcsolat jellege miatt (ez egy hosszú kapcsolat), az ügyfelek kapcsolatcsökkenést tapasztalnak, ha katasztrófa történik, és feladatátvétel történik.
Az ilyen eseteket ügyféloldalon kell kezelnie, hogy azok átláthatóak legyenek a végfelhasználók számára. Például a kapcsolat bezárása után csatlakozzon újra.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja az alkalmazást a SignalR szolgáltatás rugalmasságának elérése érdekében. Ha további részleteket szeretne megtudni a SignalR szolgáltatás kiszolgáló-ügyfél kapcsolatáról és kapcsolat-útválasztásáról, olvassa el ezt a [cikket](signalr-concept-internals.md) a SignalR szolgáltatás belső adatairól.

A méretezési forgatókönyvek, például a horizontális skálázás, amely több példányegyütt kezeli a nagy számú kapcsolatok, olvassa el, [hogyan skálázhatja több példányok.](signalr-howto-scale-multi-instances.md)

---
title: Az Azure SignalR Service rugalmasság és a katasztrófa utáni helyreállítás
description: Áttekintés arról, hogyan állítható be több SignalR-szolgáltatáspéldány rugalmasság és a vészhelyreállítás érdekében
author: chenkennt
ms.service: signalr
ms.topic: overview
ms.date: 01/29/2019
ms.author: kenchen
ms.openlocfilehash: 8852bd5ef9dbe666abebfdc64a31faa135c9b8b6
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569419"
---
# <a name="resiliency-and-disaster-recovery"></a>Rugalmasság és a vészhelyreállítás

Rugalmasság és a vészhelyreállítás online rendszerek közös szüksége. Az Azure SignalR Service már garantálja a 99,9 %-os rendelkezésre állást, de továbbra is egy regionális szolgáltatás.
A szolgáltatáspéldány mindig fut egy adott régióban, és a egy régióra kiterjedő szolgáltatáskimaradás esetén nem fog a feladatátvételt egy másik régióba.

Ehelyett az SDK szolgáltatás támogatja a több SignalR-szolgáltatáspéldány, és automatikusan átvált a többi példány, ha ezek közül néhány nem áll rendelkezésre a funkcionalitást biztosít.
Ezzel a funkcióval helyreállíthatja, ha katasztrófa történik, de a megfelelő topológia beállítása saját magának kell kell lennie. Ennek módját a jelen dokumentum megtudhatja.

## <a name="high-available-architecture-for-signalr-service"></a>A SignalR service magas rendelkezésre álló architektúra

Rendelkezik a régiók közötti rugalmasság a SignalR service, különböző régiókban lévő több szolgáltatáspéldány beállítása kell. Így ha egy adott régióban nem működik, a többi használható biztonsági.
Több szolgáltatáspéldány alkalmazás kiszolgálóhoz való csatlakozáshoz, két szerepkörök tartoznak, az elsődleges és másodlagos.
Egy példányt, akik online forgalmat tart az elsődleges és másodlagos egy teljesen működőképes, de biztonsági mentési példány elsődleges.
SDK megvalósítása fog egyeztetni csak így normális esetben az ügyfelek csak csatlakozni elsődleges végpontok elsődleges végpontok adja vissza.
Azonban az elsődleges példány nem működik, ha fog egyeztetni másodlagos végpontok adja vissza, hogy a ügyfél továbbra is kapcsolatok.
Elsődleges példány és az alkalmazáskiszolgáló normál kiszolgáló kapcsolatokon keresztül csatlakoznak, de a másodlagos példány és az alkalmazás server össze egy különleges gyenge kapcsolatok nevű kapcsolat.
A gyenge kapcsolat fő különbség az, hogy nem fogadni ügyfél kapcsolat útválasztás, mert a másodlagos példány általában egy másik régióban található. Útválasztás az ügyfél egy másik régióba általában nem használhatók, az optimális választás (növeli a késés).

Egy szolgáltatáspéldány különböző szerepkörökkel rendelkezhetnek, több alkalmazás-kiszolgálóhoz való csatlakozáskor.
Több régióban a forgatókönyvben egy szokásos telepítése, hogy két (vagy több) pár SignalR service-példányt és alkalmazások kiszolgálói.
Minden egyes pár alkalmazáson belül kiszolgáló és a SignalR service találhatók ugyanabban a régióban, és a SignalR service van csatlakoztatva, egészen az alkalmazáskiszolgálóig elsődleges szerepkör.
Minden egyes párok alkalmazás közötti kiszolgáló és a SignalR service is csatlakoztatva vannak, de a SignalR válik egy másodlagos kiszolgáló egy másik régióban való csatlakozáskor.

Ebben a topológiában az egyik kiszolgálóról is továbbra is lehet elküldeni az üzenetet, minden ügyfélnek az összes alkalmazás-kiszolgálóként, és a SignalR szolgáltatáspéldányok vannak összekapcsolva.
De amikor egy ügyfél kapcsolódik, akkor mindig egészen az alkalmazáskiszolgálóig optimális hálózati késés elérése ugyanabban a régióban legyen irányítva.

Az alábbi, egy diagram, amely az ilyen topológiát mutatja be:

![topology](media/signalr-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Több SignalR-szolgáltatáspéldány az alkalmazás-kiszolgálók konfigurálása

Miután az egyes régiókban létrehozott SignalR service és az alkalmazás kiszolgálók, az összes SignalR service-példányokhoz való csatlakozáshoz, az alkalmazáskiszolgálók is beállíthatja.

Ezt megteheti két módja van:

### <a name="through-config"></a>Config keresztül

Már tudnia kell, hogyan állíthatja be a környezeti változók és alkalmazástelepítés settings/web.cofig keresztül egy konfigurációs bejegyzés nevű keresztül a SignalR szolgáltatás kapcsolati karakterláncának `Azure:SignalR:ConnectionString`.
Ha több végpontot, állítsa be őket a több konfigurációs bejegyzéseket, minden, a következő formátumban:

```
Azure:SignalR:Connection:<name>:<role>
```

Itt `<name>` a végpont neve és `<role>` van a szerepkör (elsődleges vagy másodlagos).
Név megadása nem kötelező, de lesz hasznos, ha szeretne további testreszabási több végpontok között az útválasztási viselkedés.

### <a name="through-code"></a>Kódok segítségével

Ha inkább a kapcsolati karakterlánc valahol máshol tárolhatja, is a kódban, annak olvasása és hívásakor használja őket paraméterekként `AddAzureSignalR()` (az ASP.NET Core) vagy `MapAzureSignalR()` (az ASP.NET-ben).

A mintakód a következő:

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

## <a name="failover-sequence-and-best-practice"></a>Feladatátvétel és az ajánlott eljárás

Most már rendelkezik a megfelelő system topológia telepítése. Minden alkalommal, amikor egy SignalR-szolgáltatáspéldány nem működik, online forgalmat továbbítja a többi példány.
Íme, mi történik, ha az elsődleges példányhoz nem működik (és némi várakozás után állítja helyre):

1. Elsődleges service-példány nem működik, az összes kiszolgáló kapcsolatokat ezen a példányon a rendszer eldobja.
2. Ehhez a példányhoz kapcsolódó összes kiszolgáló megjelölése offline állapotban van, és fog egyeztetni visszaadó Ez a végpont leállítása és elindítása a másodlagos végpont visszaadása.
3. Minden olyan ügyfélkapcsolatot ezen a példányon is lezárul, az ügyfelek csatlakozik újra. Kiszolgálók másodlagos végpont visszatérve, mivel az ügyfelek másodlagos példány fog csatlakozni.
4. Most már a másodlagos példány összes online forgalmat vesz igénybe. Összes üzenet-kiszolgálóról az ügyfeleknek továbbra is letöltéséhez másodlagos összes alkalmazás-kiszolgáló csatlakozik. De az ügyfél és kiszolgáló üzeneteket a rendszer csak irányítja az app-kiszolgáló ugyanabban a régióban.
5. Miután az elsődleges példány helyreállított és online állapotba, alkalmazás kiszolgáló helyreállítani a kapcsolatot, és jelölje meg az online. Az egyeztetés lesz most visszatérési elsődleges végpont újra vissza az elsődleges így az új ügyfelek csatlakoznak. De a meglévő ügyfeleket nem dobható el, és továbbra is mindaddig, amíg azok leválasztása magukat a másodlagos csatlakoztatott.

Alábbi ábrák bemutatják, hogyan feladatátvételi SignalR service-ben történik:

Feladatátvétel Fig.1 előtt ![feladatátvétel előtt](media/signalr-disaster-recovery/before-failover.png)

Feladatátvétel Fig.2 után ![feladatátvétel után](media/signalr-disaster-recovery/after-failover.png)

Fig.3 rövid idő után elsődleges helyreáll ![rövid idő után elsődleges helyreállítása](media/signalr-disaster-recovery/after-recover.png)

Normál esetben csak az elsődleges alkalmazás kiszolgáló látható, és a SignalR service rendelkezik online forgalmat (kék).
A feladatátvételt követően másodlagos alkalmazások kiszolgálói és a SignalR service is aktívvá válik.
Miután az elsődleges SignalR service újra online állapotba kerül, új ügyfeleket az elsődleges SignalR fog csatlakozni. De a meglévő ügyfelek továbbra is csatlakoznak másodlagos így példány is a forgalom.
Amikor az összes meglévő ügyfelek kapcsolat bontása, a rendszer vissza a normál értékre (Fig.1) lesz.

Nincsenek a régiók közötti magas rendelkezésre álló architektúra megvalósítása a két fő minta:

1. Az elsőt az, hogy az alkalmazás-kiszolgáló és a SignalR service-példány összes online forgalmat véve párjai van, és egy másik pár biztonsági (aktív/passzív nevű, ahogyan Fig.1). 
2. A másik egy egyik alkalmazás kiszolgálók és a SignalR szolgáltatáspéldányok két (vagy több) párok, az online forgalmat és szolgál egy véve irányító biztonsági más párokhoz (aktív/aktív, Fig.3 hasonló nevű).

SignalR service támogathatja mind a mintákat, a fő különbség hogyan valósítható meg az alkalmazások kiszolgálói.
Ha alkalmazás-kiszolgálók aktív/passzív, SignalR service is aktív/passzív (amint az elsődleges alkalmazás server csak a elsődleges SignalR service példányát adja vissza).
Ha alkalmazás-kiszolgálók aktív-aktív, SignalR service is aktív/aktív (az összes alkalmazás-kiszolgálók a saját elsődleges SignalR-példányok ad vissza, így ezek mindegyike forgalom).

Meg kell említeni függetlenül attól, milyen minták használatát választja, meg kell kiszolgálóhoz való csatlakozáshoz minden SignalR szolgáltatáspéldány egy alkalmazás elsődlegesként.

Is (Ez a hosszú kapcsolat) SignalR kapcsolat jellege miatt az ügyfelek fog tapasztalni kapcsolat csepp Ha katasztrófa és feladatátvételi kerül sor.
Ügyfél oldalán, hogy az átlátható legyen a végfelhasználókat az ilyen esetek kezeléséhez szükséges. Ha például újból kapcsolat bezárása után.

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan rugalmasság a SignalR service-alkalmazás konfigurálása rendelkezik. Szeretné megtudni, kiszolgáló vagy ügyfél-csatlakozási és a SignalR service-ben útválasztási kapcsolat kapcsolatos további részletekért, beolvashatja [Ez a cikk](signalr-internals.md) a SignalR service belső elemei.
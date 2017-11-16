---
title: "A nézet az Azure Traffic Manager forgalom |} Microsoft Docs"
description: "A Traffic Manager forgalom nézet bemutatása"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 11/11/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: 6b4378cb293824702dd52dcdeb86619f957b83ea
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="traffic-manager-traffic-view"></a>A TRAFFIC Manager forgalom megtekintése

>[!NOTE]
>A forgalom nézet funkció a Traffic Manager nyilvános előzetes verziójában, és nem rendelkezhet azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatások, amelyek általában a rendelkezésre állási kiadási. A szolgáltatás nem támogatott, van, korlátozott képességeket, és előfordulhat, hogy nem érhető el az összes Azure helyét. A rendelkezésre állás és a szolgáltatás állapotát a legfrissebb értesítések, tekintse meg a [Azure Traffic Manager-frissítések](https://azure.microsoft.com/updates/?product=traffic-manager) lap.

A TRAFFIC Manager biztosít a DNS szintű útválasztási, hogy a végfelhasználók számára a rendszer átirányítja az útválasztási módszer alapján kifogástalan állapotú végpontok megadott a profil létrehozásakor. Forgalom nézet Traffic Manager biztosít a felhasználói körrel (szinten DNS feloldó granularitási) és a forgalom mintát nézetét. Ha engedélyezi a forgalom nézet, ezek az információk biztosítja, hogy a gyakorlatban használható elemzések dolgoz fel. 

Forgalom nézet használatával a következő műveletek végezhetők el:
- Ismerje meg, ahol a felhasználói körrel találhatók (akár a helyi DNS feloldó szintű részletesség).
- megtekintheti az (Azure Traffic Manager által kezelt DNS-lekérdezések megfigyelt) forgalom mennyiségét e régiók származó.
- Mi az, hogy ezek a felhasználók által tapasztalt reprezentatív késését betekintést beolvasása.
- részletes bemutatója be az adott forgalmat mindegyik ezek felhasználói körrel az Azure-régiók végpontok esetében. 

Például forgalom nézet használatával megérthetik, hogy mely régiókat forgalom sok, de érinti a magasabb késések fordulnak elő. A következő az ezek az információk segítségével megtervezheti a erőforrásigényét bővítése új Azure-régiók a, így ezek a felhasználók egy kisebb késést élmény.

## <a name="how-traffic-view-works"></a>Hogyan működik a forgalom megtekintése

Forgalom nézet azzal, hogy az elmúlt hét napban ellen, hogy ez a funkció engedélyezve van a bejövő lekérdezések tekintse meg a Traffic Manager működik. A bejövő lekérdezések, információk forgalom nézet kibontja a DNS-feloldási helyét, a felhasználóknak a megjelenítése, amely a forrás IP-címe. Ezek ezután sorolhatók DNS feloldó szintű részletességű összegzése felhasználói alap régiók létrehozása a földrajzi információk Traffic Manager által kezelt IP-címek használatával. A TRAFFIC Manager ezután ellenőrzi, hogy az Azure-régiókban, amelyhez a lekérdezés lett irányítva, és azokban a régiókban felhasználók forgalom folyamata leképezést hoz létre.  
A következő lépésben a Traffic Manager felel meg a felhasználó Azure-régió, leképezés a hálózati eszközintelligencia késés táblákhoz tart fenn a különböző végfelhasználói hálózatok tudni, hogy az adott helyre. a felhasználók által tapasztalt átlagos várakozási alap terület során Csatlakozás Azure-régiók. Ezeket a számításokat majd a rendszer kombinálja a helyi DNS feloldó IP szintek előtt jelölőnégyzetként jelenik meg. A különböző módokon információkat is felhasználhatnak.

>[!NOTE]
>A forgalom nézetben leírt várakozási egy reprezentatív késés a végfelhasználó és az Azure-régiók amelyhez volt csatlakoztatva a között, és nem a DNS-keresési késés.

## <a name="visual-overview"></a>Visual áttekintése

Kiválasztásakor a **forgalom nézet** szakasz a Traffic Manager oldalon lehetősége lesz a forgalom nézet insights az átfedés földrajzi térképként. A térkép a felhasználói bázis és a Traffic Manager-profil végpontjainak kapcsolatos információkat biztosít.

### <a name="user-base-information"></a>Alapszintű felhasználói adatok

Ezen helyi DNS feloldókat, hogy melyik hely áll rendelkezésre információ azok megjelennek a térképen. A DNS-feloldási színének azt jelzi, hogy a végfelhasználók számára, akik az adott DNS-feloldási használatos a Traffic Manager-lekérdezések által tapasztalt átlagos várakozási.

Ha a leképezés egy DNS feloldó helyre mutat, azt jeleníti meg a következő műveleteket:
- a DNS-feloldási IP-címe
- a Traffic Manager által látható, a DNS-lekérdezés forgalom mennyiségét
- a végpontok, mely a DNS-forgalom a feloldó lett irányítva, a végpont és a DNS-feloldási között vonalként 
- átlagosan ilyen késéssel jár arról a helyről a végponthoz,-ként csatlakoztatja őket vonal színe

### <a name="endpoint-information"></a>Végpont

A térkép kék pontokkal, amelyben a végpontok találhatók az Azure-régiók jelennek meg. Kattintson a tetszőleges végpontot (a használt DNS-feloldási alapján) a különböző helyeken megjelenítéséhez a ahol irányított forgalom az adott végpontra. A kapcsolatok a végpont és a DNS-feloldási helye között vonalként látható, és megfelelően közötti adott pár reprezentatív késés színe. Ezenkívül nevét, valamint a végpont, az Azure-régió, amelyben fut, a teljes kötet kérelem átirányításához volt rá a Traffic Manager-profil által látható.


## <a name="tabular-listing-and-raw-data-download"></a>Táblázatos listázása és a nyers adatok letöltése

A forgalom nézet adatainak táblázatos formában Azure-portálon tekintheti meg. Minden DNS-feloldási IP-cím bejegyzését / végpont pár, amely a földrajzi helyet, a DNS-feloldási (ha elérhető), a neve, az Azure-régió, ahol a végpont is található, a kérések mennyisége a DNS-feloldási és társított képviselője a késés társított a végfelhasználók számára, hogy a DNS használatával (ha elérhető). Emellett letöltheti a forgalom nézet adatainak, egy CSV-fájlt egy tetszés szerinti analytics munkafolyamat részeként használható.

## <a name="billing"></a>Számlázás

Forgalom nézet használatakor számlázása a megjelenített elemzések létrehozásához használt adatpontok száma alapján. Az egyetlen pont használt adattípuson jelenleg a lekérdezések a Traffic Manager-profil ellen. Az árakkal kapcsolatos további részletekért látogasson el a [árképzést ismertető oldalra Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, [Traffic Manager működése](traffic-manager-overview.md)
- További információ a [forgalom-útválasztási módszert](traffic-manager-routing-methods.md) Traffic Manager által támogatott
- Megtudhatja, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)


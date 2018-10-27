---
title: Forgalomnézet az Azure Traffic Managerben |} A Microsoft Docs
description: Bevezetés a Traffic Manager Forgalomáttekintője
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: jeconnoc
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: d1800fea2212628e7647b5250efa33ebb97957f9
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138072"
---
# <a name="traffic-manager-traffic-view"></a>Traffic Manager Forgalomáttekintője

A TRAFFIC Manager biztosít, a DNS-szintű útválasztás, hogy a végfelhasználók számára a rendszer átirányítja az útválasztási módszer alapján kifogástalan állapotú végpontok létrehozásakor lett megadva a profilban. A TRAFFIC View Traffic Manager (a DNS szintjén feloldó részletesség) a felhasználói körrel, és azok forgalmi minták nézetet biztosít. Ha engedélyezi a Traffic View, így gyakorlatban hasznosítható elemzéseket nyújt ezeket az adatokat dolgoz fel. 

A Traffic View használatával a következőket teheti:
- Ismerje meg, ahol a felhasználói közösségeik (a helyi DNS-feloldói szintű részletességig).
- megtekintheti a mennyiségű forgalmat (az Azure Traffic Manager által kezelt DNS-lekérdezések, megtartva) származik az ezekben a régiókban.
- Mi az, hogy ezek a felhasználók által tapasztalt késések elemzéseket.
- mélyreható irányuló forgalmi az egyes felhasználói Közösségektől az Azure-régióban, ahol végpontok rendelkezik. 

Használhatja például a Traffic View tudni, mely régiókban rendelkezik a forgalom nagy számú, de nagyobb késleltetéssel jár. sorból. Ezután ezen információk használatával az erőforrás-igényű bővítése új Azure-régiókhoz való tervezi, hogy ezek a felhasználók rendelkezhetnek egy alacsonyabb késést élményt.

## <a name="how-traffic-view-works"></a>A Traffic View működése

A TRAFFIC View úgy működik, hogy a Traffic Manager, tekintse meg a bejövő lekérdezések elleni egy profilt, amely rendelkezik a szolgáltatás nincs engedélyezve az elmúlt hét napban érkezett. A bejövő lekérdezések alapján a Traffic View kibontja a forrás IP-címét a DNS-feloldó, amely a felhasználók helye reprezentációját. Ezek ezután egy csoportba kerülnek, a DNS-feloldói szintű részletességig alap régiók felhasználó létrehozása a földrajzi információk a Traffic Manager által kezelt IP-címek használatával. Majd megvizsgálja a TRAFFIC Manager az Azure-régiók, amelyhez a lekérdezés lett irányítva, és létrehoz egy traffic folyamat térkép ezekben a régiókban a felhasználók számára.  
A következő lépésben a Traffic Manager utal. a felhasználó alap régióban az Azure-régió-lel hálózati intelligencia késés táblákkal, amely a karbantartott különböző végfelhasználói hálózatok ezekben a régiókban a felhasználók által tapasztalt átlagos késése ismertetése során Csatlakozás Azure-régióban. Ezeket a számításokat majd kombinálják a helyi DNS-feloldó IP szint előtt, megjelenik egy. Az adatokat különböző módon használhatja fel.

>[!NOTE]
>A Traffic View ismertetett késés egy reprezentatív késés a végfelhasználó és az Azure-régiók, amelyhez kellett kapcsolódnak, és nem a DNS-keresési késés. Forgalmi nézet teszi egy ajánlott beavatkozást becslése a késés, a helyi DNS-feloldási és a lekérdezés lett irányítva, ha nincs elegendő adat az Azure-régió között, akkor a várakozási visszaadott null értékű lesz. 

## <a name="visual-overview"></a>Vizuális áttekintése

Kiválasztásakor az **Traffic View** szakasz a Traffic Manager oldalon lehetősége lesz a Traffic View insights az átfedés földrajzi térképen. A térkép a felhasználói bázis és a Traffic Manager-profilhoz tartozó végpontok ismerteti.

### <a name="user-base-information"></a>Alapszintű felhasználói adatok

Ezek helyi DNS feloldók, hogy melyik hely információ áll rendelkezésre azok megjelennek a térképen. A DNS-feloldási színét azt jelzi, hogy a Traffic Manager lekérdezéseit a DNS-feloldási használt végfelhasználók által tapasztalt átlagos késése.

Ha a kurzort egy DNS-feloldási helye a térkép, ez látható:
- a DNS-feloldó IP-címe
- a Traffic Manager által látható, a DNS-lekérdezés forgalom mennyisége
- a végpontok a DNS szolgáltatásból forgalom feloldó átirányítása, a végpont és a DNS-feloldási között vonal 
- erről a helyről a végponthoz, őket vonal színe-kiszolgálókként átlagos késése

### <a name="endpoint-information"></a>Végpont

Az Azure-régióban, amelyben megtalálhatók a végpontok a térkép a kék pontok jelennek meg. Ha a végpont külső, és nem rendelkezik egy Azure-régió rendelve, a térkép tetején jelenik meg. Kattintson bármely végpont megtekintéséhez a különböző helyeken (a használt DNS-feloldási alapján) ahol forgalmat irányított, hogy a végpont. A kapcsolatok egy sort a végpont és a DNS-feloldási helye között jelennek meg, és késések között adott pár megfelelően színe. Emellett megjelenik a végpontot, az Azure-régió, amelyben fut. és a teljes felé irányuló kérések mennyiségét, amely utasítása, a Traffic Manager-profil által nevét.


## <a name="tabular-listing-and-raw-data-download"></a>Táblázatos és a nyers adatok letöltése

A Traffic View adatok táblázatos formában az Azure Portalon tekintheti meg. Minden DNS-feloldó IP-címhez bejegyzését / végpontot, amely párosítsa jeleníti meg az IP-címet a DNS-feloldó, nevét és az Azure-régióhoz a tartózkodási helyét, ahol a végpont található (ha elérhető), a társított, hogy a DNS-feloldási kérések mennyisége hogy a végpont, és késések társított a végfelhasználók számára, hogy a DNS-sel (ahol elérhető). A Traffic View data egy tetszőleges elemzési munkafolyamat részeként használt CSV-fájlként is letöltheti.

## <a name="billing"></a>Számlázás

A Traffic View használatakor jelenik meg az elemzések létrehozásához használt adatpontok száma alapján számlázzuk. Az egyetlen használt adatponttípus jelenleg a Traffic Manager-profil elleni fogadott lekérdezések. A díjszabás a további részletekért látogasson el a [díjszabását ismertető lapon a Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>További lépések

- Ismerje meg, [Traffic Manager működése](traffic-manager-overview.md)
- Tudjon meg többet a [forgalom-útválasztási módszerek](traffic-manager-routing-methods.md) a Traffic Manager által támogatott
- Ismerje meg, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)


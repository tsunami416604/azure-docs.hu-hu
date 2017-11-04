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
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: 0a07ff578c6afeedc6f3806d52bfe5aef6945c04
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="traffic-manager-traffic-view"></a>A TRAFFIC Manager forgalom megtekintése

>[!NOTE]
>A forgalom nézet funkció a Traffic Manager nyilvános előzetes verziójában, és nem rendelkezhet azonos szintű rendelkezésre állást és megbízhatóságot, szolgáltatások, amelyek általában a rendelkezésre állási kiadási. A szolgáltatás nem támogatott, van, korlátozott képességeket, és előfordulhat, hogy nem érhető el az összes Azure helyét. A rendelkezésre állás és a szolgáltatás állapotát a legfrissebb értesítések, tekintse meg a [Azure Traffic Manager-frissítések](https://azure.microsoft.com/updates/?product=traffic-manager) lap.

A TRAFFIC Manager DNS szintű útválasztási, hogy a végfelhasználók számára a rendszer átirányítja a kellett állít be a profil létrehozásakor útválasztási módszer alapján kifogástalan állapotú végpontok biztosít. A Traffic Manager biztosít a felhasználói körrel (szinten DNs feloldó granularitási) és a forgalom mintát nézetét. Ha engedélyezi a forgalom nézet, ezek az információk biztosítja, hogy a gyakorlatban használható elemzések dolgoz fel. 

Forgalom nézet használatával a következő műveletek végezhetők el:
- Ismerje meg, ahol a felhasználói körrel találhatók (akár a helyi DNS feloldó szintű részletesség).
- megtekintheti az (Azure Traffic Manager által kezelt DNS-lekérdezések megfigyelt) forgalom mennyiségét e régiók származó.
-  Mi az, hogy ezek a felhasználók által tapasztalt reprezentatív késését betekintést beolvasása.
- részletes bemutatója be az adott forgalmat mindegyik ezek felhasználói körrel az Azure-régiók végpontok esetében. 

Például forgalom nézet használatával megérthetik, hogy mely régiókat forgalom sok, de érinti a magasabb késések fordulnak elő. Ezután az ezt az információt segítségével megtervezheti a erőforrásigényét bővítése új Azure-régiók a, így ezek a felhasználók egy kisebb késést élmény.

## <a name="how-traffic-view-works"></a>Hogyan működik a forgalom megtekintése

Forgalom nézet azzal, hogy az elmúlt hét napban ellen, hogy ez a funkció engedélyezve van a bejövő lekérdezések tekintse meg a Traffic Manager működik. Ezekből az adatokból bontja ki a DNS-feloldó, majd a helyét, a felhasználók megjelenítése, amely a forrás IP-címe. Ezek ezután sorolhatók DNS feloldó szintű részletességű összegzése felhasználói alap régiók létrehozása a földrajzi információk Traffic Manager által kezelt IP-címek használatával. A TRAFFIC Manager ezután ellenőrzi, hogy az Azure-régiókban, amelyhez a lekérdezés lett irányítva, és azokban a régiókban felhasználók forgalom folyamata leképezést hoz létre.
A következő lépésben a Traffic Manager felel meg a felhasználó Azure-régió, leképezés a hálózati eszközintelligencia késés táblákhoz tart fenn különböző végfelhasználói hálózatok tudni, hogy az adott helyre. a felhasználók által tapasztalt átlagos várakozási alap terület során Csatlakozás Azure-régiók. Ezeket a számításokat majd megjelennének a helyi DNS feloldó IP szintek előtt jelölőnégyzetként jelenik meg. Ezt az információt az analytics munkafolyamat az Ön által választott tud feldolgozni, letöltheti CSV-fájlként ezt az információt.
Forgalom nézet használatakor számlázása a megjelenített elemzések létrehozásához használt adatpontok száma alapján. Jelenleg az egyetlen pont használt adattípusa szemben a Traffic Manager-profil a lekérdezések. Az árakkal kapcsolatos további részletekért látogasson el a [árképzést ismertető oldalra Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Következő lépések

- Ismerje meg, [Traffic Manager működése](traffic-manager-overview.md)
- További információ a [forgalom-útválasztási módszert](traffic-manager-routing-methods.md) Traffic Manager által támogatott
- Megtudhatja, hogyan [Traffic Manager-profil létrehozása](traffic-manager-create-profile.md)


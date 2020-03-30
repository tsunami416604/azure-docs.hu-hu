---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 40ba5a935e78cd75c4fcd7729e44f1cdf6c2859b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75773028"
---
Ha problémát észlel a megosztott rendszerkép-katalógusokon, rendszerkép-definíciókon vagy rendszerképverziókon végzett műveletek végrehajtásakor, futtassa újból a sikertelen parancsot hibakeresési módban. A debug mód a **-debug** kapcsoló CLI és a **-Debug** kapcsoló PowerShell lel történő átadásával aktiválódik. Miután megtalálta a hibát, kövesse ezt a dokumentumot a hibák elhárításához.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nem hozható létre megosztott rendszerkép-katalógus

Lehetséges okok:

*A gyűjtemény neve érvénytelen.*

A Galéria nevének megengedett karakterei a nagy- vagy kisbetűk, számjegyek, pontszámok és pont. A gyűjtemény neve nem tartalmazhat kötőjeleket. Módosítsa a galéria nevét, majd próbálkozzon újra. 

*A galéria neve nem egyedi az előfizetésen belül.*

Válasszon másik galérianevet, és próbálkozzon újra.


## <a name="unable-to-create-an-image-definition"></a>Nem hozható létre rendszerkép-definíció 

Lehetséges okok:

*a képdefiníció neve érvénytelen.*

A képdefinícióban megengedett karakterek a kis- vagy nagybetűk, számjegyek, pontok, kötőjelek és pontok. Módosítsa a képdefiníció nevét, majd próbálkozzon újra.

*A lemezkép-definíció létrehozásának kötelező tulajdonságai nincsenek feltöltve.*

A tulajdonságok, például a név, a közzétevő, az ajánlat, a termékváltozat és az operációs rendszer típusa kötelező. Ellenőrizze, hogy az összes tulajdonság át lett-e adva.

Győződjön meg arról, hogy a lemezkép-definíció **OSType**( Linux vagy Windows ) ugyanaz, mint a lemezkép-verzió létrehozásához használt felügyelt forrásrendszer. 


## <a name="unable-to-create-an-image-version"></a>Nem hozható létre rendszerképverzió 

Lehetséges okok:

*A kép verzióneve érvénytelen.*

A képverzió megengedett karakterei számok és időszakok. A számoknak a 32 bites egész szám tartományán belül kell lenniük. Formátum: *MajorVersion.MinorVersion.Patch*. Módosítsa a lemezkép verziónevét, majd próbálkozzon újra.

*A forrás felügyelt lemezképe, amelyből a lemezkép-verzió létrehozása folyamatban van, nem található.* 

Ellenőrizze, hogy a forráslemezkép létezik-e, és ugyanabban a régióban van-e, mint a lemezkép verziója.

*A felügyelt rendszerkép nem történik kiépítés alatt.*

Győződjön meg arról, hogy a forrás felügyelt lemezkép létesítési állapota **sikeres.**

*A célrégió listája nem tartalmazza a forrásrégiót.*

A célrégió-listának tartalmaznia kell a lemezkép-verzió forrásrégióját. Győződjön meg arról, hogy felvette a forrásrégiót azon célrégiók listájába, ahová az Azure replikálja a lemezkép-verziót.

*A replikáció az összes célrégióba nem fejeződött be.*

Használja a **--expand ReplicationStatus** jelzőt annak ellenőrzéséhez, hogy befejeződött-e a replikáció az összes megadott célrégióban. Ha nem, várjon akár 6 órát, amíg a feladat befejeződik. Ha ez nem sikerül, futtassa újra a parancsot a lemezkép-verzió létrehozásához és replikálásához. Ha sok célterület van, ahamely a lemezkép-verzió replikálása folyamatban van, fontolja meg a replikáció fázisokban való lebonyolítását.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nem hozható létre virtuális gép vagy méretezési csoport 

Lehetséges okok:

*A virtuális gép vagy virtuálisgép-méretezési csoport létrehozásához próbál óvó felhasználó nem rendelkezik olvasási hozzáféréssel a lemezkép-verzióhoz.*

Lépjen kapcsolatba az előfizetés tulajdonosával, és kérje meg őket, hogy adjanak olvasási hozzáférést a lemezkép-verzióhoz vagy a szülőerőforrásokhoz (például a megosztott képgyűjteményhez vagy a képdefinícióhoz) a [szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC) segítségével. 

*A lemezkép verziója nem található.*

Ellenőrizze, hogy a virtuális gép vagy virtuálisgép-méretezés létrehozásához kívánt régió szerepel-e a lemezképverzió célterületeinek listájában. Ha a régió már szerepel a célterületek listájában, ellenőrizze, hogy a replikációs feladat befejeződött-e. A **-ReplicationStatus** jelzővel ellenőrizheti, hogy befejeződött-e az összes megadott célterület replikációja. 

*A virtuális gép vagy a virtuális gép méretezési csoport létrehozása hosszú időt vesz igénybe.*

Ellenőrizze, hogy a virtuális gép vagy a virtuálisgép méretezési készletének létrehozásához használni kívánt lemezkép-verzió **OSType típusa** megegyezik-e a lemezkép-verzió létrehozásához használt felügyelt **forráslemezkép operációstípusával.** 

## <a name="unable-to-share-resources"></a>Az erőforrások nem oszthatók meg

A megosztott képgaléria, a képdefiníció és a képverzió-erőforrások előfizetések közötti megosztása engedélyezve van [a szerepköralapú hozzáférés-vezérlés](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC) használatával. 

## <a name="replication-is-slow"></a>A replikáció lassú

Használja a **--expand ReplicationStatus** jelzőt annak ellenőrzéséhez, hogy befejeződött-e a replikáció az összes megadott célrégióban. Ha nem, várjon legfeljebb 6 órát, amíg a feladat befejeződik. Ha ez nem sikerül, indítsa el újra a parancsot a lemezkép-verzió létrehozásához és replikálásához. Ha sok célterület van, ahamely a lemezkép-verzió replikálása folyamatban van, fontolja meg a replikáció fázisokban való lebonyolítását.

## <a name="azure-limits-and-quotas"></a>Az Azure-ra vonatkozó korlátok és kvóták 

[Az Azure-korlátozások és -kvóták](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) az összes megosztott képtárra, képdefinícióra és képverzió-erőforrásra vonatkoznak. Győződjön meg arról, hogy az előfizetések korlátain belül van. 




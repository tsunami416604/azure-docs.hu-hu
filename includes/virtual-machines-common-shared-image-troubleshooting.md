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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75773028"
---
Ha problémát észlel a megosztott rendszerkép-katalógusokon, rendszerkép-definíciókon vagy rendszerképverziókon végzett műveletek végrehajtásakor, futtassa újból a sikertelen parancsot hibakeresési módban. A hibakeresési mód aktiválása a **-Debug** kapcsoló parancssori felülettel való átadásával és a **-Debug** kapcsolóval a PowerShell használatával történik. A hiba megtalálása után kövesse ezt a dokumentumot a hibák elhárításához.


## <a name="unable-to-create-a-shared-image-gallery"></a>Nem hozható létre megosztott rendszerkép-katalógus

Lehetséges okok:

*A katalógus neve érvénytelen.*

A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket. Módosítsa a katalógus nevét, és próbálkozzon újra. 

*A katalógus neve nem egyedi az előfizetésen belül.*

Válasszon egy másik katalógus nevét, és próbálkozzon újra.


## <a name="unable-to-create-an-image-definition"></a>Nem hozható létre rendszerkép-definíció 

Lehetséges okok:

*a rendszerkép definíciójának neve érvénytelen.*

A képdefiníciók megengedett karakterei nagybetűs vagy kisbetűk, számjegyek, pontok, kötőjelek és időszakok. Módosítsa a rendszerkép definíciójának nevét, és próbálkozzon újra.

*A rendszerkép-definíciók létrehozásának kötelező tulajdonságai nincsenek feltöltve.*

A tulajdonságok, például a név, a közzétevő, az ajánlat, az SKU és az operációs rendszer típusa kötelező. Ellenőrizze, hogy az összes tulajdonság át lett-e adva.

Győződjön meg arról, hogy a rendszerkép definíciójának **OSType**(Linux vagy Windows) megegyezik a rendszerkép verziójának létrehozásához használt forrás által felügyelt képpel. 


## <a name="unable-to-create-an-image-version"></a>Nem hozható létre rendszerképverzió 

Lehetséges okok:

*A rendszerkép-verzió neve érvénytelen.*

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion. MinorVersion. patch*. Módosítsa a rendszerkép verziójának nevét, és próbálkozzon újra.

*Nem található a forrás által felügyelt rendszerkép, amelyből a rendszerkép verziója létrejön.* 

Ellenőrizze, hogy a forrás rendszerkép létezik-e, és a rendszerkép verziószámával megegyező régióban található-e.

*A felügyelt rendszerkép nem lett kiépítve.*

Győződjön meg arról, hogy a forrásként kezelt rendszerkép kiépítési állapota **sikeres**.

*A cél régió lista nem tartalmazza a forrás régiót.*

A célként megadott régió listájának tartalmaznia kell a rendszerkép verziójának a forrás régióját. Győződjön meg arról, hogy a forrás régió szerepel a célhelyek listájában, ahol az Azure-ba replikálja a rendszerkép verzióját.

*Az összes megcélzott régióba való replikálás nem fejeződött be.*

A **--Expand ReplicationStatus** jelzővel ellenőrizze, hogy befejeződött-e a replikáció az összes megadott régióban. Ha nem, várjon akár 6 órát, amíg a feladatok befejeződik. Ha nem sikerül, futtassa újra a parancsot a rendszerkép verziójának létrehozásához és replikálásához. Ha sok célpont van a lemezkép verziójának replikálására, a replikálást fázisokban kell elvégezni.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Nem hozható létre virtuális gép vagy méretezési csoport 

Lehetséges okok:

*A virtuális gépet vagy virtuálisgép-méretezési készletet próbáló felhasználónak nincs olvasási hozzáférése a rendszerkép verziójához.*

Lépjen kapcsolatba az előfizetés tulajdonosával, és kérje meg őket, hogy adjon olvasási hozzáférést a képverzióhoz vagy a szülő erőforrásokhoz (például a megosztott képkatalógus vagy a rendszerkép definíciója) a [szerepköralapú Access Control](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC) használatával. 

*A rendszerkép verziója nem található.*

Győződjön meg arról, hogy a virtuális gép vagy virtuálisgép-méretezést létrehozni próbáló régió szerepel a rendszerkép verziószámának megcélzott régiói listáján. Ha a régió már szerepel a célcsoportok listáján, akkor ellenőrizze, hogy befejeződött-e a replikálási feladatok. A **-ReplicationStatus** jelzővel ellenőrizze, hogy a replikáció az összes megadott régióban befejeződött-e. 

*A virtuális gép vagy a virtuálisgép-méretezési csoport létrehozása hosszú időt vesz igénybe.*

Ellenőrizze, hogy a virtuális gép vagy virtuálisgép-méretezési csoport létrehozásához használni kívánt **OSType** megegyezik-e a rendszerkép verziójának létrehozásához használt forrás által felügyelt **OSType** . 

## <a name="unable-to-share-resources"></a>Az erőforrások nem oszthatók meg

A megosztott képgyűjtemény, a képdefiníciós és a képverziós erőforrások megosztása az előfizetések között a [szerepköralapú Access Control](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC) használatával engedélyezett. 

## <a name="replication-is-slow"></a>A replikálás lassú

A **--Expand ReplicationStatus** jelzővel ellenőrizze, hogy befejeződött-e a replikáció az összes megadott régióban. Ha nem, várjon legfeljebb 6 órát, amíg a feladatok befejeződik. Ha nem sikerül, aktiválja újra a parancsot a rendszerkép verziójának létrehozásához és replikálásához. Ha sok célpont van a lemezkép verziójának replikálására, a replikálást fázisokban kell elvégezni.

## <a name="azure-limits-and-quotas"></a>Az Azure-ra vonatkozó korlátok és kvóták 

Az [Azure korlátai és kvótái](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) az összes megosztott képkatalógusra, a képdefinícióra és a rendszerkép verzió-erőforrásaira érvényesek. Győződjön meg arról, hogy az előfizetések korlátain belül van. 




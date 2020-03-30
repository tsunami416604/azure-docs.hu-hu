---
title: StorSimple Eszközkezelő-tárfiókok, előfizetések áttelepítése
description: Ismerje meg, hogyan telepítheti át az előfizetéseket, a StorSimple Eszközkezelő szolgáltatás tárfiókjait.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/14/2019
ms.author: alkohli
ms.openlocfilehash: 428c336d98e278910b229e9c0d877a9ae6268c96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77169724"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>A StorSimple Eszközkezelő szolgáltatáshoz társított előfizetések és tárfiókok áttelepítése

Előfordulhat, hogy át kell helyeznie a StorSimple szolgáltatást egy új regisztrációba vagy egy új előfizetésbe. Ezek az áttelepítési forgatókönyvek a fiók vagy az adatközpont módosításai. Az alábbi táblázat segítségével megismerheti, hogy ezek közül a forgatókönyvek közül melyik támogatott, beleértve az áthelyezés részletes lépéseit is.

## <a name="account-changes"></a>Fiókváltozások

| Tud mozogni ...| Támogatott| Állásidő| Az Azure támogatási folyamata| Módszer|
|-----|-----|-----|-----|-----|
| Egy teljes előfizetés (tartalmazza a StorSimple szolgáltatás- és tárfiókokat) egy másik regisztrációhoz? | Igen       | Nem       | **Beléptetésátvitel**<br>Használja:<li>Amikor új Azure-kötelezettséget vásárol egy új szerződés alapján.</li><li>Az összes fiókot és előfizetést át szeretné telepíteni a régi regisztrációból az újba. Ez magában foglalja az összes Azure-szolgáltatások a régi előfizetés.</li> | **1. lépés: Nyisson meg egy Azure Enterprise Operation Support jegyet.**<li>Nyissa [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)meg a.</li><li> Válassza **a Beléptetési felügyelet lehetőséget,** majd válassza **az Átvitel egy regisztrációról egy új ra.**<br>**2. lépés: A kért információk biztosítása**<br>Tartalmaz:<li>forrás igénylési száma</li><li> cél igénylési száma</li><li>átvitel hatálybalépési dátuma|
| StorSimple szolgáltatás egy meglévő fiókból egy új regisztrációba?    | Igen       | Nem       | **Számlaátvitel**<br>Használja:<li>Ha nem szeretne teljes beléptetési átvitelt.</li><li>Csak bizonyos fiókokat szeretne áthelyezni egy új regisztrációba.</li>| **1. lépés: Nyisson meg egy Azure Enterprise Operation Support jegyet.**<li>Nyissa [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport)meg a.</li><li>Válassza **a Beléptetési felügyelet lehetőséget,** majd válassza **az EA-fiók átvitele új igénylésbe**lehetőséget.<br>**2. lépés: A kért információk biztosítása**<br>Tartalmaz:<li>forrás igénylési száma</li><li> cél igénylési száma</li><li>átvitel hatálybalépési dátuma|
| StorSimple szolgáltatás egyik előfizetésből egy másik előfizetésbe?      | Nem        |    Igen         | Nincs, kézi folyamat|<li>Adatok áttelepítése a StorSimple eszközről.</li><li>Végezze el az eszköz gyári alaphelyzetbe állítását, ez törli az eszközön lévő helyi adatokat.</li><li>Regisztrálja az eszközt az új előfizetéssel egy StorSimple Eszközkezelő szolgáltatásba.</li><li>Telepítse vissza az adatokat az eszközre.|
|Átruházhatom egy Azure-előfizetés tulajdonjogát egy másik címtárba? | Igen       | Nem       | Meglévő előfizetés társítása az Azure AD-címtárhoz | Tekintse [meg, hogy társítson egy meglévő előfizetést az Azure AD könyvtárához.](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) Akár 10 percig is eltarthat, amíg minden megfelelően megjelenik.|
| StorSimple eszköz egy StorSimple Eszközkezelő szolgáltatás egy másik szolgáltatás egy másik régióban?      | Nem        | Igen            | Nincs, kézi folyamat |Ugyanaz, mint fent.|
| Tárfiók egy új előfizetéshez vagy erőforráscsoporthoz?     | Igen        | Nem             |Tárfiók áthelyezése másik előfizetésre vagy erőforráscsoportra |Az áthelyezés után, ha a tárfiók hozzáférési kulcsai frissülnek, a felhasználónak manuálisan kell konfigurálnia az áttelepített tárfiók hozzáférési kulcsait a StorSimple Eszközkezelő szolgáltatáson keresztül.|
| Klasszikus tárfiók egy Azure Resource Manager-tárfiókhoz      | Igen        | Nem             |Migrálás klasszikusról az Azure Resource Managerre |<li>A tárfiók klasszikusról azure Resource Managerbe való áttelepítésével kapcsolatos részletes útmutatásért látogasson el [a Klasszikus tárfiók áttelepítése](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-52-migrate-a-storage-account)című területre.</li><li> Ha a tárfiók hozzáférési kulcsai az áttelepítés után frissülnek, a felhasználónak szinkronizálnia kell az áttelepített tárfiók hozzáférési kulcsait a StorSimple Eszközkezelő szolgáltatáson keresztül. Ez biztosítja, hogy a StorSimple-eszközök továbbra is megfelelően működjenek, és képesek elsődleges/biztonsági mentési adatokat rétegezni az Azure-ba. A hozzáférési kulcsok szinkronizálásával kapcsolatos részletes útmutatásért látogasson el a [Forgatási munkafolyamat című e-címre.](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)</li><li> A StorSimple cloud appliance esetén, ha a klasszikus tárfiók áttelepítése van, de az alapul szolgáló virtuális gép továbbra is klasszikus marad, a készülék megfelelően kell működnie. Ha a felhőalapú berendezés alapjául szolgáló virtuális gép áttelepítése, majd az inaktiválása és törlése funkció nem fog működni.</li><li> Létre kell hoznia egy új StorSimple felhőalapú berendezések az Azure Portalon, és majd feladatátvételt a régebbi felhőalapú készülékek. Nem hozhat létre StorSimple cloud Appliance az új Azure Portalon egy klasszikus tárfiók használatával, rendelkezniük kell egy Azure Resource Manager-tárfiókkal. További információ: [StorSimple cloud appliance telepítése és kezelése.](storsimple-8000-cloud-appliance-u2.md)</li>|

## <a name="datacenter-changes"></a>Az adatközpont változásai

| Tud mozogni ...| Támogatott|Állásidő| Az Azure támogatási folyamata| Módszer|
|-----|-----|-----|-----|-----|
| Egy StorSimple szolgáltatás egyik Azure-adatközpontból a másikba? | Nem | Igen |Nincs, kézi folyamat  |<li>Adatok áttelepítése a StorSimple eszközről.</li><li>Végezze el az eszköz gyári alaphelyzetbe állítását, ez törli az eszközön lévő helyi adatokat.</li><li>Regisztrálja az eszközt az új előfizetéssel egy új StorSimple Eszközkezelő szolgáltatásba.</li><li>Telepítse vissza az adatokat az eszközre.|
| Egy tárfiók egyik Azure-adatközpontból a másikba? | Nem |Igen  |Nincs, kézi folyamat  | Ugyanaz, mint fent.|

## <a name="next-steps"></a>További lépések

* [StorSimple Eszközkezelő szolgáltatás telepítése](storsimple-8000-manage-service.md)
* [StorSimple 8000 sorozatú eszköz telepítése az Azure Portalon](storsimple-8000-deployment-walkthrough-u2.md)

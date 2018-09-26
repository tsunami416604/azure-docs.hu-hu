---
title: Storage-fiókok, előfizetések, a StorSimple-Eszközkezelő szolgáltatás migrálása |} A Microsoft Docs
description: Ismerje meg, hogyan telepítheti át az előfizetések, a StorSimple-Eszközkezelő service8000 storage-fiókot.
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
ms.date: 06/08/2018
ms.author: alkohli
ms.openlocfilehash: 5a0da47b854e625f2f4a2fcf4c95ec566ba63093
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47095678"
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Előfizetések és tárfiókok a StorSimple-Eszközkezelő szolgáltatással kapcsolatos áttelepítése

Helyezze át a StorSimple szolgáltatás egy új regisztrációs vagy egy új előfizetést szeretne. Áttelepítési forgatókönyvekben fiókmódosítások vagy datacenter módosítások. Az alábbi táblázat segítségével azonosíthatja, amelyre ezek a forgatókönyvek támogatottak, többek között a részletes lépések végrehajtásával helyezhetők át.

## <a name="account-changes"></a>Fiókmódosítások

| Áthelyezheti...| Támogatott| Állásidő| Az Azure támogatási folyamat| A módszer|
|-----|-----|-----|-----|-----|
| Egy teljes-előfizetést (beleértve a StorSimple-szolgáltatás és a storage-fiókok) egy másik regisztráció? | Igen       | Nem       | **Regisztráció átvitel**<br>Használat:<li>Vásárol egy új Azure kötelezettségvállalás mellett új szerződést.</li><li>Az új a régi regisztrációs fiókok és az előfizetések át szeretné. Ez magában foglalja az Azure-szolgáltatásokhoz a régi előfizetéshez tartozik.</li> | **1. lépés: Az Azure Enterprise művelet támogatási jegyet.**<li>Nyissa meg a következőt: [http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport).</li><li> Válassza ki **regisztrációs felügyeleti** majd **átvitele egy beléptetési egy új regisztrációs**.<br>**2. lépés: Adja meg a kért információkat**<br>A következők:<li>adatforrás-regisztrációs szám</li><li> rendeltetési regisztrációs szám</li><li>átviteli hatályba lépés dátuma|
| A StorSimple szolgáltatás egy meglévő fiókon az új regisztrációs?    | Igen       | Nem       | **Fiók**<br>Használat:<li>Ha nem szeretné, hogy a teljes regisztrációs átvitel.</li><li>Csak szeretné helyezni a konkrét fiókokat való új regisztráció.</li>| **1. lépés: Az Azure Enterprise művelet támogatási jegyet.**<li>Nyissa meg a következőt: [http://aka.ms/AzureEntSupport](http://aka.ms/AzureEntSupport).</li><li>Válassza ki **regisztrációs felügyeleti** majd **nagyvállalati szerződéssel rendelkező fiók át egy új regisztrációs**.<br>**2. lépés: Adja meg a kért információkat**<br>A következők:<li>adatforrás-regisztrációs szám</li><li> rendeltetési regisztrációs szám</li><li>átviteli hatályba lépés dátuma|
| A StorSimple szolgáltatás egy előfizetésből egy másik előfizetéshez?      | Nem        |    Igen         | Nincs, a manuális eljáráshoz|<li>Telepítse át az adatokat a StorSimple-eszköz ki.</li><li>Az eszköz gyári beállítások visszaállítására, ez a művelet törli az eszköz minden helyi adat.</li><li>Regisztrálja az eszközt a StorSimple-Eszközkezelő szolgáltatás az új előfizetéshez.</li><li>Térjen vissza az eszközt az adatok áttelepítéséhez.|
  |Átvihető-e egy Azure-előfizetés tulajdonjogának egy másik címtárral? | Igen       | Nem       | Az Azure AD-címtárhoz meglévő előfizetés társítása | Tekintse meg [az Azure AD-címtárhoz meglévő előfizetés társítása](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md). Akár 10 percig is eltarthat, amíg minden megfelelően megjelenik.|
| A StorSimple eszköz egy StorSimple-Eszközkezelő szolgáltatás és a egy másik régióban egy másik szolgáltatás?      | Nem        | Igen            | Nincs, a manuális eljáráshoz |Ugyanaz, mint a fenti.|
| Storage-fiók egy új előfizetést vagy az erőforrás-csoporthoz?     | Igen        | Nem             |Storage-fiók áthelyezése másik előfizetést vagy az erőforrás-csoporthoz |Az áthelyezés után a tárfiók hozzáférési kulcsainak frissülnek, ha a felhasználó kell konfigurálja manuálisan a StorSimple-Eszközkezelő szolgáltatással a migrált tárfiók hozzáférési kulcsait.|
| Egy Azure Resource Manager-tárfiókot klasszikus tárfiókot      | Igen        | Nem             |Áttérés klasszikus Azure Resource Manager |<li>A tárfiók migrálása klasszikusról az Azure Resource Manager részletes utasításokért ugorjon [klasszikus tárfiók Migrálása](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> A tárfiók hozzáférési kulcsainak az áttelepítés után frissülnek, ha a felhasználó kell szinkronizálni a StorSimple-Eszközkezelő szolgáltatással a migrált tárfiók hozzáférési kulcsait. Ez azért szükséges a StorSimple-eszközök továbbra is megfelelően működnek, és az elsődleges, illetve biztonsági mentési adatok az Azure-bA. A tárelérési kulcsok szinkronizálása részletes utasításokért ugorjon [Elforgatás munkafolyamat](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> A StorSimple Cloud Appliance esetén ha a klasszikus tárfiók van migrálva, de a mögöttes virtuális gép továbbra is marad, a klasszikus, a készülék kell megfelelően fog működni. Ha a a felhőalapú berendezés alapjául szolgáló virtuális gépet telepít át, majd az Inaktiválás és törlési funkciót nem fog működni.</li><li> Hozzon létre egy új StorSimple Cloud Appliance az Azure Portalon kell, és a régebbi cloud Appliance a feladatátvételt. StorSimple Cloud Appliance az új Azure Portalon klasszikus tárfiók használatával nem hozható létre, szükségük van egy Azure Resource Manager-tárfiókot. További információért ugorjon [üzembe helyezése és kezelése a StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).</li>|Ugyanaz, mint a fenti.|

## <a name="datacenter-changes"></a>Adatközpont-módosítások

| Áthelyezheti...| Támogatott|Állásidő| Az Azure támogatási folyamat| A módszer|
|-----|-----|-----|-----|-----|
| A StorSimple szolgáltatás egy Azure adatközpontból a másikba? | Nem | Igen |Nincs, a manuális eljáráshoz  |<li>Telepítse át az adatokat a StorSimple-eszköz ki.</li><li>Az eszköz gyári beállítások visszaállítására, ez a művelet törli az eszköz minden helyi adat.</li><li>Regisztrálja az eszközt az új előfizetéssel egy új StorSimple-Eszközkezelő szolgáltatáshoz.</li><li>Térjen vissza az eszközt az adatok áttelepítéséhez.|
| Egy storage-fiókot egy Azure adatközpontból a másikba? | Nem |Igen  |Nincs, a manuális eljáráshoz  | Ugyanaz, mint a fenti.|

## <a name="next-steps"></a>További lépések

* [StorSimple-Eszközkezelő szolgáltatás üzembe helyezése](storsimple-8000-manage-service.md)
* [Az Azure Portalon a StorSimple 8000 sorozatú eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)

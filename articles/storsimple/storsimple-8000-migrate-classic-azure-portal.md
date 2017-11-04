---
title: "Telepítse át a storage-fiókok, a StorSimple Device Manager szolgáltatás előfizetések |} Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti át az előfizetést, a StorSimple Device Manager service8000 storage-fiókok."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: alkohli
ms.openlocfilehash: edac2635086106f83b30316ab190bead5245f195
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-subscriptions-and-storage-accounts-associated-with-storsimple-device-manager-service"></a>Előfizetések és a StorSimple Device Manager szolgáltatással kapcsolatos storage-fiókok áttelepítése

Helyezze át a StorSimple szolgáltatás egy új regisztrációs vagy egy új előfizetést szeretne. Áttelepítési forgatókönyvekben fiókjának módosításait vagy datacenter módosítások. Az alábbi táblázat segítségével azonosíthatja, ezek a forgatókönyvek közül melyik támogatottak, beleértve a helyezhető át a részletes lépéseket.

## <a name="account-changes"></a>A fiókok változásai

| Áthelyezheti...| Támogatott| Állásidő| Az Azure támogatási folyamat| Módszer|
|-----|-----|-----|-----|-----|
| Egy teljes előfizetés (tartalmazza a StorSimple szolgáltatás és a tárolási fiók) egy másik regisztráció? | Igen       | Nem       | **Beléptetési átvitel**<br>Használata:<li>Ha vásárol egy új Azure-előfizetési új szerződések alapján.</li><li>Minden fiókjaihoz és előfizetéseihez át a régi regisztrációtól az új szeretné. Ez magában foglalja az összes Azure-szolgáltatás a régi előfizetésen belül.</li> | **1. lépés: Az Azure vállalati művelet támogatási jegy megnyitása.**<li>Ugrás a [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li> Válassza ki **beléptetési felügyeleti** , és válassza **átvitele egy beléptetési egy új regisztrációs**.<br>**2. lépés: A kért adatok megadása**<br>A következők:<li>Forrás száma</li><li> cél száma</li><li>átviteli életbelépési dátum|
| A StorSimple szolgáltatás a meglévő fiók egy új regisztrációs?    | Igen       | Nem       | **Fiók átvitel**<br>Használata:<li>Ha nem szeretné, hogy a teljes beléptetési átvitel.</li><li>Szeretné egyedi fiókokat új beléptetési helyezze át.</li>| **1. lépés: Az Azure vállalati művelet támogatási jegy megnyitása.**<li>Ugrás a [http://aka.ms/AzureEnt](http://aka.ms/AzureEnt).</li><li>Válassza ki **beléptetési felügyeleti** majd **EA fiók átvitele egy új regisztrációs**.<br>**2. lépés: A kért adatok megadása**<br>A következők:<li>Forrás száma</li><li> cél száma</li><li>átviteli életbelépési dátum|
| A StorSimple szolgáltatás egy előfizetés másik előfizetést?      | Nem        |    Igen         | Nincs, a manuális eljáráshoz|<li>Telepítse át a StorSimple eszköz adatokat.</li><li>Hajtsa végre az eszköz gyári beállításainak visszaállítását, ez a művelet törli az eszköz helyi adatokat.</li><li>Az eszközt regisztrálni kell az új előfizetés, a StorSimple Device Manager szolgáltatás.</li><li>Az adatok áttelepítését vissza az eszközt.|
| A StorSimple eszköz egy StorSimple Device Manager szolgáltatásból egy másik régióban egy másik szolgáltatásra?      | Nem        | Igen            | Nincs, a manuális eljáráshoz |Ugyanaz, mint a fentiek közül.|
| A tárfiók egy új előfizetés vagy az erőforrás-csoporthoz?     | Igen        | Nem             |Helyezze át a tárfiók különböző előfizetés vagy az erőforrás |Az áthelyezés után a fiók tárelérési kulcsok frissítése, ha a felhasználó kell manuálisan a StorSimple Device Manager szolgáltatáson keresztül az áttelepített tárfiók hozzáférési kulcsainak konfigurálása.|
| Az Azure Resource Manager-tárfiókot a klasszikus tárfiókot      | Igen        | Nem             |Telepítse át a klasszikus Azure Resource Managerbe |<li>A storage-fiók áttelepítése a klasszikus Azure Resource Manager részletes információkra van szüksége, látogasson el [telepítse át a klasszikus tárfiók](../virtual-machines/windows/migration-classic-resource-manager-ps.md#step-62-migrate-a-storage-account).</li><li> Ha a fiók tárelérési kulcsok frissítése az áttelepítés után, a felhasználó kell szinkronizálni a StorSimple Device Manager szolgáltatáson keresztül az áttelepített tárfiók hozzáférési kulcsainak listázása. Ez azért szükséges a StorSimple eszköz továbbra is megfelelően működik, és a rétegben az elsődleges, illetve biztonsági mentési adatokat az Azure-bA. A tárelérési kulcsok szinkronizálása részletes utasításokért lásd [Elforgatás munkafolyamat](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts).</li><li> A StorSimple felhő készülék esetében a hagyományos tárolási fiók áttelepítése, de az alapul szolgáló virtuális gép továbbra is marad, a classic, ha a készülék kell működnek majd megfelelően. Ha az alapul szolgáló virtuális gép a felhő készülék áttelepíti, akkor a inaktiválását vagy törlését funkciói nem fognak működni.</li><li> Hozzon létre egy új StorSimple felhő készülékek az Azure portálon kell, és a régebbi felhő készülékek a feladatátvételt. Nem hozható létre a StorSimple felhő készülék a hagyományos tárolási fiók használatával új Azure-portálon, ezek csak az Azure Resource Manager-tárfiókot. További információkért látogasson el [központi telepítése és kezelése a StorSimple felhő készülék](storsimple-8000-cloud-appliance-u2.md).</li>|Ugyanaz, mint a fentiek közül.|

## <a name="datacenter-changes"></a>Datacenter változások

| Áthelyezheti...| Támogatott|Állásidő| Az Azure támogatási folyamat| Módszer|
|-----|-----|-----|-----|-----|
| A StorSimple szolgáltatás egy Azure-adatközpontban másik? | Nem | Igen |Nincs, a manuális eljáráshoz  |<li>Telepítse át a StorSimple eszköz adatokat.</li><li>Hajtsa végre az eszköz gyári beállításainak visszaállítását, ez a művelet törli az eszköz helyi adatokat.</li><li>Az eszközt regisztrálni kell az új előfizetés egy új StorSimple Device Manager szolgáltatás a.</li><li>Az adatok áttelepítését vissza az eszközt.|
| A tárfiók egy Azure-adatközpontban a másik? | Nem |Igen  |Nincs, a manuális eljáráshoz  | Ugyanaz, mint a fentiek közül.|

## <a name="next-steps"></a>Következő lépések

* [StorSimple Device Manager szolgáltatás telepítése](storsimple-8000-manage-service.md)
* [Azure-portálon a StorSimple 8000 series eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md)

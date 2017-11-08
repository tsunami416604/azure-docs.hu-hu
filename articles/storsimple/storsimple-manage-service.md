---
title: "A StorSimple Manager szolgáltatás üzembe helyezése |} Microsoft Docs"
description: "Azt ismerteti, hogyan hozhat létre, és törölje a StorSimple Manager szolgáltatást a klasszikus Azure portálon, és a szolgáltatás regisztrációs kulcsának kezelését ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: bc1d5650-275c-42ed-bc77-cdb596f85943
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ee22a31e9c0ec23d9b042dc894cafe0fc346e742
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="deploy-the-storsimple-manager-service-in-the-azure-classic-portal"></a>A klasszikus Azure portálon a StorSimple Manager szolgáltatás telepítése
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Ez a cikk az új Azure-portálon verziójának megtekintéséhez keresse fel [a klasszikus Azure portálon a StorSimple Manager szolgáltatás telepítése](storsimple-8000-manage-service.md). Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Áttekintés
A StorSimple Manager szolgáltatás fut a Microsoft Azure-ban, és több StorSimple eszközt csatlakozik. Miután létrehozta a szolgáltatást, az eszközök kezeléséhez a Microsoft Azure portálról klasszikus böngészőjében futó használhatja. Ez lehetővé teszi, hogy egységes, központi helyről, ezáltal az adminisztratív terhek minimalizálása a StorSimple Manager szolgáltatáshoz csatlakozó eszközök figyelésére.

A StorSimple Manager kezdőlapja sorolja fel a StorSimple Manager összes olyan szolgáltatás, a StorSimple tárolóeszközök felügyeletére használható. Minden egyes StorSimple Manager szolgáltatás a következő információ a StorSimple Manager oldalon:

* **Név** – a nevét, a StorSimple Manager szolgáltatás rendelt létrehozásakor. **A szolgáltatás neve nem lehet módosítani, a szolgáltatás létrehozása után. Ez akkor is igaz, az egyéb entitások, például az eszközök, a kötetek, a kötettárolók és a biztonsági mentési házirendek, amelyek nem nevezhető át a klasszikus Azure portálon.**
* **Állapot** – a szolgáltatás, amely lehet állapotának **aktív**, **létrehozása**, vagy **Online**.
* **Hely** – az a földrajzi hely, amely a StorSimple eszköz telepítve lesz.
* **Előfizetés** – a számlázási előfizetés, amely kapcsolódik a szolgáltatáshoz.

A gyakori feladatokat, amelyek a StorSimple Manager lap segítségével végezheti el a következők:

* Szolgáltatás létrehozása
* A szolgáltatás törlése
* Szolgáltatásregisztrációs kulcs lekérése
* A szolgáltatás regisztrációs kulcsának újragenerálása

Ez az oktatóanyag leírja, hogyan hajthat végre ezen feladatok végrehajtását.

## <a name="create-a-service"></a>Szolgáltatás létrehozása
Használja a **Gyorslétrehozás** létrehozhat a StorSimple Manager szolgáltatást, ha azt szeretné, hogy a StorSimple eszköz üzembe helyezése. Szolgáltatás létrehozása kell rendelkeznie:

* Nagyvállalati szerződéssel rendelkező előfizetés
* Egy aktív Microsoft Azure storage-fiók
* A kezelési használt számlázási adatokat

Választhatja azt is, a szolgáltatás létrehozásakor egy alapértelmezett tárfiók létrehozásához.

Egyetlen szolgáltatás több eszközöket kezelheti. Azonban egy eszköz nem terjedhetnek ki több szolgáltatásra. A nagyvállalatok különböző előfizetések, szervezetek, vagy még a központi telepítési helyek több szolgáltatás példánya is lehet. Vegye figyelembe a StorSimple Manager szolgáltatás kezelheti a StorSimple 8000 sorozat eszközeire és a StorSimple virtuális tömbök példányát külön kell.

> [!IMPORTANT] 
> Ha egy nem használt szolgáltatás létrehozása (nincs ehhez az erőforráshoz a végrehajtott műveletek eszköz) 2016 augusztusától előtt, akkor nem tudja felügyelni az Azure portál vagy a klasszikus Azure portálon keresztül. Azt javasoljuk, hogy hozzon létre egy új szolgáltatás az Azure portálon.

A következő lépésekkel szolgáltatás létrehozása.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

## <a name="delete-a-service"></a>A szolgáltatás törlése
A szolgáltatás törlése előtt győződjön meg arról, hogy nem csatlakoztatott eszközök-k használják. Ha a szolgáltatás használja, kapcsolja ki a csatlakoztatott eszközök. Az inaktiválás művelet-kiszolgálón az eszköz és a szolgáltatás közötti kapcsolat, de a felhőben az eszköz adatok megőrzése érdekében.

> [!IMPORTANT] 
> Szolgáltatás törlését követően a művelet nem vonható vissza. Minden olyan eszköz, amely a szolgáltatás használatával lett kell lennie a gyári beállításokra történő visszaállításának egy másik szolgáltatás használata előtt. Ebben a forgatókönyvben az eszközt, valamint a konfiguráció a helyi adatok elvesznek.

A következő lépésekkel törölni a szolgáltatást.

### <a name="to-delete-a-service"></a>A szolgáltatás törlése
1. Az a **StorSimple Manager szolgáltatás** lapon, válassza ki a törölni kívánt szolgáltatást.
2. Kattintson a **törlése** az oldal alján.
3. Kattintson a **Igen** a megerősítési értesítés. A szolgáltatás a törlendő néhány percig is eltarthat.

## <a name="get-the-service-registration-key"></a>Szolgáltatásregisztrációs kulcs lekérése
Miután sikeresen létrehozott egy szolgáltatást, szüksége lesz a StorSimple eszköz regisztrálása a szolgáltatásban. Az első StorSimple eszköz regisztrálása, szüksége lesz a szolgáltatás regisztrációs kulcsának. További eszközök regisztrálása egy meglévő StorSimple szolgáltatásban, szüksége lesz a regisztrációs kulcsot, mind a szolgáltatásadat-titkosítási kulcs (amely a regisztráció során van az első eszközön létre). A szolgáltatásadat-titkosítási kulcs kapcsolatos további információkért lásd: [StorSimple biztonsági](storsimple-security.md). A regisztrációs kulcs kaphat elérése **regisztrációs kulcs** a a **szolgáltatások** lap.

A következő lépésekkel Szolgáltatásregisztrációs kulcs lekérése.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

A szolgáltatás regisztrációs kulcsának tartsa biztonságos helyen. Ezt a kulcsot, valamint a szolgáltatásadat-titkosítási kulcs, ez a szolgáltatás további eszközöket regisztrálni kell. Miután beszerezte a szolgáltatás regisztrációs kulcsának, szüksége lesz az eszköz StorSimple felület a Windows PowerShell segítségével konfigurálhatja.

A regisztrációs kulcs használatával, lásd: [3. lépés: konfigurálja, és a Windows PowerShell segítségével az eszközt regisztrálni kell a StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcsának újragenerálása
Szüksége lesz egy Szolgáltatásregisztrációs kulcs újbóli létrehozása, ha kulcs elforgatás végrehajtásához szükséges, vagy ha a szolgáltatás-rendszergazdák listája megváltozott. Amikor újragenerálja a kulcsot, az új kulccsal csak a következő eszközök regisztrálása. A már regisztrált eszközöket nem érinti ez a folyamat.

A következő lépésekkel egy Szolgáltatásregisztrációs kulcs újragenerálása.

### <a name="to-regenerate-the-service-registration-key"></a>A szolgáltatás regisztrációs kulcs újragenerálásához
1. Az a **StorSimple Manager szolgáltatás** kattintson **regisztrációs kulcs**.
2. Az a **Szolgáltatásregisztrációs kulcs** párbeszédpanel, kattintson a **újragenerálja**.
3. Egy megerősítő üzenet jelenik meg. Kattintson a **OK** a újragenerálása folytatásához.
4. Egy új Szolgáltatásregisztrációs kulcs jelenik meg.
5. Másolja ki ezt a kulcsot, és mentse az új eszköz regisztrálása ezt a szolgáltatást.
6. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-service/HCS_CheckIcon.png) Ez a párbeszédpanel bezárásához.

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple telepítési folyamatának](storsimple-deployment-walkthrough-u2.md).
* További információ [tárfiók felügyelete a StorSimple](storsimple-manage-storage-accounts.md).
* További tudnivalók a [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

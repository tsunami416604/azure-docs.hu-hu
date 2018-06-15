---
title: A StorSimple-jelszavak módosítása |} Microsoft Docs
description: Ismerteti a StorSimple Device Manager szolgáltatás segítségével módosítsa a StorSimple Snapshot Manager és az eszköz rendszergazdai jelszót.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 7762f8499c67672f0a2ffed99e98baea4c940fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874807"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>A StorSimple Device Manager szolgáltatással a StorSimple-jelszavak módosítása

## <a name="overview"></a>Áttekintés
Az Azure-portálon **eszközbeállítások** beállítás olyan módon konfigurálhatja újra a StorSimple eszközön a StorSimple eszköz Manager szolgáltatás által kezelt eszköz paramétereket tartalmaz. Ez az oktatóanyag azt ismerteti, hogyan használhatja a **biztonsági** lehetőség alatt **eszközbeállítások** az eszköz-rendszergazdai vagy a StorSimple Snapshot Manager jelszavának módosítása.

## <a name="change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
Ha Windows PowerShell-felületet a StorSimple eszköz elérésére használja, meg kell adja meg az eszköz rendszergazdai jelszavát. Ha az első StorSimple eszköz regisztrálva van a szolgáltatásban, az alapértelmezett Ez az interfész jelszava *jelszó1*. Az adatok biztonsága érdekében szükségesek a regisztrációs folyamat végén jelszó módosításához. Ez a jelszó módosítása nélkül nem lépjen ki a regisztrációs folyamat során. További információkért lásd: [3. lépés: konfigurálja, és a Windows PowerShell segítségével az eszközt regisztrálni kell a StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A jelszó, amelyet először a Windows PowerShell felületen regisztrálás során később módosíthatja az Azure-portálon. A következő lépésekkel módosíthatja az eszköz rendszergazdai jelszava.

#### <a name="to-change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre.

2. Eszközök táblázatos listájában, válassza ki, és kattintson az eszközre, amelynek kívánja módosítani a jelszavát.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Az a **beállítások** paneljén lépjen **eszközbeállítások > biztonsági**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Az a **biztonsági beállítások** panelen kattintson a **jelszó** módosíthatja az eszköz rendszergazdai jelszava.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Az a **jelszó** panelen adjon meg egy rendszergazdai jelszót, amely tartalmazza a 8-15 karakter. A jelszónak legalább 3 nagybetű, nagybetűk, numerikus és speciális karakterek kombinációjából kell lennie.

6. Erősítse meg a jelszót.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Kattintson a **mentése** és megerősítést kér, amikor kattintson **Igen**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Az eszköz rendszergazdai jelszava most frissíteni kell. A módosított jelszó használatával érhető el a Windows PowerShell illesztőfelülete.

## <a name="set-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának beállítása
A StorSimple Snapshot Manager szoftver a Windows-állomáson található, és lehetővé teszi a rendszergazdák számára, hogy helyi és felhőalapú pillanatfelvételek formájában kezeljék a StorSimple eszköz biztonsági mentéseit.

Amikor konfigurál egy eszközt a StorSimple Snapshot Managerben, fog kérni fogja az eszköz IP-cím és a jelszót a tárolóeszköz hitelesítése kell megadniuk.

Állítsa be, vagy módosítsa a jelszavát a StorSimple Snapshot Manager az Azure-portálon. A következő lépésekkel beállítása vagy módosítása a StorSimple Snapshot Manager jelszavát.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának beállítása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre.

2. Az eszközök táblázatos listája, jelölje ki, és kattintson az eszközt, amelynek StorSimple Snapshot Manager jelszavát kíván beállítása vagy módosítása.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Az a **beállítások** paneljén lépjen **eszközbeállítások > biztonsági**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Az a **biztonsági beállítások** panelen kattintson a **jelszó** beállítása vagy módosítása a StorSimple Snapshot Manager jelszavát.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Az a **jelszó** panelen adjon meg egy 14 vagy 15 karakter hosszú jelszót. Győződjön meg arról, hogy a jelszó tartalmazza-e a 3 vagy több nagybetű, nagybetűk, numerikus és speciális karaktert.

6. Erősítse meg a jelszót.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Kattintson a **mentése** és megerősítést kér, amikor kattintson **Igen**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

A StorSimple Snapshot Manager jelszava most frissíteni kell.

## <a name="next-steps"></a>Következő lépések
* További információ [StorSimple biztonsági](storsimple-8000-security.md).
* További információ [az eszköz konfigurációjának módosítása](storsimple-8000-modify-device-config.md).
* További információ [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).


---
title: A StorSimple-jelszavak módosítása |} A Microsoft Docs
description: A StorSimple Snapshot Manager és az eszköz rendszergazdai jelszavak módosítása a StorSimple-Eszközkezelő szolgáltatás használatát ismerteti.
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
ms.openlocfilehash: ab874bbdcd47a4bfa9abfba721afa46d0f23a338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638008"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>A StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-jelszavak módosítása

## <a name="overview"></a>Áttekintés
Az Azure Portalon **eszközbeállítások** beállítást tartalmaz, amely a StorSimple eszközön, amely a StorSimple-Eszközkezelő szolgáltatás által kezelt újrakonfigurálhatja az összes eszköz paramétert. Ez az oktatóanyag azt ismerteti, hogyan használhatja a **biztonsági** lehetőség **eszközbeállítások** az eszköz-rendszergazdai vagy a StorSimple Snapshot Manager jelszavának módosítása.

## <a name="change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
Windows PowerShell felületét a StorSimple eszköz használatakor kell egy eszközadminisztrátori jelszó megadására kéri. Ha az első StorSimple eszköz regisztrálva van egy szolgáltatással, az adott illesztő számára az alapértelmezett jelszó kérése ennyi *jelszó1*. Az adatok biztonsága szükségesek a regisztrációs folyamat végén a jelszó módosítására. Ez a jelszó módosítása nélkül nem lépjen ki a regisztrációs folyamat során. További információkért lásd: [3. lépés: Eszköz konfigurálása és regisztrálása a Windows PowerShell storsimple-höz készült](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

A jelszó, amelyet először a Windows PowerShell felületen regisztrációja során később módosítható az Azure Portalon keresztül. Hajtsa végre az alábbi lépéseket az eszköz rendszergazdai jelszavának módosítása.

#### <a name="to-change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre.

2. Az eszközök táblázatos listájából válassza ki, majd kattintson az eszközt, amelynek kívánja módosítani a jelszavát.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Az a **beállítások** panelen lépjen a **eszköz beállításai > biztonsági**.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Az a **biztonsági beállítások** panelen kattintson a **jelszó** az eszköz rendszergazdai jelszavának módosítása.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. Az a **jelszó** panelen adja meg egy rendszergazdai jelszót, amely tartalmazza a 8 – 15 karakter. A jelszó 3 vagy több nagybetűket, kisbetűket, számjegyeket és speciális karakterek kombinációjából kell lennie.

6. Erősítse meg a jelszót.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Kattintson a **mentése** és amikor a rendszer megerősítést kér, kattintson a **Igen**.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Az eszköz rendszergazdai jelszava most frissíteni kell. A módosított jelszó használatával a Windows PowerShell felületet eléréséhez.

## <a name="set-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának beállítása
A StorSimple Snapshot Manager szoftver a Windows-állomáson található, és lehetővé teszi a rendszergazdák számára, hogy helyi és felhőalapú pillanatfelvételek formájában kezeljék a StorSimple eszköz biztonsági mentéseit.

Amikor konfigurál egy eszközt a StorSimple Snapshot Managerben, a program kéri eszköz IP-címének és jelszavának a tárolóeszköz hitelesítése érdekében.

Állítsa be, vagy módosítsa a jelszót a StorSimple Snapshot Manager az Azure Portalon keresztül. A következő lépésekkel állítsa be vagy változtassa meg a StorSimple Snapshot Manager jelszavát.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának beállítása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre.

2. Az eszközök táblázatos listájából válassza ki, majd kattintson az eszközt, amelynek a StorSimple Snapshot Manager jelszavát szeretne beállítása vagy módosítása.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. Az a **beállítások** panelen lépjen a **eszköz beállításai > biztonsági**.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. Az a **biztonsági beállítások** panelen kattintson a **jelszó** állítsa be vagy változtassa meg a StorSimple Snapshot Manager jelszavát.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. Az a **jelszó** panelen adjon meg egy jelszót, amely 14 vagy 15 karakter hosszú lehet. Győződjön meg arról, hogy a jelszó tartalmazza-e a 3 vagy több nagybetűket, kisbetűket, számjegyeket és speciális karakterek kombinációját.

6. Erősítse meg a jelszót.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Kattintson a **mentése** és amikor a rendszer megerősítést kér, kattintson a **Igen**.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

A StorSimple Snapshot Manager jelszava most frissíteni kell.

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [StorSimple biztonsági](storsimple-8000-security.md).
* Tudjon meg többet [az eszköz konfigurációjának módosítása](storsimple-8000-modify-device-config.md).
* Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).


---
title: Módosítsa a StorSimple jelszavak | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy a StorSimple Eszközkezelő szolgáltatás használatával hogyan módosíthatja a StorSimple Snapshot Manager és az eszközrendszergazdai jelszavakat.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268027"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple jelszavak módosításához

## <a name="overview"></a>Áttekintés
Az Azure Portal **eszközbeállítások** beállítás tartalmazza az összes olyan eszközparamétert, amely újrakonfigurálható egy StorSimple-eszközön, amelyet egy StorSimple Eszközkezelő szolgáltatás kezel. Ez az oktatóanyag bemutatja, hogyan **használhatja** a Biztonsági beállítást az **Eszközbeállítások** csoportban az eszköz rendszergazdájának vagy a StorSimple Snapshot Manager jelszavának módosításához.

## <a name="change-the-device-administrator-password"></a>Az eszköz rendszergazdai jelszavának módosítása
Ha a Windows PowerShell-felületen keresztül éri el a StorSimple-eszközt, meg kell adnia egy eszköz rendszergazdai jelszavát. Amikor az első StorSimple eszköz regisztrálva van egy szolgáltatással, a kapcsolat alapértelmezett jelszava a *Password1*. Az adatok biztonsága érdekében a regisztrációs folyamat végén módosítania kell ezt a jelszót. A jelszó módosítása nélkül nem léphet ki a regisztrációs folyamatból. További információt a [3.](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)

A jelszó, amely először a Windows PowerShell-felületen keresztül a regisztráció során módosítható később az Azure Portalon keresztül. Hajtsa végre az alábbi lépéseket az eszközrendszergazda jelszavának módosításához.

#### <a name="to-change-the-device-administrator-password"></a>Az eszközrendszergazda jelszavának módosítása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre.

2. Az eszközök táblázatos listájában jelölje ki és kattintson arra az eszközre, amelynek jelszavát módosítani kívánja.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. A **Beállítások** panelen nyissa meg **az Eszközbeállítások > biztonság lehetőséget.**

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. A **Biztonsági beállítások** panelen kattintson a **Jelszó** gombra az eszköz rendszergazdai jelszavának módosításához.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. A **Jelszó** panelen adjon meg egy 8 és 15 karakter között lévő rendszergazdai jelszót. A jelszónak legalább 3 nagybetűs, kisbetűs, numerikus és speciális karakter kombinációjának kell lennie.

6. Erősítse meg a jelszót.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. Kattintson a **Mentés** gombra, és amikor megerősítést kér, kattintson az **Igen**gombra.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

Az eszköz rendszergazdai jelszavát most frissíteni kell. Ezzel a módosított jelszóval érheti el a Windows PowerShell-felületet.

## <a name="set-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának beállítása
A StorSimple Snapshot Manager szoftver a Windows-állomáson található, és lehetővé teszi a rendszergazdák számára, hogy helyi és felhőalapú pillanatfelvételek formájában kezeljék a StorSimple eszköz biztonsági mentéseit.

Amikor konfigurálegy eszközt a StorSimple Snapshot Managerben, a rendszer kéri, hogy adja meg az eszköz IP-címét és jelszavát a tárolóeszköz hitelesítéséhez.

Beállíthatja vagy módosíthatja a StorSimple Snapshot Manager jelszavát az Azure Portalon keresztül. Hajtsa végre az alábbi lépéseket a StorSimple Snapshot Manager jelszavának beállításához vagy módosításához.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>A StorSimple Snapshot Manager jelszavának beállítása
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre.

2. Az eszközök táblázatos listájából válassza ki és kattintson arra az eszközre, amelynek StorSimple Snapshot Manager jelszavát be kívánja állítani vagy módosítani kívánja.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. A **Beállítások** panelen nyissa meg **az Eszközbeállítások > biztonság lehetőséget.**

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. A **Biztonsági beállítások** panelen kattintson a **Jelszó** gombra a StorSimple Snapshot Manager jelszavának beállításához vagy módosításához.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. A **Jelszó** panelen adjon meg egy 14 vagy 15 karakterből álló jelszót. Győződjön meg arról, hogy a jelszó 3 vagy több nagybetűs, kis- és numerikus és speciális karaktert tartalmaz.

6. Erősítse meg a jelszót.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. Kattintson a **Mentés** gombra, és amikor megerősítést kér, kattintson az **Igen**gombra.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

A StorSimple Snapshot Manager jelszót most frissíteni kell.

## <a name="next-steps"></a>További lépések
* További információ a [StorSimple biztonságáról.](storsimple-8000-security.md)
* További információ [az eszközkonfiguráció módosításáról.](storsimple-8000-modify-device-config.md)
* További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)


---
title: A StorSimple Virtual Array az eszköz rendszergazdai jelszavának módosítása |} A Microsoft Docs
description: Az eszköz rendszergazdai jelszavának módosítása az Azure portal vagy a StorSimple Virtual Array webes felhasználói felület használatát ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60580361"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>A StorSimple Virtual Array eszköz rendszergazdai jelszava keresztül a StorSimple Device Manager módosítása

## <a name="overview"></a>Áttekintés

A Windows PowerShell felületét a StorSimple Virtual Array eléréséhez használhatja, amikor szükségesek egy eszközadminisztrátori jelszó megadására kéri. Amikor először kiépítése és indítása a StorSimple-eszköz, az alapértelmezett jelszó az *jelszó1*. Az adatok biztonságát az alapértelmezett jelszó lejár az első alkalommal jelentkezik be, és módosítsa ezt a jelszót kell.

A helyi webes felhasználói felületen vagy az Azure portal használatával is az eszköz rendszergazdai jelszava az eszköz az éles környezetben üzembe helyezését követően bármikor módosíthatja. Ezek az eljárások leírását ebben a cikkben.

 ![Eszközök panelen](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>A jelszó módosítása az Azure portal használatával

A következő lépésekkel módosítsa az eszköz rendszergazdai jelszava az Azure Portalon keresztül.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Az eszköz rendszergazdai jelszava az Azure Portalon keresztül módosítása

1. Szolgáltatás kezdőlapján válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevét, és ezután belül a **felügyeleti** területén kattintson **eszközök**. Ekkor megnyílik a **eszközök** panel, amely felsorolja a StorSimple Virtual Array eszközeit.

2. Az a **eszközök** panelen kattintson duplán az eszközt, hogy jelszót követel meg.

3. Az a **beállítások** az eszköz paneljén kattintson **biztonsági**.

4. Az a **biztonsági beállítások** panelen tegye a következőket:
   
   1. Görgessen le a **eszköz rendszergazdai jelszavát** szakaszban. Adjon meg egy rendszergazdai jelszót, amely tartalmazza a 8 – 15 karakter.
   2. Erősítse meg a jelszót.
   3. Kattintson a panel tetején lévő **Mentés** elemre.

Az eszköz rendszergazdai jelszava most frissült. Ez a módosított jelszó használatával helyben az eszköz elérésére.

![Biztonsági beállítások panel](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Módosítsa a jelszót a helyi webes felhasználói felület használatával

A következő lépésekkel módosítsa az eszköz rendszergazdai jelszava helyi webes felületén keresztül.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Módosíthatja a helyi webes felhasználói felületen, az eszköz rendszergazdai jelszava

1. A helyi webes felületén kattintson **karbantartási** > **jelszómódosítás** az eszközhöz.
   
    ![jelszó1 módosítása](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Adja meg a **jelenlegi jelszó**.
3. Adjon meg egy **új jelszót**. A jelszónak legalább 8 karakter hosszúságúnak kell lennie. A következő 4, 3 tartalmaznia kell: nagybetű, kisbetű, számjegyeket és speciális karaktereket.
   
    Vegye figyelembe, hogy a jelszó nem lehet ugyanaz, mint az utolsó 24 jelszavakat.
4. Írja be ismét a jelszót a megerősítéséhez.
   
    ![jelszó2 módosítása](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Kattintson a lap alján **alkalmaz**. Most alkalmazza az új jelszót. Ha a jelszó módosítása nem jár sikerrel, a következő hiba jelenik meg:
   
    ![jelszó típusú hibák](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    A jelszó sikeres frissítése után értesítést kap. Ezután használhatja a módosított jelszó helyileg az eszköz elérésére.


## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [felügyelete a StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).


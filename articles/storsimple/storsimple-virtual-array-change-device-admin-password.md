---
title: A StorSimple virtuális tömb eszköz rendszergazdai jelszó módosítása |} Microsoft Docs
description: Ismerteti az Azure portálon vagy a StorSimple virtuális tömb webes felhasználói felület segítségével módosíthatja az eszköz rendszergazdai jelszava.
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
ms.openlocfilehash: 260a23003d705e6598da8c51bb5a96f2539a0014
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875570"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>A StorSimple virtuális tömb eszköz rendszergazdai jelszava keresztül StorSimple Device Manager módosítása

## <a name="overview"></a>Áttekintés

Használatakor a Windows PowerShell felületet a StorSimple virtuális tömb eléréséhez meg kell írnia egy eszköz rendszergazdai jelszava. A StorSimple eszköz először kiosztásakor és elindítva, az alapértelmezett jelszó az *jelszó1*. Az adatok biztonsága az alapértelmezett jelszó lejár az első alkalommal bejelentkezik, és a jelszó módosításához szükségesek.

Az eszköz rendszergazdai jelszava az éles környezetben az eszköz telepítését követően bármikor módosíthatja vagy a helyi webes felhasználói felületen, vagy az Azure-portálon is használja. Ezek az eljárások leírását ebben a cikkben.

 ![Eszközök panel](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>A jelszó módosítása az Azure-portál használatával

A következő lépésekkel módosíthatja az eszköz rendszergazdai jelszava az Azure portálon keresztül.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Az eszköz rendszergazdai jelszava az Azure-portálon módosítása

1. A szolgáltatás követően lapon válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd belül a **felügyeleti** területen kattintson **eszközök**. Ekkor megnyílik a **eszközök** panel, amelyen a StorSimple virtuális tömb-eszközeinek felsorolását tartalmazza.

2. Az a **eszközök** panelen kattintson duplán az eszközt, hogy a jelszó módosítására van szükség.

3. Az a **beállítások** az eszköz paneljén kattintson **biztonsági**.

4. Az a **biztonsági beállítások** panelen tegye a következőket:
   
   1. Görgessen le a **eszköz rendszergazdai jelszavát** szakasz. Adjon meg egy rendszergazdai jelszót, amely tartalmazza a 8-15 karakter.
   2. Erősítse meg a jelszót.
   3. Kattintson a **mentése** a panel tetején.

Az eszköz rendszergazdai jelszava most frissül. A módosított jelszó használatával helyileg az eszközhöz való hozzáféréshez.

![Biztonsági beállítások panel](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>Módosítsa a jelszót a helyi webes felhasználói felület használatával

A következő lépésekkel módosíthatja az eszköz rendszergazdai jelszava a helyi webes felhasználói felületen.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Az eszköz rendszergazdai jelszava a helyi webes felhasználói felületen keresztül módosítása

1. Kattintson a helyi webes felhasználói felület **karbantartási** > **jelszómódosítás** az eszközhöz.
   
    ![jelszó1 módosítása](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Adja meg a **jelenlegi jelszó**.
3. Adjon meg egy **új jelszó**. A jelszónak legalább 8 karakter hosszúságúnak kell lennie. 4. a következő 3 tartalmaznia kell: nagybetűk, nagybetűk, numerikus és speciális karaktereket.
   
    Vegye figyelembe, hogy a jelszó nem lehet ugyanaz, mint a legutóbbi 24 jelszó.
4. Adja meg újra a jelszót a megerősítéshez.
   
    ![jelszó2 módosítása](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. Kattintson a lap alján **alkalmaz**. Az új jelszót a rendszer most alkalmazza. Ha a jelszó módosítása sikertelen, a következő hibát látja:
   
    ![jelszó hiba](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    Miután a jelszó frissítése sikeres volt, értesítés jelenik meg. Ezután használhatja a módosított jelszó helyileg az eszköz elérésére.


## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [felügyelete a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).


---
title: Változás StorSimple Virtual Array eszköz admin jelszó | Microsoft dokumentumok
description: Ez a témakör ismerteti, hogyan használhatja az Azure Portal vagy a StorSimple virtual array webes felhasználói felületét az eszköz rendszergazdai jelszavának módosításához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60580361"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>A StorSimple Virtual Array eszköz rendszergazdai jelszavának módosítása a StorSimple Eszközkezelőn keresztül

## <a name="overview"></a>Áttekintés

Ha a Windows PowerShell felületen keresztül éri el a StorSimple virtuális tömb, meg kell adnia egy eszköz rendszergazdai jelszót. A StorSimple eszköz első kiépítésekor és indításakor az alapértelmezett jelszó *a Password1*. Az adatok biztonsága érdekében az alapértelmezett jelszó az első bejelentkezéskor lejár, és módosítania kell ezt a jelszót.

A helyi webes felhasználói felület vagy az Azure Portal használatával is módosíthatja az eszköz rendszergazdai jelszavát az eszköz éles környezetben való üzembe helyezése után. Ezen eljárások mindegyikét ez a cikk ismerteti.

 ![eszközök panel](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>A jelszó módosítása az Azure Portalon

Hajtsa végre az alábbi lépéseket az eszköz rendszergazdai jelszavának az Azure Portalon keresztül történő módosításához.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Az eszközrendszergazda jelszavának módosítása az Azure Portalon keresztül

1. A szolgáltatás céllapján jelölje ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **Kezelés** csoportban kattintson az **Eszközök gombra.** Ez megnyitja az **eszközök** panelt, amely felsorolja az összes StorSimple virtuális tömb eszközök.

2. Az **Eszközök** panelen kattintson duplán arra az eszközre, amely jelszómódosítást igényel.

3. Az eszköz **Beállítások** paneljén kattintson a **Biztonság**gombra.

4. A **Biztonsági beállítások** panelen tegye a következőket:
   
   1. Görgessen le az **Eszközrendszergazdai jelszó** szakaszhoz. Adjon meg 8 és 15 karakter között lévő rendszergazdai jelszót.
   2. Erősítse meg a jelszót.
   3. Kattintson a **mentés** gombra a fűrészlap tetején.

Az eszköz rendszergazdai jelszava most frissül. Ezzel a módosított jelszóval helyileg érheti el az eszközt.

![Biztonsági beállítások panel](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>A jelszó módosítása a helyi webes felhasználói felületen

Hajtsa végre az alábbi lépéseket az eszközrendszergazda jelszavának módosításához a helyi webes felhasználói felületen keresztül.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Az eszközrendszergazda jelszavának módosítása a helyi webes felhasználói felületen keresztül

1. A helyi webes felhasználói felületen kattintson az eszköz **karbantartási** > **jelszó módosítása** elemre.
   
    ![jelszó módosítása1](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. Adja meg az **Aktuális jelszót**.
3. Adjon meg **egy új jelszót**. A jelszónak legalább 8 karakter hosszúnak kell lennie. A következők közül a 4-et kell tartalmaznia: nagybetűs, kisbetűs, numerikus és speciális karakterek.
   
    Ne feledje, hogy a jelszó nem egyezhet meg az utolsó 24 jelszóval.
4. Írja be ismét a jelszót a megerősítéséhez.
   
    ![jelszó módosítása2](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. A lap alján kattintson az **Alkalmaz gombra.** Az új jelszó alkalmazása most. Ha a jelszómódosítása nem sikerült, a következő hibaüzenet jelenik meg:
   
    ![jelszó hiba](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    A jelszó sikeres frissítése után értesítést kap. Ezután használhatja ezt a módosított jelszót az eszköz helyi eléréséhez.


## <a name="next-steps"></a>További lépések
További információ [a StorSimple virtuális tömb felügyeletéről.](storsimple-ova-web-ui-admin.md)


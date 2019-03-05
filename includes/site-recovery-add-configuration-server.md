---
title: fájl belefoglalása
description: fájl belefoglalása
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: include
ms.date: 02/28/2019
ms.author: mayg
ms.custom: include file
ms.openlocfilehash: 7c682105113dac7c1d457489cf926210ead77993
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57343567"
---
1. Futtassa az egyesített telepítő fájlját.
2. A **alapismeretek**válassza **a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése**.

    ![Előkészületek](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. A **Külső szoftver licence** területen kattintson az **Elfogadom** elemre a MySQL letöltéséhez és telepítéséhez.

    ![Külső gyártótól származó szoftverek](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. A **Regisztráció** területen válassza ki a kulcstartóból letöltött regisztrációs kulcsot.

    ![Regisztráció](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül. Győződjön meg arról, hogy engedélyezte a szükséges URL-címek.

    - Ha szeretne csatlakozni, amely jelenleg be van állítva a számítógépen, válassza a proxy **csatlakozás az Azure Site Recoveryhez proxykiszolgálóval**.
    - Ha azt szeretné, hogy a szolgáltatót, hogy közvetlenül kapcsolódjon, válassza ki a **közvetlen csatlakozás az Azure Site Recoveryhez proxykiszolgáló nélkül**.
    - Ha a meglévő proxy hitelesítést igényel, vagy ha egyéni proxyt használ a Provider csatlakoztatásához, válassza ki a kívánt **csatlakozás egyéni proxybeállításokkal**, és adja meg a cím, port és hitelesítő adatokat.
     ![Tűzfal](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

    ![Előfeltételek](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. A **környezet részletei**, jelölje be, nem, ha az Azure Stack-beli virtuális vagy fizikai kiszolgálókat replikál. 
9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

    ![Telepítés helye](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. A **hálózat kiválasztása**, először válassza ki a hálózati Adaptert, a beépített folyamatkiszolgáló által a felderítés és leküldéses telepítési forrásgépek a mobilitási szolgáltatást, és válassza ki a konfigurációs kiszolgáló által a kapcsolat hálózati adapter az Azure-ral. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. Ne használja a 443-as porton küldése vagy fogadása a replikációs forgalom számára.

    ![Hálózat kiválasztása](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.

    ![Összegzés](./media/site-recovery-add-configuration-server/combined-wiz10.png)

A regisztráció befejezését követően a kiszolgáló megjelenik a tároló **Beállítások** > **Kiszolgálók** paneljén.

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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178833"
---
1. Futtassa az egyesített telepítő fájlját.
2. A **Kezdés előtt**jelölje be a **Konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése**lehetőséget.

    ![Előkészületek](./media/site-recovery-add-configuration-server/combined-wiz1.png)

3. A **Külső szoftver licence** területen kattintson az **Elfogadom** elemre a MySQL letöltéséhez és telepítéséhez.

    ![Külső gyártótól származó szoftverek](./media/site-recovery-add-configuration-server/combined-wiz2.png)
4. A **Regisztráció** területen válassza ki a kulcstartóból letöltött regisztrációs kulcsot.

    ![Regisztráció](./media/site-recovery-add-configuration-server/combined-wiz3.png)
5. Az **Internetbeállítások** területen adja meg, hogy a konfigurációs kiszolgálón futó Provider hogyan csatlakozzon az Azure Site Recoveryhez az interneten keresztül. Győződjön meg arról, hogy engedélyezte a szükséges URL-eket.

    - Ha csatlakozni szeretne a számítógépen jelenleg beállított proxyhoz, válassza a Csatlakozás az **Azure Site Recovery szolgáltatáshoz proxykiszolgálóhasználatával lehetőséget.**
    - Ha azt szeretné, hogy a szolgáltató közvetlenül csatlakozzon, válassza **a Csatlakozás közvetlenül az Azure Site Recovery szolgáltatáshoz proxykiszolgáló nélkül**lehetőséget.
    - Ha a meglévő proxy hitelesítést igényel, vagy ha egyéni proxyt szeretne használni a szolgáltatói kapcsolathoz, válassza a **Csatlakozás egyéni proxybeállításokkal**lehetőséget, és adja meg a címet, a portot és a hitelesítő adatokat.
     ![Tűzfal](./media/site-recovery-add-configuration-server/combined-wiz4.png)
6. Az **Előfeltételek ellenőrzése** területen a telepítő ellenőrzi, hogy a telepítés végrehajtható-e. Ha megjelenik egy figyelmeztetés a **globális időszinkron ellenőrzéséről**, ellenőrizze, hogy a rendszeróra ideje (a **Dátum és idő** beállítások) megegyeznek-e az időzónával.

    ![Előfeltételek](./media/site-recovery-add-configuration-server/combined-wiz5.png)
7. A **MySQL-konfiguráció** területen hozza létre a telepített MySQL-kiszolgálópéldányra való bejelentkezéshez szükséges hitelesítő adatokat.

    ![MySQL](./media/site-recovery-add-configuration-server/combined-wiz6.png)
8. A **Környezet részletei**területen válassza a Nem lehetőséget, ha az Azure Stack virtuális gépek vagy fizikai kiszolgálók replikálása. 
9. A **Telepítés helye** területen válassza ki, hová szeretné telepíteni a bináris fájlokat, és hol kívánja tárolni a gyorsítótárat. A kiválasztott meghajtón legalább 5 GB szabad lemezterületre van szükség, de javasoljuk, hogy a gyorsítótárazáshoz használt lemezen legyen legalább 600 GB szabad hely.

    ![Telepítés helye](./media/site-recovery-add-configuration-server/combined-wiz8.png)
10. A **Hálózatkiválasztása területen**válassza ki a hálózati adaptert, amelyet a beépített folyamatkiszolgáló használ a mobilitási szolgáltatás felderítéséhez és leküldéses telepítéséhez a forrásgépeken, majd válassza ki azt a hálózati adaptert, amelyet a Configuration Server használ az Azure-ral való kapcsolathoz. A 9443-as port a replikációs forgalom küldésére és fogadására használt alapértelmezett port, ez azonban a környezeti követelményektől függően módosítható. A 9443-as port mellett a 443-as portot is megnyitjuk, amelyen keresztül egy webkiszolgáló a replikálási műveleteket vezényli. Ne használja a 443-as portot replikációs forgalom küldésére vagy fogadására.

    ![Hálózat kiválasztása](./media/site-recovery-add-configuration-server/combined-wiz9.png)


11. Az **Összefoglalás** területen ellenőrizze az adatokat, majd kattintson a **Telepítés** gombra. A telepítés után a rendszer létrehoz egy hozzáférési kódot. Erre szüksége lesz, amikor engedélyezi a replikálást, ezért másolja le, és tárolja biztonságos helyen.

    ![Összefoglalás](./media/site-recovery-add-configuration-server/combined-wiz10.png)

A regisztráció befejezése után a kiszolgáló megjelenik a **beállítások** > **kiszolgálói** panelen a tárolóban.

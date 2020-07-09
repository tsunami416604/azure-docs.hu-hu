---
title: Frissítés telepítése Azure Data Box Gateway Series-eszközön | Microsoft Docs
description: Ismerteti, hogyan alkalmazhatók a frissítések a Azure Portal és a helyi webes KEZELŐFELÜLET használatával Azure Data Box Gateway Series-eszközhöz
services: databox
author: priestlg
ms.service: databox
ms.topic: article
ms.date: 06/30/2020
ms.author: v-grpr
ms.openlocfilehash: 4c17488a875484b2d3dc0e7e8e1045ce8ea75cf0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85802131"
---
# <a name="update-your-azure-data-box-gateway"></a>A Azure Data Box Gateway frissítése

Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek a frissítés telepítéséhez a Azure Data Box Gateway a helyi webes felületen és a Azure Portalon keresztül. A szoftverfrissítések vagy gyorsjavítások alkalmazásával a Data Box Gateway-eszköz naprakészen tartható.

> [!IMPORTANT]
>
> - Az **1911** -es frissítés megfelel az eszközön **1.6.1049.786** szoftver verziójának. A frissítéssel kapcsolatos további információkért nyissa meg a [kibocsátási megjegyzéseket](data-box-gateway-1911-release-notes.md).
>
> - Ne feledje, hogy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Mivel a Data Box Gateway egyetlen csomópontos eszköz, a folyamatban lévő I/O-műveletek megszakadnak, és az eszközön az eszköz szoftverfrissítés-frissítése akár 30 percet is igénybe vehet.

Ezeket a lépéseket az alábbi szakaszokban ismertetjük.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

Javasoljuk, hogy telepítse a frissítéseket a Azure Portalon keresztül. Az eszköz naponta egyszer automatikusan megkeresi a frissítéseket. Ha a frissítések elérhetők, megjelenik egy értesítés a portálon. Ezután letöltheti és telepítheti a frissítéseket.

> [!NOTE]
> Győződjön meg arról, hogy az eszköz kifogástalan állapotban van, és az állapota **online** állapotba kerül, mielőtt folytatná a frissítések telepítését.

1. Ha a frissítések elérhetők az eszközhöz, egy értesítés jelenik meg. Válassza ki az értesítést, vagy a felső menüsorban **frissítse az eszközt**. Ez lehetővé teszi az eszköz szoftverfrissítések alkalmazását.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. A **Device Updates (eszközök frissítése** ) panelen győződjön meg arról, hogy áttekintette a kibocsátási megjegyzések új szolgáltatásaihoz társított licencfeltételeket.

    Választhat, hogy **letölti és telepíti** a frissítéseket, vagy csak **letölti** a frissítéseket. Ezután később is telepítheti ezeket a frissítéseket.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    Ha le szeretné tölteni és telepíteni kívánja a frissítéseket, ellenőrizze, hogy a frissítések telepítése automatikusan megtörtént-e a letöltés befejeződése után.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. A frissítések letöltése megkezdődik. Ekkor megjelenik egy értesítés arról, hogy a letöltés folyamatban van.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    Egy értesítési szalagcím is megjelenik a Azure Portalban. Ez a letöltési folyamatra utal.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    Kiválaszthatja ezt az értesítést, vagy az **eszköz frissítése** lehetőség kiválasztásával megtekintheti a frissítés részletes állapotát.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. A letöltés befejezését követően az értesítési szalagcím frissíti a befejezést. Ha úgy dönt, hogy letölti és telepíti a frissítéseket, a telepítés automatikusan elindul.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    Ha úgy döntött, hogy csak a frissítések letöltését választotta, válassza ki az értesítést az **eszköz frissítései** panel megnyitásához. Válassza a **Telepítés** gombot.
  
    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. Ekkor megjelenik egy értesítés arról, hogy a telepítés folyamatban van.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    A portál egy tájékoztató riasztást is megjelenít, amely jelzi, hogy a telepítés folyamatban van. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. Mivel ez egy 1 csomópontos eszköz, az eszköz a frissítések telepítése után újra fog indulni. Az újraindítás során a kritikus riasztás azt jelzi, hogy az eszköz szívverése megszakadt.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    Válassza ki a riasztást a megfelelő eszköz eseményének megtekintéséhez.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. A frissítések telepítése után az eszköz állapota **online** állapotra frissül.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    A felső menüsávban válassza az **eszközök frissítései**lehetőséget. Ellenőrizze, hogy a frissítés telepítése sikeres volt-e, és hogy az eszköz szoftverének verziója megfelel-e.

    ![Szoftverfrissítés utáni szoftververzió](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

- A frissítés vagy a gyorsjavítás letöltése
- A frissítés vagy a gyorsjavítás telepítése

Ezeket a lépéseket a következő szakaszokban részletesen ismertetjük.

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

A frissítés letöltéséhez hajtsa végre a következő lépéseket. A frissítést a Microsoft által biztosított helyről vagy a Microsoft Update katalógusból töltheti le.

Az alábbi lépések végrehajtásával töltse le a frissítést a Microsoft Update-katalógusból.

1. Indítsa el a böngészőt, és navigáljon a gombra [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

   ![Keresés a katalógusban](./media/data-box-gateway-apply-updates/download-update-1.png)

2. A Microsoft Update-katalógus keresőmező mezőjébe írja be a letölteni kívánt frissítés gyorsjavításának vagy használati feltételeinek a tudásbázisát (KB). Írja be például a következőt: **Azure Data Box Gateway**, majd kattintson a **Keresés**gombra.

   A frissítési lista **Azure Data Box Gateway 1911**-as értékkel jelenik meg.

   ![Keresés a katalógusban](./media/data-box-gateway-apply-updates/download-update-2.png)

3. Válassza a **Download** (Letöltés) lehetőséget. Egyetlen fájl tölthető le, amely megfelel az eszköz szoftverfrissítés-frissítésének *SoftwareUpdatePackage.exe* . Töltse le a fájlt a helyi rendszer egyik mappájába. A mappát átmásolhatja egy olyan hálózati megosztásra is, amely elérhető az eszközről.

   ![Keresés a katalógusban](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg az alábbiakról:

- A frissítés vagy a gyorsjavítás helyileg, a gazdagépen vagy egy hálózati megosztáson keresztül érhető el.
- Az eszköz állapota Kifogástalan, ahogy a helyi webes felhasználói felület **Áttekintés** lapján látható.

   ![eszköz frissítése](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

Az eljárás végrehajtása körülbelül 20 percet vesz igénybe. A frissítés vagy a gyorsjavítás telepítéséhez hajtsa végre a következő lépéseket.

1. A helyi webes kezelőfelületen lépjen a **karbantartási**  >  **szoftverfrissítés**elemre. Jegyezze fel a futtatott szoftver verzióját.

   ![eszköz frissítése](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. Adja meg a frissítési fájl elérési útját. Ha hálózati megosztásra helyezi, tallózással is megkeresheti a frissítési telepítési fájlt. Válassza ki *SoftwareUpdatePackage.exe* utótaggal rendelkező szoftverfrissítési fájlt.

   ![eszköz frissítése](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. Kattintson az **Alkalmaz** gombra.

   ![eszköz frissítése](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra a folytatáshoz. Mivel az eszköz egyetlen csomópontos eszköz, a frissítés alkalmazása után az eszköz újraindul, és leállási idő van.
   ![eszköz frissítése](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület ebben az időtartamban nem érhető el.

6. Az újraindítás után a rendszer a **bejelentkezési** oldalra kerül. Annak ellenőrzéséhez, hogy az eszköz szoftvere frissítve lett-e, a helyi webes felületen lépjen a **karbantartási**  >  **szoftverfrissítés**lapra. Ebben a példában a **1.6.1049.786**jelenik meg.

   ![eszköz frissítése](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>További lépések

További információ [a Azure Data Box Gateway felügyeletéről](data-box-gateway-manage-users.md).

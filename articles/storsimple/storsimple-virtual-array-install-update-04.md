---
title: Frissítések telepítése a StorSimple virtuális tömbre | Microsoft dokumentumok
description: A storSimple virtuális tömb webes felhasználói felületének használata a frissítések azure portal és gyorsjavítási módszer rel történő alkalmazásához
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: b67fcb82bdcc94d7faeceedb7420a869e6578cad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436391"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>A 0.4-es frissítés telepítése a StorSimple virtuális tömbre

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti a 0.4-es frissítés telepítéséhez szükséges lépéseket a StorSimple virtuális tömba a helyi webes felhasználói felületen keresztül és az Azure Portalon keresztül. A StorSimple virtuális tömb naprakészen tartásához szoftverfrissítéseket vagy gyorsjavításokat kell alkalmaznia. 

Ne feledje, hogy egy frissítés vagy gyorsjavítás telepítése újraindítja az eszközt. Tekintettel arra, hogy a StorSimple virtuális tömb egyetlen csomópontú eszköz, minden folyamatban lévő I/O megszakad, és az eszköz leállást tapasztal. 

A frissítés alkalmazása előtt javasoljuk, hogy először a köteteket vagy megosztásokat állítsa offline állapotba a gazdagépen, majd az eszközön. Ezzel a lehető legkisebbre csökkenti az adatsérülés esélyét.

> [!IMPORTANT]
> Ha a 0.1-es frissítés vagy a GA szoftververziókat futtatja, a 0.3-as frissítés telepítéséhez a gyorsjavítási módszert kell használnia a helyi webes felhasználói felületen keresztül. Ha a 0.2-es vagy újabb frissítést futtatja, javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül.
 

## <a name="use-the-local-web-ui"></a>A helyi webes felhasználói felület használata

A helyi webes felhasználói felület használata két lépésből áll:

* A frissítés vagy a gyorsjavítás letöltése
* A frissítés vagy a gyorsjavítás telepítése

### <a name="download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

Hajtsa végre a következő lépéseket a szoftverfrissítés a Microsoft Update katalógusból történő letöltéséhez.

#### <a name="to-download-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás letöltése

1. Indítsa el az [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)Internet Explorer t, és keresse meg a programot.

2. Ha most használja először a Microsoft Update katalógust ezen a számítógépen, kattintson a **Telepítés** gombra, amikor a rendszer a Microsoft Update katalógus beépülő moduljának telepítésére kéri.

3. A Microsoft Update katalógus keresőmezőjébe írja be a letölteni kívánt gyorsjavítás Tudásbázisának (KB) számát. Írja be a **3216577-es értéket** a 0.4-es frissítéshez, majd kattintson a **Keresés**gombra.
   
    Megjelenik a gyorsjavítás-lista, például a **StorSimple Virtual Array Update 0.4**.
   
    ![Keresés a katalógusban](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Kattintson a **Hozzáadás** gombra. Ezzel a frissítést hozzáadja a kosárhoz.

5. Kattintson a **Kosár megtekintése** gombra.

6. Kattintson **a Letöltés gombra.** Adja meg vagy **tallózással** válassza ki a helyet, ahová a fájlokat le szeretné tölteni. A frissítések a megadott helyre lesznek letöltve, azon belül az egyes frissítések nevével egyező nevű almappákba. A mappa átmásolható egy, az eszközről elérhető hálózati megosztásra is.

7. Nyissa meg a másolt mappát, és meg `WindowsTH-KB3011067-x64`kell jelennie egy Microsoft Update önálló csomagfájlnak. Ez a fájl a frissítés vagy a gyorsjavítás telepítésére szolgál.

### <a name="install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

A frissítés vagy a gyorsjavítás telepítése előtt győződjön meg arról, hogy a frissítést vagy a gyorsjavítást helyileg töltötte le a gazdagépen, vagy elérhető egy hálózati megosztáson keresztül. 

Ezzel a módszerrel ga vagy Update 0.1 szoftververziókat futtató eszközökön telepíthet frissítéseket. Ez az eljárás kevesebb, mint 2 percet vesz igénybe. A frissítés vagy gyorsjavítás telepítéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-install-the-update-or-the-hotfix"></a>A frissítés vagy a gyorsjavítás telepítése

1. A helyi internetes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című webhelyet.**
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update1m.png)

2. A **Fájl elérési útjának frissítése**mezőbe írja be a frissítés vagy a gyorsjavítás fájlnevét. A frissítési vagy gyorsjavítás-telepítési fájlba is tallózhat, ha hálózati megosztásra helyezi. Kattintson az **Alkalmaz** gombra.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update2m.png)

3. Figyelmeztetés jelenik meg. Mivel ez egy csomópontos eszköz, a frissítés alkalmazása után az eszköz újraindul, és van állásidő. Kattintson az ellenőrzés ikonra.
   
   ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update3m.png)

4. A frissítés elindul. Az eszköz sikeres frissítése után újraindul. A helyi felhasználói felület nem érhető el ebben az időtartamban.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update5m.png)

5. Az újraindítás befejezése után a **bejelentkezési** lapra kerül. Az eszközszoftver frissítésének ellenőrzéséhez a helyi webes felhasználói felületen nyissa meg a **Karbantartási** > **szoftverfrissítés című**részt. A megjelenített szoftververziónak **10.0.0.0.0.10289.0-nak** kell lennie a 0.4-es frissítéshez.
   
   > [!NOTE]
   > A szoftververziókat némileg eltérő módon jelentjük a helyi webes felhasználói felületen és az Azure Portalon. A helyi webes felhasználói felület például **a 10.0.0.0.0.10289-et** jelenti, az Azure Portal pedig **a 10.0.10289.0-t** ugyanahhoz a verzióhoz.
   
    ![eszköz frissítése](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

A 0.2-es és újabb frissítés futtatásaesetén azt javasoljuk, hogy telepítse a frissítéseket az Azure Portalon keresztül. A portáleljárás megköveteli a felhasználótól a frissítések befrissítését, letöltését és telepítését. Ez az eljárás körülbelül 7 percet vesz igénybe. A frissítés vagy gyorsjavítás telepítéséhez hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

A telepítés befejezése után (amint azt a feladat állapota 100 %-on jelzi, nyissa meg a StorSimple Eszközkezelő szolgáltatást. Válassza **az Eszközök lehetőséget,** majd jelölje ki és kattintson a frissíteni kívánt eszközre a szolgáltatáshoz csatlakoztatott eszközök listájából. A **Beállítások** panelen válassza a **Kezelés szakaszt,** és válassza **az Eszközfrissítések**lehetőséget. A megjelenített szoftververziónak **10.0.10289.0-nak**kell lennie.


## <a name="next-steps"></a>További lépések

További információ [a StorSimple virtuális tömb felügyeletéről.](storsimple-ova-web-ui-admin.md)


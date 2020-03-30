---
title: Értékelési eszközök hozzáadása az Azure Áttelepítése szolgáltatásban
description: Ismerje meg, hogyan adhat hozzá értékelési eszközöket az Azure Migrate szolgáltatásban.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185914"
---
# <a name="add-assessment-tools"></a>Felmérőeszközök hozzáadása

Ez a cikk azt ismerteti, hogyan adhat hozzá értékelési eszközöket az [Azure Migrate](migrate-overview.md)szolgáltatásban.

Az Azure Migrate az értékelések és az Azure-ba való migrálás eszközeinek központja. Ez magában foglalja az Azure Migrate eszközöket, valamint más eszközöket és független szoftverszállítói (ISV) ajánlatokat.

Ha szeretne hozzáadni egy értékelési eszközt, és még nem rendelkezik Azure Migrate projekttel, kövesse ezt a [cikket.](how-to-add-tool-first-time.md)

## <a name="select-a-tool"></a>Eszköz kijelölése

Ha nem Azure-áttelepítési eszközt választ az értékeléshez, kezdje egy licenc beszerzésével, vagy az eszközszabályzatnak megfelelően egy ingyenes próbaverzióra való feliratkozással. Az eszközök rendelkeznek az Azure Migrate szolgáltatáshoz való csatlakozáslehetőséggel. Kövesse az utasításokat és a dokumentációt, hogy csatlakoztassa az eszközt az Azure Migrate. [További információ](migrate-services-overview.md) az eszközökről.


## <a name="select-an-assessment-scenario"></a>Értékelési forgatókönyv kiválasztása

1. Az Azure Migrate projektben kattintson **az Áttekintés gombra.**
2. Válassza ki a használni kívánt értékelési forgatókönyvet:

    - Az Azure-ba való áttelepítéshez szükséges gépek és munkaterhelések felderítéséhez és értékeléséhez válassza **a Kiszolgálók felmassza és áttelepítése lehetőséget.**
    - A helyszíni SQL-gépek értékeléséhez válassza **az Adatbázisok felmérése és áttelepítése**lehetőséget.
    - A helyszíni webalkalmazások értékeléséhez válassza **a Webalkalmazások értékelése és áttelepítése**lehetőséget.

    ![Értékelési forgatókönyv](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Kiszolgálóértékelési eszköz kiválasztása 

1. Kattintson **a Kiszolgálók felmérése és áttelepítése gombra.**
2. Ha **Azure Migrate - Servers**még nem adott hozzá értékelési eszközt, kattintson az **Értékelési** **eszköz hozzáadásához kattintson ide.** Ha már hozzáadott értékelési eszközöket, a **További értékelési eszközök hozzáadása**csoportban válassza a Módosítás **lehetőséget.**

    > [!NOTE]
    > Ha egy másik projektre szeretne navigálni, az **Azure Áttelepítés – Kiszolgálók**csoportban válassza egy **másik áttelepítési projekt részleteinek megtekintése**lehetőséget, kattintson **ide.**

3. Az **Azure Migrate**alkalmazásban válassza ki a használni kívánt értékelési eszközt.

    - Ha az Azure Migrate Server Assessment, beállíthatja, futtathatja és megtekintheti értékelések közvetlenül az Azure Migrate projektben.
    - Ha másik értékelési eszközt használ, keresse meg a webhelyhez megadott linket, és futtassa az értékelést az általuk megadott utasításoknak megfelelően.


## <a name="select-a-database-assessment-tool"></a>Adatbázis-értékelési eszköz kiválasztása

1. Kattintson **az Adatbázisok értékelése és áttelepítése parancsra.**
2. Az **Adatbázisok csoportban**kattintson az **Eszközök hozzáadása gombra.**
3. A Eszköz hozzáadása > **Az értékelési eszköz kiválasztása eszközben**válassza ki az adatbázis értékeléséhez használni kívánt eszközt.

## <a name="select-a-web-app-assessment-tool"></a>Webalkalmazás-értékelési eszköz kiválasztása

1. Kattintson **a Webalkalmazások értékelése és áttelepítése gombra.**
2. Kövesse az Azure App Service áttelepítési eszközére mutató hivatkozást. Az áttelepítési eszközzel:

    - **Alkalmazások online felmérése:** Az Azure App Service Migration Assistant használatával online értékelheti a nyilvános URL-címmel rendelkező alkalmazásokat.
    - **.NET/PHP**: Belső .NET és PHP alkalmazások esetén letöltheti és futtathatja az Áttelepítési segédet.



## <a name="next-steps"></a>További lépések

Kipróbálhat egy felmérést az Azure Migrate Server Assessment for [VMware](tutorial-prepare-vmware.md) VM, [Hyper-V](tutorial-prepare-hyper-v.md)vagy [fizikai kiszolgálók](tutorial-prepare-physical.md) használatával

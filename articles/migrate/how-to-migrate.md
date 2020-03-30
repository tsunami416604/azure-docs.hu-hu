---
title: Áttelepítési eszközök hozzáadása az Azure Áttelepítése szolgáltatásban
description: Ismerje meg, hogyan adhat hozzá áttelepítési eszközöket az Azure Migrate szolgáltatásban.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185960"
---
# <a name="add-migration-tools"></a>Migrálási eszközök hozzáadása

Ez a cikk bemutatja, hogyan adhat hozzá áttelepítési eszközöket az [Azure Áttelepítés szolgáltatásban.](migrate-overview.md)

Az Azure Migrate az értékelések és az Azure-ba való migrálás eszközeinek központja. Ez magában foglalja a natív eszközök, eszközök által biztosított más Azure-szolgáltatások, és a külső független szoftverszállító (ISV) ajánlatok.

Ha egy áttelepítési eszközt szeretne hozzáadni, és még nem állított be Azure Migrate projektet, kövesse ezt a [cikket.](how-to-add-tool-first-time.md)



## <a name="selecting-an-isv-tool"></a>Független eszköz kiválasztása

Ha az áttelepítéshez [is fontos eszköz](migrate-services-overview.md#isv-integration) lehetőséget választ, először is beszerezhet egy licencet, vagy regisztrálhat egy ingyenes próbaverzióra, a független eszközegyesítési irányelv szabályzatának megfelelően. Az egyes eszközökben van egy lehetőség az Azure Migrate való csatlakozáshoz. Telepítse az eszközt, és kövesse az eszköz utasításait és dokumentációját az eszköz munkaterület ének azure migratehez való csatlakoztatásához. 

## <a name="select-a-migration-scenario"></a>Áttelepítési forgatókönyv kiválasztása

1. Az Azure Migrate projektben kattintson **az Áttekintés gombra.**
2. Válassza ki a használni kívánt áttelepítési forgatókönyvet:

    - A gépek és munkaterhelések Azure-ba való áttelepítéséhez válassza **a Kiszolgálók felmassza és áttelepítése lehetőséget.**
    - A helyszíni SQL-gépek áttelepítéséhez válassza **az Adatbázisok felmérése és áttelepítése**lehetőséget.
    - A helyszíni webalkalmazások áttelepítéséhez válassza **a Webalkalmazások értékelése és áttelepítése**lehetőséget.
    - Ha nagy mennyiségű helyszíni adatot szeretne áttelepíteni az Azure-ba offline módban, válassza **az Adatmező rendelése**lehetőséget.

    ![Értékelési forgatókönyv](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Kiszolgálóáttelepítési eszköz kijelölése

1. Kattintson **a Kiszolgálók felmérése és áttelepítése gombra.**
2. Ha még nem adott hozzá áttelepítési eszközöket az **Azure Migrate – Servers alkalmazásban,** az **Áttelepítési eszközök csoportban** **kattintson ide az áttelepítési eszköz hozzáadásához.** Ha már hozzáadott áttelepítési eszközöket, a **További áttelepítési eszközök hozzáadása**csoportban válassza a Módosítás **lehetőséget.**

    > [!NOTE]
    > Ha egy másik projektre szeretne navigálni, az **Azure Áttelepítés – Kiszolgálók**csoportban válassza egy **másik áttelepítési projekt részleteinek megtekintése**lehetőséget, kattintson **ide.**

3. Az **Azure Migrate**alkalmazásban válassza ki a használni kívánt áttelepítési eszközt.
    - Ha az Azure Migrate Server Migration, beállíthatja, és futtathatja az áttelepítések közvetlenül az Azure Migrate projektben.
    - Ha külső értékelési eszközt használ, keresse meg a szoftverszoftverhez megadott hivatkozást, és futtassa az áttelepítést az általuk megadott utasításoknak megfelelően.

## <a name="select-a-database-migration-tool"></a>Adatbázis-áttelepítési eszköz kijelölése

1. Kattintson **az Adatbázisok értékelése és áttelepítése parancsra.**
2. Az **Adatbázisok csoportban**kattintson az **Eszközök hozzáadása gombra.**
3. Az Eszköz hozzáadása > **Az áttelepítés kiválasztása eszközben**jelölje ki az adatbázis áttelepítéséhez használni kívánt eszközt.

## <a name="select-a-web-app-migration-tool"></a>Webalkalmazás-áttelepítési eszköz kiválasztása

1. Kattintson **a Webalkalmazások értékelése és áttelepítése gombra.**
2. Kövesse az Azure App Service áttelepítési eszközére mutató hivatkozást. Az áttelepítési eszközzel:

    - **Alkalmazások online felmérése:** Az Azure App Service Migration Assistant használatával felmérheti és áttelepítheti az alkalmazásokat egy nyilvános URL-címmel az interneten.
    - **.NET/PHP**: Belső .NET és PHP alkalmazások esetén letöltheti és futtathatja az Áttelepítési segédet.

## <a name="order-an-azure-data-box"></a>Azure-adatdoboz megrendelése

Nagy mennyiségű adat Azure-ba való áttelepítéséhez rendelhet egy Azure DAta Box offline adatátvitelhez.

1. Kattintson **az Adatmező rendelése gombra.**
2. Az **Azure-adatok kiválasztása párbeszédpanelen**adja meg az előfizetést. 
3. Az átvitel az Azure-ba való importálás lesz. Adja meg az adatforrást és az adatok Azure-régió célját.

## <a name="next-steps"></a>További lépések

Próbálja ki az áttelepítést az Azure Migrate Server Migration for [Hyper-V](tutorial-migrate-hyper-v.md) vagy [vMware](tutorial-migrate-vmware.md) virtuális gépek használatával.

---
title: Értékelési eszközök hozzáadása az Azure Migrate |} A Microsoft Docs
description: Értékelési eszközök hozzáadása az Azure Migrate hubban módját ismerteti.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811647"
---
# <a name="add-assessment-tools"></a>Értékelési eszközök hozzáadása

Ez a cikk bemutatja, hogyan adhat hozzá az értékelési eszközök [Azure Migrate](migrate-overview.md).

Az Azure Migrate elemzésekhez és migráláshoz hub eszközöket biztosít az Azure-bA. Ez magában foglalja natív eszközök más Azure-szolgáltatásokat, és a külső független szoftverszállítók (ISV) szállító ajánlatok által biztosított eszközökkel.

Ha szeretne hozzáadni a frissítésfelmérő eszközt, és még nem rendelkezik Azure Migrate-projektet, kövesse ezt [cikk](how-to-add-tool-first-time.md).

## <a name="selecting-an-isv-tool"></a>Egy független Szoftverszállító eszköz kiválasztása

Ha úgy dönt, egy [ISV eszköz](migrate-services-overview.md#isv-integration) értékelés, első lépésként licenc, vagy regisztráljon egy ingyenes próbaverzióra, az ISV-szabályzatának megfelelően. Minden egyes eszköz csatlakozni az Azure Migrate lehetőség van. Kövesse az eszköz utasítások és a dokumentáció az Azure Migrate az eszköz munkaterület csatlakozni. 


## <a name="select-an-assessment-scenario"></a>Egy értékelés választása

1. Az Azure Migrate-projektben kattintson **áttekintése**.
2. Válassza ki a használni kívánt értékelés forgatókönyvet:

    - Fedezze fel, és felmérheti a gépek és a számítási feladatok migrálásának az Azure-ba, válassza ki a **felmérési és a kiszolgálók áttelepítése**.
    - Válassza ki a helyszíni SQL gépek értékeléséhez, **felmérési és migrálhat adatbázisokat**.
    - Annak ellenőrzéséhez, a helyszíni webalkalmazásokat, válassza ki a **felmérési és a web Apps alkalmazások áttelepítése a**.

    ![Értékelés forgatókönyv](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Válassza ki a kiszolgáló frissítésfelmérő eszköz 

1. Kattintson a **értékelni és Migrálni a kiszolgálók**.
2. A **Azure Migrate - kiszolgálók**, ha a frissítésfelmérő eszközt a még nem vett **értékelési eszközök**válassza **Ide kattintva hozzáadhat egyet egy frissítésfelmérő eszköz**. Ha már hozzáadott felmérőeszközöket, a **adjon hozzá további felmérőeszközöket**válassza **módosítás**.

    > [!NOTE]
    > Ha szeretné-e egy másik projekthez, navigáljon a **Azure Migrate - kiszolgálók**mellett található **különböző migrate projekt részleteit lásd**, kattintson a **kattintson ide**.

3. A **Azure Migrate**, válassza ki a használni kívánt frissítésfelmérő eszköz.

    
    ![Értékelési eszközök](./media/how-to-assess/assess-tool.png)

    - Ha használja az Azure Migrate Server Assessment, állítsa be, futtatja, és közvetlenül az Azure Migrate-projektben értékelések megtekintése.
    - Ha egy külső frissítésfelmérő eszközt használ, lépjen a hivatkozásra helyük megadott, és az utasításoknak nyújtanak az értékelés futtatása.


## <a name="select-a-database-assessment-tool"></a>Válassza ki egy adatbázist frissítésfelmérő eszköz

1. Kattintson a **felmérési és migrálhat adatbázisokat**
2. A **adatbázisok**, kattintson a **eszközök hozzáadása**.
3. Az eszköz hozzáadása > **válassza frissítésfelmérő eszköz**, válassza ki az eszközt annak ellenőrzéséhez, az adatbázishoz használni kívánt.

## <a name="select-a-web-app-assessment-tool"></a>Válassza ki a web app frissítésfelmérő eszköz

1. Kattintson a **felmérési és a web Apps alkalmazások áttelepítése a**.
2. Kövesse a hivatkozást a Migrálási eszközben, az Azure App Service. Az áttelepítési eszköz használata:

    - **Mérje fel az alkalmazások online**: Egy nyilvános URL-cím, az alkalmazások használata az Azure App Service Migration Assistant értékelhet.
    - **.NET/PHP**: Belső .NET és a PHP-alkalmazások esetén töltse le, és futtassa az áttelepítési Segéd.



## <a name="next-steps"></a>További lépések

Próbálja ki az Azure Migrate Server Assessment használatával felmérési [Hyper-V](tutorial-prepare-hyper-v.md) vagy [VMware](tutorial-prepare-vmware.md) virtuális gépeket.

---
title: Gépek csoportosítása az Azure Migrate szolgáltatással kapcsolatos értékeléshez | Microsoft dokumentumok
description: Bemutatja, hogyan csoportosíthatja a gépeket, mielőtt az Azure Migrate szolgáltatással futtatna egy értékelést.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68301703"
---
# <a name="create-a-group-for-assessment"></a>Csoport létrehozása értékelésre

Ez a cikk ismerteti, hogyan hozhat létre gépcsoportokat az Azure Áttelepítés: Kiszolgálóértékelése.

[Az Azure Migrate](migrate-services-overview.md) segítségével áttelepülaz Azure-ba. Az Azure Migrate egy központi központot biztosít a helyszíni infrastruktúra, alkalmazások és adatok Azure-ba való felderítésének, értékelésének és áttelepítésének nyomon követéséhez. A központ Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint külső független szoftverszállítói (ISV) ajánlatokhoz. 

## <a name="grouping-machines"></a>Csoportosító gépek

A gépeket csoportokba gyűjtheti, hogy felmérje, alkalmasak-e az Azure-ba való migrálásra, és hogy az Azure méretezését és költségbecsléseit bekérhesse számukra. A csoportok létrehozásának több módja is van:

- Ha ismeri a gépeket, amelyeket együtt kell áttelepíteni, manuálisan létrehozhatja a csoportot az Azure Migrate.If you know the machines that need rererered together, you can manually create the group in Azure Migrate.
- Ha nem biztos a gépek, amelyeket össze kell csoportosítani, használhatja a függőségi vizualizációs funkció az Azure Migrate csoportok létrehozásához. 

> [!NOTE]
> A függőségi vizualizációs funkció nem érhető el az Azure Government ben.

## <a name="create-a-group-manually"></a>Csoport manuális létrehozása

A csoportot a felméréssel egy időben is [létrehozhatja.](how-to-create-assessment.md)

Ha az értékelés létrehozásán kívül manuálisan szeretne csoportot létrehozni, tegye a következőket:

1. Az Azure Migrate projekt > **áttekintése**( Áttekintés ) kattintson **a Kiszolgálók felmérése és áttelepítése**elemre. Az **Azure Migrate: Server Assessment (Kiszolgálói felmérés)** menüben kattintson **a Csoportok elemre.**
    - Ha még nem adta hozzá az Azure Migrate: Server Assessment eszközt, kattintson ide a hozzáadáshoz. [További információ](how-to-assess.md).
    - Ha még nem hozott létre Azure Migrate projektet, [további információ.](how-to-add-tool-first-time.md)

    ![Csoportok kijelölése](./media/how-to-create-a-group/select-groups.png)

2. Kattintson a **Csoport** ikonra.
3. A **Csoport létrehozása csoportban**adja meg a csoport nevét, és a **készülék neve,** válassza ki az Azure Migrate készüléket használ a gépfelderítéshez.
1. A számítógéplistában válassza ki a Létrehozás > csoporthoz hozzáadni kívánt **gépeket.**

    ![Csoport létrehozása](./media/how-to-create-a-group/create-group.png)

Ezt a csoportot most már [használhatja,](how-to-create-assessment.md)amikor értékelést hoz létre.

## <a name="refine-a-group-with-dependency-mapping"></a>Függőségleképezéssel rendelkező csoport finomítása

A függőségi leképezés segít a gépek közötti függőségek megjelenítésében. Általában függőségi leképezést használ, ha magasabb szintű megbízhatósággal szeretné felmérni a gépcsoportokat.
- Segít a számítógép-függőségek keresztellenőrzésében, mielőtt egy felmérést futtatna. 
- Emellett segít az Azure-ba való migrálás hatékony megtervezésében, biztosítva, hogy semmi ne maradjon le, és így elkerülve a váratlan kimaradásokat az áttelepítés során.
- Felderítheti az egymástól függő rendszereket, amelyeknek együtt kell áttérniük, és meghatározhatja, hogy egy futó rendszer továbbra is szolgálja-e a felhasználókat, vagy az áttelepítés helyett leszerelésre jelölt.

Ha már [beállította a függőségi leképezést,](how-to-create-group-machine-dependencies.md)és egy meglévő csoportot szeretne finomítani, tegye a következőket:

1. A **Kiszolgálók** lap **Azure Migrate: Server Assessment (Kiszolgálói felmérés)** csempéjében kattintson a **Csoportok**elemre.
2. Kattintson a finomítani kívánt csoportra.
    - Ha még nem állított be függőségi leképezést, a **Függőségek** oszlopban megjelenik a **Szükséges telepítési** állapot. Minden olyan virtuális gépesetében, amelynek függőségeit meg szeretné vizivizíteni, kattintson **a Telepítés iránti gombra.** Telepítsen néhány ügynököt minden virtuális gépre, mielőtt leképezheti volna a gépfüggőségeket. [További információ](how-to-create-group-machine-dependencies.md).

        ![Függőségleképezés hozzáadása](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Ha már beállította a függőségi leképezést, a csoportlapon kattintson a **Függőségek megtekintése** elemre a csoportfüggőségi leképezés megnyitásához.

3. A **Függőségek megtekintése**gombra kattintás után a csoportfüggőségi térkép a következőket jeleníti meg:

    - Bejövő (ügyfelek) és kimenő (kiszolgálók) TCP-kapcsolatok a csoport összes olyan gépére, amelyen telepítve vannak a függőségi ügynökök.
    - Azok a függő gépek, amelyeken nincs telepítve a függőségi ügynökök, portszámok szerint vannak csoportosítva.
    - A függőségi ügynökökkel rendelkező függő gépek külön mezőkként jelennek meg.
    - A gépbelsejében futó folyamatok. A folyamatok megtekintéséhez bontsa ki az egyes gépeket.
    - A számítógép tulajdonságai (beleértve az FQDN-t, az operációs rendszert, a MAC-címet). Kattintson az egyes gépdobozra a részletek megtekintéséhez.

4. Ha a függőségeket egy ön által választott időintervallumban szeretné megtekinteni, módosítsa az időtartományt (alapértelmezés szerint egy óra) a kezdési és befejezési dátumok, illetve az időtartam megadásával.

    > [!NOTE]
    > Az időtartomány akár egy óra is lehet. Ha hosszabb tartományra van szüksége, az Azure Monitor használatával hosszabb ideig [kérdezheti le](how-to-create-group-machine-dependencies.md) a függő adatokat.

5. Miután azonosította azokat a függőségeket, amelyeket hozzá szeretne adni a csoporthoz, vagy eltávolítja őket, módosíthatja a csoportot. A Ctrl+Click billentyűkombinációval gépeket vehet fel vagy távolíthat el a csoportból.

    - Csak a felderített gépeket adhat hozzá.
    - Gépek hozzáadása és eltávolítása érvényteleníti a csoport korábbi felméréseit.
    - A csoport módosításakor tetszés szerint létrehozhat egy új értékelést.


## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan állíthatja be és használhatja a [függőségi leképezést](how-to-create-group-machine-dependencies.md) magas megbízhatósági csoportok létrehozásához.


---
title: Ügynök nélküli függőségelemzés beállítása az Azure Áttelepítési kiszolgáló felmérésében
description: Ügynök nélküli függőségelemzés beállítása az Azure Áttelepítési kiszolgáló értékelésében.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: af767bf73a3b9a6f2a91298987f11974499fd694
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455706"
---
# <a name="set-up-agentless-dependency-visualization"></a>Ügynök nélküli függőségi megjelenítés beállítása 

Ez a cikk bemutatja, hogyan állíthatja be ügynök nélküli függőségi elemzést az Azure Áttelepítés:Kiszolgáló értékelése. [A függőségi elemzés](concepts-dependency-visualization.md) segítségével azonosíthatja és megértheti a felmérni kívánt és az Azure-ba áttelepíteni kívánt gépek közötti függőségeket.


> [!IMPORTANT]
> Ügynök nélküli függőségi vizualizáció jelenleg előzetes verzióban csak vmware virtuális gépek, az Azure Migrate:Server Assessment eszközzel felderített.
> A szolgáltatások korlátozottak vagy hiányosak lehetnek.
> Ezt az előzetes verziót az ügyfélszolgálat fedezi, és éles számítási feladatokhoz használható.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)



## <a name="before-you-start"></a>Előkészületek

- [Ismerje meg](concepts-dependency-visualization.md#agentless-analysis) az ügynök nélküli függőségi elemzést.
- [Tekintse át](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) a VMware virtuális gépek ügynök nélküli függőségi vizualizációjának beállításához szükséges előfeltételeket és támogatási követelményeket
- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [hozzáadta](how-to-assess.md) az Azure Migrate:Server Assessment eszközt.
- Győződjön meg arról, hogy beállítottegy [Azure Migrate-berendezést](migrate-appliance.md) a helyszíni gépek felderítéséhez. További információ a rról, hogyan állíthat be egy készüléket [a VMware](how-to-set-up-appliance-vmware.md) virtuális gépekhez. A készülék felderíti a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld az Azure Migrate:Server Assessment szolgáltatásnak.


## <a name="current-limitations"></a>Aktuális korlátozások

- Jelenleg nem lehet hozzáadni vagy eltávolítani egy kiszolgálót egy csoportból a függőségelemző nézetben.
- Kiszolgálók egy csoportjának függőségi leképezése jelenleg nem érhető el.
- Jelenleg a függőségi adatok nem tölthetők le táblázatos formátumban.

## <a name="create-a-user-account-for-discovery"></a>Felhasználói fiók létrehozása felderítéshez

Állítson be egy felhasználói fiókot, hogy a kiszolgálófelmérés hozzáférhessen a virtuális gép felderítéséhez. [További információ](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) a fiókkövetelményekről.


## <a name="add-the-user-account-to-the-appliance"></a>Felhasználói fiók hozzáadása a készülékhez

Adja hozzá a felhasználói fiókot a készülékhez.

1. Nyissa meg a készülékkezelő alkalmazást. 
2. Nyissa meg a **VCenter-részletek panelt.**
3. Az **Alkalmazás és a virtuális gépekfüggőségek felderítése**területen kattintson a Hitelesítő adatok **hozzáadása** elemre.
3. Válassza ki az **Operációs rendszert**, adja meg a fiók rövid nevét, és adja meg a**Jelszó** **felhasználónevet**/
6. Kattintson a **Mentés** gombra.
7. Kattintson a **Mentés gombra, és indítsa el a felderítést.**

    ![Virtuálisgép felhasználói fiókjának hozzáadása](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Függőségfelderítés indítása

Válassza ki azaz gépeket, amelyeken engedélyezni szeretné a függőségfelderítést.

1. Az **Azure Migrate: Server Assessment (Azure Migrate: Server Assessment)** alkalmazásban kattintson **a Felderített kiszolgálók**elemre.
2. Kattintson a **Függőségelemzés** ikonra.
3. Kattintson **a Kiszolgálók hozzáadása gombra.**
3. A **Kiszolgálók hozzáadása** lapon válassza ki a megfelelő gépeket felfedező készüléket.
4. A gépek listájából válassza ki a gépeket.
5. Kattintson **a Kiszolgálók hozzáadása gombra.**

    ![Függőségfelderítés indítása](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

A függőségek körülbelül hat órával a függőségek felderítésének megkezdése után jeleníthető meg.

## <a name="visualize-dependencies"></a>Függőségek megjelenítése

1. Az **Azure Migrate: Server Assessment (Azure Migrate: Server Assessment)** alkalmazásban kattintson **a Felderített kiszolgálók**elemre.
2. Keresse meg a megtekinteni kívánt gépet.
3. A **Függőségek** oszlopban kattintson a **Függőségek megtekintése elemre.**
4. Módosítsa azt az időszakot, amelynek a térképét meg szeretné tekinteni az **Idő időtartam** legördülő menüvel.
5. Bontsa ki az **Ügyfél** csoportot a kijelölt géptől függő gépek listázásához.
6. Bontsa ki a **Port** csoportot a kijelölt géptől függőségben lévő gépek listázásához.
7. Bármely függő gép térképnézetének megnézetéhez kattintson a számítógép nevére > **Kiszolgálóbetöltési térképre**

    ![Kiszolgálóportcsoport kibontása és kiszolgálóleképezés betöltése](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Ügyfélcsoport kibontása ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Bontsa ki a kijelölt gépet az egyes függőségek folyamatszintű részleteinek megtekintéséhez.

    ![A kiszolgáló kibontása a folyamatok megjelenítéséhez](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> A függőség feldolgozási adatai nem mindig érhetők el. Ha nem érhető el, a függőség "Ismeretlen folyamat" néven van megjelölve.

## <a name="stop-dependency-discovery"></a>Függőségfelderítés leállítása

Válassza ki azaz gépeket, amelyeken le szeretné állítani a függőségfelderítést.

1. Az **Azure Migrate: Server Assessment (Azure Migrate: Server Assessment)** alkalmazásban kattintson **a Felderített kiszolgálók**elemre.
2. Kattintson a **Függőségelemzés** ikonra.
3. Kattintson **a Kiszolgálók eltávolítása gombra.**
3. A **Kiszolgálók eltávolítása** lapon válassza ki azt a **készüléket,** amely a függőségfelderítés leállításához kívánt virtuális gépeket észleli.
4. A gépek listájából válassza ki a gépeket.
5. Kattintson **a Kiszolgálók eltávolítása gombra.**


## <a name="next-steps"></a>További lépések

[Csoportosítsa a gépeket](how-to-create-a-group.md) az értékeléshez.

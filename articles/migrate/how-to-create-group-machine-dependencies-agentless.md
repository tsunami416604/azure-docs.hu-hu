---
title: Az ügynök nélküli függőségi vizualizáció beállítása a Azure Migrateban
description: Csoportok beállítása ügynök nélküli függőségi vizualizációval Azure Migrate kiszolgáló értékelése során.
ms.topic: article
ms.date: 2/24/2020
ms.openlocfilehash: c9425ad1fa78f14a194d3fe13c259dadf4eb5eb6
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77589130"
---
# <a name="set-up-agentless-dependency-visualization"></a>Ügynök nélküli függőségi vizualizáció beállítása 

Ez a cikk azt ismerteti, hogyan állítható be a függőségi vizualizáció a Azure Migrateban: kiszolgáló értékelése. A függőségi [vizualizáció](concepts-dependency-visualization.md#what-is-dependency-visualization) segítségével azonosíthatja és értelmezheti a függőségeket az Azure-ba felmérni és migrálni kívánt gépek között.

Az ügynök nélküli függőségi vizualizáció segítséget nyújt a számítógépek függőségeinek azonosításához anélkül, hogy ügynököket kellene telepítenie a gépekre. Úgy működik, hogy rögzíti a TCP-kapcsolatok adatait azokról a gépekről, amelyeken engedélyezve van.

> [!IMPORTANT]
> Az ügynök nélküli függőségi vizualizáció jelenleg csak előzetes verzióban érhető el az Azure VMware virtuális gépekhez, amely a Azure Migrate: Server Assessment Tool eszközzel lett felderítve.
> Lehetséges, hogy a funkciók korlátozottak vagy hiányosak.
> Ezt az előzetes verziót az ügyfélszolgálat támogatja, és az éles számítási feladatokhoz is használható.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuális korlátozások

- Mostantól nem adhat hozzá és nem távolíthat el kiszolgálót egy csoportból a függőség elemzése nézetben.
- A kiszolgálók egy csoportjának függőségi leképezése jelenleg nem érhető el.
- Jelenleg a függőségi adatokat táblázatos formátumban nem lehet letölteni.

## <a name="before-you-start"></a>Előkészületek

- [Tekintse át](concepts-dependency-visualization.md#agentless-visualization) az ügynök nélküli függőségi vizualizációval kapcsolatos követelményeket és költségeket.
- Tekintse át az ügynök nélküli függőségi vizualizáció beállításának [támogatási követelményeit](migrate-support-matrix-vmware.md#agentless-dependency-visualization) .
- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Server Assessment eszközt.
- Győződjön meg arról, hogy beállított egy [Azure Migrate berendezést](migrate-appliance.md) a helyszíni gépek felderítéséhez. Megtudhatja, hogyan állíthat be egy készüléket a [VMware](how-to-set-up-appliance-vmware.md) virtuális gépekhez. A készülék felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld Azure Migratenak: a kiszolgáló értékelését.


## <a name="create-a-user-account-for-discovery"></a>Felhasználói fiók létrehozása a felderítéshez

Hozzon létre egy felhasználói fiókot, hogy a kiszolgáló értékelése hozzáférhessen a virtuális géphez a felderítéshez. Egy felhasználói fiókot is megadhat.

- **Windows rendszerű virtuális gépek**: a felhasználói fióknak helyi vagy tartományi rendszergazdának kell lennie.
- **Linuxos virtuális gépek**: a fiókhoz rendszergazdai jogosultság szükséges. A felhasználói fióknak ezt a két funkciót kell megadnia a/bin/netstat és a/bin/ls fájlokhoz: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>A felhasználói fiók hozzáadása a berendezéshez

Adja hozzá a felhasználói fiókot a készülékhez.

1. Nyissa meg a berendezés-kezelő alkalmazást. 
2. Navigáljon az **adja meg a vCenter részletei** panelt.
3. Az **alkalmazás és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása** lehetőségre.
3. Válassza ki az **operációs rendszert**, adjon meg egy felhasználóbarát nevet a fióknak, valamint a **felhasználónevet**/a **jelszót**
6. Kattintson a **Save** (Mentés) gombra.
7. Kattintson **a Mentés gombra, és indítsa el a felderítést**.

    ![VM-felhasználói fiók hozzáadása](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Függőségi felderítés elindítása

Válassza ki azokat a gépeket, amelyeken engedélyezni szeretné a függőségi felderítést.

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Kattintson a **függőség elemzése** ikonra.
3. Kattintson a **kiszolgálók hozzáadása**elemre.
3. A **kiszolgálók hozzáadása** lapon válassza ki azt a készüléket, amely a megfelelő gépeket felfedi.
4. A gép listából válassza ki a gépeket.
5. Kattintson a **kiszolgálók hozzáadása**elemre.

    ![Függőségi felderítés elindítása](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

A függőségek felderítésének megkezdése után hat órán belül megjelenítheti a függőségeket.

## <a name="visualize-dependencies"></a>Függőségek megjelenítése

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Keresse meg a megtekinteni kívánt gépet.
3. A **függőségek** oszlopban kattintson a **függőségek megtekintése** elemre.
4. Módosítsa azt az időtartamot, ameddig a térképet meg szeretné **tekinteni az időtartam legördülő lista** használatával.
5. Bontsa ki az **ügyféloldali** csoportot a kiválasztott gépen függőséggel rendelkező gépek listázásához.
6. Bontsa ki a **port** csoportot a kiválasztott gépről függőséggel rendelkező gépek listázásához.
7. Ha bármelyik függő gép Térkép nézetére szeretne navigálni, kattintson a gép nevére > **Load Server Map**

    ![A kiszolgáló portszámának kibontása és a kiszolgálói Térkép betöltése](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Ügyféloldali csoport kibontása ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. A kiválasztott gép kibontásával megtekintheti az egyes függőségek folyamat-szintű részleteit.

    ![A kiszolgáló kibontása a folyamatok megjelenítéséhez](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> A függőségi adatok feldolgozása nem mindig érhető el. Ha nem érhető el, a függőséget az "ismeretlen folyamat" jelölésű folyamat mutatja.

## <a name="stop-dependency-discovery"></a>Függőségi felderítés leállítása

Válassza ki azokat a gépeket, amelyeken le szeretné állítani a függőségi felderítést.

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Kattintson a **függőség elemzése** ikonra.
3. Kattintson a **kiszolgálók eltávolítása**elemre.
3. A **kiszolgálók eltávolítása** lapon válassza ki azt a **készüléket** , amely felderíti azokat a virtuális gépeket, amelyeken meg szeretné szüntetni a függőségi felderítést.
4. A gép listából válassza ki a gépeket.
5. Kattintson a **kiszolgálók eltávolítása**elemre.


## <a name="next-steps"></a>Következő lépések

[A gépek csoportosítása](how-to-create-a-group.md) az értékeléshez.

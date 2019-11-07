---
title: Azure Migrate gépek az ügynök nélküli függőségi vizualizáció használatával
description: Leírja, hogyan hozhatók létre csoportok ügynök nélküli módon a számítógép függőségeivel.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: e9f9e812d5463f0a503b100780f9b988e43f748d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720268"
---
# <a name="set-up-agentless-dependency-visualization-for-assessment"></a>Az ügynök nélküli függőségi vizualizáció beállítása az értékeléshez

> [!NOTE]
> Ha még nem látja ezt a funkciót a Azure Migrate-portálon, kattintson a lefagy elemre. A következő héten vagy így fog megjelenni.

Ez a cikk azt ismerteti, hogyan állítható be az ügynök nélküli függőségi leképezés a Azure Migrateban: kiszolgáló értékelése. Ez a funkció jelenleg előzetes verzióban érhető el a Azure Migrate készülék használatával felderített VMware-gépekhez. 

> [!IMPORTANT]
> Az ügynök nélküli függőségi vizualizáció jelenleg előzetes verzióban érhető el egy Azure Migrate berendezés használatával felderített Azure VMware virtuális gépek számára.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="about-dependency-mapping"></a>Függőségek leképezése 

A függőségek leképezése segítséget nyújt a függőségek megjelenítéséhez a felmérni és áttelepíteni kívánt gépek között. Jellemzően függőségi leképezést használ, ha a magasabb szintű megbízhatóságú gépeket szeretné felmérni.

- Azure Migrate: a kiszolgáló értékelése során összegyűjtheti a gépeket csoportokba az értékeléshez. A csoportok általában az áttelepíteni kívánt gépekből állnak, és a függőségek leképezése segít a számítógépek függőségeinek átadásában, hogy pontosan csoportosítsa a gépeket.
- A leképezés használatával felderítheti azokat az egymástól függő rendszereket, amelyeknek együtt kell áttelepíteniük. Azt is meghatározhatja, hogy egy futó rendszer továbbra is a felhasználókat szolgálja-e, vagy az áttelepítés helyett a leszerelésre jelölt.
- A függőségek megjelenítésével gondoskodhat arról, hogy semmi ne maradjon hátra, és elkerülje a kiesést az áttelepítés során.

## <a name="about-agentless-visualization"></a>Az ügynök nélküli vizualizáció ismertetése

Az ügynök nélküli függőségi vizualizációk nem igénylik a gépeken lévő ügynökök telepítését. Úgy működik, hogy rögzíti a TCP-kapcsolatok adatait azokról a gépekről, amelyeken engedélyezve van.

- A függőségi felderítés elindítása után a készülék öt perces lekérdezési időköz alapján gyűjt adatokat a gépekről.
- A rendszer a következő adatokat gyűjti össze:
    - TCP-kapcsolatok
    - Aktív kapcsolattal rendelkező folyamatok nevei
    - A fenti folyamatokat futtató telepített alkalmazások nevei
    - Nem. az összes lekérdezési időszakban észlelt kapcsolatok

## <a name="current-limitations"></a>Aktuális korlátozások

- Az ügynök nélküli függőségi vizualizáció jelenleg csak a VMware virtuális gépek számára érhető el.
- Mostantól nem adhat hozzá és nem távolíthat el kiszolgálót egy csoportból a függőség elemzése nézetben. 
- A kiszolgálók csoportjának függőségi térképe jelenleg nem érhető el.
- Jelenleg a függőségi adatokat táblázatos formátumban nem lehet letölteni.

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, hogy [létrehozott](how-to-add-tool-first-time.md) egy Azure Migrate projektet.
- Az ügynök nélküli függőség elemzése jelenleg csak a VMware rendszerű gépek esetében érhető el.
- Ha már létrehozott egy projektet, győződjön meg arról, hogy [felvette](how-to-assess.md) a Azure Migrate: Server Assessment eszközt.
- Győződjön meg arról, hogy felderítette a VMware-gépeket a Azure Migrate; Ezt úgy teheti meg, hogy létrehoz egy Azure Migrate készüléket a [VMware](how-to-set-up-appliance-vmware.md)-hez. A készülék felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld Azure Migratenak: a kiszolgáló értékelését. [Részletek](migrate-appliance.md).
- Győződjön meg arról, hogy a VMware virtuális gépek az ügynök nélküli függőségi vizualizációk esetében támogatottak, az alábbi táblázatban foglaltak szerint.


### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek
 
Az ügynök nélküli függőségi vizualizációk által támogatott operációs rendszerek a következők.

**Típus** | **Támogatott operációs rendszerek**
--- | --- 
**Windows** | Microsoft Windows Server 2016 <br/> Microsoft Windows Server 2012 R2 <br/> Microsoft Windows Server 2012 <br/> Microsoft Windows Server 2008 R2 (64 bites) 
**Linux** | Red Hat Enterprise Linux 7, 6, 5 <br/> Ubuntu Linux 14,04, 16,04 <br/> Debian 7, 8 <br/> Oracle Linux 6, 7 <br/> CentOS 5, 6, 7  


## <a name="create-a-user-account-for-discovery"></a>Felhasználói fiók létrehozása a felderítéshez

Állítson be egy olyan felhasználói fiókot, amely rendelkezik a szükséges engedélyekkel, hogy a kiszolgáló értékelése hozzáférhessen a virtuális géphez a felderítéshez. Egy felhasználói fiókot is megadhat.

- **Szükséges engedélyek a Windows rendszerű virtuális gépeken**: a felhasználói fióknak "vendég" hozzáférésre van szüksége.
- **Szükséges engedélyek Linux rendszerű virtuális gépeken**: a fiókhoz rendszergazdai jogosultság szükséges. A felhasználói fióknak ezt a két funkciót kell megadnia a/bin/netstat és a/bin/ls fájlokhoz: CAP_DAC_READ_SEARCH és CAP_SYS_PTRACE.

## <a name="add-the-user-account-to-the-appliance"></a>A felhasználói fiók hozzáadása a berendezéshez

Hozzá kell adnia a felhasználói fiókot a berendezéshez.

Adja hozzá a fiókot a következő módon:

1. Nyissa meg a berendezés-kezelő alkalmazást. Navigáljon az **adja meg a vCenter részletei** panelt.
2. Az **alkalmazás és a virtuális gépek függőségeinek felderítése** szakaszban kattintson a **hitelesítő adatok hozzáadása** elemre.
3. Válassza ki az **operációs rendszert**. 
4. Adja meg a fiók rövid nevét.
5. Adja meg a **felhasználónevet** és a **jelszót**
6. Kattintson a **Save** (Mentés) gombra.
7. Kattintson **a Mentés gombra, és indítsa el a felderítést**.

    ![VM-felhasználói fiók hozzáadása](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Függőségi felderítés elindítása

Válassza ki azokat a gépeket, amelyeken engedélyezni szeretné a függőségi felderítést.

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Kattintson a **függőség elemzése** ikonra.
3. Kattintson a **függőségi felderítés indítása**elemre.
3. A **függőségi felderítés indítása** lapon válassza ki azt a készüléket, amely a megfelelő gépeket deríti fel.
4. A gép listából válassza ki a gépeket.
5. Kattintson a **függőségi felderítés indítása**elemre.

    ![Függőségi felderítés elindítása](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

A függőségi felderítés megkezdése után 6 órával is megjelenítheti a függőségeket.

## <a name="visualize-dependencies"></a>Függőségek vizualizálása

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Keresse meg azt a gépet, amelyre vonatkozóan meg szeretné tekinteni a függőségi térképet.
3. A **függőségek** oszlopban kattintson a **függőségek megtekintése** elemre.
4. Módosítsa azt az időtartamot, ameddig a térképet meg szeretné **tekinteni az időtartam legördülő lista** használatával.
5. Bontsa ki az **ügyféloldali** csoportot a kiválasztott gépen függőséggel rendelkező gépek listázásához. 
6. Bontsa ki a **port** csoportot a kiválasztott gépről függőséggel rendelkező gépek listázásához. 
7. Ha bármelyik függő gép Térkép nézetére szeretne navigálni, kattintson a gép nevére, majd a **kiszolgáló Térkép betöltése** elemre.

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
3. Kattintson a **függőségi felderítés leállítása**elemre.
3. A **függőségek felderítésének leállítása** lapon válassza ki azt a **készüléket** , amely felderíti azokat a virtuális gépeket, amelyeken meg szeretné szüntetni a függőségi felderítést.
4. A gép listából válassza ki a gépeket.
5. Kattintson a **függőségi felderítés leállítása** elemre.


## <a name="next-steps"></a>További lépések

[Gépek csoportosítása](how-to-create-a-group.md)

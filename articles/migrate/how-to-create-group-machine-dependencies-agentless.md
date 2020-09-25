---
title: Ügynök nélküli függőségi elemzés beállítása Azure Migrate Server Assessment-ben
description: Az ügynök nélküli függőségek elemzésének beállítása Azure Migrate Server Assessment-ben.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 164cc20632faa1d444d06da6688000e9b40d7e76
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275591"
---
# <a name="analyze-machine-dependencies-agentless"></a>A gép függőségeinek elemzése (ügynök nélküli)

Ez a cikk az ügynök nélküli függőségek elemzésének beállítását ismerteti Azure Migrateban: kiszolgáló értékelése. A függőségek [elemzése](concepts-dependency-visualization.md) segítséget nyújt a számítógépek közötti függőségek azonosításában és értelmezésében az Azure-ba történő értékeléshez és áttelepítéshez.


> [!IMPORTANT]
> Az ügynök nélküli függőségi vizualizáció jelenleg előzetes verzióban érhető el a Azure Migrate: Server Assessment Tool eszközzel felderített VMware virtuális gépek számára.
> Lehetséges, hogy a funkciók korlátozottak vagy hiányosak.
> Ezt az előzetes verziót az ügyfélszolgálat támogatja, és az éles számítási feladatokhoz is használható.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuális korlátozások

- A függőség elemzése nézetben jelenleg nem adhat hozzá és nem távolíthat el kiszolgálót egy csoportból.
- Jelenleg nem érhető el függőségi Térkép a kiszolgálók csoportjához.
- A függőségi adatgyűjtés a 1000-kiszolgálókhoz egyidejűleg állítható be. Nagyobb számú kiszolgálót elemezheti a 1000 kötegekben történő előkészítéssel.

## <a name="before-you-start"></a>Előkészületek

- [Tekintse át](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) a támogatott operációs rendszereket és a szükséges engedélyeket.
- Győződjön meg róla, hogy:
    - Azure Migrate projekttel rendelkezik. Ha nem, [hozzon létre](how-to-add-tool-first-time.md) egyet most.
    - Győződjön meg arról, hogy [hozzáadta](how-to-assess.md) a Azure Migrate: Server Assessment eszközt a projekthez.
    - [Azure Migrate berendezés](migrate-appliance.md) beállítása a helyszíni gépek felderítéséhez. [Állítson be egy készüléket](how-to-set-up-appliance-vmware.md) a VMWare virtuális gépekhez. A készülék felfedi a helyszíni gépeket, és metaadatokat és teljesítményadatokat küld Azure Migratenak: a kiszolgáló értékelését.
- Győződjön meg arról, hogy az összes elemezni kívánt virtuális gépre telepítve van a VMware-eszközök (10,2-nál újabb).


## <a name="create-a-user-account-for-discovery"></a>Felhasználói fiók létrehozása a felderítéshez

Hozzon létre egy felhasználói fiókot, hogy a kiszolgáló értékelése hozzáférhessen a virtuális géphez a függőségek felderítése érdekében. A Windows-és Linux-alapú virtuális gépek fiókra vonatkozó követelményeinek [megismerése](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) .


## <a name="add-the-user-account-to-the-appliance"></a>A felhasználói fiók hozzáadása a berendezéshez

Adja hozzá a felhasználói fiókot a készülékhez.

1. Nyissa meg a berendezés-kezelő alkalmazást. 
2. Navigáljon az **adja meg a vCenter részletei** panelt.
3. Az **alkalmazás és a virtuális gépek függőségeinek felderítése**területen kattintson a **hitelesítő adatok hozzáadása** lehetőségre.
3. Válassza ki az **operációs rendszert**, adjon meg egy felhasználóbarát nevet a fiókhoz, és a **Felhasználónév** / **jelszava**
6. Kattintson a **Mentés** gombra.
7. Kattintson **a Mentés gombra, és indítsa el a felderítést**.

    ![VM-felhasználói fiók hozzáadása](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Függőségi felderítés elindítása

Válassza ki azokat a gépeket, amelyeken engedélyezni szeretné a függőségi felderítést. 

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Kattintson a **függőség elemzése** ikonra.
3. Kattintson a **kiszolgálók hozzáadása**elemre.
4. A **kiszolgálók hozzáadása** lapon válassza ki azt a készüléket, amely a megfelelő gépeket felfedi.
5. A gép listából válassza ki a gépeket.
6. Kattintson a **kiszolgálók hozzáadása**elemre.

    ![Függőségi felderítés elindítása](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

A függőségek felderítésének megkezdése után hat órán belül megjelenítheti a függőségeket. Ha több gépet szeretne engedélyezni, a [PowerShell](#start-or-stop-dependency-discovery-using-powershell) használatával teheti meg.

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

## <a name="export-dependency-data"></a>Függőségi adatgyűjtés exportálása

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Kattintson a **függőség elemzése** ikonra.
3. Kattintson az **alkalmazás függőségeinek exportálása**elemre.
4. Az **alkalmazás-függőségek exportálása** lapon válassza ki azt a készüléket, amely a megfelelő gépeket felfedi.
5. Válassza ki a kezdési és befejezési időpontot. Vegye figyelembe, hogy csak az elmúlt 30 napban töltheti le az adatgyűjtést.
6. Kattintson a **függőség exportálása**elemre.

A függőségi adatfájlok exportálása és letöltése CSV-formátumban történik. A letöltött fájl a függőségi elemzéshez engedélyezett összes gépen tartalmazza a függőségi adataikat. 

![Függőségek exportálása](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Függőségi adatok

Az exportált CSV-fájl minden sora a megadott időpontban megfigyelt függőségnek felel meg. 

Az alábbi táblázat összefoglalja az exportált CSV-fájl mezőit. Vegye figyelembe, hogy a kiszolgáló neve, az alkalmazás és a folyamat mező csak olyan kiszolgálókon töltődik fel, amelyeken engedélyezve van az ügynök nélküli függőségi elemzés.

**Mezőnév** | **Részletek**
--- | --- 
Időrés | Az a időrés, amely alatt a függőség megfigyelhető. <br/> A függőségi adat jelenleg 6 órás tárolóhelyre van rögzítve.
Forráskiszolgáló neve | A forrásoldali gép neve 
Forrásoldali alkalmazás | Az alkalmazás neve a forrásoldali gépen 
Forrásoldali folyamat | A forrás gépen lévő folyamat neve 
Célkiszolgáló neve | A célszámítógép neve
Cél IP-címe | A célszámítógép IP-címe
Célalkalmazás | Az alkalmazás neve a célszámítógépen
Cél folyamat | A folyamat neve a célszámítógépen 
Célport | Portszám a célszámítógépen


## <a name="stop-dependency-discovery"></a>Függőségi felderítés leállítása

Válassza ki azokat a gépeket, amelyeken le szeretné állítani a függőségi felderítést. 

1. **Azure Migrate: kiszolgáló értékelése**, kattintson a **felderített kiszolgálók**elemre.
2. Kattintson a **függőség elemzése** ikonra.
3. Kattintson a **kiszolgálók eltávolítása**elemre.
3. A **kiszolgálók eltávolítása** lapon válassza ki azt a **készüléket** , amely felderíti azokat a virtuális gépeket, amelyeken meg szeretné szüntetni a függőségi felderítést.
4. A gép listából válassza ki a gépeket.
5. Kattintson a **kiszolgálók eltávolítása**elemre.

Ha több gép függőségét szeretné leállítani, a [PowerShell](#start-or-stop-dependency-discovery-using-powershell) használatával teheti meg.


### <a name="start-or-stop-dependency-discovery-using-powershell"></a>Függőségi felderítés elindítása vagy leállítása a PowerShell használatával

Töltse le a PowerShell-modult [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) -tárházból a githubon.


#### <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

1. Jelentkezzen be az Azure-előfizetésbe a AzAccount parancsmag használatával.

    ```PowerShell
    Connect-AzAccount
    ```
    Azure Government használata esetén használja a következő parancsot.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Válassza ki azt az előfizetést, amelyben létrehozta a Azure Migrate projektet 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. A letöltött AzMig_Dependencies PowerShell-modul importálása

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

#### <a name="enable-or-disable-dependency-data-collection"></a>Függőségi adatgyűjtés engedélyezése vagy letiltása

1. A következő parancsokkal lekérheti a felderített VMware virtuális gépek listáját a Azure Migrate projektben. Az alábbi példában a projekt neve FabrikamDemoProject, és a hozzá tartozó erőforráscsoport FabrikamDemoRG. A gépek listája FabrikamDemo_VMs.csv lesz mentve

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    A fájlban megtekintheti a virtuális gép megjelenítendő nevét, a függőségi gyűjtemény aktuális állapotát és az összes felderített virtuális gép ARM-AZONOSÍTÓját. 

2. A függőségek engedélyezéséhez vagy letiltásához hozzon létre egy bemeneti CSV-fájlt. A fájlnak rendelkeznie kell egy "ARM ID" fejléctel rendelkező oszloppal. A CSV-fájlban szereplő további fejlécek figyelmen kívül lesznek hagyva. A CSV-t az előző lépésben létrehozott fájl használatával hozhatja létre. Hozzon létre egy másolatot a fájlról, és őrizze meg azokat a virtuális gépeket, amelyeken engedélyezni vagy letiltani szeretné a függőségeket. 

    A következő példában a függőségi elemzés engedélyezve van a bemeneti fájlban lévő virtuális gépek listáján FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    A következő példában a függőségi elemzés le van tiltva a bemeneti fájlban lévő virtuális gépek listáján FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Hálózati kapcsolatok megjelenítése Power BIban

Azure Migrate olyan Power BI-sablont kínál, amellyel egyszerre több kiszolgáló hálózati kapcsolatai jeleníthetők meg, és a szűrés folyamat és kiszolgáló alapján. A megjelenítéshez a következő utasítások szerint töltse be a Power BI függőségi adattal.

1. Töltse le a PowerShell-modult és a Power BI sablont a GitHubon található [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) tárházból.

2. Jelentkezzen be az Azure-ba az alábbi utasítások alapján: 
- Jelentkezzen be az Azure-előfizetésbe a AzAccount parancsmag használatával.

    ```PowerShell
    Connect-AzAccount
    ```

- Azure Government használata esetén használja a következő parancsot.

    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

- Válassza ki azt az előfizetést, amelyben létrehozta a Azure Migrate projektet 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. A letöltött AzMig_Dependencies PowerShell-modul importálása

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Futtassa az alábbi parancsot. Ez a parancs letölti a függőségi adatmennyiséget egy CSV-fájlban, és feldolgozza azokat az egyedi függőségek listájának létrehozásához, amelyeket a Power BI vizualizációhoz használhat. Az alábbi példában a projekt neve FabrikamDemoProject, és a hozzá tartozó erőforráscsoport FabrikamDemoRG. A függőségek a FabrikamAppliance által felderített gépekre lesznek letöltve. Az egyedi függőségek a FabrikamDemo_Dependencies.csvba lesznek mentve

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. A letöltött Power BI sablon megnyitása

6. Töltse be Power BI a letöltött függőségi adatkészleteket.
    - Nyissa meg Power BI a sablont.
    - Kattintson az **adatlekérdezés** gombra az eszköztáron. 
    - Válassza a **text/CSV** elemet a gyakori adatforrások közül.
    - Válassza ki a letöltött függőségek fájlt.
    - Kattintson a **Betöltés**elemre.
    - Megjelenik egy tábla, amely a CSV-fájl nevével lesz importálva. A táblázat a jobb oldali mezők sávban látható. Nevezze át AzMig_Dependencies
    - Kattintson a frissítés elemre az eszköztáron.

    A hálózati kapcsolatok diagram és a forráskiszolgáló neve, a célkiszolgáló neve, a forrás folyamat neve, a cél folyamat neve szeletelők az importált értékekkel.

7. Jelenítse meg a hálózati kapcsolatok leképezését kiszolgálók és folyamatok alapján. Mentse a fájlt.


## <a name="next-steps"></a>Következő lépések

Az értékeléshez [csoportosítsa a gépeket](how-to-create-a-group.md) .

---
title: A Hyper-V virtuális gépek felmérése az Azure-ba való Migrálás Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan értékelheti a helyszíni Hyper-V virtuális gépeket az Azure-ba való áttelepítéshez Azure Migrate Server Assessment használatával.
ms.topic: tutorial
ms.date: 06/03/2020
ms.custom: mvc
ms.openlocfilehash: eae7e2d371ed8156debe9ae24cf0744bd6273943
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950272"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>A Hyper-V virtuális gépek felmérése Azure Migrate kiszolgáló értékelésével

Ez a cikk bemutatja, hogyan értékelheti a helyszíni Hyper-V virtuális gépeket a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával.


Ez az oktatóanyag egy sorozat második része, amely bemutatja, hogyan lehet felmérni és áttelepíteni a Hyper-V virtuális gépeket az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Migrate projekt beállítása.
> * Azure Migrate berendezés beállítása és regisztrálása.
> * Indítsa el a helyszíni virtuális gépek folyamatos felderítését.
> * Csoportosítsa a felderített virtuális gépeket, és mérje fel a csoportot.
> * Tekintse át az értékelést.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a útmutató cikkeket.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/), mielőtt hozzákezd.


## <a name="prerequisites"></a>Előfeltételek

- [Fejezze](tutorial-prepare-hyper-v.md) be az első oktatóanyagot ebben a sorozatban. Ha nem, az oktatóanyagban szereplő utasítások nem fognak működni.
- Az első oktatóanyagban az alábbiakat kell elvégeznie:
    - [Készítse elő az Azure](tutorial-prepare-hyper-v.md#prepare-azure) -t a Azure Migrate való együttműködéshez.
    - A [Hyper-V-](tutorial-prepare-hyper-v.md#prepare-for-assessment) gazdagépek és a virtuális gépek felmérésének előkészítése.
    - [Ellenőrizze](tutorial-prepare-hyper-v.md#prepare-for-appliance-deployment) , hogy mire van szüksége ahhoz, hogy a Azure Migrate berendezést telepíteni lehessen a Hyper-V értékeléséhez.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A keresési eredmények között válassza a **Azure Migrate**lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. Az **Első lépések** területen kattintson az **Eszközök hozzáadása** elemre.
5. A **projekt Átmigrálása** lapon válassza ki az Azure-előfizetését, és hozzon létre egy erőforráscsoportot, ha még nem rendelkezik ilyennel.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét, valamint azt a régiót, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

    - A projekt régió csak a helyszíni virtuális gépekről összegyűjtött metaadatok tárolására szolgál.
    - A virtuális gépek áttelepítésekor egy másik Azure Target-régiót is kijelölhet. Az összes Azure-régió áttelepítési célként támogatott.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-hyper-v/migrate-project.png)

7. Kattintson a **Tovább** gombra.
8. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment Next (kiszolgáló értékelése**  >  **Next**) elemet.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. A **felülvizsgálat + eszközök hozzáadása**lapon tekintse át a beállításokat, majd kattintson az **eszközök hozzáadása**elemre.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.

## <a name="set-up-the-azure-migrate-appliance"></a>A Azure Migrate berendezés beállítása


Azure Migrate: a kiszolgáló értékelése egy könnyű Azure Migrate berendezést használ. A készülék virtuálisgép-felderítést végez, és a virtuális gépek metaadatait és teljesítményadatait Azure Migrateba küldi. A készülék több módon is beállítható.

- Állítsa be a Hyper-V virtuális gépeket egy letöltött Hyper-V virtuális merevlemez használatával. Ez az oktatóanyagban használt módszer.
- Állítsa be a Hyper-V virtuális gépre vagy fizikai gépre egy PowerShell-telepítő parancsfájl használatával. [Ezt a módszert](deploy-appliance-script.md) akkor kell használni, ha a virtuális merevlemezt nem lehet beállítani, vagy ha Azure Government.

A berendezés létrehozása után győződjön meg róla, hogy tud csatlakozni Azure Migrate: kiszolgáló-értékeléshez, először konfigurálja, majd regisztrálja azt a Azure Migrate projektben.

### <a name="generate-the-azure-migrate-project-key"></a>A Azure Migrate projekt kulcsának előállítása

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
2. A **felderítési gépek**a  >  **gépek virtualizáltak?** területen válassza **az igen, a Hyper-V**lehetőséget.
3. **1.: Azure Migrate projekt kulcsának létrehozásakor**adja meg a Hyper-V virtuális gépek felderítéséhez beállított Azure Migrate berendezés nevét. a névnek alfanumerikusnak kell lennie 14 karakternél vagy kevesebb értékkel.
1. Kattintson a **kulcs létrehozása** lehetőségre a szükséges Azure-erőforrások létrehozásának elindításához. Az erőforrások létrehozásakor ne zárja be a gépek felderítése lapot.
1. Az Azure-erőforrások sikeres létrehozása után létrejön egy **Azure Migrate projekt kulcsa** .
1. Másolja a kulcsot, mert szüksége lesz rá, hogy elvégezze a berendezés regisztrációját a konfiguráció során.

### <a name="download-the-vhd"></a>A VHD letöltése

**2.: töltse le Azure Migrate készüléket**, és válassza a (z) elemet. VHD-fájl, majd kattintson a **Letöltés**gombra. 

   ![A felderítési gépek kiválasztása](./media/tutorial-assess-hyper-v/servers-discover.png)


   ![A kulcs létrehozásának kiválasztása](./media/tutorial-assess-hyper-v/generate-key-hyperv.png)


### <a name="verify-security"></a>Biztonság ellenőrzése

A telepítése előtt győződjön meg arról, hogy a tömörített fájl biztonságos.

1. A gépen, amelyre a fájlt letöltötte, nyisson meg egy rendszergazdai parancsablakot.

2. Futtassa a következő PowerShell-parancsot a ZIP-fájl kivonatának létrehozásához
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Gyakorlati példa: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  Ellenőrizze a készülék legújabb verzióit és a kivonatoló értékeket:

    - Az Azure nyilvános felhőben:

        **Forgatókönyv** | **Letöltés** | **SHA256**
        --- | --- | ---
        Hyper-V (10,4 GB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140422) |  79c151588de049cc102f61b910d6136e02324dc8d8a14f47772da351b46d9127

    - Azure Government esetén:

        **Forgatókönyv*** | **Letöltés** | **SHA256**
        --- | --- | ---
        Hyper-V (85 MB) | [Legújabb verzió](https://go.microsoft.com/fwlink/?linkid=2140424) |  8025f315e41c01ebdb4ffb1de87982ae6cc4ea7c4cce612612c7e90a44e79b44


### <a name="create-the-appliance-vm"></a>A berendezés virtuális gép létrehozása

Importálja a letöltött fájlt, és hozza létre a virtuális gépet.

1. Miután letöltötte a tömörített VHD-fájlt arra a Hyper-V-gazdagépre, amelyre a készülék virtuális gépe kerül, bontsa ki a tömörített fájlt.
    - A kinyert helyen a fájl kibontja **AzureMigrateAppliance_VersionNumber**nevű mappába.
    - Ez a mappa almappát tartalmaz, más néven **AzureMigrateAppliance_VersionNumber**.
    - Ez az almappa három további almappát tartalmaz: **Pillanatképek**, **virtuális merevlemezek**és **Virtual Machines**.

2. Nyissa meg a Hyper-V kezelőjét. A **műveletek**területen kattintson a **virtuális gép importálása**elemre.

    ![VHD üzembe helyezése](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. A virtuális gép importálása varázslóban > a **Kezdés előtt**kattintson a **tovább**gombra.
3. A **mappa keresése**területen válassza a **Virtual Machines** mappát. Ezután kattintson a **Tovább** gombra.
1. A **virtuális gép kiválasztása lapon**kattintson a **tovább**gombra.
2. Az **importálási típus kiválasztása**területen kattintson **a virtuális gép másolása (új egyedi azonosító létrehozása)** elemre. Ezután kattintson a **Tovább** gombra.
3. A **cél kiválasztása**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
4. A **tárolási mappák**területen hagyja meg az alapértelmezett beállítást. Kattintson a **Tovább** gombra.
5. A **hálózat kiválasztása**területen adja meg azt a virtuális kapcsolót, amelyet a virtuális gép használni fog. A kapcsolónak internetkapcsolattal kell rendelkeznie az Azure-ba való adatküldéshez. [További](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-switch-for-hyper-v-virtual-machines) információ a virtuális kapcsolók létrehozásáról.
6. Az **Összefoglalás**területen tekintse át a beállításokat. Ezután kattintson a **Befejezés** gombra.
7. A Hyper-V kezelőjében > **Virtual Machines**indítsa el a virtuális gépet.


## <a name="verify-appliance-access-to-azure"></a>A készülék Azure-beli hozzáférésének ellenőrzése

Győződjön meg arról, hogy a készülék virtuális gépe tud csatlakozni az Azure URL-címekhez a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhők számára.

### <a name="configure-the-appliance"></a>A berendezés konfigurálása

Állítsa be a készüléket első alkalommal.

> [!NOTE]
> Ha a letöltött VHD helyett [PowerShell-parancsfájl](deploy-appliance-script.md) használatával állítja be a készüléket, az ebben az eljárásban szereplő első két lépés nem releváns.

1. A Hyper-V kezelőjében > **Virtual Machines**kattintson a jobb gombbal a virtuális gépre > a **kapcsolat**elemre.
2. Adja meg a berendezés nyelvét, időzónáját és jelszavát.
3. Nyisson meg egy böngészőt bármely olyan gépen, amely csatlakozhat a virtuális géphez, és nyissa meg a berendezés webalkalmazásának URL-címét: **https://*készülék neve vagy IP-címe*: 44368**.

   Másik lehetőségként megnyithatja az alkalmazást a készülék asztaláról az alkalmazás parancsikonra kattintva.
1. Fogadja el a **licencfeltételeket**, és olvassa el a harmadik féltől származó információkat.
1. A webalkalmazás-> **Előfeltételek beállítása**lapon tegye a következőket:
    - **Kapcsolat**: az alkalmazás ellenőrzi, hogy a virtuális gép rendelkezik-e internet-hozzáféréssel. Ha a virtuális gép proxyt használ:
      - Kattintson a **proxy beállítása** elemre, és adja meg a proxy címe (az űrlapon http://ProxyIPAddress vagy a http://ProxyFQDN) figyelési porton.
      - Adja meg a hitelesítő adatokat, ha a proxykiszolgáló hitelesítést igényel.
      - Csak a HTTP-proxyk használata támogatott.
      - Ha hozzáadta a proxy részleteit, vagy letiltotta a proxyt és/vagy a hitelesítést, kattintson a **Save (Mentés** ) gombra a kapcsolat ismételt elindításához.
    - **Idő szinkronizálása**: az idő ellenőrzése megtörtént. A készüléken az idő a virtuális gép felderítésének megfelelő működéséhez szinkronban kell lennie.
    - **Frissítések telepítése**: Azure Migrate Server Assessment ellenőrzi, hogy a készüléken telepítve vannak-e a legújabb frissítések. Az ellenőrzések befejezése után a berendezés **megtekintése** lehetőségre kattintva megtekintheti a készüléken futó összetevők állapotát és verzióit.

### <a name="register-the-appliance-with-azure-migrate"></a>A készülék regisztrálása a Azure Migrate

1. Illessze be a portálról másolt **Azure Migrate Project kulcsot** . Ha nem rendelkezik a kulccsal, lépjen a **kiszolgáló értékelése> felderítés> a meglévő berendezések kezelése**lehetőségre, válassza ki a készüléknek a kulcs létrehozásakor megadott nevét, és másolja a megfelelő kulcsot.
1. Kattintson a **Bejelentkezés**elemre. Egy új böngésző lapon nyit meg egy Azure-beli bejelentkezési kérést. Ha nem jelenik meg, ellenőrizze, hogy letiltotta-e az előugró ablakokat a böngészőben.
1. Az új lapon jelentkezzen be az Azure-beli felhasználónevével és jelszavával.
   
   A PIN-kóddal való bejelentkezés nem támogatott.
3. A sikeres bejelentkezést követően térjen vissza a webalkalmazáshoz. 
4. Ha a naplózáshoz használt Azure-beli felhasználói fiók rendelkezik a megfelelő [engedélyekkel](tutorial-prepare-hyper-v.md#prepare-azure) a kulcs létrehozása során létrehozott Azure-erőforrásokhoz, a készülék regisztrációja kezdeményezve lesz.
1. A készülék sikeres regisztrálása után a **részletek megtekintése**lehetőségre kattintva megtekintheti a regisztráció részleteit.



### <a name="delegate-credentials-for-smb-vhds"></a>Az SMB virtuális merevlemezek hitelesítő adatainak delegálása

Ha virtuális merevlemezeket futtat az SMB-n, engedélyeznie kell a hitelesítő adatok delegálását a készülékről a Hyper-V gazdagépekre. Ehhez engedélyeznie kell, hogy mindegyik gazdagép meghatalmazottként működjön a berendezésben. Ha követte az oktatóanyagokat, ezt az előző oktatóanyagban végezte el, amikor felkészítette a Hyper-V-t az értékeléshez és az áttelepítéshez. [Manuálisan](tutorial-prepare-hyper-v.md#enable-credssp-to-delegate-credentials)kell beállítania a CredSSP a gazdagépekhez, vagy [egy olyan parancsfájl futtatásával](tutorial-prepare-hyper-v.md#run-the-script) , amely ezt teszi.

A következő módon engedélyezheti a készüléket:

#### <a name="option-1"></a>1\. lehetőség

Futtassa ezt a parancsot a készülék virtuális gépén. A HyperVHost1/HyperVHost2 például állomásnevek.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com, HyperVHost2.contoso.com, HyperVHost1, HyperVHost2 -Force
```

Például: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>2\. lehetőség

Ezt is megteheti a berendezés Helyicsoportházirend-szerkesztőján:

1. A **helyi számítógép-házirend**  >  **Számítógép konfigurációja**területen kattintson **Felügyeleti sablonok**  >  **rendszer**  >  **hitelesítő adatok delegálása**elemre.
2. Kattintson duplán a **új hitelesítő adatok delegálásának engedélyezése**lehetőségre, és válassza az **engedélyezve**lehetőséget.
3. A **Beállítások**területen kattintson a **Megjelenítés**elemre, és adja hozzá a listában felderíteni kívánt Hyper-V-gazdagépeket a **wsman/** előtagként.
4. Ezután a **hitelesítő adatok delegálása**lehetőségre duplán kattintva **engedélyezze a friss hitelesítő adatok delegálását csak NTLM kiszolgálói hitelesítéssel**. Ismét adja hozzá a **wsman/** előtagként használni kívánt Hyper-V-gazdagépeket a listához.

## <a name="start-continuous-discovery"></a>Folyamatos felderítés indítása

Kapcsolódjon a készülékről a Hyper-V-gazdagépekhez vagy-fürtökhöz, és indítsa el a virtuális gépek felderítését.

1. Az **1. lépés: a Hyper-v gazdagép hitelesítő adatainak**megadása területen kattintson a **hitelesítő adatok hozzáadása** lehetőségre a hitelesítő adatok rövid nevének megadásához, adja hozzá a **felhasználónevet** és a **jelszót** egy olyan Hyper-V-gazdagéphez vagy-fürthöz, amelyet a berendezés a virtuális gépek felderítéséhez használ Kattintson a **Save (Mentés**) gombra.
1. Ha egyszerre több hitelesítő adatot szeretne felvenni, kattintson a **továbbiak hozzáadása** elemre, és adjon hozzá további hitelesítő adatokat. A Hyper-V virtuális gépek felderítéséhez több hitelesítő adat is támogatott.
1. A **2. lépés: a Hyper-v-gazdagép/-fürt részleteinek**megadása elemnél kattintson a **felderítési forrás hozzáadása** lehetőségre a Hyper-v-gazdagép/ **-fürt IP-címének/teljes tartománynevének** , valamint a gazdagéphez/fürthöz való kapcsolódáshoz szükséges hitelesítő adatok rövid nevének
1. Egyszerre **egyetlen elemet is hozzáadhat** , vagy egy menetben **több elemet is hozzáadhat** . Emellett lehetőség van a Hyper-V-gazdagép/-fürt adatainak biztosítására is a **CSV importálásával**.

    ![A felderítési forrás hozzáadásának kijelölése](./media/tutorial-assess-hyper-v/add-discovery-source-hyperv.png)

    - Ha az **egyetlen elem hozzáadása**lehetőséget választja, meg kell adnia a hitelesítő adatok és a Hyper-V-gazdagép/fürt **IP-CÍMÉNEK/teljes tartománynevének** rövid nevét, majd kattintson a **Mentés**gombra.
    - Ha a **több elem hozzáadása** _(alapértelmezés szerint)_ lehetőséget választja, egyszerre több rekordot is hozzáadhat, ha a szövegmezőben a hitelesítő adatok rövid nevét adja meg a Hyper-V gazdagép/fürt **IP-címének/teljes tartománynevének** a megadásával. **Ellenőrizze** a hozzáadott rekordokat, és kattintson a **Save (Mentés**) gombra.
    - Ha a **CSV importálása**lehetőséget választja, letöltheti a CSV-sablonfájlt, feltöltheti a fájlt a Hyper-V gazdagép/fürt **IP-CÍMÉVEL/teljes tartománynevével** , valamint a hitelesítő adatok rövid nevét. Ezután importálja a fájlt a készülékbe, **ellenőrizze** a fájlban szereplő rekordokat, és kattintson a **Mentés**gombra.

1. A Save (Mentés) gombra kattintva a készülék megpróbálja ellenőrizni a kapcsolódást a hozzáadott Hyper-V-gazdagépekhez/-fürtökhöz, és megjeleníti a tábla **érvényesítési állapotát** az egyes gazdagépeken/fürtökön.
    - A sikeresen érvényesített gazdagépek/fürtök esetében további részleteket a saját IP-címére/teljes tartománynevére kattintva tekinthet meg.
    - Ha az érvényesítés sikertelen a gazdagépen, tekintse át a hibát, ha a tábla állapot oszlopában a **sikertelen érvényesítés** gombra kattint. Javítsa ki a problémát, és ismételje meg az érvényesítést.
    - Gazdagépek vagy fürtök eltávolításához kattintson a **Törlés**gombra.
    - Egy adott gazdagép nem távolítható el fürtből. Csak a teljes fürtöt távolíthatja el.
    - Hozzáadhat egy fürtöt, még akkor is, ha a fürt adott gazdagépével problémák léptek fel.
1. A felderítés megkezdése előtt bármikor **újraérvényesítheti** a gazdagépek/fürtök kapcsolatát.
1. Kattintson a **felderítés indítása**lehetőségre a virtuális gép felderítésének kikapcsolásához a sikeresen érvényesített gazdagépekről/fürtökről. A felderítés sikeres elindítása után megtekintheti a felderítési állapotot az egyes gazdagépeken/fürtökön a táblában.

Ez elindítja a felderítést. Gazdagépen körülbelül 2 percet vesz igénybe, hogy a felderített kiszolgálók metaadatai megjelenjenek a Azure Portalban.

### <a name="verify-vms-in-the-portal"></a>Virtuális gépek ellenőrzése a portálon

A felderítés befejezését követően ellenőrizheti, hogy a virtuális gépek megjelennek-e a portálon.

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-Servers**  >  **Azure Migrate: kiszolgáló értékelése** lapon kattintson arra az ikonra, amely megjeleníti a **felderített kiszolgálók**darabszámát.

## <a name="set-up-an-assessment"></a>Értékelés beállítása

Az értékeléseknek két típusa lehet a Azure Migrate Server Assessment használatával.

**Értékelés** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | Értékelések az összegyűjtött teljesítményadatok alapján | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat adatai alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)**: a helyszíni lemezek IOPS és átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: a helyszíni virtuális gép méretétől függően<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott tárolási típus alapján.



### <a name="run-an-assessment"></a>Értékelés futtatása

Az értékelést a következőképpen futtathatja:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
2. A **kiszolgálók**  >  **Azure Migrate: kiszolgáló értékelése**területen kattintson az **értékelés**elemre.

    ![Kiértékelés](./media/tutorial-assess-hyper-v/assess.png)

3. A **kiszolgálók értékelése**lapon adja meg az értékelés nevét.
4. Kattintson az **Összes megtekintése** elemre az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. A **válasszon ki vagy hozzon létre egy csoportot**, válassza az **új létrehozása**lehetőséget, és adjon meg egy csoportnevet. Egy csoport egy vagy több virtuális gépet gyűjt össze az értékeléshez.
4. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt virtuális gépeket.
5. A csoport létrehozásához kattintson az **Értékelés létrehozása** elemre, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Az értékelés létrehozása után tekintse meg a **kiszolgálók**  >  **Azure Migrate: kiszolgáló értékelése**című részt.
7. Az értékelés az **Értékelés exportálása** gombra kattintva Excel-fájlként letölthető.


## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: azt, hogy a virtuális gépek alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a virtuális gépek Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.


### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok**  >   **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**, kattintson az **értékelések**elemre.
2. Az **értékelésekben**kattintson egy értékelésre a megnyitásához.

    ![Értékelés összegzése](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén ellenőrizze, hogy a virtuális gépek készen állnak-e az Azure-ba való áttelepítésre.
2. A virtuális gép állapotának áttekintése:
    - **Készen áll az Azure-ra**: Azure Migrate javasolja a virtuális gépek méretének és becsült értékének becslését az értékelés során.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémákat és a javasolt szervizelést jeleníti meg.
    - **Felkészültségi ismeretlen**: akkor használatos, ha az adatelérhetőségi problémák miatt Azure Migrate nem tudja felmérni a készültséget.

2. Kattintson egy **Azure-készültségi** állapotra. Megtekintheti a VM-készültség részleteit, és részletesen megtekintheti a virtuális gép részleteit, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit jeleníti meg.

1. Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az összes virtuális gép számára történik a vizsgált csoportban.

    - A költségbecslés a gép méretére vonatkozó javaslatokon, valamint a lemezek és a tulajdonságok alapján történik.
    - A számítási és tárolási költségek becsült havi költségei láthatók.
    - A költségbecslés a helyszíni virtuális gépek IaaS virtuális gépekként való futtatására szolgál. Azure Migrate Server Assessment nem veszi figyelembe a Pásti vagy az SaaS költségeit.

2. Megtekintheti a havi tárolási díjak becsült összegét. Ez a nézet az értékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök felosztásával.
3. A részletezéssel megtekintheti az adott virtuális gépek részleteit.


### <a name="review-confidence-rating"></a>Megbízhatósági minősítés áttekintése

Ha teljesítmény-alapú értékeléseket futtat, a rendszer megbízhatósági minősítést rendel az értékeléshez.

![Megbízhatósági minősítés](./media/tutorial-assess-hyper-v/confidence-rating.png)

- A rendszer kinyeri az 1 csillagos (legalacsonyabb) és az 5 csillagos (legmagasabb) minősítést.
- A megbízhatósági minősítés segít megbecsülni az értékelés által biztosított méretre vonatkozó ajánlások megbízhatóságát.
- A megbízhatósági minősítés az értékelés kiszámításához szükséges adatpontok rendelkezésre állásán alapul.

Az értékelés megbízhatósági minősítése a következő.

**Adatpont rendelkezésre állása** | **Megbízhatósági minősítés**
--- | ---
0%–20% | 1 csillag
21%–40% | 2 csillag
41%–60% | 3 csillag
61%–80% | 4 csillag
81%–100% | 5 csillag

[További](best-practices-assessment.md#best-practices-for-confidence-ratings) információ a megbízhatósági minősítéssel kapcsolatos ajánlott eljárásokról.





## <a name="next-steps"></a>Következő lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure Migrate berendezés beállítása
> * Értékelés létrehozva és felülvizsgálva

Folytassa a sorozat harmadik oktatóanyagával, amelyből megtudhatja, hogyan telepítheti át a Hyper-V virtuális gépeket az Azure-ba Azure Migrate Server áttelepítéssel.

> [!div class="nextstepaction"]
> [Hyper-V virtuális gépek migrálása](./tutorial-migrate-hyper-v.md)

---
title: Kiszolgálók felmérése importált kiszolgálóadatok használatával az Azure Migrate Server Assessment segítségével
description: Ez a témakör azt ismerteti, hogy miként értékelheti a helyszíni kiszolgálókat az Azure-ba való áttelepítéshez az Azure Áttelepítési kiszolgáló értékelése segítségével importált adatok használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 9e6b451901da00243c458c06d51ba19fb64c0fdc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530453"
---
# <a name="assess-servers-by-using-imported-data"></a>Kiszolgálók felmérése importált adatok használatával

Ez a cikk bemutatja, hogyan értékelheti a helyszíni kiszolgálókat az [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel, a kiszolgáló metaadatainak vesszővel tagolt értékek (CSV) formátumban történő importálásával. Ez az értékelési módszer nem követeli meg, hogy az Azure Migrate készüléket egy értékelés létrehozásához. Ez akkor hasznos, ha:

- A készülék üzembe helyezése előtt gyors, kezdeti értékelést szeretne létrehozni.
- Az Azure Migrate berendezés nem telepíthető a szervezetben.
- Nem oszthatja meg a hitelesítő adatokat, amelyek lehetővé teszik a hozzáférést a helyszíni kiszolgálókhoz.
- A biztonsági korlátozások megakadályozzák, hogy a készülék által gyűjtött adatokat összegyűjtse és elküldje az Azure-ba. Az importált fájlban megosztott adatokat szabályozhatja. Emellett az adatok nagy része (például IP-címek megadása) nem kötelező.

## <a name="before-you-start"></a>Előkészületek

Legyen tisztában az alábbi pontokkal:

- Egyetlen CSV-fájlban legfeljebb 20 000 kiszolgálót adhat hozzá.
- Egy Azure Migrate projektben legfeljebb 20 000 kiszolgálót adhat hozzá a CSV használatával.
- A kiszolgálóadatokat a CSV használatával többször is feltöltheti a kiszolgálóértékelésbe.
- Az alkalmazásadatok összegyűjtése hasznos a helyszíni környezet áttelepítési kiértékelésében. A kiszolgálófelmérés azonban jelenleg nem végez alkalmazásszintű felmérést, és nem veszi figyelembe az alkalmazásokat az értékelés létrehozásakor.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy Azure Migrate projektet.
> * Töltsön ki egy CSV-fájlt a kiszolgáló adataival.
> * Importálja a fájlt, hogy kiszolgálóadatokat adjon hozzá a kiszolgálófelmérésbe.
> * Értékelés létrehozása és áttekintése.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyv legegyszerűbb telepítési útvonalát, így gyorsan beállíthat egy koncepcióigazolást. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Részletes útmutatásért tekintse át az útmutatót.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.

## <a name="set-azure-permissions-for-azure-migrate"></a>Azure-engedélyek beállítása az Azure Áttelepítéséhez

Az Azure-fióknak engedélyekre van szüksége egy Azure Migrate projekt létrehozásához.

1. Az Azure Portalon nyissa meg az előfizetést, és válassza **a hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése**területen keresse meg a megfelelő fiókot, majd jelölje ki az engedélyek megtekintéséhez.
3. Győződjön meg arról, hogy **rendelkezik közreműködői** vagy **tulajdonosi** engedélyekkel.
    - Ha most hozott létre egy ingyenes Azure-fiókot, ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együttműködve rendelje hozzá a szerepkört.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Új Azure Migrate projekt beállítása:

1. Az Azure Portalon a **Minden szolgáltatás**ban keresse meg az Azure Migrate című **mezőt.**
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés csoportban**a **Kiszolgálók felderítése, felmérése és áttelepítése**csoportban válassza **a Kiszolgálók felmassza és áttelepítése**lehetőséget.

    ![Kiszolgálók felfedezése és értékelése](./media/tutorial-assess-import/assess-migrate.png)

4. Az **első lépések csoportban**válassza **az Eszköz(ök) hozzáadása**lehetőséget.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
6. A **PROJEKT RÉSZLETEI területén**adja meg a projekt nevét és azt a földrajzi elhelyezkedést, amelyben a projektet létre kívánja hozni. További információk:

    - Tekintse át a támogatott földrajzi köz- [és](migrate-support-matrix.md#supported-geographies-public-cloud) [kormányzati felhőket.](migrate-support-matrix.md#supported-geographies-azure-government)
    - Migrálás futtatása során bármilyen célrégiót választhat.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-import/migrate-project.png)

7. Kattintson a **Tovább** gombra.
8. Az **Assessment (Felmérés kiválasztása) eszközben**válassza az **Azure Migrate: Server Assessment** > **Next**lehetőséget.

    ![Azure Áttelepítési felmérés létrehozása](./media/tutorial-assess-import/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. Az **Ellenőrzés + eszközök hozzáadása párbeszédpanelen**tekintse át a beállításokat, és válassza az Eszközök **hozzáadása**lehetőséget.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Ezután a program a projekt oldalára kerül. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.

## <a name="prepare-the-csv"></a>A CSV előkészítése

Töltse le a CSV-sablont, és adja hozzá a kiszolgálóadatait.

### <a name="download-the-template"></a>A sablon letöltése

1. Az**Azure Migrate: Server Assessment (Áttelepítés: Kiszolgálófelmérés)** **áttelepítési** > **célkiszolgálókon** > válassza **a Discover (Felderítés)** lehetőséget.
2. A **Discover machines (Gépek felderítése)** területén válassza az Importálás **CSV használatával**lehetőséget.
3. A CSV-sablon letöltéséhez válassza a **Letöltés** lehetőséget. Azt is megteheti, [hogy közvetlenül letölti](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![CSV-sablon letöltése](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Kiszolgálóadatok hozzáadása

Gyűjtse össze a kiszolgáló adatait, és adja hozzá a CSV fájlhoz.

- Adatok gyűjtéséhez exportálhatja azokat a helyszíni kiszolgálók kezeléséhez használt eszközökből, például a VMware vSphere vagy a konfigurációkezelő adatbázisból (CMDB).
- A mintaadatok áttekintéséhez töltse le [a példafájlt.](https://go.microsoft.com/fwlink/?linkid=2108405)

Az alábbi táblázat összefoglalja a kitöltandó fájlmezőket:

**Mezőnév** | **Kötelező** | **Részletek**
--- | --- | ---
**Kiszolgáló neve** | Igen | Javasoljuk, hogy adja meg a teljesen minősített tartománynevet (FQDN).
**IP-cím** | Nem | Kiszolgáló címe.
**Cores** | Igen | A kiszolgálóhoz rendelt processzormagok száma.
**Memory (Memória)** | Igen | A kiszolgálószámára lefoglalt teljes RAM(MB) értékben.
**Operációs rendszer neve** | Igen | Kiszolgálói operációs rendszer. <br/> Az értékelés felismeri az okat [this](#supported-operating-system-names) az operációsrendszer-neveket, amelyek megfelelnek a listában szereplő neveknek, vagy azokat tartalmazzák.
**Operációs rendszer verziója** | Nem | A kiszolgáló operációs rendszerének verziója.
**Lemezek száma** | Nem | Nem szükséges, ha az egyes lemez adatok at.
**1. lemez mérete**  | Nem | A lemez maximális mérete GB-ban.<br/>További lemezek adatait a [sablonoszlopainak hozzáadásával](#add-multiple-disks) adhatmeg hozzá. Legfeljebb nyolc lemezt adhat hozzá.
**1. lemez olvasási ops** | Nem | Lemezolvasási műveletek másodpercenként.
**1. lemez írási művelete** | Nem | Lemezírási műveletek másodpercenként.
**1. lemez olvasási átviteli-átmenő** | Nem | A lemezről másodpercenként beolvasott adatok MB/másodpercben.
**1. lemez írási átviteli áteresztőmódja** | Nem | A lemezre másodpercenként írt adatok, MB/másodpercben.
**CPU-kihasználtsági százalék** | Nem | A felhasznált processzor százaléka.
**Memóriakihasználtság százaléka** | Nem | A felhasznált RAM százaléka.
**Összes lemez olvasási ops** | Nem | Lemezolvasási műveletek másodpercenként.
**Összes lemez írási ops** | Nem | Lemezírási műveletek másodpercenként.
**Összes lemez olvasási átviteli-átmenője** | Nem | A lemezről beolvasott adatok MB/másodpercben.
**A lemezek teljes írási átviteli áteresztőszáma** | Nem | Lemezre írt adatok, MB/másodpercben.
**Hálózat az átviteli-csatornában** | Nem | A kiszolgáló által fogadott adatok MÁSODPERCENKÉNT IMb-ban.
**Hálózati kimenő átviteli** | Nem | A kiszolgáló által továbbított adatok MÁSODPERCENKÉNT IMb-ban.
**Belső vezérlőprogram típusa** | Nem | Kiszolgáló belső vezérlőprogramja. Az értékek lehetnek "BIOS" vagy "UEFI".
**MAC-cím**| Nem | Kiszolgáló MAC-címe.


### <a name="add-operating-systems"></a>Operációs rendszerek hozzáadása

Az értékelés felismeri az operációs rendszer adott neveit. A megadott neveknek pontosan meg kell egyezniük a támogatott nevek listájában szereplő karakterláncok [egyikével.](#supported-operating-system-names)

### <a name="add-multiple-disks"></a>Több lemez hozzáadása

A sablon az első lemez alapértelmezett mezőit tartalmazza. Legfeljebb nyolc lemezhez adhat hozzá hasonló oszlopokat.

Ha például egy második lemez összes mezőjét meg szeretné adni, adja hozzá az alábbi oszlopokat:

- 2. lemez mérete
- 2. lemez olvasási ops
- 2. lemez írási ops
- 2. lemez olvasási átviteli-átmenő
- 2. lemez írási átviteli áteresztőmódja


## <a name="import-the-server-information"></a>A kiszolgálóadatainak importálása

Miután adatokat adott hozzá a CSV-sablonhoz, importálja a kiszolgálókat a Kiszolgálóértékelés programba.

1. Az Azure Migrate, **a Discover gépek**, nyissa meg a befejezett sablont.
2. Kattintson az **Importálás** gombra.
3. Megjelenik az importálási állapot.
    - Ha figyelmeztetések jelennek meg az állapotban, javíthatja őket, vagy folytathatja azok megszólítása nélkül.
    - Az értékelés pontosságának javítása érdekében javítsa a kiszolgáló adatait a figyelmeztetésekben javasoltak szerint.
    - A figyelmeztetések megtekintéséhez és javításához válassza **a Figyelmeztetés részleteinek letöltése lehetőséget. CSV**. Ez a művelet letölti a CSV-t a figyelmeztetésekkel együtt. Tekintse át a figyelmeztetéseket, és szükség szerint javítsa ki a problémákat.
    - Ha hibák jelennek meg az állapotban, hogy az importálási állapot sikertelen legyen, az importálás folytatása előtt ki kell **javítania**ezeket a hibákat:
        1. Töltse le a CSV-t, amely mostantól tartalmazza a hiba részleteit.
        1. Szükség esetén tekintse át és kezelje a hibákat. 
        1. Töltse fel újra a módosított fájlt.
4. Az importálási állapot **befejezéseután**a kiszolgálóadatait importálta a rendszer.

## <a name="update-server-information"></a>Kiszolgálóadatainak frissítése

A kiszolgáló adatait úgy frissítheti, hogy ismét importálja a kiszolgáló adatait ugyanazzal a **kiszolgálónévvel.** A **Kiszolgáló név** mezője nem módosítható. A kiszolgálók törlése jelenleg nem támogatott.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

Annak ellenőrzése, hogy a kiszolgálók a felderítés után megjelennek-e az Azure Portalon:

1. Nyissa meg az Azure Migrate irányítópultot.
2. Az **Azure Migrate - Servers** > **Azure Migrate: Server Assessment (Azure Migrate: Server Assessment)** lapon válassza ki a **felderített kiszolgálók**számát megjelenítő ikont.
3. Válassza az **Importálás alapú** lapot.

## <a name="set-up-and-run-an-assessment"></a>Értékelés beállítása és futtatása

Kétféle felmérést hozhat létre a Kiszolgálói értékelés használatával.

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A megadott teljesítményadatok értékén alapuló értékelések. | **Ajánlott virtuális gép mérete:** A PROCESSZOR és a memória használati adatok alapján.<br/><br/> **Ajánlott lemeztípus (standard vagy prémium díjas felügyelt lemez)**: A bemeneti/kimeneti másodpercenkénti (IOPS) és a helyszíni lemezek átviteli teljesítménye alapján.
**A helyszíni** | A helyszíni méretezésen alapuló értékelések. | **Ajánlott virtuális gép mérete**: A megadott kiszolgálóméret alapján.<br/><br> **Ajánlott lemeztípus:** Az értékeléshez kiválasztott tárolási típus beállítás alapján.

Értékelés futtatása:

1. Tekintse át az értékelések létrehozásának [ajánlott gyakorlatait.](best-practices-assessment.md)
2. A **Kiszolgálók** lap **Azure Áttelepítés: Kiszolgálófelmérés** csempéjén válassza a **Felértékelés lehetőséget.**

    ![Kiértékelés](./media/tutorial-assess-physical/assess.png)

3. A **Kiszolgálók felmérése**területen adja meg az értékelés nevét.
4. A **felderítési forrásban**válassza a **Gépek az Azure Migrate importálása révén hozzáadott lehetőséget.**
5. Válassza **az Összes megtekintése** lehetőséget az értékelési tulajdonságok áttekintéséhez.

    ![Értékelési tulajdonságok](./media/tutorial-assess-physical/view-all.png)

6. A **Csoport kijelölése vagy létrehozása**csoportban válassza az Új **létrehozása**lehetőséget, és adja meg a csoport nevét. Egy csoport összegyűjti egy vagy több virtuális gépek együtt értékelésre.
7. A **Gépek hozzáadása a csoporthoz**csoportban válassza ki a csoporthoz hozzáadni kívánt kiszolgálókat.
8. Válassza **az Értékelés létrehozása lehetőséget** a csoport létrehozásához, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-physical/assessment-create.png)

9. Az értékelés létrehozása után tekintse meg a **Kiszolgálók** > **Azure Áttelepítés: Kiszolgálóértékelési** > **értékelések**című részben.
10. Válassza **az Értékelés exportálása** lehetőséget, ha Microsoft Excel fájlként szeretné letölteni.

## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készenlét:** A kiszolgálók alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés:** Becsült havi számítási és tárolási költségek a kiszolgálók Azure-beli futtatásához.
- **Havi tárolási költség becslés:** Az áttelepítés utáni lemeztárolás becsült költségei.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok** > **kiszolgálói között**válassza az Értékelések **az** **Azure Áttelepítés: Kiszolgálói értékelés**lehetőséget.
2. Az **Értékelések alkalmazásban**válasszon ki egy értékelést a megnyitásához.

    ![Az értékelés összefoglalója](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure-ra való felkészültség áttekintése

1. Az **Azure-ban készenléti,** határozza meg, hogy a kiszolgálók készen állnak az Azure-ba való migrálásra.
2. Tekintse át az állapotot:
    - **Készen áll az Azure-ra:** Az Azure Migrate a virtuális gépek méretének és költségbecslésének ajánlott a felmérésben.
    - **Kész a feltételekkel:** Problémák és javasolt szervizelés megjelenítése.
    - **Nem áll készen az Azure-ra:** Problémák és a javasolt szervizelés megjelenítése.
    - **Készenlét ismeretlen:** Az Azure Migrate nem tudja felmérni a készenlétet az adatok rendelkezésre állásával kapcsolatos problémák miatt.

3. Válasszon egy **Azure-készenléti** állapotot. Megtekintheti a kiszolgálóra való felkészültség részleteit, és leáshat a kiszolgáló részleteinek megtekintéséhez, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>Költségrészletek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségét jeleníti meg. A következőket teheti:

- Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az értékelt csoport összes kiszolgálójára vonatkozóan.

    - A költségbecslések a gép méretre vonatkozó javaslatain, valamint lemezein és tulajdonságain alapulnak.
    - A számítási és tárolási becsült havi költségek láthatók.
    - A költségbecslés a helyszíni kiszolgálók infrastruktúra-szolgáltatásként (IaaS) virtuális gépekként való futtatásához. A Kiszolgálófelmérés nem veszi figyelembe a platformszolgáltatásként (PaaS) vagy a szoftverszolgáltatásként (SaaS) kapcsolatos költségeket.

- Tekintse át a havi tárolási költségbecsléseket. Ez a nézet az értékelt csoport összesített tárolási költségeit mutatja, amelyek a különböző típusú tárolólemezek között oszlanak meg.
- Részletezés az adott virtuális gépek részleteinek megtekintéséhez.

> [!NOTE]
> A megbízhatósági minősítések nincsenek hozzárendelve a kiszolgálóértékelésbe CSV használatával importált kiszolgálók értékeléséhez.

## <a name="supported-operating-system-names"></a>Támogatott operációsrendszer-nevek

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A - H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux, 3.<br/>
      Asianux, 4.<br/>
      Asianux, 5.
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      CentOS<br/>
      CentOS 4/5
   :::column-end:::
   :::column span="":::
      CoreOS Linux
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Debian GNU/Linux 4<br/>
      Debian GNU/Linux 5<br/>
      Debian GNU/Linux 6<br/>
      Debian GNU/Linux 7<br/>
      Debian GNU/Linux 8
   :::column-end:::
   :::column span="":::
      FreeBSD
   :::column-end:::
:::row-end:::

<!-- BEGIN I - R -->

:::row:::
   :::column span="2":::
      **I - R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      Ms-dos
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare, 6.
   :::column-end:::
   :::column span="":::
      Oracle Linux<br/>
       Oracle Linux 4/5<br/>
      Oracle Solaris 10<br/>
       Oracle Solaris 11
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Red Hat Enterprise Linux 2<br/>
      Red Hat Enterprise Linux 3<br/>
      Red Hat Enterprise Linux 4<br/>
      Red Hat Enterprise Linux 5<br/>
      Red Hat Enterprise Linux 6<br/>
      Red Hat Enterprise Linux 7<br/>
      Red Hat Fedora
   :::column-end:::
:::row-end:::

<!-- BEGIN S - T -->

:::row:::
   :::column span="2":::
      **S - T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6 (SCO OpenServer, 6.<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Mikrorendszerek Solaris 8<br/>
      Nap Mikrorendszerek Solaris 9
   :::column-end:::
   :::column span="":::
      SUSE Linux Enterprise 10<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE Linux Enterprise 12<br/>
      SUSE Linux Enterprise 8/9<br/>
      SUSE Linux Enterprise 11<br/>
      SUSE openSUSE
   :::column-end:::
:::row-end:::

<!-- BEGIN U - Z -->
:::row:::
   :::column span="2":::
      **U - Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi, 5.<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3 rendszerben<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95 rendszerben<br/>
      Windows 98<br/>
      Windows NT<br/>
      Windows Server (R) 2008<br/>
      Windows Server 2003
   :::column-end:::
   :::column span="":::
      Windows Server 2008<br/>
      Windows Server 2008 R2<br/>
      Windows Server 2012<br/>
      Windows Server 2012 R2<br/>
      Windows Server 2016<br/>
      Windows Server 2019<br/>
      Windows Server küszöbérték<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Importált kiszolgálók az Azure Áttelepítése: Kiszolgáló értékelése csv használatával.
> * Létrehozott és felülvizsgált egy értékelést.

Most [telepítsen egy berendezést](./migrate-appliance.md) a pontosabb értékelésekhez, és gyűjtse össze a kiszolgálókat csoportokba a [függőségi elemzés](./concepts-dependency-visualization.md)használatával a mélyebb értékeléshez.

---
title: Kiszolgálók értékelése az importált kiszolgáló adataival Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan értékelheti a helyszíni kiszolgálókat az Azure-ba való áttelepítéshez Azure Migrate kiszolgáló értékelésével az importált adatértékek használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 519520538c16b1bde18f0810344864d37090accf
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342646"
---
# <a name="assess-servers-by-using-imported-data"></a>Kiszolgálók felmérése importált adatai alapján

Ez a cikk bemutatja, hogyan értékelheti a helyszíni kiszolgálókat a [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) Tool használatával a kiszolgálói metaadatok vesszővel tagolt (CSV) formátumú importálásával. Ez az értékelési módszer nem igényli a Azure Migrate berendezés beállítását az értékelés létrehozásához. Ez akkor hasznos, ha:

- A berendezés üzembe helyezése előtt létre szeretne hozni egy gyors, kezdeti értékelést.
- A Azure Migrate berendezés nem helyezhető üzembe a szervezetében.
- A helyszíni kiszolgálókhoz való hozzáférést engedélyező hitelesítő adatok nem oszthatók meg.
- A biztonsági korlátozások megakadályozzák a készülék által az Azure-ba gyűjtött adatok összegyűjtését és küldését. Az importált fájlokban megoszthatja az Ön által megosztott adatkészleteket. Emellett a nagy mennyiségű (például IP-címet biztosító) adathalmaz nem kötelező.

## <a name="before-you-start"></a>Előkészületek

Vegye figyelembe a következő pontokat:

- Legfeljebb 20 000 kiszolgálót adhat hozzá egyetlen CSV-fájlban.
- A CSV használatával legfeljebb 20 000 kiszolgálót adhat hozzá egy Azure Migrate-projekthez.
- A kiszolgálói információk több alkalommal is feltölthetők a kiszolgálói felmérésbe CSV használatával.
- Az alkalmazásadatok összegyűjtése hasznos a helyszíni környezet áttelepítésre való kiértékeléséhez. A kiszolgáló értékelése azonban jelenleg nem hajtja végre az alkalmazás szintű értékelést, vagy az értékelés létrehozásakor figyelembe veszi az alkalmazásokat.

Az oktatóanyag a következőket ismerteti:
> [!div class="checklist"]
> * Azure Migrate projekt beállítása.
> * Töltsön ki egy CSV-fájlt a kiszolgáló adataival.
> * Importálja a fájlt, és adja hozzá a kiszolgálói adatokat a kiszolgáló-értékeléshez.
> * Értékelés létrehozása és áttekintése.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállítható a koncepció igazolása. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a útmutatók című témakört.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="set-azure-permissions-for-azure-migrate"></a>Azure-engedélyek beállítása Azure Migratehoz

Az Azure-fióknak engedélyre van szüksége Azure Migrate projekt létrehozásához.

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**lapon keresse meg a megfelelő fiókot, majd jelölje ki az engedélyek megtekintéséhez.
3. Győződjön meg arról, hogy **közreműködői** vagy **tulajdonosi** engedélyekkel rendelkezik.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate projekt beállítása

Új Azure Migrate projekt beállítása:

1. A Azure Portal **minden szolgáltatás**területén keressen rá a **Azure Migrate**kifejezésre.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés**területen a **kiszolgálók felderítése, felmérése és migrálása**szakaszban válassza a **kiszolgálók felmérése és áttelepíteni**lehetőséget.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-assess-import/assess-migrate.png)

4. Az **első lépések**területen válassza a **Hozzáadás eszköz (ek)** elemet.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
6. A **Project DEtails**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. További információk:

    - Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [Government-felhők](migrate-support-matrix.md#supported-geographies-azure-government) támogatott régióit.
    - Migrálás futtatása során bármilyen célrégiót választhat.

    ![Azure Migrate projekt létrehozása](./media/tutorial-assess-import/migrate-project.png)

7. Kattintson a **Tovább** gombra.
8. Az **Assessment (kiértékelés) eszközben**válassza a **Azure Migrate: Server Assessment Next (kiszolgáló értékelése**  >  **Next**) elemet.

    ![Azure Migrate Értékelés létrehozása](./media/tutorial-assess-import/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. A **felülvizsgálat + eszközök hozzáadása**lapon tekintse át a beállításokat, majd válassza az **eszközök hozzáadása**lehetőséget.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Ezután a projekt lapra kerül. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.

## <a name="prepare-the-csv"></a>A CSV előkészítése

Töltse le a CSV-sablont, és adja hozzá a kiszolgáló adatait.

### <a name="download-the-template"></a>A sablon letöltése

1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló értékelése**területen válassza a **felderítés**lehetőséget.
2. A **számítógépek felderítése**lapon válassza az **Importálás CSV használatával**lehetőséget.
3. A CSV-sablon letöltéséhez kattintson a **Letöltés** gombra. Azt is megteheti, hogy [közvetlenül letölti](https://go.microsoft.com/fwlink/?linkid=2109031).

    ![CSV-sablon letöltése](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Kiszolgáló adatainak hozzáadása

Gyűjtsön kiszolgáló adatokat, és adja hozzá a CSV-fájlhoz.

- Az adatok összegyűjtéséhez exportálhatja azt a helyszíni kiszolgálók felügyeletéhez használt eszközökről, például VMware vSphere vagy a konfigurációs felügyeleti adatbázishoz (CMDB).
- A mintaadatok áttekintéséhez töltse le a [példánkban szereplő fájlt](https://go.microsoft.com/fwlink/?linkid=2108405).

A következő táblázat összefoglalja a kitöltendő fájl mezőket:

**Mezőnév** | **Kötelező** | **Részletek**
--- | --- | ---
**Kiszolgáló neve** | Igen | Javasoljuk, hogy adja meg a teljes tartománynevet (FQDN).
**IP-cím** | Nem | Kiszolgáló címe.
**Cores** | Igen | A kiszolgáló számára lefoglalt processzor-magok száma.
**Memory (Memória)** | Igen | A kiszolgálóhoz lefoglalt összes memória (MB).
**Operációs rendszer neve** | Igen | Kiszolgálói operációs rendszer. <br/> Az értékelés felismeri azokat az operációs rendszerek neveit, amelyek megfelelnek vagy tartalmazzák [a listában szereplő](#supported-operating-system-names) neveket.
**Operációs rendszer verziója** | Nem | Kiszolgáló operációs rendszerének verziója.
**Operációs rendszer architektúrája** | Nem | Kiszolgálói operációs rendszer architektúrája <br/> Az érvényes értékek: x64, x86, amd64, 32 bites vagy 64 bites
**Lemezek száma** | Nem | Nem szükséges, ha az egyes lemezek adatait megadja.
**1. lemez mérete**  | Nem | A lemez maximális mérete GB-ban.<br/>A sablon [oszlopainak hozzáadásával](#add-multiple-disks) további lemezeket is hozzáadhat. Legfeljebb nyolc lemezt adhat hozzá.
**1. lemez olvasási Ops** | Nem | Lemez olvasási műveletei másodpercenként.
**1. lemez írási Ops** | Nem | Lemez írási műveletei másodpercenként.
**1. lemez olvasási sebesség** | Nem | A lemezről másodpercenként olvasott adatok másodpercenkénti száma (MB/s).
**1. lemez írási átviteli sebessége** | Nem | A másodpercenként lemezre írt adatmennyiség MB-onként.
**CPU-kihasználtság százalékos aránya** | Nem | A felhasznált CPU százalékos aránya.
**Memória kihasználtsága (%)** | Nem | A felhasznált memória százalékos aránya.
**Összes lemez olvasási művelete** | Nem | Lemezes olvasási műveletek másodpercenként.
**Összes lemez írási Ops** | Nem | Lemezes írási műveletek másodpercenként.
**Összes lemez olvasási sebessége** | Nem | A lemezről beolvasott adatok (MB/s).
**Összes lemez írási sebessége** | Nem | Lemezre írt, másodpercenként MB-ban tárolt adatmennyiség.
**Átviteli sebességű hálózat** | Nem | A kiszolgáló által fogadott, MEGABÁJT/másodpercben tárolt adatmennyiség.
**Hálózati kimenő átviteli sebesség** | Nem | A kiszolgáló által továbbított adatok (MB/s).
**Belső vezérlőprogram típusa** | Nem | Kiszolgáló belső vezérlőprogram. Az értékek "BIOS" vagy "UEFI" lehet.
**MAC-cím**| Nem | Kiszolgáló MAC-címe.


### <a name="add-operating-systems"></a>Operációs rendszerek hozzáadása

Az értékelés felismeri az operációs rendszerek bizonyos neveit. A megadott névnek pontosan egyeznie kell a [támogatott nevek listában](#supported-operating-system-names)szereplő karakterláncok egyikével.

### <a name="add-multiple-disks"></a>Több lemez hozzáadása

A sablon alapértelmezett mezőket biztosít az első lemezhez. Akár nyolc lemezhez is hozzáadhat hasonló oszlopokat.

Ha például egy második lemez összes mezőjét meg szeretné adni, adja hozzá a következő oszlopokat:

- 2. lemez mérete
- 2. lemez olvasási Ops
- 2. lemez írási Ops
- 2. lemez olvasási sebesség
- 2. lemez írási átviteli sebessége


## <a name="import-the-server-information"></a>A kiszolgáló adatainak importálása

Miután hozzáadta az adatokat a CSV-sablonhoz, importálja a kiszolgálókat az értékelésbe.

1. Azure Migrate a **gépek felderítése**területen lépjen a befejezett sablonra.
2. Kattintson az **Importálás** gombra.
3. Megjelenik az importálás állapota.
    - Ha figyelmeztetések jelennek meg az állapotában, megjavíthatja őket, vagy folytathatja a kezelés nélkül.
    - Az értékelés pontosságának javítása érdekében a figyelmeztetésekben javasolt módon javítsa a kiszolgáló adatait.
    - A figyelmeztetések megtekintéséhez és kijavításához válassza a **Figyelmeztetés részleteinek letöltése lehetőséget. CSV**. Ez a művelet letölti a CSV-fájlt, amely tartalmazza a figyelmeztetéseket. Szükség szerint tekintse át a figyelmeztetéseket és javítsa ki a problémákat.
    - Ha a hibák úgy jelennek meg az állapotban, hogy az importálási állapot **meghiúsuljon**, akkor az importálás folytatása előtt ki kell javítania ezeket a hibákat:
        1. Töltse le a CSV-fájlt, amely mostantól tartalmazza a hiba részleteit.
        1. Szükség szerint tekintse át és oldja meg a hibákat. 
        1. Töltse fel újra a módosított fájlt.
4. Az importálási állapot **befejezése után**a kiszolgáló adatai importálása megtörtént.

## <a name="update-server-information"></a>Kiszolgáló adatainak frissítése

A kiszolgáló adatainak frissítéséhez importálja újra a kiszolgáló adatait ugyanazzal a **kiszolgálónévvel**. A **kiszolgáló neve** mező nem módosítható. A kiszolgálók törlése jelenleg nem támogatott.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

Annak ellenőrzése, hogy a kiszolgálók megjelennek-e a Azure Portal a felderítés után:

1. Nyissa meg a Azure Migrate irányítópultot.
2. A **Azure Migrate-Servers**  >  **Azure Migrate: kiszolgáló értékelése** lapon válassza ki azt az ikont, amely a **felderített kiszolgálók**darabszámát jeleníti meg.
3. Válassza az **importálási alapú** lapot.

## <a name="set-up-and-run-an-assessment"></a>Értékelés beállítása és futtatása

A kiszolgálók értékelése során két típusú értékelést hozhat létre.

**Értékelés típusa** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítmény-alapú** | A teljesítményen alapuló értékelések – megadott adatértékek. | **Ajánlott**virtuálisgép-méret: a processzor-és memóriahasználat-adatok alapján.<br/><br/> **Ajánlott lemez típusa (standard vagy prémium szintű felügyelt lemez)**: a másodpercenkénti bemeneti/kimeneti (IOPS) és a helyszíni lemezek átviteli sebessége alapján.
**Helyszíni** | Helyszíni méretezésen alapuló értékelések. | **Ajánlott**virtuálisgép-méret: a megadott kiszolgáló mérete alapján.<br/><br> **Ajánlott lemez típusa**: az értékeléshez kiválasztott Storage-Type beállítás alapján.

Értékelés futtatása:

1. Tekintse át az értékelések létrehozásával kapcsolatos [ajánlott eljárásokat](best-practices-assessment.md) .
2. A **kiszolgálók** lap **Azure Migrate: kiszolgáló értékelése** csempén válassza az **értékelés**lehetőséget.

    ![Kiértékelés](./media/tutorial-assess-physical/assess.png)

3. A **kiszolgálók értékelése**lapon adja meg az értékelés nevét.
4. A **felderítési forrás**területen válassza ki a **Azure Migrate importálásával hozzáadott gépeket**.
5. Válassza az **Összes megtekintése** elemet az értékelési tulajdonságok áttekintéséhez.

    ![Értékelés tulajdonságai](./media/tutorial-assess-physical/view-all.png)

6. A **válasszon ki vagy hozzon létre egy csoportot**, válassza az **új létrehozása**lehetőséget, és adjon meg egy csoportnevet. Egy csoport egy vagy több virtuális gépet gyűjt össze az értékeléshez.
7. A **számítógépek hozzáadása a csoporthoz**területen válassza ki a csoportba felvenni kívánt kiszolgálókat.
8. A csoport létrehozásához válassza az **Értékelés létrehozása** elemet, majd futtassa az értékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-physical/assessment-create.png)

9. Az értékelés létrehozása után tekintse meg a **kiszolgálók**  >  **Azure Migrate: kiszolgáló-értékelési**  >  **értékelések**.
10. A Microsoft Excel-fájlként való letöltéséhez válassza az **értékelés exportálása** lehetőséget.

## <a name="review-an-assessment"></a>Értékelés áttekintése

Az értékelés a következőket írja le:

- **Azure-készültség**: az, hogy a kiszolgálók alkalmasak-e az Azure-ba való áttelepítésre.
- **Havi költségbecslés**: a kiszolgálók Azure-ban való futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: a lemezes tárolás becsült költségei az áttelepítés után.

### <a name="view-an-assessment"></a>Értékelés megtekintése

1. Az **áttelepítési célok**  >  **kiszolgálóin**válassza az **értékelések** **Azure Migrate: kiszolgáló értékelése**elemet.
2. Az **értékelések**területen válasszon ki egy értékelést a megnyitásához.

    ![Értékelés összegzése](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure készültségének áttekintése

1. Az **Azure készültségi**területén állapítsa meg, hogy a kiszolgálók készen állnak-e az Azure-ba való áttelepítésre.
2. Tekintse át az állapotot:
    - **Készen áll az Azure-ra**: Azure Migrate javasolja a virtuális gépek méretének és becsült értékének becslését az értékelés során.
    - **Feltételekkel kész**: megjeleníti a problémákat és a javasolt szervizelést.
    - **Nem áll készen az Azure-ra: a**problémák megjelenítése és a javasolt szervizelés.
    - **Készültség ismeretlen**: Azure Migrate nem tudja felmérni a készültséget az adatelérhetőségi problémák miatt.

3. Válasszon ki egy **Azure-készültségi** állapotot. Megtekintheti a kiszolgáló-készültség részleteit, és részletesen megtekintheti a kiszolgáló részleteit, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>A Cost részleteinek áttekintése

Ez a nézet az Azure-ban futó virtuális gépek becsült számítási és tárolási költségeit jeleníti meg. A következőket teheti:

- Tekintse át a havi számítási és tárolási költségeket. A költségek összesítése az összes kiszolgáló számára a vizsgált csoportban történik.

    - A költségbecslés a gép méretére vonatkozó javaslatokon, valamint a lemezek és a tulajdonságok alapján történik.
    - A számítási és tárolási költségek becsült havi költségei láthatók.
    - A költségbecslés a helyszíni kiszolgálók infrastruktúra-szolgáltatásként (IaaS) virtuális gépekként való futtatására szolgálnak. A kiszolgáló értékelése nem tekinti a szolgáltatásként szolgáló platform ("Pásti") vagy a szolgáltatott szoftver (SaaS) költségeit.

- Tekintse át a havi tárolási költség becsléseit. Ez a nézet a vizsgált csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolóeszközök között felosztva.
- Részletes információk az adott virtuális gépek részleteinek megtekintéséhez.

> [!NOTE]
> A megbízhatósági minősítések nem vannak hozzárendelve a kiszolgáló-értékelésbe importált kiszolgálók kiértékeléséhez a CSV használatával.

## <a name="supported-operating-system-names"></a>Támogatott operációs rendszerek nevei

A CSV-fájlban megadott operációsrendszer-neveknek egyezniük kell, vagy tartalmaznia kell a listában szereplő neveket. Erre azért van szükség, hogy a megadott nevek felismerhetők legyenek az értékelés alapján.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A – H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      3. Asianux<br/>
      4. Asianux<br/>
      5. Asianux
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
      **I-R**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      IBM OS/2
   :::column-end:::
   :::column span="":::
      MS-DOS
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Novell NetWare 5<br/>
      Novell NetWare 6
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
      **S-T**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      SCO OpenServer 5<br/>
      SCO OpenServer 6<br/>
      SCO UnixWare 7
   :::column-end:::
   :::column span="":::
      Serenity Systems eComStation 1<br/>
      Serenity Systems eComStation 2
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Sun Microsystems Solaris 8<br/>
      Sun Microsystems Solaris 9
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
      **U-Z**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Ubuntu Linux
   :::column-end:::
   :::column span="":::
      VMware ESXi 4<br/>
      VMware ESXi 5<br/>
      VMware ESXi 6
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Windows 10<br/>
      Windows 2000<br/>
      Windows 3<br/>
      Windows 7<br/>
      Windows 8<br/>
      Windows 95<br/>
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
      Windows Server küszöbértéke<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Importált kiszolgálók Azure Migrateba: kiszolgáló értékelése CSV használatával.
> * Létrehozta és felülvizsgálta az értékelést.

Most [helyezzen üzembe egy készüléket](./migrate-appliance.md) a pontosabb értékelésekhez, és csoportosítsa a kiszolgálókat csoportokba a [függőségi elemzések](./concepts-dependency-visualization.md)használatával végzett mélyebb értékeléshez.

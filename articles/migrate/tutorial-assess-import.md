---
title: Kiszolgálók kiértékelése importált kiszolgálói adatok és az Azure Migrate kiszolgálóértékelési szolgáltatása használatával
description: A cikk ismerteti, hogyan értékelheti ki a helyszíni kiszolgálókat az Azure-ba történő migráláshoz az Azure Migrate kiszolgálóértékelési szolgáltatása segítségével, importált adatok használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: ff7e423063859a6cdc1a4362fb030c0deb75eb32
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89658694"
---
# <a name="assess-servers-by-using-imported-data"></a>Kiszolgálók kiértékelése importált adatok használatával

Ez a cikk a helyszíni kiszolgálók az [Azure Migrate: Kiszolgáló értékelése](migrate-services-overview.md#azure-migrate-server-assessment-tool) eszközzel történő, a kiszolgálói metaadatok vesszővel tagolt (CSV) formátumban való importálásával történő kiértékelését ismerteti. E kiértékelési módszer esetében nem kell beállítania az Azure Migrate-berendezést a kiértékelés létrehozásához. Ez a következő esetekben hasznos:

- Gyors, kezdeti felmérést szeretne végezni a berendezés üzembe helyezése előtt.
- Nem tudja a szervezetében üzembe helyezni az Azure Migrate-berendezést.
- Nem tudja megosztani a helyszíni kiszolgálókhoz való hozzáférés engedélyezéséhez szükséges hitelesítő adatokat.
- A biztonsági korlátozások megakadályozzák, hogy a berendezés adatokat gyűjtsön, és hogy ezeket az adatokat elküldje az Azure-ba. Szabályozhatja az importált fájlokban megosztott adatok körét. Emellett a legtöbb adat (például az IP-címek) megadása nem kötelező.

## <a name="before-you-start"></a>Előkészületek

Vegye figyelembe a következőket:

- Legfeljebb 20 000 kiszolgálót vehet fel egy CSV-fájlba.
- Legfeljebb 20 000 kiszolgálót vehet fel egy Azure Migrate-projektbe CSV-fájl használatával.
- CSV-fájl használatával a kiszolgálóadatok több alkalommal is feltölthetők a kiszolgálóértékelési szolgáltatásba.
- Az alkalmazásadatok gyűjtése hasznos lehet a helyszíni környezet migrálási szempontból való kiértékeléséhez. A kiszolgálóértékelési szolgáltatás azonban jelenleg nem végez alkalmazásszintű kiértékelést, illetve kiértékelés létrehozásakor nem veszi figyelembe az alkalmazásokat.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure Migrate-projektek beállítása.
> * CSV-fájl feltöltése kiszolgálói adatokkal.
> * A fájl importálása a kiszolgálói adatok a kiszolgálóértékelési szolgáltatásba való felviteléhez.
> * Kiértékelések létrehozása és megtekintése.

> [!NOTE]
> Az oktatóanyagok az adott forgatókönyvesetében a legegyszerűbb üzembehelyezési megoldást mutatják be, így gyorsan beállíthat egy megvalósíthatósági vizsgálatot. Ahol lehet, az oktatóanyagok az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és módot. Részletes utasításokért tekintse meg az útmutatókat.

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="set-azure-permissions-for-azure-migrate"></a>Azure-engedélyek beállítása az Azure Migrate esetében

Az Azure-fióknak engedélyekre van szüksége az Azure Migrate-projektek létrehozásához.

1. Az Azure Portalon nyissa meg az előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése** területen keresse meg a megfelelő fiókot, majd válassza ki az engedélyek megtekintéséhez.
3. Győződjön meg arról, hogy rendelkezik **Közreműködő** vagy **Tulajdonos** engedéllyel.
    - Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, akkor a tulajdonossal együttműködve rendelje hozzá a szerepkört.

## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate-projekt beállítása

Új Azure Migrate-projekt beállítása:

1. Az Azure Portal **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen a **Kiszolgálók felderítése, értékelése és migrálása** alatt válassza a **Kiszolgálók értékelése és migrálása** lehetőséget.

    ![Kiszolgálók felderítése és kiértékelése](./media/tutorial-assess-import/assess-migrate.png)

4. Az **Első lépések** területen válassza az **Eszköz(ök) hozzáadása** lehetőséget.
5. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
6. A **Projekt részletei** területen adja meg a projekt nevét és a földrajzi területet, ahol létre szeretné hozni a projektet. További információk:

    - Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [Government-felhők](migrate-support-matrix.md#supported-geographies-azure-government) támogatott régióit.
    - Migrálás futtatása során bármilyen célrégiót választhat.

    ![Azure Migrate-projekt létrehozása](./media/tutorial-assess-import/migrate-project.png)

7. Kattintson a **Tovább** gombra.
8. Az **Értékelési eszköz kiválasztása** területen válassza az **Azure Migrate: Kiszolgáló értékelése** > **Következő** elemet.

    ![Azure Migrate-értékelés létrehozása](./media/tutorial-assess-import/assessment-tool.png)

9. A **Migrálási eszköz kiválasztása** területen válassza **A migrálási eszköz hozzáadásának mellőzése egyelőre** > **Tovább** lehetőséget.
10. Az **Áttekintés + eszközök hozzáadása** területen ellenőrizze a beállításokat, majd válassza az **Eszközök hozzáadása** lehetőséget.
11. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik. Ezután megnyílik a projekt oldala. Ha nem látja a projektet, az Azure Migrate irányítópult **Kiszolgálók** területéről elérheti.

## <a name="prepare-the-csv"></a>A CSV-fájl előkészítése

Töltse le a CSV-sablont, és adja hozzá a kiszolgálói adatokat.

### <a name="download-the-template"></a>A sablon letöltése

1. A **Migrálási célok** > **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** területen válassza a **Felderítés** lehetőséget.
2. A **Gépek felderítése** területen válassza az **Importálás CSV-fájl használatával** lehetőséget.
3. Válassza a **Letöltés** lehetőséget a CSV-sablon letöltéséhez. Másik lehetőségként [közvetlenül is letöltheti](https://go.microsoft.com/fwlink/?linkid=2109031) a sablont.

    ![CSV-sablon letöltése](./media/tutorial-assess-import/download-template.png)

### <a name="add-server-information"></a>Kiszolgálói adatok hozzáadása

Gyűjtse össze a kiszolgálói adatokat, majd adja hozzá őket a CSV-fájlhoz.

- Az adatgyűjtéshez exportálhatja az adatokat a helyszíni kiszolgálófelügyelethez használt eszközökből, ilyenek például a VMware, a vSphere vagy a konfigurációkezelési adatbázis (CMDB).
- A mintaadatok áttekintéséhez töltse le a [példafájlt](https://go.microsoft.com/fwlink/?linkid=2108405).

Az alábbi táblázat a kitöltendő fájlmezőket összegzését tartalmazza:

**Mezőnév** | **Kötelező** | **Részletek**
--- | --- | ---
**Kiszolgáló neve** | Igen | Azt javasoljuk, hogy a teljes tartománynevet (FQDN) adja meg.
**IP-cím** | No | Kiszolgáló címe.
**Cores** | Igen | A kiszolgáló számára lefoglalt processzormagok száma.
**Memória** | Igen | A kiszolgáló számára lefoglalt memória teljes mérete, MB-ban.
**Operációs rendszer neve** | Igen | Kiszolgálói operációs rendszer. <br/> Az értékelés felismeri azon operációsrendszer-neveket, amelyek megegyeznek az [ezen](#supported-operating-system-names) a listán szereplő nevekkel, vagy tartalmazzák őket.
**Operációs rendszer verziója** | No | Kiszolgálói operációs rendszer verziója.
**Operációs rendszer architektúrája** | No | Kiszolgálói operációs rendszer architektúrája <br/> Az érvényes értékek a következők: x64, x86, amd64, 32 bites vagy 64 bites
**Lemezek száma** | No | Nem szükséges, ha az egyes lemezek adatai meg vannak adva.
**1. lemez mérete**  | No | A lemez maximális mérete, GB-ban.<br/>Több lemez adatait is hozzáadhatja, ha [további oszlopokat ad hozzá](#add-multiple-disks) a sablonhoz. Legfeljebb nyolc lemez adható hozzá.
**1. lemez olvasási műveletei** | No | A lemez olvasási műveleteinek száma másodpercenként.
**1. lemez írási műveletei** | No | A lemez írási műveleteinek száma másodpercenként.
**1. lemez olvasási teljesítménye** | No | A lemezről másodpercenként beolvasott adatok mennyisége, MB/s-ben.
**1. lemez írási teljesítménye** | No | A lemezre másodpercenként írt adatok mennyisége, MB/s-ben.
**Processzor százalékos kihasználtsága** | No | A processzorhasználat százalékos aránya.
**Memória százalékos kihasználtsága** | No | A memóriahasználat százalékos aránya.
**Minden lemez olvasási műveletei** | No | Az összes lemez olvasási műveleteinek száma másodpercenként. <br/> Használja ezt a mezőt, ha nem tud lemezszintű adatokat megadni. 
**Minden lemez írási műveletei** | No | Az összes lemez írási műveleteinek száma másodpercenként. <br/> Használja ezt a mezőt, ha nem tud lemezszintű adatokat megadni.
**Minden lemez olvasási teljesítménye** | No | Az összes lemezről másodpercenként beolvasott adatok mennyisége, MB/s-ben. <br/> Használja ezt a mezőt, ha nem tud lemezszintű adatokat megadni. 
**Minden lemez írási teljesítménye** | No | Az összes lemezre másodpercenként írt adatok mennyisége, MB/s-ben. <br/> Használja ezt a mezőt, ha nem tud lemezszintű adatokat megadni.
**Bejövő hálózati teljesítmény** | No | A kiszolgáló által fogadott adatok mennyisége, MB/s-ben.
**Kimenő hálózati teljesítmény** | No | A kiszolgáló által továbbított adatok mennyisége, MB/s-ben.
**Belső vezérlőprogram típusa** | No | A kiszolgáló belső vezérlőprogramja. Az érték „BIOS” vagy „UEFI” lehet.
**MAC-cím**| No | Kiszolgáló MAC-címe.


### <a name="add-operating-systems"></a>Operációs rendszerek hozzáadása

A kiértékelés felismer bizonyos operációsrendszer-neveket. A megadott névnek pontosan meg kell egyeznie a [támogatott nevek listáján](#supported-operating-system-names) szereplő sztringek valamelyikével.

### <a name="add-multiple-disks"></a>Több lemez hozzáadása

A sablon tartalmazza az első lemez alapértelmezett mezőit. Legfeljebb nyolc lemezhez adhat hozzá hasonló oszlopokat.

Például a második lemez minden mezőjének megadásához adja hozzá a következő oszlopokat:

- 2\. lemez mérete
- 2\. lemez olvasási műveletei
- 2\. lemez írási műveletei
- 2\. lemez olvasási teljesítménye
- 2\. lemez írási teljesítménye

Ha nem tud lemezszintű adatokat megadni, az alábbi mezők használatával megadhatja a kiszolgálónkénti lemezteljesítmény-adatokat. A mezőkkel kapcsolatos részleteket [ez](#add-server-information) a szakasz ismerteti.
- Minden lemez olvasási műveletei
- Minden lemez írási műveletei
- Minden lemez olvasási teljesítménye
- Minden lemez írási teljesítménye

## <a name="import-the-server-information"></a>Kiszolgálói adatok importálása

Miután hozzáadta az adatokat a CSV-sablonhoz, importálja a kiszolgálókat a kiszolgálóértékelési szolgáltatásba.

1. Az Azure Migrate **Gépek felderítése** területén keresse meg a véglegesített sablont.
2. Válassza az **Importálás** lehetőséget.
3. Megjelenik az importálás állapota.
    - Ha figyelmeztetések jelennek meg az állapotban, dönthet úgy, hogy kijavítja őket, de figyelmen kívül is hagyhatja őket, és továbbléphet.
    - A kiértékelés pontosságának javítása érdekében javítsa a kiszolgálói adatokat a figyelmeztetésekben javasolt módon.
    - A figyelmeztetések megtekintéséhez és javításához válassza a **figyelmeztetések részleteit tartalmazó CSV-fájl letöltéséhez** tartozó lehetőséget. Ez a művelet letölti a figyelmeztetéseket tartalmazó CSV-fájlt. Tekintse át a figyelmeztetéseket, és szükség szerint javítsa a hibákat.
    - Ha a hibák jelennek meg az állapotban, amelyek szerint az importálás állapota **Meghiúsult**, akkor az importálás folytatása előtt ki kell javítania ezeket a hibákat:
        1. Töltse le a CSV-fájlt, amely tartalmazza a hiba részleteit.
        1. Szükség szerint tekintse át és kezelje a hibákat. 
        1. Töltse fel ismét a módosított fájlt.
4. Ha az importálás állapota **Befejezve**, akkor a kiszolgálói adatok importálva lettek.

## <a name="update-server-information"></a>Kiszolgálói adatok frissítése

A kiszolgáló adatait úgy frissítheti, ha ugyanazzal a **kiszolgálónévvel** újból importálja a kiszolgálói adatokat. A **Kiszolgáló neve** mező nem módosítható. A kiszolgálók törlése jelenleg nem támogatott.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

Annak ellenőrzése, hogy a kiszolgálók megjelennek-e az Azure Portalon a felderítés után:

1. Nyissa meg az Azure Migrate irányítópultját.
2. Az **Azure Migrate – Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** oldalon kattintson a **felderített kiszolgálók** számát megjelenítő ikonra.
3. Válassza az **Importálásalapú** lapot.

## <a name="set-up-and-run-an-assessment"></a>Kiértékelés beállítása és futtatása

A kiszolgálóértékelési szolgáltatás használatával kétféle típusú kiértékelést hozhat létre.


**Kiértékelés típusa** | **Részletek**
--- | --- 
**Azure VM** | Kiértékelés a helyszíni kiszolgálók Azure-beli virtuális gépekre történő migrálásához. <br/><br/> Az Azure-ba történő migráláshoz ezen kiértékeléstípus használatával értékelheti a helyszíni [VMware rendszerű virtuális gépeket](how-to-set-up-appliance-vmware.md), a [Hyper-V rendszerű virtuális gépeket](how-to-set-up-appliance-hyper-v.md) és [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md).
**Azure VMware Solution (AVS)** | Kiértékelés a helyszíni kiszolgálók [Azure VMware Solutionbe (AVS-be)](../azure-vmware/introduction.md) történő migrálásához. <br/><br/> Az Azure VMware Solutionbe (AVS-be) történő migráláshoz ezen kiértékeléstípus használatával értékelheti a helyszíni [VMware rendszerű virtuális gépeket](how-to-set-up-appliance-vmware.md).[További információ](concepts-azure-vmware-solution-assessment-calculation.md)

### <a name="sizing-criteria"></a>Méretezési feltételek

A kiszolgálóértékelési szolgáltatásban a következő két méretezési feltétel adható meg:

**Méretezési feltételek** | **Részletek** | **Adatok**
--- | --- | ---
**Teljesítményalapú** | A gyűjtött teljesítményalapok alapján javaslatot tevő kiértékelések | **Azure-beli virtuális gépek kiértékelése**: A virtuális gép méretére vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.<br/><br/> A lemeztípusra vonatkozó javaslatok (standard HDD/SSD vagy prémium felügyelt lemez) a helyszíni lemezek IOPS értékén és átviteli sebességén alapulnak.<br/><br/> **Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a processzor- és memóriahasználati adatokon alapulnak.
**Módosítás nélküli helyszíni** | A javaslatok létrehozásához teljesítményadatokat nem használó kiértékelések. | **Azure-beli virtuális gépek kiértékelése**: A virtuálisgép-méretre vonatkozó javaslatok a helyszíni virtuális gép méretén alapulnak<br/><br> A lemeztípus-ajánlás azon alapul, hogy mit választ ki a kiértékeléshez a tárolótípus beállításai között.<br/><br/> **Azure VMware Solution- (AVS-) kiértékelés**: Az AVS-csomópontokra vonatkozó javaslatok a helyszíni virtuális gép méretén alapulnak.


Kiértékelés futtatása:

1. Tekintse át az értékelések létrehozásának [ajánlott eljárásait](best-practices-assessment.md).
2. A **Kiszolgálók** lapon, az **Azure Migrate: Kiszolgáló értékelése** csempén válassza az **Értékelés** lehetőséget.

    ![Értékelés](./media/tutorial-assess-physical/assess.png)

3. A **Kiszolgálók értékelése** területen adja meg a kiértékelés nevét, majd a **kiértékelés** típusaként válassza az *Azure-beli virtuális gép* lehetőséget, ha Azure-beli virtuális gép kiértékelését szeretné elvégezni, vagy az *Azure VMware Solution (AVS)* lehetőséget, ha AVS-kiértékeléseket szeretne végezni.

    ![A kiértékelés alapjai](./media/how-to-create-assessment/assess-servers-azurevm.png)

4. Az **Észlelés forrása** területen válassza **Az Azure Migrate-hez importálással hozzáadott gépek** elemet.

5. Válassza az **Összes megtekintése** elemet az értékelési tulajdonságok áttekintéséhez.

    ![Kiértékelés tulajdonságai](./media/tutorial-assess-physical/view-all.png)

6. Kattintson a **Tovább** gombra az **értékelni kívánt gépek kiválasztásához**. A **Csoport kiválasztása vagy létrehozása** területen válassza az **Új létrehozása** lehetőséget, majd adja meg a csoport nevét. A csoport egy vagy több virtuális gépet foglal magába a kiértékeléshez.
7. A **Gépek felvétele a csoportba** területen válassza ki azokat a kiszolgálókat, amelyeket hozzá szeretne adni a csoporthoz.
8. Kattintson a **Tovább** gombra a **Felülvizsgálat + létrehozás** területre lépéshez, a kiértékelés részleteinek áttekintéséhez.
9. Kattintson az **Értékelés létrehozása** elemre a csoport létrehozásához, majd futtassa a kiértékelést.

    ![Értékelés létrehozása](./media/tutorial-assess-physical/assessment-create.png)

9. A kiértékelés létrehozása után megtekintheti a **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** > **Értékelések** területen.
10. A kiértékelés az **Értékelés exportálása** gombra kattintva Microsoft Excel-fájlként letölthető.

Az **Azure VMware Solution- (AVS-)** kiértékelésekről [itt](how-to-create-azure-vmware-solution-assessment.md) kattintva meg többet. 

## <a name="review-an-azure-vm-assessment"></a>Azure-beli virtuális gép kiértékelésének áttekintése

Az Azure-beli virtuális gépek kiértékelése a következőket tartalmazza:

- **Azure-kompatibilitás**: Megadja, hogy a kiszolgálók megfelelők-e az Azure-ba történő migráláshoz.
- **Havi költség becslése**: A kiszolgálók Azure-ban történő futtatásának becsült havi számítási és tárolási költségei.
- **Havi tárolási költségek becslése**: A lemezes tárolás becsült költségei a migrálás után.

### <a name="view-an-assessment"></a>Kiértékelés megtekintése

1. A **Migrálási célok** > **Kiszolgálók** területen válassza az **Értékelések** lehetőséget az **Azure Migrate: Kiszolgáló értékelése** alatt.
2. Az **Értékelések** területen válasszon ki egy kiértékelést a megnyitáshoz.

    ![Kiértékelés összegzése](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Az Azure-kompatibilitás áttekintése

1. Az **Azure-kompatibilitás** területen állapítsa meg, hogy a kiszolgálók készen állnak-e az Azure-ba történő migrálásra.
2. Az állapot áttekintése:
    - **Azure-beli használatra kész**: Az Azure Migrate javaslatot tesz a virtuálisgép-méretre, illetve költségbecslést ad az értékelésben szereplő virtuális gépekhez.
    - **Feltételekkel kész**: A problémákat és a javasolt megoldásokat jeleníti meg.
    - **Nem áll készen az Azure-beli használatra**: A problémákat és a javasolt megoldásokat jeleníti meg.
    - **A kompatibilitás ismeretlen**: Az Azure Migrate nem tudja kiértékelni a kompatibilitást az adatok rendelkezésre állásával kapcsolatos probléma miatt.

3. Válasszon ki egy **Azure-kompatibilitási** állapotot. Megtekintheti a kiszolgálói kompatibilitás részleteit, illetve részletesen megtekintheti a kiszolgáló adatait, beleértve a számítási, tárolási és hálózati beállításokat.

### <a name="review-cost-details"></a>Költségadatok áttekintése

Ez a nézet a virtuális gépek az Azure-ban való futtatásával kapcsolatos becsült számítási és tárolási költséget mutatja. A következőket teheti:

- A havi számítási és tárolási költségek áttekintése. A kiértékelt csoportban lévő kiszolgálók költségei összesítve jelennek meg.

    - A költségbecslések a gépre, valamint annak lemezeire és tulajdonságaira vonatkozó méretjavaslatokon alapulnak.
    - A becsült havi számítási és tárolási költségek jelennek meg.
    - A költségbecslés a helyszíni kiszolgálók IaaS virtuális gépekként történő futtatására vonatkozik. A kiszolgálóértékelési szolgáltatás nem veszi figyelembe a PaaS- és SaaS-költségeket.

- A havi tárolási költségek becslésének áttekintése. Ez a nézet a kiértékelt csoport összesített tárolási költségeit jeleníti meg, a különböző típusú tárolólemezek szerint felosztva.
- Részletes elemzés az adott virtuális gépek részleteinek megtekintéséhez.

> [!NOTE]
> A CSV-fájl használatával a kiszolgálóértékelési szolgáltatásba importált kiszolgálók kiértékeléséhez nincsenek megbízhatósági minősítések hozzárendelve.

## <a name="supported-operating-system-names"></a>Támogatott operációsrendszer-nevek

A CSV-fájlban megadott operációsrendszer-neveknek meg kell egyezniük a listában szereplő nevekkel, vagy tartalmazniuk kell őket. Erre azért van szükség, hogy a kiértékelés érvényesként ismerje fel a megadott neveket.

<!-- BEGIN A - H -->

:::row:::
   :::column span="2":::
      **A–H**
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      Apple Mac OS X 10
   :::column-end:::
   :::column span="":::
      Asianux 3<br/>
      Asianux 4<br/>
      Asianux 5
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
      **I–R**
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
      **S–T**
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
      **U–Z**
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
      Windows Server Threshold<br/>
      Windows Vista<br/>
      Windows Web Server 2008 R2<br/>
      Windows XP Professional
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * CSV-fájl használatával importált kiszolgálók az Azure Migrate: Kiszolgáló értékelése eszközbe.
> * Létrehozott és megtekintett egy kiértékelést.

Most [helyezzen üzembe egy berendezést](./migrate-appliance.md) a pontosabb kiértékeléshez, majd rendezze csoportokba a kiszolgálókat a [függőségelemzéssel](./concepts-dependency-visualization.md) végzett részletesebb kiértékeléshez.

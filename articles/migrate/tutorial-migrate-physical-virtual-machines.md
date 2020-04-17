---
title: Az Azure Migrate szolgáltatással fizikai kiszolgálóként telepítheti át a gépeket az Azure-ba.
description: Ez a cikk ismerteti, hogyan telepítheti át a fizikai gépeket az Azure-ba az Azure Áttelepítése.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: MVC
ms.openlocfilehash: 1824fc6c7cbc0fd0390770027f4a15d9130139de
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535383"
---
# <a name="migrate-machines-as-physical-servers-to-azure"></a>Gépek áttelepítése fizikai kiszolgálóként az Azure-ba

Ez a cikk bemutatja, hogyan telepítheti át a gépeket fizikai kiszolgálóként az Azure-ba az Azure Áttelepítés:Server áttelepítése eszközzel. A gépek fizikai kiszolgálóként való áttelepítése számos esetben hasznos:

- Telepítse át a helyszíni fizikai kiszolgálókat.
- Telepítsen virtuális gépeket virtualizált platformok, mint például a Xen, KVM.
- Hyper-V vagy VMware virtuális gépek áttelepítése, ha valamilyen okból nem tudja használni a normál áttelepítési folyamat [hyper-V](tutorial-migrate-hyper-v.md)vagy [VMware](server-migrate-overview.md) áttelepítés.
- Privát felhőkben futó virtuális gépek áttelepítése.
- Nyilvános felhőkben, például amazon webszolgáltatásokban (AWS) vagy a Google Cloud Platformon (GCP) futó virtuális gépek áttelepítése.


Ez az oktatóanyag a harmadik olyan sorozat, amely bemutatja, hogyan lehet felmérni és áttelepíteni a fizikai kiszolgálókat az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készüljön fel az Azure és az Azure Áttelepítés:Server Áttelepítés használatára.
> * Ellenőrizze az áttelepíteni kívánt gépek követelményeit, és készítsen elő egy gépet az Azure Migrate replikációs berendezéshez, amely a gépek felderítésére és az Azure-ba való áttelepítésére szolgál.
> * Adja hozzá az Azure Áttelepítési kiszolgáló eszköz az Azure Áttelepítési központ.
> * Állítsa be a replikációs berendezést.
> * Telepítse a Mobilitás szolgáltatást az áttelepíteni kívánt gépekre.
> * Engedélyezze a replikációt.
> * Futtasson egy tesztáttelepítést, hogy minden a várt módon működjön.
> * Futtasson teljes áttelepítést az Azure-ba.

> [!NOTE]
> Az oktatóanyagok a forgatókönyv legegyszerűbb telepítési útvonalát mutatják be, így gyorsan beállíthat egy proof-of-concept-et. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Részletes utasításokért tekintse át az Azure Áttelepítés útmutatóját.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

[Tekintse át](migrate-architecture.md) az áttelepítési architektúrát.




## <a name="prepare-azure"></a>Az Azure előkészítése

Készítse elő az Azure-t az áttelepítésre a kiszolgálóáttelepítéssel.

**Tevékenység** | **Részletek**
--- | ---
**Azure Migrate projekt létrehozása** | Az Azure-fiókjának közreműködői vagy tulajdonosi engedélyekre van szüksége a projekt létrehozásához.
**Az Azure-fiók engedélyeinek ellenőrzése** | Az Azure-fiók nak engedélyeket kell létrehoznia egy virtuális gép létrehozásához, és írniegy Azure felügyelt lemezre.


### <a name="assign-permissions-to-create-project"></a>Engedélyek hozzárendelése projekt létrehozásához

1. Az Azure Portalon nyissa meg az előfizetést, és válassza **a hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy tulajdonosi engedélyekkel kell **rendelkeznie.**
    - Ha most hozott létre egy ingyenes Azure-fiókot, ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együttműködve rendelje hozzá a szerepkört.


### <a name="assign-azure-account-permissions"></a>Azure-fiók engedélyek hozzárendelése

Rendelje hozzá a Virtuálisgép közreműködőszerepkört az Azure-fiókhoz. Ez a következőkre vonatkozó engedélyeket biztosít:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írjon egy Azure felügyelt lemezre. 

### <a name="create-an-azure-network"></a>Azure-hálózat létrehozása

[Hozzon létre](../virtual-network/manage-virtual-network.md#create-a-virtual-network) egy Azure virtuális hálózat (VNet). Amikor replikálja az Azure-ba, az Azure virtuális gépek jönnek létre, és csatlakozott az Azure virtuális hálózat, amely az áttelepítés beállításakor megadott.

## <a name="prepare-for-migration"></a>Előkészületek a migráláshoz

A kiszolgáló fizikai áttelepítésére való felkészüléshez ellenőriznie kell a kiszolgáló fizikai beállításait, és elő kell készítenie a replikációs berendezés telepítését.

### <a name="check-machine-requirements-for-migration"></a>A gép áttelepítési követelményeinek ellenőrzése

Győződjön meg arról, hogy a gépek megfelelnek az Azure-ba való migrálás követelményeinek. 

> [!NOTE]
> Fizikai gépek áttelepítésekor az Azure Migrate:Server Migration ugyanazt a replikációs architektúrát használja, mint az Azure Site Recovery szolgáltatás ügynökalapú vész-helyreállítási, és egyes összetevők ugyanazt a kódbázist használják. Előfordulhat, hogy egyes tartalmak a Site Recovery dokumentációjára hivatkoznak.

1. [Ellenőrizze a](migrate-support-matrix-physical-migration.md#physical-server-requirements) fizikai kiszolgáló követelményeit.
2. Ellenőrizze, hogy az Azure-ba replikáló helyszíni gépek megfelelnek-e [az Azure virtuális gép követelményeinek.](migrate-support-matrix-physical-migration.md#azure-vm-requirements)


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Gép előkészítése a replikációs berendezéshez

Azure Migrate:Server Migration egy replikációs berendezés segítségével replikálja a gépeket az Azure-ba. A replikációs berendezés a következő összetevőket futtatja.

- **Konfigurációs kiszolgáló**: A konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt, és kezeli az adatreplikációt.
- **Folyamatkiszolgáló**: A folyamatkiszolgáló replikációs átjáróként működik. Replikációs adatokat fogad; gyorsítótárazásával, tömörítéssel és titkosítással optimalizálja, és elküldi egy azure-beli gyorsítótár-tárfiókba. 

Készüljön fel a berendezés üzembe helyezésére az alábbiak szerint:

- Előkészítegy gépet a replikációs berendezés üzemeltetésére. [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a gép követelményeit. A készüléket nem szabad a replikálni kívánt forrásgépre telepíteni.
- A replikációs készülék a MySQL-t használja. Tekintse át a MySQL készülékre történő telepítésének [lehetőségeit.](migrate-replication-appliance.md#mysql-installation)
- Tekintse át a replikációs készülék nyilvános [public](migrate-replication-appliance.md#url-access) és [kormányzati](migrate-replication-appliance.md#azure-government-url-access) felhők eléréséhez szükséges Azure-URL-címeket.
- Tekintse át a replikációs berendezés [port] (áttelepítés-replikáció-készülék.md#port-access) hozzáférési követelményeit.

## <a name="add-the-server-migration-tool"></a>A Kiszolgálóáttelepítés eszköz hozzáadása

Állítson be egy Azure Áttelepítési projektet, majd adja hozzá a Kiszolgálóáttelepítés eszközt.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.
4. A **Kiszolgálók felderítése, felmérése és áttelepítése csoportban**kattintson **a Kiszolgálók felmassza és áttelepítése**elemre.

    ![Kiszolgálók felfedezése és értékelése](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. A **Kiszolgálók felderítése, értékelése és migrálása** területen kattintson az **Eszközök hozzáadása** lehetőségre.
6. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
7. A **Projekt részletei** területen adja meg a projekt nevét és a földrajzi területet, ahol létre szeretné hozni a projektet, majd kattintson a **Következő** gombra. Tekintse át a támogatott földrajzi területeket [az állami](migrate-support-matrix.md#supported-geographies-public-cloud) és [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)számára.

    ![Azure Migrate projekt létrehozása](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

8. Az **Assessment eszköz kiválasztása**csoportban válassza az Értékelési eszköz hozzáadása lehetőség kiválasztása **Most** > antól**tovább**lehetőséget.
9. Az **Áttelepítés kiválasztása eszközben**válassza az **Azure Áttelepítés: Kiszolgálóáttelepítés** > **ezután**lehetőséget.
10. Az **Áttekintés + eszközök hozzáadása** területen ellenőrizze a beállításokat, majd kattintson az **Eszközök hozzáadása** lehetőségre.
11. Az eszköz hozzáadása után megjelenik az Azure Áttelepítés projekt > **kiszolgálók** > **áttelepítési eszközeiben.**

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

Az áttelepítés első lépése a replikációs berendezés beállítása. A készülék fizikai kiszolgálóáttelepítésre való beállításához töltse le a készülék telepítőfájlját, majd futtassa azt az [előkészített gépen.](#prepare-a-machine-for-the-replication-appliance) A készülék telepítése után regisztrálja azt az Azure Áttelepítési kiszolgáló áttelepítése.


### <a name="download-the-replication-appliance-installer"></a>A replikációs eszköz telepítőjének letöltése

1. Az Azure Migrate projekt **>-kiszolgálók ban**kattintson a Discover **(Azure Migrate: Server Migration)** **projektben.**

    ![Virtuális gépek felderítése](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. A **Discover machines** > **Are your machines virtualizált?**, kattintson a Nem **virtualizált/ Egyéb gombra.**
4. A **Célrégióban**válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
5. Válassza **a Megerősítés lehetőséget, hogy az áttelepítés célrégiója régiónév.Select Confirm that the target region for migration is region-name**.
6. Kattintson **az Erőforrások létrehozása gombra.** Ez létrehoz egy Azure Site Recovery-tárolóak a háttérben.
    - Ha már beállította az áttelepítést az Azure Áttelepítési kiszolgáló áttelepítésével, a célbeállítás nem konfigurálható, mivel az erőforrások korábban be vannak állítva.
    - A gombra kattintás után nem módosíthatja a projekt célrégióját.
    - Minden ezt követő áttelepítés erre a régióra van.

7. A **Szeretne telepíteni egy új replikációs berendezést?** **Install a replication appliance**
9. A **Letöltés és a telepítés a replikációs szoftver**, töltse le a készülék telepítője, és a regisztrációs kulcsot. A készülék regisztrálásához a kulcshoz kell beadni. A kulcs a letöltést követő öt napig érvényes.

    ![Letöltésszolgáltató](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Másolja a készülék telepítőfájlját és kulcsfájlját a készülékhez létrehozott Windows Server 2016-gépre.
11. Futtassa a replikációs berendezés telepítőfájlját a következő eljárásnak. A telepítés befejezése után a Készülék konfigurálása varázsló automatikusan elindul (A varázslót manuálisan is elindíthatja a készülék asztalán létrehozott cpsconfigtool parancsikon használatával). A varázsló Fiókok kezelése lapján adja meg a Mobilszolgáltatás leküldéses telepítéséhez használandó fiókadatokat. Ebben az oktatóanyagban manuálisan telepítjük a mobilitási szolgáltatást a replikálandó gépekre, ezért hozzon létre egy hamis fiókot ebben a lépésben, és folytassa.

12. Miután a telepítés után a készülék újraindult, a **Gépek felderítése**párbeszédpanelen válassza ki az új készüléket a **Konfigurációs kiszolgáló kiválasztása párbeszédpanelen,** majd kattintson a **Regisztráció véglegesítése gombra.** A regisztráció véglegesítése néhány végső feladatot hajt végre a replikációs berendezés előkészítéséhez.

    ![Regisztráció véglegesítése](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

A regisztráció véglegesítése után eltarthat egy ideig, amíg a felderített gépek megjelennek az Azure Áttelepítési kiszolgáló áttelepítése során. A virtuális gépek felderítése, a **felderített kiszolgálók** száma emelkedik.

![Felderített kiszolgálók](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése

Az áttelepíteni kívánt gépeken telepítenie kell a Mobilitási szolgáltatási ügynököt. Az ügynöktelepítők elérhetők a replikációs készüléken. Keresse meg a megfelelő telepítőt, és telepítse az ügynököt minden áttelepíteni kívánt számítógépre. Ezt a következőképpen teheti meg:

1. Jelentkezzen be a replikációs készülékbe.
2. Keresse meg a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository mappát.**
3. Keresse meg a telepítőt a gép operációs rendszeréhez és verziójához. Tekintse át [a támogatott operációs rendszereket.](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) 
4. Másolja a telepítőfájlt az áttelepíteni kívánt számítógépre.
5. Győződjön meg arról, hogy rendelkezik a készülék telepítésekor létrehozott jelmondattal.
    - A fájlt ideiglenes szöveges fájlban tárolja a számítógépen.
    - A jelszót a replikációs készüléken szerezheti be. A parancssorból futtassa a **C:\ProgramData\ASR\home\svsystems\bin\genpassphrase.exe -v** parancsot az aktuális jelszó megtekintéséhez.
    - Ne hozza létre újra a jelszót. Ez megszakítja a kapcsolatot, és újra kell regisztrálnia a replikációs készüléket.


### <a name="install-on-windows"></a>Telepítés Windows rendszeren

1. Bontsa ki a telepítőfájl tartalmát egy helyi mappába (például C:\Temp) a számítógépen, az alábbiak szerint:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. Futtassa a Mobilitási szolgáltatás telepítőjét:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Regisztrálja az ügynököt a replikációs készülékkel:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Telepítés Linux rendszeren

1. Bontsa ki a telepítő tarball tartalmát egy helyi mappába (például /tmp/MobSvcInstaller) a gépen, az alábbiak szerint:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Futtassa a telepítőparancsfájlt:
    ```
    sudo ./install -r MS -q
    ```
3. Regisztrálja az ügynököt a replikációs készülékkel:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Gépek replikálása

Most válassza ki a gépeket az áttelepítéshez. 

> [!NOTE]
> Legfeljebb 10 gépet replikálhat együtt. Ha több replikálnia kell, akkor azokat egyszerre 10-es kötegekben replikálja.

1. Az Azure Migrate projekt > **az** **Azure Áttelepítés: Kiszolgálóáttelepítés**, kattintson a **Replikálás gombra.**

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. A **Replikálás**párbeszédpanelen > **A** > **gépei virtualizáltak?** lehetőséget válassza a Nem **virtualizált/egyéb**lehetőséget.
3. A **helyszíni berendezésben**válassza ki a beállított Azure Migrate-berendezés nevét.
4. A **Process Server alkalmazásban**válassza ki a replikációs berendezés nevét.
6. A **Vendég hitelesítő adatok**ban megkell adnia egy hamis fiókot, amelyet a program a Mobilitásszolgáltatás manuális telepítéséhez fog használni (a leküldéses telepítés nem támogatott a Fizikai szolgáltatásban). Ezután kattintson a **Tovább: Virtuális gépek gombra.**

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. A **Virtuális gépek**párbeszédpanelen az **Áttelepítési beállítások importálása felmérésből?** mezőben hagyja meg a Nem alapértelmezett **beállítást, manuálisan adom meg az áttelepítési beállításokat.**
8. Ellenőrizze az áttelepíteni kívánt virtuális gépeket. Ezután kattintson a **Tovább: Célbeállítások gombra.**

    ![Virtuális gépek kijelölése](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
10. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.
11. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Kattintson a **Tovább** gombra.

    ![Célbeállítások](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. A **Számítás** területen ellenőrizze a virtuális gép nevét, méretét, az operációs rendszer lemeztípusát és a rendelkezésreállási csoportot. A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-physical-migration.md#azure-vm-requirements).

    - **Virtuális gép mérete:** Alapértelmezés szerint az Azure Migrate Server Migration az Azure-előfizetéslegközelebbi egyezése alapján választ ki méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **OPERÁCIÓS RENDSZER lemeze**: Adja meg a virtuális gép operációs rendszerének (rendszerindító) lemezét. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási készlet:** Ha a virtuális gép kell egy Azure rendelkezésre állási csoport áttelepítés után, adja meg a készlet. A csoportnak a migrálás során megadott cél-erőforráscsoportban kell lennie.

    ![Számítási beállítások](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. A **Lemezek mezőben**adja meg, hogy a virtuális gép lemezek et kell replikálni az Azure-ba, és válassza ki a lemez típusát (standard SSD/HDD vagy prémium szintű felügyelt lemezek) az Azure-ban. Kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

    ![Lemezbeállítások](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikáció beállításait a replikáció megkezdése előtt bármikor frissítheti,**replikálógépek** **kezelése** > . A replikáció kezdete után a beállítások már nem módosíthatók.



## <a name="track-and-monitor"></a>Nyomon követése és monitorozása

- Amikor a **Replikáció replikálása feladat replikálása** parancsra kattint. 
- A replikáció indítása sikeres befejezése után a gépek megkezdik az Azure-ba történő kezdeti replikációt.
- A kezdeti replikáció befejezése után megkezdődik a különbözeti replikáció. A helyszíni lemezek növekményes módosításait rendszeres időközönként replikálja a replikalemezek az Azure-ban.


A feladat állapotát a portál értesítései ben követheti nyomon.

A replikáció állapotát az **Azure Áttelepítés: Kiszolgálóáttelepítés** **szolgáltatás replikálási kiszolgálóira** kattintva figyelheti.
![Replikáció monitorozása](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdésekor futtathat egy tesztáttelepítést a virtuális gépekhez, mielőtt teljes áttelepítést futtatna az Azure-ba. Javasoljuk, hogy ezt minden géphez legalább egyszer tegye meg, mielőtt áttelepítene.

- Tesztáttelepítési ellenőrzések futtatása, amelyek áttelepítése a várt módon fog működni, anélkül, hogy befolyásolnák a helyszíni gépek, amelyek továbbra is működőképes, és továbbra is replikálása. 
- A tesztáttelepítés szimulálja az áttelepítést egy Azure virtuális gép replikált adatok használatával történő létrehozásával (általában nem éles virtuális hálózatra való áttelepítés az Azure-előfizetésben).
- Használhatja a replikált teszt Azure virtuális gép az áttelepítés érvényesítéséhez, az alkalmazástesztelésének végrehajtásához, és a problémák megoldása a teljes áttelepítés előtt.

Végezze el a tesztáttelepítést az alábbiak szerint:


1. Az **áttelepítési célok** > **kiszolgálóinak** > **Azure Migrate: Server Migration**területén kattintson az **Áttelepített kiszolgálók tesztelése**elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután meggyőződött arról, hogy a tesztáttelepítés a várt módon működik, áttelepítheti a helyszíni gépeket.

1. Az Azure Migrate projektben > **A Kiszolgálók** > **Azure Áttelepítés: Kiszolgáló áttelepítése**, kattintson **a kiszolgálók replikálása**elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **Áttelepítés** > leállítása a virtuális gépek leállítása**és a tervezett áttelepítés adatvesztés nélküli végrehajtása**csoportban válassza az **Igen** > **OK**lehetőséget.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.

    Megjegyzés: A fizikai kiszolgáló áttelepítése esetén az a javaslat, hogy az alkalmazást az áttelepítési ablak részeként hozza le (ne hagyja, hogy az alkalmazások bármilyen kapcsolatot fogadjanak el), majd kezdeményezze az áttelepítést (A kiszolgálót tovább kell futtatni, hogy a fennmaradó módosítások szinkronizálhatók legyenek) az áttelepítés befejezése előtt.

4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gép > **az áttelepítés leállítása**elemre. Ez a következőket teszi:
    - Leállítja a helyszíni gép replikációját.
    - Eltávolítja a gépet a **replikáló kiszolgálók** száma az Azure Áttelepítés: Kiszolgáló áttelepítése.
    - Törli a gép replikációs állapotadatait.
2. Telepítse az Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) ügynök az áttelepített gépeken.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány forgalmának csökkentése.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni gyakorlati tanácsok

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő forgalom elérését az [Azure Security Centerrel – Just in time administration](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>További lépések

Vizsgálja meg a [felhőmigrálási utat](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerben.

---
title: A helyszíni fizikai gépek vagy a virtuális gépek migrálása az Azure Migrate-kiszolgáló áttelepítése Azure-bA |} A Microsoft Docs
description: Ez a cikk ismerteti a helyszíni fizikai gépek vagy a virtuális gépek migrálása az Azure Migrate-kiszolgáló áttelepítése Azure-bA.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2554501ecf6d4ef115e3283fa635c24510b8c797
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249590"
---
# <a name="migrate-physical-or-virtualized-servers-to-azure"></a>Fizikai vagy virtuális kiszolgálók áttelepítése az Azure-bA 

Ez a cikk bemutatja, hogyan lehet fizikai vagy virtuális kiszolgálók áttelepítése az Azure-bA. Az Azure Migrate Server áttelepítési eszköz a fizikai és virtualizált kiszolgálók, a replikációs ügynök-alapú áttelepítés kínál. Ezzel az eszközzel, az Azure számos különféle gépek telepíthet át:

- A helyszíni fizikai kiszolgálók áttelepítése.
- Telepítse át a virtuális gépek, virtualizált platformokhoz, mint a Xen, KVM szerint.
- A Hyper-V vagy VMware virtuális gépek áttelepítéséhez. Ez akkor hasznos, ha valamilyen okból nem tud használni a szabványos áttelepítési folyamatot, amely az Azure Migrate-kiszolgáló áttelepítése biztosít [Hyper-V](tutorial-migrate-hyper-v.md), [ügynök nélküli kivételfigyelés VMware](tutorial-migrate-vmware.md) áttelepítési, vagy [VMware az ügynök-alapú](tutorial-migrate-vmware-agent.md) áttelepítés.
- A privát felhőkben futó virtuális gépek áttelepítéséhez.
- A nyilvános felhő mint az Amazon Web Services (AWS) vagy a Google Cloud Platform (GCP) futó virtuális gépek áttelepítéséhez.


[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és számítási feladatok migrálásának nyomon követését és a felhő Virtuálisgép-példányok, az Azure-bA. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az Azure előkészítése az Azure Migrate Server áttelepítési eszköz az áttelepítés.
> * Ellenőrizze a migrálni kívánt gépekre vonatkozó követelmények, és Fedezze fel, és a gépek migrálása az Azure-bA az Azure Migrate replikációs a berendezés gép előkészítése.
> * Az Azure Migrate Server áttelepítési eszköz hozzáadása az Azure Migrate hubban.
> * Állítsa be a replikációs berendezés.
> * A mobilitási szolgáltatás telepítése a migrálni kívánt gépen.
> * Engedélyezze a replikációt.
> * Győződjön meg arról, hogy minden a várt módon működik a migrálási teszt futtatása.
> * Futtassa a teljes migrálás az Azure-bA.

> [!NOTE]
> Az oktatóprogramok bemutatják, a legegyszerűbb telepítési út forgatókönyv esetén, hogy gyorsan beállíthat egy proof-of-concept. Az oktatóanyagok lehetőség szerint használja az alapértelmezett, és ne jelenjen meg az összes lehetséges beállítások és elérési út. Részletes útmutatásért tekintse át az útmutató az Azure Migrate.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Felülvizsgálat](migrate-architecture.md) a migrálási architektúra.
2. Győződjön meg arról, hogy az Azure-fiókjával a virtuális gépek Közreműködője szerepkör van hozzárendelve, hogy az engedélyekkel rendelkezik:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írni az Azure-beli felügyelt lemezt. 

3. [Azure-hálózat beállítása](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Azure-bA replikálni, amikor Azure virtuális gépek létrehozása, és csatlakozik az Azure-hálózatot, adja meg a migrálási beállításakor.


## <a name="prepare-azure"></a>Az Azure előkészítése

Az Azure Migrate-kiszolgáló áttelepítése áttelepítéshez beállítása Azure-engedélyeket.

- **Hozzon létre egy projektet**: Az Azure-fiókjával engedélyre van szüksége az Azure Migrate-projekt létrehozása. 
- **A replikáció az Azure Migrate berendezés regisztráljon**: A replikációs berendezés hoz létre, és a egy Azure Active Directory-alkalmazás regisztrálása az Azure-fiókjával. Ez vonatkozó engedélyek delegálása.
- **Hozzon létre kulcstartót**: Gépek migrálása az Azure Migrate hoz létre egy Key Vaultot az erőforráscsoport, a replikáció tárfiókot az előfizetésében a hozzáférési kulcsok kezelése. Hozza létre a tárolót, az erőforráscsoport, amelyben az Azure Migrate-projektben található a szerepkör-hozzárendelési engedéllyel kell. 


### <a name="assign-permissions-to-create-project"></a>Rendelje hozzá a projekt létrehozásához szükséges engedélyek

1. Az Azure Portalon nyissa meg az előfizetést, és válassza ki **hozzáférés-vezérlés (IAM)** .
2. A **hozzáférés ellenőrzése**, keresse meg a megfelelő fiók és engedélyek megtekintéséhez kattintson rá.
3. Rendelkeznie kell **közreműködői** vagy **tulajdonosa** engedélyeket.
    - Ha az imént létrehozott ingyenes Azure-fiókra, Ön az előfizetés tulajdonosa.
    - Ha Ön nem az előfizetés tulajdonosa, együttműködve rendelje hozzá a szerepkört a tulajdonosa.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Regisztrálja a replikációs berendezés engedélyek hozzárendelése

Az ügynök-alapú áttelepítés az Azure Migrate Kiszolgálóáttelepítés hozhat létre, és az Azure AD alkalmazás regisztrálása az a fiók engedélyeinek delegálása. Az alábbi módszerek egyikével engedélyeket rendelhet a:

- Egy bérlő globális rendszergazdai engedélyek megadásához a bérlő létrehozására és regisztrálására az Azure AD-alkalmazások a felhasználók számára.
- A bérlő globális rendszergazdája rendelhet a fiók a alkalmazásfejlesztő szerepkör (amely engedélyekkel rendelkezik).

Érdemes megjegyezni, hogy:

- Az alkalmazások nem tartoznak semmilyen más hozzáférési engedélyt a fent leírtaktól eltérő előfizetés.
- Amikor regisztrál egy új replikációs készülék csak kell ezeket az engedélyeket. Az engedélyek távolíthatja el a replikációs berendezés telepítése után. 


#### <a name="grant-account-permissions"></a>Fiók engedélyek megadása

A bérlő globális rendszergazdája a következő engedélyek megadásához

1. Az Azure AD-ben a bérlő globális rendszergazdája kell lépjen **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
2. A rendszergazdának kell beállítania **alkalmazásregisztrációk** való **Igen**.

    ![Az Azure AD-engedélyekről](./media/tutorial-migrate-physical-virtual-machines/aad.png)

> [!NOTE]
> Ez a nem bizalmas alapértelmezett beállítás. [További információk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztője szerepkör hozzárendelése 

A bérlő globális rendszergazdája a alkalmazásfejlesztő szerepkört rendelhet egy fiókot. [További információk](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>A Key Vault létrehozásához szükséges engedélyek hozzárendelése

Az az erőforráscsoport, amelyben az Azure Migrate-projektben található, a következő szerepkör-hozzárendelés engedélyek hozzárendelése:

1. Az erőforráscsoport az Azure Portalon, válassza ki **hozzáférés-vezérlés (IAM)** .
2. A **hozzáférés ellenőrzése**, keresse meg a megfelelő fiók és engedélyek megtekintéséhez kattintson rá. Szükséges **tulajdonosa** (vagy **közreműködői** és **felhasználói hozzáférés rendszergazdája**) engedélyekkel.
3. Ha nem rendelkezik a szükséges engedélyekkel, kérjen őket az erőforráscsoport-tulajdonosnak. 

## <a name="prepare-for-migration"></a>Előkészületek a migráláshoz

### <a name="check-machine-requirements-for-migration"></a>Ellenőrizze a gép áttelepítésre vonatkozó követelmények

Ellenőrizze, hogy a gépek megfelelnek a követelményeknek, az Azure-ba való migrálásra. 

> [!NOTE]
> Az ügynök-alapú áttelepítés az Azure Migrate-kiszolgáló áttelepítése az Azure Site Recovery szolgáltatás funkcióinak alapul. Site Recovery dokumentációja bizonyos követelményeknek lehet társítani.

1. [Ellenőrizze](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) a VMware-kiszolgáló követelményeit.
2. [Győződjön meg arról](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) virtuális gép áttelepítésének követelmények támogatása.
3. A virtuális gép beállításainak ellenőrzése. Azure-bA replikált helyszíni virtuális gépeknek meg kell felelnie [Azure virtuális gépekre vonatkozó](migrate-support-matrix-vmware.md#azure-vm-requirements).


### <a name="prepare-a-machine-for-the-replication-appliance"></a>A replikációs berendezés gép előkészítése

Az Azure Migrate kiszolgáló áttelepítése egy replikációs készülék használja a gépek replikálása az Azure-bA. A replikációs berendezés fut, a következő összetevőket.

- **Konfigurációs kiszolgáló**: A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamatkiszolgáló**: A folyamatkiszolgáló replikációs átjáróként üzemel. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, és elküldi azokat a gyorsítótárfiókot, az Azure-ban. 

A Kezdés előtt kell a replikációs berendezés üzemeltetéséhez Windows Server 2016 gép előkészítése. A gép meg kell felelniük az [ezek a követelmények](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements).


## <a name="add-the-azure-migrate-server-migration-tool"></a>Az Azure Migrate Server áttelepítési eszköz hozzáadása

Állítsa be az Azure Migrate-projektet, és az Azure Migrate Server áttelepítési eszköz hozzáadása.

1. Az Azure Portal > **minden szolgáltatás**, keressen **Azure Migrate**.
2. A **szolgáltatások**válassza **Azure Migrate**.
3. A **áttekintése**, kattintson a **felmérési és a kiszolgálók áttelepítése**.
4. A **felfedezheti, értékelni és migrálni a kiszolgálók**, kattintson a **felmérési és a kiszolgálók áttelepítése**.

    ![Felderítés és értékelés kiszolgálók](./media/tutorial-migrate-physical-virtual-machines/assess-migrate.png)

5. A **felfedezheti, értékelni és migrálni a kiszolgálók**, kattintson a **eszközök hozzáadása**.
6. A **Migrate projekt**, válassza ki az Azure-előfizetés, és ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
7. A **Project Details**, adja meg a projekt nevét, és a földrajzi hely, amelyben a projekt létrehozásához, és kattintson a kívánt **tovább**

    ![Az Azure Migrate-projekt létrehozása](./media/tutorial-migrate-physical-virtual-machines/migrate-project.png)

    Létrehozhat egy Azure Migrate-projekt bármely alábbi földrajzi területek számára.

    **Régiócsoport** | **Régió**
    --- | ---
    Ázsia | Délkelet-Ázsia
    Európa | Észak-Európában és Nyugat-Európa
    Egyesült Államok | USA keleti RÉGIÓJA és USA nyugati középső RÉGIÓJA

    A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. Kiválaszthatja, hogy az áttelepítés tényleges bármely célrégióban.
8. A **válassza frissítésfelmérő eszköz**válassza **egy frissítésfelmérő eszköz felvétele most kihagyása** > **tovább**.
9. A **válassza áttelepítési eszköz**válassza **Azure Migrate: Kiszolgáló-áttelepítési** > **tovább**.
10. A **tekintse át + eszközök hozzáadása**, tekintse át a beállításokat, majd kattintson **eszközök hozzáadása**
11. Miután hozzáadta az eszközt, megjelenik az Azure Migrate-Projekt > **kiszolgálók** > **áttelepítési eszközök**.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

A migrálás első lépése, hogy a replikációs berendezés beállítása. Letöltés a telepítőfájl a berendezéshez, és futtassa a [gép előkészített](#prepare-a-machine-for-the-replication-appliance). Miután telepítette a készülék, hogy regisztrálja az Azure Migrate-kiszolgáló áttelepítése.


### <a name="download-the-replication-appliance-installer"></a>Töltse le a replikációt készülék

1. Az Azure Migrate-projektben > **kiszolgálók**, a ***Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **felderítési**.

    ![Virtuális gépek felderítése](./media/tutorial-migrate-physical-virtual-machines/migrate-discover.png)

3. A **gépek felderítése** > **virtualizáltak a gépek?** , kattintson a **nem virtualizált/egyéb**.
4. A **célrégió**, válassza ki, amelyhez hozzá szeretné a gépek áttelepítése az Azure-régióban.
5. Válassza ki **erősítse meg, hogy az áttelepítéshez a célrégióban régiónévhez**.
6. Kattintson a **létre erőforrásokat**. Ez létrehoz egy Azure Site Recovery-tároló a háttérben.
    - Ha már beállította az Azure Migrate-kiszolgáló áttelepítése a migrálást, a célként megadott beállítás nem állítható be, mert erőforrások korábban volt állítva.
    - Erre a gombra kattintás után nem módosíthatja a célrégióban a projekthez.
    - Minden ezt követő áttelepítés ebben a régióban vannak.

7. A **szeretné telepíteni egy új replikációs készülék?** válassza **telepítése egy replikációs készülék**.
9. A **töltse le és telepítse a replikációs készülék szoftver**, a készülék telepítő és a regisztrációs kulcs letöltése. Annak érdekében, hogy a berendezés regisztrálni kell a kulcsot. A kulcs a nincs letöltve öt napig érvényes.

    ![Töltse le a szolgáltató](media/tutorial-migrate-physical-virtual-machines/download-provider.png)

10. Másolja a készülék fájl és a kulcs fájlját a berendezés a Windows Server 2016 gépet hozott létre.
11. Futtassa a replikáció készülék telepítőfájl, a következő eljárásban leírtak szerint.
12. A készülék újraindítása a telepítés után a **gépek felderítése**, válassza ki az új készülék **válassza ki a konfigurációs kiszolgáló**, és kattintson a **regisztráció véglegesítése**. Véglegesítése regisztrációs végső feladatok előkészítése a replikációs berendezés néhány hajt végre.

    ![Regisztráció véglegesítése](./media/tutorial-migrate-physical-virtual-machines/finalize-registration.png)

Regisztráció véglegesítése mindaddig, amíg a felderített gépek jelennek meg az Azure Migrate-kiszolgáló áttelepítése után akár 15 percet is igénybe vehet. Mivel a virtuális gépeket a felderítésüket, a **felderített kiszolgálók** megnövekszik száma.

![Észlelt kiszolgálók](./media/tutorial-migrate-physical-virtual-machines/discovered-servers.png)


## <a name="install-the-mobility-service"></a>A mobilitási szolgáltatás telepítése

Migrálni kívánt gépen kell telepíteni a mobilitási szolgáltatás. Az ügynök telepítőcsomagjai érhetők el a replikációs berendezésen. A megfelelő telepítőt, és telepítse az ügynököt minden olyan gép, amelyeket szeretné áttelepíteni. Ehhez a következő:

1. Jelentkezzen be a replikációs berendezésre.
2. Navigáljon a **%ProgramData%\ASR\home\svsystems\pushinstallsvc\repository**.
3. A telepítő a gép operációs rendszere és verziója található. Felülvizsgálat [támogatott operációs rendszerek](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines). 
4. Másolja a telepítőfájl a migrálni kívánt gépen.
5. Győződjön meg arról, hogy az a készülék üzembe helyezésekor létrejött hozzáférési kódot.
    - A fájl Store ideiglenes fájlt a számítógépen.
    - A hozzáférési kódot, a replikáció berendezésen szerezheti be. A parancssorból futtassa **C:\ProgramData\ASR\svsystems\bin\genpassphrase.exe - v** megtekintéséhez a jelenlegi jelszava.
    - Ne hozza létre újra a jelszót. Ez megszünteti a kapcsolatot, és regisztrálja újra a replikációs berendezés kell.


### <a name="install-on-windows"></a>Telepítés Windows rendszeren

1. Bontsa ki a következő installer-fájl egy helyi mappába (például C:\Temp) a gépen tartalmát:

    ```
    ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
    MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
    cd C:\Temp\Extracted
    ```
2. A mobilitási szolgáltatás telepítőjének futtatásával:
    ```
   UnifiedAgent.exe /Role "MS" /Silent
    ```
3. Az ügynök regisztrálása a replikációs berendezéssel:
    ```
    cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
    UnifiedAgentConfigurator.exe  /CSEndPoint <replication appliance IP address> /PassphraseFilePath <Passphrase File Path>
    ```

### <a name="install-on-linux"></a>Telepítés Linux rendszeren

1. Bontsa ki egy helyi mappába (például /tmp/MobSvcInstaller) a számítógépen, a telepítő tarball tartalmát a következőképpen:
    ```
    mkdir /tmp/MobSvcInstaller
    tar -C /tmp/MobSvcInstaller -xvf <Installer tarball>
    cd /tmp/MobSvcInstaller
    ```
2. Futtassa a telepítő szkriptet:
    ```
    sudo ./install -r MS -q
    ```
3. Az ügynök regisztrálása a replikációs berendezéssel:
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <replication appliance IP address> -P <Passphrase File Path>
    ```

## <a name="replicate-machines"></a>Gépek replikálása

1. Az Azure Migrate-projektben > **kiszolgálók**, **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **replikálása**.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/select-replicate.png)

2. A **replikálása**, > **adatforrás beállításaiban** > **virtualizáltak a gépek?** válassza **Igen, a VMware vSphere**.
3. A **helyszíni berendezés**, válassza ki az Azure Migrate készülék beállított nevét.
4. A **vCenter-kiszolgáló**, adja meg a nevét, a vCenter-kiszolgáló a virtuális gépek kezelése vagy a vSphere-kiszolgálóhoz, amelyen a virtuális gépek futnak.
5. A **Folyamatkiszolgáló**, válassza ki a replikációs berendezés nevét.
6. A **Vendég hitelesítő adatok**, megadhat egy virtuális Gépet a mobilitási szolgáltatás leküldéses telepítéséhez használt rendszergazdai fiókot. Ebben az oktatóanyagban azt telepíti a mobilitási szolgáltatást manuálisan, így bármely próbafiók is hozzáadhat. Kattintson a **tovább: Virtuális gépek**.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-physical-virtual-machines/source-settings.png)

7. A **virtuális gépek**, a **áttelepítési beállítások importálása egy értékelés?** , ne módosítsa az alapértelmezett **nem, fogja meg az áttelepítési beállításokat manuálisan**.
8. Ellenőrizze az egyes virtuális Gépeket szeretne áttelepíteni. Kattintson a **tovább: Cél beállításai**.

    ![Virtuális gépek kiválasztása](./media/tutorial-migrate-physical-virtual-machines/select-vms.png)


9. A **cél beállításai**, válassza ki az előfizetést, és a célrégió, amelyhez fog át, és adja meg az erőforráscsoportot, amelyben az Azure virtuális gépek helyezkednek el az áttelepítés után.
10. A **virtuális hálózat**, válassza ki az Azure virtuális hálózat/alhálózat, amelyhez az Azure virtuális gépek tartományhoz fog csatlakozni az áttelepítés után.
11. A **Azure Hybrid Benefit értékelem**:

    - Válassza ki **nem** Ha nem szeretné az Azure Hybrid Benefit alkalmazására. Ezután kattintson a **Next** (Tovább) gombra.
    - Válassza ki **Igen** Ha Windows Serveres gépek, aktív frissítési garancia vagy a Windows Server előfizetések amelyekre rendelkezik, és alkalmazza a juttatás a migráláshoz gépekhez. Ezután kattintson a **Next** (Tovább) gombra.

    ![Tárolóbeállítások](./media/tutorial-migrate-physical-virtual-machines/target-settings.png)

12. A **számítási**, tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemez típusa és a rendelkezésre állási csoport. Virtuális gépek meg kell felelniük az [Azure-követelmények](migrate-support-matrix-vmware.md#azure-vm-requirements).

    - **Virtuálisgép-méret**: Alapértelmezés szerint az Azure Migrate-kiszolgáló áttelepítése a leginkább egyezik a az Azure-előfizetés alapján méret választja ki. Azt is megteheti, válassza ki a manuális méretét **Azure Virtuálisgép-méret**. 
    - **Operációsrendszer-lemez**: Adja meg az (Indítás) operációsrendszer-lemez a virtuális gép számára. Az operációsrendszer-lemezének mérete a lemezt, amely az operációs rendszer rendszerbetöltőt, és a telepítő rendelkezik. 
    - **A rendelkezésre állási csoport**: Ha a virtuális gép az Azure rendelkezésre állási csoportba az áttelepítés után kell lennie, adja meg a készletet. A készlet adja meg, ha az áttelepítés a célerőforrás-csoportban kell lennie.

    ![Számítási beállítások](./media/tutorial-migrate-physical-virtual-machines/compute-settings.png)

13. A **lemezek**, adja meg, hogy a Virtuálisgép-lemezek replikálni kell az Azure-ba, válassza ki a lemez típusát (standard szintű SSD vagy HDD vagy a prémium szintű managed disks) az Azure-ban. Ezután kattintson a **Next** (Tovább) gombra.
    - Lemezeket zárhat ki a replikációból.
    - Lemezek kizárása nem elérhetőnek lennie az Azure virtuális gépen az áttelepítés után. 

    ![Lemezbeállítások](./media/tutorial-migrate-physical-virtual-machines/disks.png)


14. A **tekintse át, és indítsa el a replikációt**, tekintse át a beállításokat, majd kattintson **replikálása** a kezdeti replikáció a kiszolgálókhoz.

> [!NOTE]
> Replikációs beállítások bármikor replikáció megkezdése előtt is frissítenie **kezelés** > **replikáló**. Replikációs elindulása után nem lehet módosítani a beállításokat.



## <a name="track-and-monitor"></a>Nyomon követése és figyelése

- Amikor rákattint **replikálása** replikáció indítása feladat kezdődik. 
- A replikáció indítása feladat sikeres befejezését követően a gépek kezdje a kezdeti replikáció az Azure-bA.
- Kezdeti replikálás befejezése után kezdődik a változásreplikációhoz. A replika lemezeket az Azure-ban rendszeres időközönként replikálja a helyi lemezek növekményes módosításokat.


A portál értesítési a feladat állapotának nyomon követéséhez.

Kattintson a replikációs állapot figyelheti **kiszolgálók replikálásához** a **Azure Migrate: Kiszolgáló-áttelepítési**.
![A figyelő replikáció](./media/tutorial-migrate-physical-virtual-machines/replicating-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


Amikor elindul a változások replikálása az Azure-ban a teljes migrálás futtatása előtt egy áttelepítési teszt futtathatja a virtuális gépek esetén. Kifejezetten ajánljuk, hogy ehhez legalább egyszer minden egyes számítógépen, akkor az áttelepítés előtt.

- Futó migrálási teszt ellenőrzi, hogy a migrálás megfelelően működik, a helyszíni gépek befolyásolása nélkül fog működni. amely működőképes marad, és továbbra is replikálásához. 
- Migrálási teszt hoz létre a replikált adatok (az Azure-előfizetés nem éles hálózatba általában áttelepítése) használata Azure virtuális Gépen az áttelepítés szimulálja.
- Az Azure virtuális gép replikált teszt segítségével az áttelepítés ellenőrzése, hajtsa végre az alkalmazás tesztelése, és a teljes áttelepítés előtt problémák elhárítása.

Migrálási teszt hajtsa végre a következő:


1. A **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **teszt át kiszolgálók**.

     ![Az áttelepített kiszolgálók teszt](./media/tutorial-migrate-physical-virtual-machines/test-migrated-servers.png)

2. Kattintson a jobb gombbal a virtuális gép teszteléséhez, és kattintson a **teszt áttelepítése**.

    ![Migrálási teszt](./media/tutorial-migrate-physical-virtual-machines/test-migrate.png)

3. A **Migrálási teszt**, válassza ki az Azure virtuális hálózatot, amelyben az Azure virtuális gép található a migrálás után. Azt javasoljuk, hogy egy nem éles virtuális hálózathoz használhat.
4. A **migrálási teszt** feladat elindul. A portál értesítési a feladat figyeléséhez.
5. Az áttelepítés befejezése után megtekintheti a migrált Azure a virtuális gép **virtuális gépek** az Azure Portalon. A számítógépnek a neve tartalmaz egy utótagot **-teszt**.
6. A vizsgálat után kattintson a jobb gombbal az Azure VM **replikáló**, és kattintson a **migrálási teszt karbantartása**.

    ![Migrálás törlése](./media/tutorial-migrate-physical-virtual-machines/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután meggyőződött arról, hogy a migrálási teszt a várt módon működik-e, a helyszíni gépeket telepíthet át.

1. Az Azure Migrate-projektben > **kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **kiszolgálók replikálásához**.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-physical-virtual-machines/replicate-servers.png)

2. A **replikáló**, kattintson a jobb gombbal a virtuális gép > **áttelepítése**.
3. A **áttelepítése** > **virtuális gépek leállítása és az adatvesztés a tervezett áttelepítés**válassza **Igen** > **OK** .
    - Alapértelmezés szerint az Azure Migrate a helyszíni virtuális gép leáll, és futtat egy igény szerinti replikációs történt a legutóbbi replikáció óta történt virtuális gépek változásainak szinkronizálását. Ez biztosítja, hogy nincs adatvesztés.
    - Ha nem szeretné a virtuális gép leállítására, válassza ki a **nem**
4. Áttelepítési feladat elindul a virtuális gép számára. Az Azure-értesítések a feladatok nyomon követése.
5. A feladat befejezése után megtekintheti és kezelheti a virtuális gépről a **virtuális gépek** lapot.

## <a name="complete-the-migration"></a>A migrálás befejezése

1. Miután az áttelepítés elkészült, kattintson a jobb gombbal a virtuális gép > **migrálás leállítása**. A helyi gép replikálása leáll, és törli a replikációs állapot információt a virtuális gép számára.
2. Az Azure virtuális gép telepítése [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) az áttelepített gépeken az ügynök.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Kivágás felett a migrált Azure-beli Virtuálisgép-példányhoz.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Áttelepítés utáni eljárások

- A nagyobb rugalmasság érdekében:
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információk](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információk](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolását, és a bejövő forgalmat korlátozni [Azure Security Center – csak az adminisztrációs alkalommal](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>További lépések

Vizsgálja meg a [felhőbe történő migrálás folyamatának](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) a az Azure Cloud bevezetési keretrendszert.

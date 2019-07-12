---
title: Migrate a helyszíni VMware virtuális gépek Azure-alapú ügynök kiszolgáló áttelepítése Migrálása az Azure-bA |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hajthat végre egy ügynök-alapú áttelepítés, a helyszíni gépek Azure Migrate-kiszolgáló áttelepítése az Azure-bA
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: fe83cd5f38e8c091ee72875da370b6929a99b727
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854137"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware virtuális gépek áttelepítése az Azure-ba (az ügynök-alapú)

Ez a cikk bemutatja, hogyan a helyszíni VMware virtuális gépek áttelepítése az Azure-bA az Azure Migrate Server áttelepítési eszköz az ügynök-alapú áttelepítés használatával.

[Az Azure Migrate](migrate-services-overview.md) kínál egy központi agyhoz felderítési, felmérési és a helyszíni alkalmazások és számítási feladatok és az AWS/GCP Virtuálisgép-példányok, az Azure-ba való migrálásának nyomon követéséhez. A hub felmérés és migrálás, valamint a külső független szoftverszállítók (ISV) szállító ajánlatok az Azure Migrate eszközöket biztosít.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * A forráskörnyezet beállításához, és a egy ügynök-alapú áttelepítés az Azure Migrate replikációs készülék üzembe helyezése.
> * Az áttelepítéshez a célkörnyezet beállítása.
> * Állítsa be a replikációs szabályzatot.
> * Engedélyezze a replikációt.
> * Győződjön meg arról, hogy minden a várt módon működik a migrálási teszt futtatása.
> * Futtassa a teljes migrálás az Azure-bA.

> [!NOTE]
> Az oktatóprogramok bemutatják, a legegyszerűbb telepítési út forgatókönyv esetén, hogy gyorsan beállíthat egy proof-of-concept. Az oktatóanyagok lehetőség szerint használja az alapértelmezett, és ne jelenjen meg az összes lehetséges beállítások és elérési út. Részletes útmutatásért tekintse át a VMware-elemzésekhez és migráláshoz útmutatók.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Azt javasoljuk, hogy, próbálja ki a VMware virtuális gépek értékelése az Azure Migrate Server Assessment az Azure virtuális gépek áttelepítése előtt. Az értékelés az alábbiak szerint állíthatja:

1. Az oktatóanyag [előkészítése az Azure és VMware](tutorial-prepare-vmware.md) értékeléshez.
2. Ezután kövesse [ebben az oktatóanyagban](tutorial-assess-vmware.md) állítsa be az Azure Migrate a készülék értékelése, és Fedezze fel, és mérje fel a virtuális gépek.


Habár javasoljuk, hogy próbálja ki az értékelés, nem kell értékelés futtatása a virtuális gépek áttelepítése előtt.

## <a name="migration-methods"></a>Áttelepítési módszereinek

VMware virtuális gépek áttelepíthetők az Azure-bA az Azure Migrate Server áttelepítési eszköz használata. Ez az eszköz egy néhány további lehetőség a VMware virtuális gépek migrálása kínálja:

- Ügynök nélküli kivételfigyelés replikáció. Telepítse át a virtuális gépek anélkül, hogy telepít semmit a őket.
- Az ügynök-alapú áttelepítés. vagy a replikáció. A virtuális gép-ügynök (a mobilitási szolgáltatások ügynök) telepítse.

Annak eldöntéséhez, hogy szeretné-e ügynök nélküli kivételfigyelés és az ügynök-alapú áttelepítést használ, tekintse át a következő cikkeket:

- [Ismerje meg](server-migrate-overview.md) VMware áttelepítési lehetőségek.
- [Tekintse át a korlátozások](server-migrate-overview.md#agentless-migration-limitations) az ügynök nélküli migrálást.
- [Kövesse az ebben a cikkben](tutorial-migrate-vmware.md) , kipróbálhatja az ügynök nélküli migrálást.



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Felülvizsgálat](migrate-architecture.md) a VMware-migrálási architektúra.
2. Győződjön meg arról, hogy az Azure-fiókjával a virtuális gépek Közreműködője szerepkör van hozzárendelve, hogy az engedélyekkel rendelkezik:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írni az Azure-beli felügyelt lemezt. 

3. [Azure-hálózat beállítása](../virtual-network/manage-virtual-network.md#create-a-virtual-network). A helyszíni gépeket az Azure-bA replikálja a rendszer felügyelt lemezeket. Ha átadja a feladatokat az Azure-bA az áttelepítéshez, Azure virtuális gépek a felügyelt lemezek alapján létrehozott, és csatlakozik az Azure-hálózatot, adja meg az áttelepítési beállításakor.


## <a name="prepare-azure"></a>Az Azure előkészítése

Ha már futtatta egy értékelést az Azure Migrate Server Assessment, kihagyhatja a jelen szakaszban található útmutatásokat, mivel Ön már teljesítette ezeket a lépéseket. 

Ha még nem futtatta az értékelés, meg kell beállítása Azure-engedélyeket is áttelepítése az Azure Migrate-kiszolgáló áttelepítése előtt.

- **Hozzon létre egy projektet**: Az Azure-fiókjával engedélyre van szüksége az Azure Migrate-projekt létrehozása. 
- **A replikáció az Azure Migrate berendezés regisztráljon**: A replikációs berendezés hoz létre, és a egy Azure Active Directory-alkalmazás regisztrálása az Azure-fiókjával. Ez vonatkozó engedélyek delegálása kell.
- **Hozzon létre kulcstartót**: Át a VMware virtuális gépek Azure Migrate-áttelepítés használatával, az Azure Migrate létrehoz egy Key Vaultot az erőforráscsoport, a replikációs tárfiókot az előfizetésében a hozzáférési kulcsok kezeléséhez. Hozza létre a tárolót, az erőforráscsoport, amelyben az Azure Migrate-projektben található a szerepkör-hozzárendelési engedéllyel kell. 


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

    ![Az Azure AD-engedélyekről](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Ez a nem bizalmas alapértelmezett beállítás. [További információk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztője szerepkör hozzárendelése 

A bérlő globális rendszergazdája a alkalmazásfejlesztő szerepkört rendelhet egy fiókot. [További információk](https://docs.microsoft.comazure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-permissions-to-create-key-vault"></a>A Key Vault létrehozásához szükséges engedélyek hozzárendelése

Az az erőforráscsoport, amelyben az Azure Migrate-projektben található, a következő szerepkör-hozzárendelés engedélyek hozzárendelése:

1. Az erőforráscsoport az Azure Portalon, válassza ki **hozzáférés-vezérlés (IAM)** .
2. A **hozzáférés ellenőrzése**, keresse meg a megfelelő fiók és engedélyek megtekintéséhez kattintson rá. Szükséges **tulajdonosa** (vagy **közreműködői** és **felhasználói hozzáférés rendszergazdája**) engedélyekkel.
3. Ha nem rendelkezik a szükséges engedélyekkel, kérjen őket az erőforráscsoport-tulajdonosnak. 


## <a name="prepare-on-premises-vmware"></a>Helyszíni VMware előkészítése

### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

Az Azure Migrate kiszolgáló áttelepítése a VMware-kiszolgálókhoz való hozzáférésre van szüksége:

- A virtuális gépek automatikus felderítése. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Szükség van egy fiókra, amely képes műveleteket futtatni, mint például lemezek létrehozása és eltávolítása, valamint virtuális gépek működtetése.

Hozza létre a fiókot az alábbiak szerint:

1. Dedikált fiók használatához hozzon létre egy szerepkört a vCenter-szinten. Adjon meg egy nevet, például: **Azure_Site_Recovery**.
2. Rendelje hozzá a szerepkörhöz az engedélyeket az alábbi táblázatban összefoglaltaknak megfelelően.
3. Hozzon létre egy felhasználót a vCenter-kiszolgálón vagy vSphere-gazdagépen. Rendelje hozzá a szerepkört a felhasználóhoz.

#### <a name="vmware-account-permissions"></a>A VMware-fiók engedélyei

**Tevékenység** | **Szerepkör/Engedélyek** | **Részletek**
--- | --- | ---
**Virtuális gépek felderítése** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználó<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).
**Teljes replikáció, feladatátvétel, feladat-visszavétel** |  Hozzon létre egy szerepkört (Azure_Site_Recovery) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy -csoporthoz<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = Azure_Site_Recovery<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje a **Gyermekobjektumba propagálás** objektummal rendelkező **Nincs hozzáférés** szerepkört a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt gépeken.

- A replikáció az Azure Migrate berendezés felhasználóival, márpedig leküldéses telepítését, ez a szolgáltatás engedélyezi a replikálást egy géphez, vagy telepítheti manuálisan, vagy telepítési eszközökkel.
- Ebben az oktatóanyagban leküldéses telepítéssel telepítjük a mobilitási szolgáltatást.
- Ügyfélleküldéses telepítéshez meg kell készíteni egy fiókot, amely az Azure Migrate-kiszolgáló áttelepítése a virtuális gép elérésére szolgál.

Készítse elő a fiókot az alábbiak szerint:

1. Készítsen elő egy tartományi vagy helyi fiókot, amely rendelkezik a virtuális gépre való telepítéshez szükséges engedélyekkel.
2. Windows virtuális gépek esetében, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi gépen a DWORD bejegyzés hozzáadásával **LocalAccountTokenFilterPolicy**, a beállításjegyzék értékkel alatt **HKEY_ LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Linux rendszerű virtuális gépekhez készítsen elő egy rendszergazdai fiókot a Linux-forráskiszolgálón.


### <a name="check-vmware-requirements"></a>A VMware követelményeinek ellenőrzése

Győződjön meg arról, hogy a VMware-kiszolgálók és virtuális gépek megfelelnek a követelményeknek, az Azure-ba való migrálásra. 


> [!NOTE]
> Az ügynök-alapú áttelepítés az Azure Migrate-kiszolgáló áttelepítése az Azure Site Recovery szolgáltatás funkcióinak alapul. Site Recovery dokumentációja bizonyos követelményeknek lehet társítani.

1. [Ellenőrizze](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) a VMware-kiszolgáló követelményeit.
2. [Győződjön meg arról](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) virtuális gép áttelepítésének követelmények támogatása.
3. A virtuális gép beállításainak ellenőrzése. Azure-bA replikált helyszíni virtuális gépeknek meg kell felelnie [Azure virtuális gépekre vonatkozó](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>Az Azure Migrate Server áttelepítési eszköz hozzáadása

Ha nem követi a VMware virtuális gépek értékeléséhez az oktatóanyag, állítsa be az Azure Migrate-projektet, és adja hozzá az az Azure Migrate Server áttelepítési eszköz:

1. Az Azure Portal > **minden szolgáltatás**, keressen **Azure Migrate**.
2. A **szolgáltatások**válassza **Azure Migrate**.

    ![Az Azure Migrate beállítása](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. A **áttekintése**, kattintson a **felmérési és a kiszolgálók áttelepítése**.
4. A **felfedezheti, értékelni és migrálni a kiszolgálók**, kattintson a **felmérési és a kiszolgálók áttelepítése**.

    ![Felderítés és értékelés kiszolgálók](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. A **felfedezheti, értékelni és migrálni a kiszolgálók**, kattintson a **eszközök hozzáadása**.
2. A **Migrate projekt**, válassza ki az Azure-előfizetés, és ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
3. A **Project Details**, adja meg a projekt nevét, és a földrajzi hely, amelyben a projekt létrehozásához, és kattintson a kívánt **tovább**

    ![Az Azure Migrate-projekt létrehozása](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Létrehozhat egy Azure Migrate-projekt bármely alábbi földrajzi területek számára.

    **Régiócsoport** | **Régió**
    --- | ---
    Ázsia | Délkelet-Ázsia
    Európa | Észak-Európában és Nyugat-Európa
    Egyesült Államok | USA keleti RÉGIÓJA és USA nyugati középső RÉGIÓJA

    A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. Kiválaszthatja, hogy az áttelepítés tényleges bármely célrégióban.
4. A **válassza frissítésfelmérő eszköz**válassza **egy frissítésfelmérő eszköz felvétele most kihagyása** > **tovább**.
5. A **válassza áttelepítési eszköz**válassza **Azure Migrate: Kiszolgáló-áttelepítési** > **tovább**.
6. A **tekintse át + eszközök hozzáadása**, tekintse át a beállításokat, és kattintson a **eszközök hozzáadása**
7. Miután hozzáadta az eszközt, megjelenik az Azure Migrate-Projekt > **kiszolgálók** > **áttelepítési eszközök**.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

A migrálás első lépése, hogy a replikációs berendezés beállítása. A replikációs berendezés egyetlen, magas rendelkezésre állású, a helyszíni VMware virtuális gép, amelyen ezek az összetevők:

- **Konfigurációs kiszolgáló**: A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamatkiszolgáló**: A folyamatkiszolgáló replikációs átjáróként üzemel. Ez fogadja a replikált adatokat; gyorsítótárazás, tömörítés és titkosítás segítségével optimalizálja őket, és elküldi azokat a gyorsítótárfiókot, az Azure-ban. A folyamatkiszolgáló Ezenfelül telepíti a mobilitási szolgáltatás ügynökének szeretne replikálni, virtuális gépeken, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.


Állítsa be a replikációs berendezés, töltse le egy előkészített Open Virtualization alkalmazás (OVA) sablont. Importálja a VMware, és a replikációs berendezés virtuális gép létrehozásához. 

### <a name="download-the-replication-appliance-template"></a>A replikációs készülék sablon letöltése

Töltse le a sablont a következőképpen:

1. Az Azure Migrate-projektben kattintson **kiszolgálók** alatt **áttelepítési célok**.
2. A **Azure Migrate - kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **felderítési**.

    ![Virtuális gépek felderítése](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. A **gépek felderítése** > **virtualizáltak a gépek?** , kattintson a **Igen, a VMWare vSphere hipervizor**.
4. A **hogyan szeretne áttelepíteni?** válassza **a replikációs ügynök-alapú**.
5. A **célrégió**, válassza ki, amelyhez hozzá szeretné a gépek áttelepítése az Azure-régióban.
6. Válassza ki **erősítse meg, hogy az áttelepítéshez a célrégióban régiónévhez**.
7. Kattintson a **létre erőforrásokat**. Ez létrehoz egy Azure Site Recovery-tároló a háttérben.
    - Erre a gombra kattintás után nem módosíthatja a célrégióban a projekthez.
    - Minden ezt követő áttelepítés ebben a régióban vannak.

    ![Helyreállítási tár létrehozása](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. A **szeretné telepíteni egy új replikációs készülék?** válassza **telepítése egy replikációs készülék**.
9. Kattintson a **letöltése**, a replikáció berendezés letöltése. Ez letölti az OVF-sablonnal, amely létrehoz egy új VMware virtuális gép, amelyen a berendezés.
    ![Töltse le az OVA](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Jegyezze fel az erőforráscsoportot és a Recovery Services-tároló nevét. Ezek a berendezés üzembe helyezés során szüksége.


### <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

Az OVF-sablon a letöltés után történő importálása a replikáció alkalmazás létrehozása a Windows Server 2016 rendszert futtató VMware virtuális gép VMware.

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget a **Deploy OVF Template** (OVF-sablon telepítése) varázsló elindításához. 
3. A **forrás kiválasztása**, adja meg a letöltött OVF helyét.
4. A **részletek áttekintése**válassza **tovább**.
5. A **név és mappa kiválasztása** és **válassza konfigurációs**, fogadja el az alapértelmezett beállításokat.
6. A **válassza ki a storage** > **válassza ki a virtuális lemez formátum**, a legjobb teljesítmény érdekében válassza **Thick Provision Eager Zeroed**.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **való**, az alapértelmezett beállításokkal, válassza ki a virtuális gép beállításához **üzembe helyezés után a Power** > **Befejezés**.

   > [!TIP]
   > Ha további hálózati adaptereket szeretne hozzáadni, törölje a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) beállítás jelölését. Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

### <a name="kick-off-replication-appliance-setup"></a>Felhőplatformos termékeiért replikációs berendezés beállítása

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként, a rendszergazdai jelszó használata a virtuális Gépet.
4. Az első alkalommal jelentkezik be, a replikáció készülék telepítő eszköz (az Azure Site Recovery Configuration Tool) néhány másodpercen belül elindul.
5. Adjon meg egy nevet az Azure Migrate-kiszolgáló áttelepítése a készülék regisztrálásához. Ezután kattintson a **Next** (Tovább) gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe.
7. Várjon, amíg az eszköz befejezi a készülék azonosításához az Azure AD alkalmazás regisztrálása. A berendezés újraindul.
1. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.

### <a name="register-the-replication-appliance"></a>A replikációs berendezés regisztráljon

Befejezés beállítása és regisztrálása a replikációs berendezés.

1. A konfigurációs kiszolgáló felügyeleti varázslójában válassza **kapcsolat beállítása**.
2. Válassza ki a hálózati Adaptert (alapértelmezés szerint csak egy hálózati adapter be van), hogy a replikációs készülék akkor használ, a virtuális gépek felderítésének, és a egy forrásgépek futó mobilitási szolgáltatás leküldéses telepítéséhez hajtsa végre.
3. Válassza ki a hálózati Adaptert, amely a replikáció készülék akkor használ, a hálózati kapcsolatot az Azure-ral. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás konfigurálását követően nem módosítható.
4. Ha a készülék egy proxykiszolgáló mögött található, adja meg a proxybeállítások szeretné.
    - Adja meg a proxykiszolgáló nevét **http://ip-address** , vagy **http://FQDN** . HTTPS-proxykiszolgálók nem támogatottak.
5. Ha a rendszer kéri az előfizetés, erőforráscsoport és tár adatait, adja hozzá a részleteit, ha a készülék sablon letöltött feljegyzett.
6. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
7. Válassza a **VMware PowerCLI telepítése** elemet. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután válassza a **Folytatás** elemet.
8. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
9. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
10. Adja meg a fiók hitelesítő adatait, [létrehozott](#prepare-an-account-for-automatic-discovery) VMware felderítéshez. Válassza ki **hozzáadása** > **továbbra is**.
11. A **virtuális gép hitelesítő adatainak konfigurálása**, adja meg a hitelesítő adatait, [létrehozott](#prepare-an-account-for-mobility-service-installation) az ügyfélleküldéses telepítés a mobilitási szolgáltatást, ha engedélyezi a virtuális gépek replikációját.  
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
12. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.


A replikációs berendezés regisztrálása után az Azure Migrate Server Assessment VMware-kiszolgálókat a megadott beállításokkal csatlakozik, és felderíti a virtuális gépeket. Megtekintheti a felderített virtuális gépen **kezelés** > **elemek felderítése**, a a **más** fülre.


## <a name="replicate-vms"></a>Virtuális gépek replikálása

1. Az Azure Migrate-projektben > **kiszolgálók**, **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **replikálása**.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. A **replikálása**, > **adatforrás beállításaiban** > **virtualizáltak a gépek?** válassza **Igen, a VMware vSphere**.
3. A **helyszíni berendezés**, válassza ki az Azure Migrate készülék beállított nevét.
4. A **vCenter-kiszolgáló**, adja meg a nevét, a vCenter-kiszolgáló a virtuális gépek kezelése vagy a vSphere-kiszolgálóhoz, amelyen a virtuális gépek futnak.
5. A **Folyamatkiszolgáló**, válassza ki a replikációs berendezés nevét.
6. A **Vendég hitelesítő adatok**, adja meg a virtuális gép rendszergazdai fiókkal, amely a mobilitási szolgáltatás leküldéses telepítéséhez használható. Kattintson a **tovább: Virtuális gépek**.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. A **virtuális gépek**, válassza ki a replikálni kívánt gépeken.

    - Ha egy értékelést a virtuális gépeken futtatott, virtuális gép méretezése és a lemez típusát (prémium vagy standard) javaslatokat az értékelés eredményeinek is alkalmazhat. Ehhez a **áttelepítési beállításokat importálhat egy Azure Migrate értékelést?** , jelölje be a **Igen** lehetőséget.
    - Ha nem futtatta az értékelés, vagy nem kívánja az értékelési beállításokat használja, jelölje be a **nem** beállítások.
    - Ha az értékelés használatára, válassza ki a VM-csoportazonosítóval, és az értékelés nevét.

8. Ellenőrizze az egyes virtuális Gépeket szeretne áttelepíteni. Kattintson a **tovább: Cél beállításai**.
9. A **cél beállításai**, válassza ki az előfizetést, és a célrégió, amelyhez fog át, és adja meg az erőforráscsoportot, amelyben az Azure virtuális gépek helyezkednek el az áttelepítés után.
10. A **virtuális hálózat**, válassza ki az Azure virtuális hálózat/alhálózat, amelyhez az Azure virtuális gépek tartományhoz fog csatlakozni az áttelepítés után.
11. A **Azure Hybrid Benefit értékelem**:

    - Válassza ki **nem** Ha nem szeretné az Azure Hybrid Benefit alkalmazására. Ezután kattintson a **Next** (Tovább) gombra.
    - Válassza ki **Igen** Ha Windows Serveres gépek, aktív frissítési garancia vagy a Windows Server előfizetések amelyekre rendelkezik, és alkalmazza a juttatás a migráláshoz gépekhez. Ezután kattintson a **Next** (Tovább) gombra.

12. A **számítási**, tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemez típusa és a rendelkezésre állási csoport. Virtuális gépek meg kell felelniük az [Azure-követelmények](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Virtuálisgép-méret**: Kapcsolódó javaslatokat használja, ha a virtuális gép mérete legördülő listában az ajánlott mérete fogja tartalmazni. Ellenkező esetben az Azure Migrate választja ki a mérete alapján a leginkább egyezik az Azure-előfizetésében. Azt is megteheti, válassza ki a manuális méretét **Azure Virtuálisgép-méret**. 
    - **Operációsrendszer-lemez**: Adja meg az (Indítás) operációsrendszer-lemez a virtuális gép számára. Az operációsrendszer-lemezének mérete a lemezt, amely az operációs rendszer rendszerbetöltőt, és a telepítő rendelkezik. 
    - **A rendelkezésre állási csoport**: Ha a virtuális gép az Azure rendelkezésre állási csoportba az áttelepítés után kell lennie, adja meg a készletet. A készlet adja meg, ha az áttelepítés a célerőforrás-csoportban kell lennie.

13. A **lemezek**, adja meg, hogy a Virtuálisgép-lemezek replikálni kell az Azure-ba, válassza ki a lemez típusát (standard szintű SSD vagy HDD vagy a prémium szintű managed disks) az Azure-ban. Ezután kattintson a **Next** (Tovább) gombra.
    - Lemezeket zárhat ki a replikációból.
    - Lemezek kizárása nem elérhetőnek lennie az Azure virtuális gépen az áttelepítés után. 

14. A **tekintse át, és indítsa el a replikációt**, tekintse át a beállításokat, majd kattintson **replikálása** a kezdeti replikáció a kiszolgálókhoz.

> [!NOTE]
> Replikációs beállítások bármikor replikáció megkezdése előtt is frissítenie **kezelés** > **replikáló**. Replikációs elindulása után nem lehet módosítani a beállításokat.




## <a name="track-and-monitor"></a>Nyomon követése és figyelése

- Amikor rákattint **replikálása** replikáció indítása feladat kezdődik. 
- A replikáció indítása feladat sikeres befejezését követően a gépek kezdje a kezdeti replikáció az Azure-bA.
- Kezdeti replikálás befejezése után kezdődik a változásreplikációhoz. A replika lemezeket az Azure-ban rendszeres időközönként replikálja a helyi lemezek növekményes módosításokat.


A portál értesítési a feladat állapotának nyomon követéséhez.

![Feladatok nyomon követése](./media/tutorial-migrate-vmware-agent/jobs.png)

Kattintson a replikációs állapot figyelheti **kiszolgálók replikálásához** a **Azure Migrate: Kiszolgáló-áttelepítési**.
![A figyelő replikáció](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


Amikor elindul a változások replikálása az Azure-ban a teljes migrálás futtatása előtt egy áttelepítési teszt futtathatja a virtuális gépek esetén. Kifejezetten ajánljuk, hogy ehhez legalább egyszer minden egyes számítógépen, akkor az áttelepítés előtt.

- Futó migrálási teszt ellenőrzi, hogy a migrálás megfelelően működik, a helyszíni gépek befolyásolása nélkül fog működni. amely működőképes marad, és továbbra is replikálásához. 
- Migrálási teszt hoz létre a replikált adatok (az Azure-előfizetés nem éles hálózatba általában áttelepítése) használata Azure virtuális Gépen az áttelepítés szimulálja.
- Az Azure virtuális gép replikált teszt segítségével az áttelepítés ellenőrzése, hajtsa végre az alkalmazás tesztelése, és a teljes áttelepítés előtt problémák elhárítása.

Migrálási teszt hajtsa végre a következő:


1. A **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **teszt át kiszolgálók**.

     ![Az áttelepített kiszolgálók teszt](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kattintson a jobb gombbal a virtuális gép teszteléséhez, és kattintson a **teszt áttelepítése**.

    ![Migrálási teszt](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. A **Migrálási teszt**, válassza ki az Azure virtuális hálózatot, amelyben az Azure virtuális gép található a migrálás után. Azt javasoljuk, hogy egy nem éles virtuális hálózathoz használhat.
4. A **migrálási teszt** feladat elindul. A portál értesítési a feladat figyeléséhez.
5. Az áttelepítés befejezése után megtekintheti a migrált Azure a virtuális gép **virtuális gépek** az Azure Portalon. A számítógépnek a neve tartalmaz egy utótagot **-teszt**.
6. A vizsgálat után kattintson a jobb gombbal az Azure VM **replikáló**, és kattintson a **migrálási teszt karbantartása**.

    ![Migrálás törlése](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután meggyőződött arról, hogy a migrálási teszt a várt módon működik-e, a helyszíni gépeket telepíthet át.

1. Az Azure Migrate-projektben > **kiszolgálók** > **Azure Migrate: Kiszolgáló-áttelepítési**, kattintson a **kiszolgálók replikálásához**.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

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
-  Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.


## <a name="next-steps"></a>További lépések

Vizsgálja meg a [felhőbe történő migrálás folyamatának](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) a az Azure Cloud bevezetési keretrendszert.

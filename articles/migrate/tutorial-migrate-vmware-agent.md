---
title: VMware virtuális gépek migrálása ügynökön alapuló Azure Migrate kiszolgáló áttelepítése
description: Ismerje meg, hogyan futtathatja a VMware virtuális gépek ügynök-alapú áttelepítését Azure Migrate használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 581014b89627905e3206705dffade5ba19443b65
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196301"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware virtuális gépek migrálása az Azure-ba (ügynök-alapú)

Ez a cikk bemutatja, hogyan telepítheti át a helyszíni VMware virtuális gépeket az Azure-ba az ügynök-alapú áttelepítés és a Azure Migrate Server áttelepítési eszköz használatával.

A [Azure Migrate](migrate-services-overview.md) egy központi központot biztosít a helyszíni alkalmazások és munkaterhelések, valamint az AWS/GCP virtuálisgép-példányok felderítésének, értékelésének és áttelepítésének nyomon követéséhez az Azure-ban. Az elosztó Azure Migrate eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz.


Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Állítsa be a forrás-környezetet, és helyezzen üzembe egy Azure Migrate replikációs berendezést az ügynök-alapú áttelepítéshez.
> * Állítsa be a célként megadott környezetet az áttelepítéshez.
> * Állítson be replikációs szabályzatot.
> * Engedélyezze a replikációt.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson teljes áttelepítést az Azure-ba.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a következő témakört: a VMware értékeléséhez és áttelepítéséhez szükséges útmutatók.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="before-you-begin"></a>Előkészületek

Javasoljuk, hogy a virtuális gépek Azure-ba való áttelepítését megelőzően próbálja ki a VMware virtuális gép értékelését Azure Migrate Server Assessment használatával. Az értékelést a következőképpen állíthatja be:

1. Az oktatóanyagot követve [készítse elő az Azure és a VMware](tutorial-prepare-vmware.md) értékelését.
2. Ezután kövesse [ezt az oktatóanyagot](tutorial-assess-vmware.md) egy Azure Migrate berendezés értékeléshez való beállításához, valamint a virtuális gépek felderítéséhez és értékeléséhez.


Bár javasoljuk, hogy kipróbáljon egy értékelést, a virtuális gépek áttelepítését megelőzően nem kell értékelést futtatnia.

## <a name="migration-methods"></a>Áttelepítési módszerek

A VMware virtuális gépeket áttelepítheti az Azure-ba az Azure Migrate Server áttelepítési eszköz használatával. Ez az eszköz néhány lehetőséget kínál a VMware virtuális gépek áttelepítésére:

- Ügynök nélküli replikálás. Telepítse át a virtuális gépeket anélkül, hogy bármit telepítenie kellene rájuk.
- Ügynök-alapú áttelepítés. vagy replikálás. Telepítsen egy ügynököt (a mobilitási szolgáltatások ügynököt) a virtuális gépre a replikáláshoz.

Annak eldöntéséhez, hogy ügynök nélkül vagy ügynökön alapuló áttelepítést szeretne-e használni, tekintse át a következő cikkeket:

- [További](server-migrate-overview.md) információ a VMware áttelepítési lehetőségekről.
- Az [áttelepítési módszerek összehasonlítása](server-migrate-overview.md#compare-migration-methods).
- Az ügynök nélküli áttelepítés kipróbálásához [kövesse ezt a cikket](tutorial-migrate-vmware.md) .



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Tekintse át](migrate-architecture.md) a VMware áttelepítési architektúrát.
2. Győződjön meg arról, hogy az Azure-fiókja hozzá van rendelve a virtuálisgép-közreműködő szerepkörhöz, így rendelkezik a következő engedélyekkel:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írás egy Azure-beli felügyelt lemezre. 

3. [Hozzon létre egy Azure-hálózatot](../virtual-network/manage-virtual-network.md#create-a-virtual-network). A helyszíni gépek replikálódnak az Azure Managed Disks szolgáltatásba. Az Azure-ba történő áttelepítéskor az Azure-beli virtuális gépek ezekből a felügyelt lemezekről jönnek létre, és az áttelepítés beállításakor megadott Azure-hálózathoz csatlakoznak.


## <a name="prepare-azure"></a>Az Azure előkészítése

Ha már futtatott értékelést Azure Migrate Server Assessment használatával, kihagyhatja a szakasz utasításait, mivel már végrehajtotta ezeket a lépéseket. 

Ha még nem futtatott értékelést, be kell állítania az Azure-engedélyeket, mielőtt áttelepíti a Azure Migrate-kiszolgáló áttelepítését.

- **Projekt létrehozása**: az Azure-fióknak rendelkeznie kell egy Azure Migrate projekt létrehozásához szükséges engedélyekkel. 
- **Regisztrálja a Azure Migrate replikációs berendezést**: a replikációs készülék létrehoz és regisztrál egy Azure Active Directory alkalmazást az Azure-fiókjában. Ehhez engedélyeket kell delegálnia.
- **Key Vault létrehozása**: Ha VMWare virtuális gépeket szeretne áttelepíteni Azure Migrate kiszolgáló áttelepítésével, a Azure Migrate létrehoz egy Key Vault az erőforráscsoporthoz, hogy a hozzáférési kulcsokat az előfizetésében lévő replikációs Storage-fiókhoz kezelhesse. A tároló létrehozásához szerepkör-hozzárendelési engedélyekre van szükség ahhoz az erőforráscsoporthoz, amelyben az Azure Migrate-projekt található. 


### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Engedélyek kiosztása a replikációs berendezés regisztrálásához

Az ügynök-alapú áttelepítés esetében Azure Migrate kiszolgáló áttelepítésére vonatkozó engedélyeket delegálhat az Azure AD-alkalmazás létrehozásához és regisztrálásához a fiókjában. Engedélyeket a következő módszerek egyikével rendelhet hozzá:

- A bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói számára az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- A bérlői/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik engedélyekkel) a fiókhoz.

Érdemes megjegyezni, hogy:

- Az alkalmazások nem rendelkeznek más hozzáférési engedélyekkel az előfizetéshez a fent leírtak kivételével.
- Ezeket az engedélyeket csak akkor kell megadnia, amikor új replikációs berendezést regisztrál. Az engedélyeket a replikációs berendezés beállítása után távolíthatja el. 


#### <a name="grant-account-permissions"></a>Fiók engedélyeinek megadása

A bérlő/globális rendszergazda a következőképpen adhat meg engedélyeket.

1. Az Azure AD-ben a bérlői/globális rendszergazdának **Azure Active Directory** > **felhasználó** > **felhasználói beállításokat**kell megkeresnie.
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** .

    ![Azure AD-engedélyek](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Ez egy alapértelmezett beállítás, amely nem érzékeny. [Részletek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása 

A bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét egy fiókhoz. [Részletek](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Engedélyek kiosztása Key Vault létrehozásához

Rendeljen szerepkör-hozzárendelési engedélyeket azon az erőforráscsoporthoz, amelyben az Azure Migrate-projekt található, a következőképpen:

1. A Azure Portal erőforráscsoporthoz válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez. Szüksége van a **tulajdonos** (vagy a **közreműködő** és a **felhasználói hozzáférés rendszergazdai**) engedélyeire.
3. Ha nem rendelkezik a szükséges engedélyekkel, kérje meg őket az erőforráscsoport tulajdonosától. 


## <a name="prepare-on-premises-vmware"></a>Helyszíni VMware előkészítése

### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

Azure Migrate kiszolgáló áttelepítésének a következőhöz kell hozzáférnie a VMware-kiszolgálókhoz:

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

- A Azure Migrate replikációs berendezés elvégezheti a szolgáltatás leküldéses telepítését, amikor engedélyezi a replikálást egy gép számára, vagy manuálisan telepítheti, vagy a telepítési eszközöket is használhatja.
- Ebben az oktatóanyagban leküldéses telepítéssel telepítjük a mobilitási szolgáltatást.
- A leküldéses telepítéshez elő kell készítenie egy fiókot, amely Azure Migrate a kiszolgáló áttelepítése a virtuális gép elérésére használható.

Készítse elő a fiókot az alábbiak szerint:

1. Készítsen elő egy tartományi vagy helyi fiókot, amely rendelkezik a virtuális gépre való telepítéshez szükséges engedélyekkel.
2. Windows rendszerű virtuális gépek esetén, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi gépen a **LOCALACCOUNTTOKENFILTERPOLICY**DWORD-bejegyzés hozzáadásával a beállításjegyzékben, a **HKEY_LOCAL_MACHINE \software\microsoft\windows\currentversion\policies\system**
3. Linux rendszerű virtuális gépek esetében készítse elő a root fiókot a forrás Linux-kiszolgálón.


### <a name="check-vmware-requirements"></a>A VMware követelményeinek ellenőrzése

Győződjön meg arról, hogy a VMware-kiszolgálók és a virtuális gépek megfelelnek az Azure-ba való Migrálás követelményeinek. 


> [!NOTE]
> Az ügynök-alapú áttelepítés Azure Migrate kiszolgáló áttelepítésével a Azure Site Recovery szolgáltatás szolgáltatásain alapul. Néhány követelmény Site Recovery dokumentációra mutató hivatkozásokat is tartalmazhat.

1. [Ellenőrizze](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) a VMware-kiszolgáló követelményeit.
2. [Ellenőrzés](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) A VM-támogatás követelményei az áttelepítéshez.
3. A virtuális gép beállításainak ellenőrzése. Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az Azure-beli [virtuális gép követelményeinek](migrate-support-matrix-vmware.md#azure-vm-requirements).



## <a name="add-the-azure-migrate-server-migration-tool"></a>A Azure Migrate Server áttelepítési eszköz hozzáadása

Ha nem követte az oktatóanyagot a VMware virtuális gépek felméréséhez, állítson be egy Azure Migrate projektet, majd adja hozzá a Azure Migrate Server áttelepítési eszközt:

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.

    ![Azure Migrate beállítása](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Az **Áttekintés** területen kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.
4. A **kiszolgálók felderítése, felmérése és migrálása**területen kattintson a **kiszolgálók felmérése és migrálása**elemre.

    ![Kiszolgálók felderítése és értékelése](./media/tutorial-migrate-vmware-agent/assess-migrate.png)

1. A **Kiszolgálók felderítése, értékelése és migrálása** területen kattintson az **Eszközök hozzáadása** lehetőségre.
2. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
3. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet, majd kattintson a **tovább** gombra.

    ![Azure Migrate projekt létrehozása](./media/tutorial-migrate-vmware-agent/migrate-project.png)

    Ezen földrajzi területek bármelyikében létrehozhat egy Azure Migrate projektet.

    **Régiócsoport** | **Régió**
    --- | ---
    Ázsia | Délkelet-Ázsia
    Európa | Észak-Európa vagy Nyugat-Európa
    Egyesült Államok | Az USA keleti régiója vagy az USA nyugati középső régiója

    A projekthez megadott földrajzi hely csak a helyszíni virtuális gépekről gyűjtött metaadatok tárolására szolgál. A tényleges áttelepítéshez kiválaszthatja a kívánt régiót.
4. Az **értékelési eszköz kiválasztása**lapon válassza a **kiértékelési eszköz hozzáadásának mellőzése most** > **tovább**lehetőséget.
5. Az **áttelepítési eszköz kiválasztása**lapon válassza a **Azure Migrate: kiszolgáló áttelepítése** > **tovább**lehetőséget.
6. A **felülvizsgálat + eszközök hozzáadása**lapon tekintse át a beállításokat, majd kattintson az **eszközök hozzáadása** elemre.
7. Az eszköz hozzáadása után a Azure Migrate Project > **servers** > **áttelepítési eszközök**területen jelenik meg.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

Az áttelepítés első lépése a replikációs berendezés beállítása. A replikációs berendezés egyetlen, magasan elérhető helyszíni VMware virtuális gép, amely a következő összetevőket tárolja:

- **Konfigurációs kiszolgáló**: a konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamat kiszolgálója**: a Process Server replikációs átjáróként működik. Replikációs adatkérést kap; a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és egy gyorsítótárbeli Storage-fiókba küldi az Azure-ban. A Process Server a mobilitási szolgáltatás ügynökét is telepíti a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.


A replikációs berendezés beállításához le kell töltenie egy előkészített Open Virtualization Application (PETESEJT) sablont. Importálja a sablont a VMware-be, majd hozza létre a replikációs berendezés virtuális gépét. 

### <a name="download-the-replication-appliance-template"></a>A replikációs berendezés sablonjának letöltése

Töltse le a sablont a következőképpen:

1. A Azure Migrate projektben kattintson az **áttelepítési célok**területen található **kiszolgálók** elemre.
2. A **Azure Migrate-** servers > **Azure Migrate: kiszolgáló áttelepítése**területen kattintson a **felderítés**gombra.

    ![Virtuális gépek felderítése](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. A **Gépek felderítése** > **Virtualizáltak a gépek?** területen kattintson az **Igen, a VMware vSphere hipervizorral** lehetőségre.
4. A **hogyan kívánja áttelepíteni?** területen válassza az **ügynök-alapú replikáció használata**lehetőséget.
5. A **cél régióban**válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
6. Válassza **a megerősítés lehetőséget, hogy az áttelepítéshez a régió neve legyen**.
7. Kattintson az **erőforrások létrehozása**gombra. Ezzel létrehoz egy Azure Site Recovery tárolót a háttérben.
    - Erre a gombra kattintva nem módosítható a projekthez tartozó cél régió.
    - Az összes további áttelepítés erre a régióra vonatkozik.

    ![Helyreállítási tár létrehozása](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. A szeretné **telepíteni az új replikációs készüléket?** területen válassza **a replikációs berendezés telepítése**lehetőséget.
9. A replikációs berendezés letöltéséhez kattintson a **Letöltés**gombra. Ezzel letölt egy OVF-sablont, amelyet egy új, a készüléket futtató VMware virtuális gép létrehozásához használ.
    ![a petesejtek letöltése](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Jegyezze fel az erőforráscsoport és a Recovery Services tároló nevét. Ezeket a berendezések üzembe helyezése során kell megadnia.


### <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

A OVF-sablon letöltése után importálja a VMware-be a replikációs alkalmazás létrehozásához egy Windows Server 2016 rendszert futtató VMware virtuális gépen.

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere Client használatával.
2. A **File** (Fájl) menüben válassza a **Deploy OVF Template** (OVF-sablon telepítése) lehetőséget a **Deploy OVF Template** (OVF-sablon telepítése) varázsló elindításához. 
3. A **forrás kiválasztása**területen adja meg a letöltött OVF helyét.
4. A **részletek áttekintése**lapon válassza a **tovább**lehetőséget.
5. A **név és mappa kiválasztása** és a **konfiguráció kiválasztása lapon**fogadja el az alapértelmezett beállításokat.
6. A **tároló kiválasztása** > a **virtuális lemez formátuma**lehetőségnél válassza a legjobb teljesítmény lehetőséget, ha a **vastag kiépítés alig nulla értékű**.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **készen áll**, ha a virtuális gépet az alapértelmezett beállításokkal szeretné beállítani, válassza a **bekapcsolás az üzembe helyezés után** > **Befejezés**lehetőséget.

   > [!TIP]
   > Ha további hálózati adaptereket szeretne hozzáadni, törölje a **Power on after deployment** > **Finish** (Bekapcsolás a telepítést követően > Befejezés) beállítás jelölését. Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

### <a name="kick-off-replication-appliance-setup"></a>A replikációs berendezés beállításának elindításához

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre a rendszergazdai jelszó használatával.
4. Amikor először jelentkezik be, a replikációs berendezés telepítési eszköze (Azure Site Recovery konfigurációs eszköz) néhány másodpercen belül elindul.
5. Adja meg a berendezés Azure Migrate kiszolgáló áttelepítéssel való regisztrálásához használni kívánt nevet. Ezután kattintson a **Next** (Tovább) gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe.
7. Várja meg, amíg az eszköz befejeződik egy Azure AD-alkalmazás regisztrálása a berendezés azonosításához. A készülék újraindul.
1. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.

### <a name="register-the-replication-appliance"></a>A replikációs berendezés regisztrálása

A replikációs berendezés beállításának és regisztrálásának befejezése.

1. A konfigurációs kiszolgáló kezelése varázslóban válassza a **kapcsolat beállítása**lehetőséget.
2. Válassza ki a hálózati adaptert (alapértelmezés szerint csak egy hálózati adapter van), amelyet a replikációs berendezés használ a virtuális gépek felderítéséhez, és a mobilitási szolgáltatás leküldéses telepítését a forrásszámítógépen.
3. Válassza ki azt a hálózati adaptert, amelyet a replikációs berendezés az Azure-hoz való kapcsolódáshoz használ. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás a konfigurálás után nem módosítható.
4. Ha a készülék proxykiszolgáló mögött található, meg kell adnia a proxybeállításokat.
    - Adja meg a proxy nevét **http://ip-addressként** vagy **http://FQDNként** . A HTTPS-proxy kiszolgálók nem támogatottak.
5. Ha a rendszer rákérdez az előfizetés, az erőforráscsoport és a tár részleteire, adja meg a berendezés sablonjának letöltésekor feljegyzett adatokat.
6. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
7. Válassza a **VMware PowerCLI telepítése** elemet. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután válassza a **Folytatás** elemet.
8. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
9. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
10. Adja meg a VMware discoveryhez [létrehozott](#prepare-an-account-for-automatic-discovery) fiók hitelesítő adatait. Válassza a **hozzáadás** > **Folytatás**lehetőséget.
11. A **virtuális gép hitelesítő adatainak konfigurálása**területen adja meg a mobilitási szolgáltatás leküldéses telepítéséhez [létrehozott](#prepare-an-account-for-mobility-service-installation) hitelesítő adatokat, amikor engedélyezi a replikációt a virtuális gépek számára.  
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
12. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.


A replikációs berendezés regisztrálása után Azure Migrate Server Assessment a megadott beállítások használatával csatlakozik a VMware-kiszolgálókhoz, és felfedi a virtuális gépeket. Megtekintheti a felderített virtuális gépeket a > **felderített elemek** **kezelése** területen a **másik** lapon.


## <a name="replicate-vms"></a>Virtuális gépek replikálása

Most válassza a virtuális gépek lehetőséget az áttelepítéshez.

> [!NOTE]
> Egyszerre legfeljebb 10 gépet replikálhat. Ha többre van szüksége, replikálja őket egyszerre 10 kötegben.

1. A Azure Migrate projekt > **kiszolgálók**, **Azure Migrate: kiszolgáló áttelepítése**elemre, majd kattintson a **replikálás**gombra.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. A **Replikálás**, > **Forrásbeállítások** > **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere szolgáltatással** lehetőséget.
3. A helyszíni **készülék**területen válassza ki a beállított Azure Migrate berendezés nevét.
4. A **vCenter-kiszolgáló**lapon adja meg a virtuális gépeket kezelő vCenter-kiszolgáló nevét, vagy azt a vSphere-kiszolgálót, amelyen a virtuális gépek futnak.
5. A **Process Server**lapon válassza ki a replikációs berendezés nevét.
6. A **vendég hitelesítő adatai**területen határozza meg a mobilitási szolgáltatás leküldéses telepítéséhez használni kívánt virtuális gép rendszergazdai fiókját. Ezután kattintson a **Tovább gombra: Virtual Machines**.

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. **Virtual Machines**válassza ki a replikálni kívánt gépeket.

    - Ha futtatta a virtuális gépek értékelését, alkalmazhatja az eredményekben szereplő, a virtuális gépek méretezésére és a lemeztípusra (prémium/standard) vonatkozó javaslatokat. Ehhez a **Importálja a migrálási beállításokat az Azure Migrate-értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelés beállításait, válassza a **Nem** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.

8. Tekintse át az áttelepíteni kívánt virtuális gépeket. Ezután kattintson a **Tovább: cél beállítások**elemre.
9. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
10. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.
11. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Next** (Tovább) gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Next** (Tovább) gombra.

12. A **Számítás** területen ellenőrizze a virtuális gép nevét, méretét, az operációs rendszer lemeztípusát és a rendelkezésreállási csoportot. A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - Virtuálisgép- **méret**: Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő lista az ajánlott méretet fogja tartalmazni. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **Operációsrendszer-lemez**: a virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási csoport**: Ha a virtuális gépnek az áttelepítés után Azure-beli rendelkezésre állási csoportba kell tartoznia, adja meg a készletet. A csoportnak a migrálás során megadott cél-erőforráscsoportban kell lennie.

13. A **lemezek**területen adja meg, hogy a virtuálisgép-lemezeket replikálni kell-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium szintű felügyelt lemez) az Azure-ban. Ezután kattintson a **Next** (Tovább) gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

14. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálási beállításokat bármikor frissítheti a replikálás megkezdése előtt, **kezelheti** > **replikáló gépeket**. A replikáció kezdete után a beállítások már nem módosíthatók.




## <a name="track-and-monitor"></a>Nyomon követés és figyelés

- Ha rákattint a **replikálás** indítása a replikálási feladatokhoz lehetőségre. 
- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.


A feladatok állapotát a portál értesítéseiben követheti nyomon.

![Feladatok nyomon követése](./media/tutorial-migrate-vmware-agent/jobs.png)

A replikálási állapot figyeléséhez kattintson a **kiszolgálók replikálásához** **Azure Migrate: kiszolgáló áttelepítése**elemre.
![a replikáció figyelése](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el ezt az egyes gépeken.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy ez hatással lenne a helyszíni gépekre, amelyek továbbra is működőképesek maradnak. 
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem üzemi VNet való áttelepítéssel).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Végezzen el egy teszt-áttelepítést a következőképpen:


1. Az **áttelepítési célok** > **kiszolgálók** > **Azure Migrate: kiszolgáló áttelepítése**területen kattintson az **áttelepített kiszolgálók tesztelése**elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a helyszíni gépeket.

1. A Azure Migrate projekt > **kiszolgálók** > **Azure Migrate: kiszolgáló áttelepítése**területen kattintson a **kiszolgálók replikálása**elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. A **Migrálás** > **Virtuális gépek leállítása és adatvesztés nélküli tervezett migrálás végrehajtása** területen válassza az **Igen** > **OK** lehetőséget.
    - Alapértelmezés szerint az Azure Migrate leállítja a helyszíni virtuális gépet, majd igény szerint replikációt végez a legutóbbi replikáció óta a virtuális gépen történt módosítások szinkronizálása érdekében. Így elkerülhető az adatvesztés.
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>A migrálás befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > az **áttelepítés leállítása**elemre. Ez leállítja a helyszíni gép replikálását, és megtisztítja a virtuális gép replikációs állapotával kapcsolatos információkat.
2. Telepítse az Azure-beli VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -ügynököt az áttelepített gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- Helyszíni követelmények
    - Helyezze át az alkalmazás-forgalmat az áttelepített Azure virtuálisgép-példányon futó alkalmazásra.
    - Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
    - Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
    - Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével.
- Azure-beli virtuális gép beállításainak csípése az áttelepítés után:
    - Az [Azure virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) kezeli a virtuális gépek kommunikációját az Azure-hálóvezérlővel. Erre egyes Azure-szolgáltatások, például az Azure Backup, a Site Recovery és az Azure Security esetében van szükség. VMare-alapú virtuális gépek ügynök szerinti áttelepítéssel történő áttelepítésekor a mobilitási szolgáltatás telepítője telepíti az Azure VM Agent ügynököt a Windows rendszerű gépekre. Linux rendszerű virtuális gépeken azt javasoljuk, hogy telepítse az ügynököt az áttelepítés után.
    - A Migrálás után manuálisan távolítsa el a mobilitási szolgáltatást az Azure virtuális gépről.
    - Az áttelepítés után manuálisan távolítsa el a VMware-eszközöket.
- Az Azure-ban:
    - Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
    - Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
- Üzletmenet-folytonosság/vész-helyreállítás
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [Részletek](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [Részletek](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/security-overview) használatával.
    - Az [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/security-center/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.




 ## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a [felhőalapú migrációs utat](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.

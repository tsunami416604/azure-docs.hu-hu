---
title: VMware virtuális gépek áttelepítése ügynökalapú Azure Áttelepítési kiszolgáló áttelepítésével
description: Ismerje meg, hogyan futtathat ügynökalapú áttelepítést a VMware virtuális gépek az Azure Migrate használatával.
ms.topic: tutorial
ms.date: 03/09/2020
ms.custom: MVC
ms.openlocfilehash: 6855c3e81aece0358146608b6cf179fb923c54c8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535332"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware virtuális gépek áttelepítése az Azure-ba (ügynökalapú)

Ez a cikk bemutatja, hogyan telepítheti át a helyszíni VMware virtuális gépeket az Azure-ba, ügynökalapú áttelepítés használatával az Azure Áttelepítési kiszolgáló áttelepítése eszközzel.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Állítsa be a forráskörnyezetet, és telepítse n: Azure Migrate replikációs berendezés ügynökalapú áttelepítés.
> * Az áttelepítés célkörnyezetének beállítása.
> * Állítson be replikációs szabályzatot.
> * Engedélyezze a replikációt.
> * Futtasson egy tesztáttelepítést, hogy minden a várt módon működjön.
> * Futtasson teljes áttelepítést az Azure-ba.

> [!NOTE]
> Az oktatóanyagok a forgatókönyv legegyszerűbb telepítési útvonalát mutatják be, így gyorsan beállíthat egy proof-of-concept-et. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Részletes utasításokért tekintse át a VMware-értékelés és áttelepítés útmutatóját.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

Azt javasoljuk, hogy próbálja ki a VMware VM-felmérést az Azure Áttelepítési kiszolgáló értékelés, mielőtt virtuális gépek áttelepítése az Azure-ba. Az értékelés beállítása a következőképpen:

1. Kövesse az oktatóanyagot az Azure és a [VMware](tutorial-prepare-vmware.md) felmérésre való előkészítéséhez.
2. Ezután [kövesse ezt az oktatóanyagot](tutorial-assess-vmware.md) egy Azure Migrate-berendezés beállításához az értékeléshez, és fedezze fel és értékelje a virtuális gépeket.


Bár azt javasoljuk, hogy próbálja ki egy értékelést, nem kell futtatni a felmérést a virtuális gépek áttelepítése előtt.

## <a name="migration-methods"></a>Áttelepítési módszerek

A VMware virtuális gépek az Azure-ba az Azure-áttelepítési kiszolgáló áttelepítése eszközzel. Ez az eszköz néhány lehetőséget kínál a VMware VM-áttelepítéshez:

- Ügynök nélküli replikáció. Telepítse a virtuális gépeket anélkül, hogy bármit is telepítenie kellene rájuk.
- Ügynökalapú áttelepítés. vagy replikáció. Telepítsen egy ügynököt (a Mobilitási szolgáltatások ügynökét) a virtuális gépreplikációhoz.

Annak eldöntéséhez, hogy ügynök nélküli vagy ügynökalapú áttelepítést szeretne-e használni, tekintse át az alábbi cikkeket:

- További információ a VMware áttelepítési [beállításairól.](server-migrate-overview.md)
- [Az áttelepítési módszerek összehasonlítása](server-migrate-overview.md#compare-migration-methods).
- [Kövesse ezt a cikket,](tutorial-migrate-vmware.md) hogy kipróbálja az ügynök nélküli áttelepítést.



## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

1. [Tekintse át](migrate-architecture.md) a VMware áttelepítési architektúráját.
2. Győződjön meg arról, hogy az Azure-fiók rendelkezik a Virtuálisgép közreműködő szerepkör, így rendelkezik engedélyekkel:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írjon egy Azure felügyelt lemezre. 

3. [Hozzon létre egy Azure-hálózatot.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) A helyszíni gépek replikálódnak az Azure által felügyelt lemezekre. Amikor átadja a feladatátaz Azure-ba a migrálás, az Azure virtuális gépek jönnek létre ezekből a felügyelt lemezek, és csatlakozott egy Azure-hálózat hoz létre, amikor az áttelepítés beállításakor.


## <a name="prepare-azure"></a>Az Azure előkészítése

Ha már futtatott egy értékelést az Azure Migrate Server Assessment használatával, kihagyhatja az ebben a szakaszban található utasításokat, mivel már elvégezte ezeket a lépéseket. 

Ha még nem futtatott felmérést, be kell állítania az Azure-engedélyeket, mielőtt áttelepítheti az Azure Áttelepítési kiszolgáló áttelepítésével.

- **Projekt létrehozása:** Az Azure-fióknak engedélyekre van szüksége egy Azure Migrate projekt létrehozásához. 
- **Regisztrálja az Azure Migrate replikációs berendezés:** A replikációs berendezés létrehoz és regisztrál egy Azure Active Directory alkalmazást az Azure-fiókban. Delegálja az ehhez szükséges engedélyeket.
- **Key Vault létrehozása:** A VMware virtuális gépek áttelepítése az Azure Migrate Server Migration használatával, az Azure Migrate létrehoz egy key vault az erőforráscsoportban, az előfizetés replikációs tárfiók hozzáférési kulcsainak kezeléséhez. A tároló létrehozásához szerepkör-hozzárendelési engedélyeket kell létrehozni a szerepkör-hozzárendelési engedélyeket az erőforráscsoport, amelyben az Azure Migrate projekt található. 


### <a name="assign-permissions-to-create-project"></a>Engedélyek hozzárendelése projekt létrehozásához

1. Az Azure Portalon nyissa meg az előfizetést, és válassza **a hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy tulajdonosi engedélyekkel kell **rendelkeznie.**
    - Ha most hozott létre egy ingyenes Azure-fiókot, ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együttműködve rendelje hozzá a szerepkört.

### <a name="assign-permissions-to-register-the-replication-appliance"></a>Engedélyek hozzárendelése a replikációs berendezés regisztrálásához

Ügynökalapú áttelepítés esetén delegálhatja az Azure Migrate Server Migration engedélyeit egy Azure AD-alkalmazás létrehozásához és regisztrálásához a fiókjában. Az engedélyeket az alábbi módszerek egyikével rendelheti hozzá:

- A bérlő/globális rendszergazda engedélyeket adhat a bérlői felhasználóknak az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- A bérlő/globális rendszergazda hozzárendelheti az alkalmazásfejlesztői szerepkört (amely rendelkezik az engedélyekkel) a fiókhoz.

Érdemes megjegyezni, hogy:

- Az alkalmazások a fent leírtakon kívül nem rendelkeznek más hozzáférési engedélyekkel az előfizetéshez.
- Csak akkor van szüksége ezekre az engedélyekre, ha új replikációs berendezést regisztrál. A replikációs eszköz beállítása után eltávolíthatja az engedélyeket. 


#### <a name="grant-account-permissions"></a>Fiókengedélyek megadása

A bérlő/globális rendszergazda az alábbiak szerint adhat engedélyeket

1. Az Azure AD-ben a bérlőnek/globális rendszergazdának az **Azure Active Directory** > **felhasználói** > **nak felhasználói beállításaira**kell navigálnia.
2. A rendszergazdának az **alkalmazásregisztrációkat** **Igen**beállításra kell állítania.

    ![Az Azure AD engedélyei](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Ez az alapértelmezett beállítás nem érzékeny. [További információ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Alkalmazásfejlesztői szerepkör hozzárendelése 

A bérlő/globális rendszergazda hozzárendelheti az Alkalmazásfejlesztő szerepkört egy fiókhoz. [További információ](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="assign-permissions-to-create-key-vault"></a>Engedélyek hozzárendelése a Kulcstár létrehozásához

Szerepkör-hozzárendelési engedélyek hozzárendelése azon erőforráscsoporthoz, amelyben az Azure Migrate projekt található, az alábbiak szerint:

1. Az Azure Portal erőforráscsoportjában válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.**
2. A **Hozzáférés ellenőrzése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez. **Tulajdonosi** (vagy **közreműködői** és **felhasználói hozzáférés-rendszergazdai)** engedélyekre van szüksége.
3. Ha nem rendelkezik a szükséges engedélyekkel, kérje őket az erőforráscsoport tulajdonosától. 


## <a name="prepare-on-premises-vmware"></a>Helyszíni VMware előkészítése

### <a name="prepare-an-account-for-automatic-discovery"></a>Fiók előkészítése automatikus felderítéshez

Az Azure Áttelepítési Kiszolgáló áttelepítése hozzáférést igényel a VMware-kiszolgálókhoz a következőkhöz:

- A virtuális gépek automatikus felderítése. Szükség van legalább egy csak olvasási jogokat biztosító fiókra.
- Replikáció, feladatátvétel és feladat-visszavétel vezénylése. Szükség van egy fiókra, amely képes műveleteket futtatni, mint például lemezek létrehozása és eltávolítása, valamint virtuális gépek működtetése.

Hozza létre a fiókot az alábbiak szerint:

1. Dedikált fiók használatához hozzon létre egy szerepkört a vCenter-szinten. Adjon meg egy nevet, például: **Azure_Site_Recovery**.
2. Rendelje hozzá a szerepkörhöz az engedélyeket az alábbi táblázatban összefoglaltaknak megfelelően.
3. Hozzon létre egy felhasználót a vCenter-kiszolgálón vagy vSphere-gazdagépen. Rendelje hozzá a szerepkört a felhasználóhoz.

#### <a name="vmware-account-permissions"></a>A VMware-fiók engedélyei

**Tevékenység** | **Szerepkör/Engedélyek** | **Részletek**
--- | --- | ---
**Virtuális gépek felderítése** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználó<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **Nincs hozzáférési** szerepkört a **Propagálás gyermekobjektumhoz,** a gyermekobjektumokhoz (vSphere-állomásokhoz, adattárakhoz, virtuális gépekhez és hálózatokhoz).
**Teljes replikáció, feladatátvétel, feladat-visszavétel** |  Hozzon létre egy szerepkört (Azure_Site_Recovery) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy -csoporthoz<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = Azure_Site_Recovery<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **Nincs hozzáférési** szerepkört a **Propagálás gyermekobjektumhoz** a gyermekobjektumokhoz (vSphere-állomások, adattárak, virtuális gépek, nd hálózatok).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt gépeken.

- Az Azure Migrate replikációs berendezés leküldéses telepítést tehet lehetővé, ha engedélyezi a replikációt egy gépszámára, vagy manuálisan telepítheti, vagy telepítőeszközök használatával.
- Ebben az oktatóanyagban leküldéses telepítéssel telepítjük a mobilitási szolgáltatást.
- A leküldéses telepítéshez elő kell készítenie egy fiókot, amely az Azure Áttelepítési kiszolgáló áttelepítése a virtuális gép eléréséhez használható. Ez a fiók csak a leküldéses telepítéshez használatos, ha nem telepíti manuálisan a Mobilitás szolgáltatást.

Készítse elő a fiókot az alábbiak szerint:

1. Készítsen elő egy tartományi vagy helyi fiókot, amely rendelkezik a virtuális gépre való telepítéshez szükséges engedélyekkel.
2. Windows virtuális gépek esetén, ha nem tartományi fiókot használ, tiltsa le a távoli felhasználói hozzáférés vezérlését a helyi számítógépen a **LocalAccountTokenSzűrőPolicy**duplaszó bejegyzés hozzáadásával, amelynek értéke a rendszerleíró adatbázisban van, **a HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System csoportban.**
3. Linux os virtuális gépek esetén készítsen elő egy gyökérfiókot a forrás Linux-kiszolgálón.


### <a name="check-vmware-requirements"></a>A VMware követelményeinek ellenőrzése

Győződjön meg arról, hogy a VMware-kiszolgálók és a virtuális gépek megfelelnek az Azure-ba való migrálás követelményeinek. 


> [!NOTE]
> Ügynök-alapú áttelepítés az Azure Áttelepítési kiszolgáló áttelepítése az Azure Site Recovery szolgáltatás szolgáltatásaialapján. Előfordulhat, hogy egyes követelmények a Site Recovery dokumentációjára hivatkoznak.

1. [Ellenőrizze](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) a VMware-kiszolgáló követelményeit.
2. [Ellenőrzés](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) Virtuális gép az áttelepítés támogatási követelményeit.
3. Ellenőrizze a virtuális gép beállításait. Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük [az Azure virtuális gép követelményeinek.](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)



## <a name="add-the-azure-migrate-server-migration-tool"></a>Az Azure Áttelepítési kiszolgáló eszközének hozzáadása

Ha nem követte az oktatóanyagot a VMware virtuális gépek értékeléséhez, állítson be egy Azure Migrate projektet, majd adja hozzá az Azure Áttelepítési kiszolgáló áttelepítése eszközt:

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.

    ![Az Azure Migrate beállítása](./media/tutorial-migrate-vmware-agent/azure-migrate-search.png)

3. Az **Áttekintés** területen kattintson a **Kiszolgálók értékelése és migrálása** lehetőségre.
4. A **Kiszolgálók felderítése, felmérése és áttelepítése csoportban**kattintson **a Kiszolgálók felmassza és áttelepítése**elemre.

    ! [Fedezze fel és értékelje szerverek] (./media/tutorial-migrate-vmware-agent/assess-migrate.png

1. A **Kiszolgálók felderítése, értékelése és migrálása** területen kattintson az **Eszközök hozzáadása** lehetőségre.
2. A **Projekt migrálása** területen válassza ki az Azure-előfizetését, majd hozzon létre egy erőforráscsoportot, ha még nem rendelkezik eggyel.
3. A **Projekt részletei** területen adja meg a projekt nevét és a földrajzi területet, ahol létre szeretné hozni a projektet, majd kattintson a **Következő** gombra. Tekintse át a támogatott földrajzi területeket [az állami](migrate-support-matrix.md#supported-geographies-public-cloud) és [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)számára.

    ![Azure Migrate projekt létrehozása](./media/tutorial-migrate-vmware-agent/migrate-project.png)


4. Az **Assessment eszköz kiválasztása**csoportban válassza az Értékelési eszköz hozzáadása lehetőség kiválasztása **Most** > antól**tovább**lehetőséget.
5. Az **Áttelepítés kiválasztása eszközben**válassza az **Azure Áttelepítés: Kiszolgálóáttelepítés** > **ezután**lehetőséget.
6. Az **Áttekintés + eszközök hozzáadása** területen ellenőrizze a beállításokat, majd kattintson az **Eszközök hozzáadása** lehetőségre.
7. Az eszköz hozzáadása után megjelenik az Azure Áttelepítés projekt > **kiszolgálók** > **áttelepítési eszközeiben.**

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

Az áttelepítés első lépése a replikációs berendezés beállítása. A replikációs berendezés egyetlen, magas rendelkezésre állású, helyszíni VMware virtuális gép, amely ezeket az összetevőket üzemelteti:

- **Konfigurációs kiszolgáló**: A konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt, és kezeli az adatreplikációt.
- **Folyamatkiszolgáló**: A folyamatkiszolgáló replikációs átjáróként működik. Replikációs adatokat fogad; gyorsítótárazásával, tömörítéssel és titkosítással optimalizálja, és elküldi egy azure-beli gyorsítótár-tárfiókba. A folyamatkiszolgáló telepíti a replikálni kívánt virtuális gépeken a Mobility Service-ügynököt is, és automatikusan elvégzi a helyszíni VMware virtuális gépek felderítését.


A replikációs berendezést többféleképpen is beállíthatja.

- Állítsa be a letöltött Open Virtualization Application (OVA) sablont. Importálja a sablont a VMware-be, és létrehozza a replikációs készülék virtuális gép. Ez az oktatóanyagban használt módszer.
- Állítsa be a forgatókönyvet.

### <a name="download-the-replication-appliance-template"></a>A replikációs berendezés sablonletöltése

Töltse le a sablont az alábbiak szerint:

1. Az Azure Áttelepítés projektben kattintson a **Kiszolgálók** elemre az **Áttelepítési célok csoportban.**
2. Az **Azure Migrate - Servers** > Azure Migrate: Server Migration (Azure**Áttelepítés: Kiszolgálóáttelepítés)** területén kattintson a **Discover**gombra.

    ![Virtuális gépek felderítése](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. A **Discover machines** > **Are your machines virtualizált?**, kattintson **igen, a VMWare vSphere hipervizor**.
4. A **Hogyan szeretné áttelepíteni?** lehetőséget válassza **az Ügynökalapú replikáció használata**lehetőséget.
5. A **Célrégióban**válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
6. Válassza **a Megerősítés lehetőséget, hogy az áttelepítés célrégiója régiónév.Select Confirm that the target region for migration is region-name**.
7. Kattintson **az Erőforrások létrehozása gombra.** Ez létrehoz egy Azure Site Recovery-tárolóak a háttérben.
    - A gombra kattintás után nem módosíthatja a projekt célrégióját.
    - Minden ezt követő áttelepítés erre a régióra van.

    ![Helyreállítási tár létrehozása](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. A **Szeretne telepíteni egy új replikációs berendezést?** **Install a replication appliance**
9. A **replikációs**berendezés letöltéséhez kattintson a Letöltés gombra. Ez letölti az OVF-sablont, amelyet a készüléket futtató új VMware virtuális gép létrehozásához használ.
    ![OVA letöltése](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Jegyezze fel az erőforráscsoport nevét és a Helyreállítási szolgáltatások tárolóját. Ezekre a berendezés üzembe helyezése során van szükség.


### <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

Az OVF-sablon letöltése után importálja azt a VMware programba, hogy létrehozza a replikációs alkalmazást egy Windows Server 2016 rendszerű VMware vm-en.

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere Client használatával.
2. A **Fájl** **menüOVF-sablon telepítése parancsára** kattintson az **OVF-sablon telepítése varázsló**elindításához. 
3. A **Forrás kiválasztása mezőbe**írja be a letöltött OVF helyét.
4. A **Véleményezés részletei területen**válassza a **Tovább**lehetőséget.
5. A **Név és mappa kiválasztása,** valamint a Konfiguráció kiválasztása **párbeszédpanelen**fogadja el az alapértelmezett beállításokat.
6. A **Tárolás kiválasztása** > : Válassza ki a**virtuális lemez formátumát**a legjobb teljesítmény érdekében válassza a Vastag **kiépítés eager zeroed**lehetőséget.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A Készen áll a **műveletbefejezéséhez,** ha a virtuális gép az alapértelmezett beállításokkal szeretné beállítani, válassza **a Bekapcsolás a telepítés** > **befejezése**után lehetőséget.

   > [!TIP]
   > Ha további hálózati adaptert szeretne hozzáadni, törölje **a bekapcsolás** > lehetőséget a telepítés**befejezése**után. Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

### <a name="kick-off-replication-appliance-setup"></a>A replikációs berendezés beállításának kezdőrúgása

1. A VMWare vSphere Client-konzolon kapcsolja be a virtuális gépet.
2. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be a virtuális géprendszergazdaként a rendszergazdai jelszó használatával.
4. Az első bejelentkezéskor a replikációs eszköz telepítőeszköze (Az Azure Site Recovery Configuration Tool) néhány másodpercen belül elindul.
5. Adjon meg egy nevet, amelyet a készülék Azure Migrate Server Migration alkalmazásával való regisztrálásához használni kell. Kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe.
7. Várja meg, amíg az eszköz befejezi az Azure AD-alkalmazás regisztrálását a készülék azonosításához. A készülék újraindul.
1. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.

### <a name="register-the-replication-appliance"></a>A replikációs berendezés regisztrálása

Fejezze be a replikációs berendezés beállítását és regisztrálását.

1. A Konfigurációs kiszolgáló kezelése varázslóban válassza a **Telepítési kapcsolat**lehetőséget.
2. Válassza ki a hálózati adaptert (alapértelmezés szerint csak egy hálózati adapter van), amelyet a replikációs eszköz a virtuális gép felderítéséhez használ, és végezze el a Mobility szolgáltatás leküldéses telepítését a forrásgépeken.
3. Válassza ki azt a hálózati adaptert, amelyet a replikációs berendezés az Azure-ral való kapcsolathoz használ. Ezután válassza a **Save** (Mentés) lehetőséget. Ez a beállítás nem módosítható a beállítás konfigurálása után.
4. Ha a készülék proxykiszolgáló mögött található, meg kell adnia a proxybeállításokat.
    - Adja meg a **http://ip-address**proxynevet a vagy **http://FQDN**a . A HTTPS-proxykiszolgálók nem támogatottak.
5. Amikor a rendszer kéri az előfizetést, az erőforráscsoportokat és a tároló adatait, adja meg a készüléksablon letöltésekor észlelt részleteket.
6. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
7. Válassza a **VMware PowerCLI telepítése** elemet. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután válassza a **Folytatás** elemet.
8. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
9. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
10. Adja meg a VMware-felderítéshez [létrehozott](#prepare-an-account-for-automatic-discovery) fiók hitelesítő adatait. Válassza **a Hozzáadás** > **folytatás a lehetőséget.**
11. A **Virtuálisgép hitelesítő adatainak konfigurálása**csoportban adja meg a Mobility szolgáltatás leküldéses telepítéséhez [létrehozott](#prepare-an-account-for-mobility-service-installation) hitelesítő adatokat, amikor engedélyezi a virtuális gépek replikációját.  
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
12. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.


A replikációs berendezés regisztrálása után az Azure Migrate Server Assessment a megadott beállításokkal csatlakozik a VMware-kiszolgálókhoz, és felderíti a virtuális gépeket. A felderített virtuális gépeket a**Felderített elemek** **kezelése** > lapon tekintheti meg, az **Egyéb** lapon.


## <a name="replicate-vms"></a>Virtuális gépek replikálása

Most válassza a virtuális gépek áttelepítéshez.

> [!NOTE]
> Legfeljebb 10 gépet replikálhat együtt. Ha több replikálnia kell, akkor azokat egyszerre 10-es kötegekben replikálja.

1. Az Azure Migrate projekt > **az** **Azure Áttelepítés: Kiszolgálóáttelepítés**, kattintson a **Replikálás gombra.**

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. A **Replikálás**, > **Forrásbeállítások** > **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere szolgáltatással** lehetőséget.
3. A **helyszíni berendezésben**válassza ki a beállított Azure Migrate-berendezés nevét.
4. A **vCenter-kiszolgálón**adja meg a virtuális gépeket kezelő vCenter-kiszolgáló nevét, vagy azt a vSphere-kiszolgálót, amelyen a virtuális gépek találhatók.
5. A **Process Server alkalmazásban**válassza ki a replikációs berendezés nevét.
6. A **Vendég hitelesítő adatok ban**adja meg a virtuális gép rendszergazdai fiókját, amely a mobilitási szolgáltatás leküldéses telepítéséhez lesz használva. Ezután kattintson a **Tovább: Virtuális gépek gombra.**

    ![Virtuális gépek replikálása](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. A **Virtuális gépek**alkalmazásban válassza ki a replikálni kívánt gépeket.

    - Ha futtatta a virtuális gépek értékelését, alkalmazhatja az eredményekben szereplő, a virtuális gépek méretezésére és a lemeztípusra (prémium/standard) vonatkozó javaslatokat. Ehhez a **Importálja a migrálási beállításokat az Azure Migrate-értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelés beállításait, válassza a **Nem** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.

8. Ellenőrizze az áttelepíteni kívánt virtuális gépeket. Ezután kattintson a **Tovább: Célbeállítások gombra.**
9. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
10. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.
11. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Kattintson a **Tovább** gombra.

12. A **Számítás** területen ellenőrizze a virtuális gép nevét, méretét, az operációs rendszer lemeztípusát és a rendelkezésreállási csoportot. A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms).

    - **Virtuális gép mérete:** Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő tartalmazza az ajánlott méretet. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **OPERÁCIÓS RENDSZER lemeze**: Adja meg a virtuális gép operációs rendszerének (rendszerindító) lemezét. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási készlet:** Ha a virtuális gép kell egy Azure rendelkezésre állási csoport áttelepítés után, adja meg a készlet. A csoportnak a migrálás során megadott cél-erőforráscsoportban kell lennie.

13. A **Lemezek mezőben**adja meg, hogy a virtuális gép lemezek et kell replikálni az Azure-ba, és válassza ki a lemez típusát (standard SSD/HDD vagy prémium szintű felügyelt lemezek) az Azure-ban. Kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

14. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikáció beállításait a replikáció megkezdése előtt bármikor frissítheti,**replikálógépek** **kezelése** > . A replikáció kezdete után a beállítások már nem módosíthatók.




## <a name="track-and-monitor"></a>Nyomon követése és monitorozása

- Amikor a **Replikáció replikálása feladat replikálása** parancsra kattint. 
- A replikáció indítása sikeres befejezése után a gépek megkezdik az Azure-ba történő kezdeti replikációt.
- A kezdeti replikáció befejezése után megkezdődik a különbözeti replikáció. A helyszíni lemezek növekményes módosításait rendszeres időközönként replikálja a replikalemezek az Azure-ban.


A feladat állapotát a portál értesítései ben követheti nyomon.

![Feladat nyomon követése](./media/tutorial-migrate-vmware-agent/jobs.png)

A replikáció állapotát az **Azure Áttelepítés: Kiszolgálóáttelepítés** **szolgáltatás replikálási kiszolgálóira** kattintva figyelheti.
![Replikáció monitorozása](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdésekor futtathat egy tesztáttelepítést a virtuális gépekhez, mielőtt teljes áttelepítést futtatna az Azure-ba. Javasoljuk, hogy ezt minden géphez legalább egyszer tegye meg, mielőtt áttelepítene.

- Tesztáttelepítési ellenőrzések futtatása, amelyek áttelepítése a várt módon fog működni, anélkül, hogy befolyásolnák a helyszíni gépek, amelyek továbbra is működőképes, és továbbra is replikálása. 
- A tesztáttelepítés szimulálja az áttelepítést egy Azure virtuális gép replikált adatok használatával történő létrehozásával (általában nem éles virtuális hálózatra való áttelepítés az Azure-előfizetésben).
- Használhatja a replikált teszt Azure virtuális gép az áttelepítés érvényesítéséhez, az alkalmazástesztelésének végrehajtásához, és a problémák megoldása a teljes áttelepítés előtt.

Végezze el a tesztáttelepítést az alábbiak szerint:


1. Az **áttelepítési célok** > **kiszolgálóinak** > **Azure Migrate: Server Migration**területén kattintson az **Áttelepített kiszolgálók tesztelése**elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gépek áttelepítése

Miután meggyőződött arról, hogy a tesztáttelepítés a várt módon működik, áttelepítheti a helyszíni gépeket.

1. Az Azure Migrate projektben > **A Kiszolgálók** > **Azure Áttelepítés: Kiszolgáló áttelepítése**, kattintson **a kiszolgálók replikálása**elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **Áttelepítés** > leállítása a virtuális gépek leállítása**és a tervezett áttelepítés adatvesztés nélküli végrehajtása**csoportban válassza az **Igen** > **OK**lehetőséget.
    - Alapértelmezés szerint az Azure Migrate leállítja a helyszíni virtuális gép minimális adatvesztés biztosítása érdekében. 
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gép > **az áttelepítés leállítása**elemre. Ez a következőket teszi:
    - Leállítja a helyszíni gép replikációját.
    - Eltávolítja a gépet a **replikáló kiszolgálók** száma az Azure Áttelepítés: Kiszolgáló áttelepítése.
    - Törli a virtuális gép replikációs állapotadatait.
2. Telepítse az Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) ügynök az áttelepített gépeken.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány forgalmának csökkentése.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni gyakorlati tanácsok

- Helyszíni követelmények
    - Helyezze át az alkalmazás-forgalmat az áttelepített Azure virtuálisgép-példányon futó alkalmazásra.
    - Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
    - Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
    - Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével.
- Az Azure virtuális gép beállításainak módosítása az áttelepítés után:
    - Az [Azure virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) kezeli a virtuális gépek kommunikációját az Azure-hálóvezérlővel. Erre egyes Azure-szolgáltatások, például az Azure Backup, a Site Recovery és az Azure Security esetében van szükség. Virtuális gépvirtuális gép ügynökalapú áttelepítéssel történő áttelepítésekor a Mobility Service telepítője telepíti az Azure Virtuálisgép-ügynököt Windows-gépekre. Linux os virtuális gépeken azt javasoljuk, hogy az áttelepítés után telepítse az ügynököt.
    - Manuálisan távolítsa el a mobilitási szolgáltatást az Azure virtuális gépről az áttelepítés után.
    - Az áttelepítés után manuálisan távolítsa el a VMware-eszközöket.
- Az Azure-ban:
    - Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
    - Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
- Üzletmenet folytonossága/vészhelyreállítás
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

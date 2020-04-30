---
title: Fizikai kiszolgálók előkészítése az értékeléshez és a Migrálás Azure Migrate
description: Ismerje meg, hogyan készítheti elő a fizikai kiszolgálók értékelését és áttelepítését Azure Migrate használatával.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 539e25f8b6cc92674fef567de6e6de16d0a9394a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535281"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Felkészülés a fizikai kiszolgálók Azure-ba történő értékelésére és áttelepítésére

Ez a cikk azt ismerteti, hogyan lehet felkészülni a helyszíni fizikai kiszolgálók értékelésére [Azure Migrate](migrate-services-overview.md)használatával.

[Azure Migrate](migrate-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 

Ez az oktatóanyag az első egy olyan sorozatban, amely bemutatja, hogyan kell felmérni a fizikai kiszolgálókat a Azure Migrateokkal. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő az Azure-t. Állítsa be az Azure-fiókra és-erőforrásokra vonatkozó engedélyeket a Azure Migrate való együttműködéshez.
> * A helyszíni fizikai kiszolgálók előkészítése a kiszolgálók értékeléséhez.


> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a fizikai kiszolgálók értékelésének útmutatója című témakört.


Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .


## <a name="prepare-azure-for-server-assessment"></a>Az Azure előkészítése a kiszolgálók értékeléséhez

Állítsa be az Azure-t, hogy működjön együtt Azure Migrateokkal. 

**Tevékenység** | **Részletek** 
--- | --- 
**Azure Migrate projekt létrehozása** | Az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie a projekt létrehozásához. 
**Erőforrás-szolgáltatók regisztrálása (csak értékelés)** | A Azure Migrate egy könnyű Azure Migrate berendezéssel észleli és értékeli a gépeket a Azure Migrate: Server Assessment használatával.<br/><br/> A készülék regisztrálása során az erőforrás-szolgáltatók regisztrálva vannak a berendezésben kiválasztott előfizetésben. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Az erőforrás-szolgáltatók regisztrálásához közreműködői vagy tulajdonosi szerepkörre van szüksége az előfizetésben.
**Azure AD-alkalmazás létrehozása (csak értékelés)** | A berendezés regisztrálása során Azure Migrate létrehoz egy Azure Active Directory (Azure AD) alkalmazást, amely a készüléken futó ügynökök és az Azure-on futó szolgáltatások közötti kommunikációhoz használható. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Engedélyekre van szüksége az Azure AD-alkalmazások (az Application Developer) szerepkörben való létrehozásához.


### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához 

Győződjön meg arról, hogy rendelkezik Azure Migrate-projekt létrehozásához szükséges engedélyekkel.

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.


### <a name="assign-permissions-to-register-the-appliance"></a>Engedélyek kiosztása a készülék regisztrálásához 

Az Azure AD-alkalmazás létrehozásához az alábbi módszerek egyikének használatával rendelhet engedélyeket Azure Migrate számára:

- A bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói számára az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- A bérlői/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik engedélyekkel) a fiókhoz.

> [!NOTE]
> - Az alkalmazás nem rendelkezik más hozzáférési engedélyekkel az előfizetéshez a fent leírtak kivételével.
> - Az új berendezések regisztrálásához csak ezekre az engedélyekre van szükség. Az engedélyeket a készülék beállítása után távolíthatja el.


#### <a name="grant-account-permissions"></a>Fiók engedélyeinek megadása

A bérlő/globális rendszergazda a következőképpen adhat meg engedélyeket:

1. Az Azure ad-ben a bérlői/globális rendszergazdának meg kell keresnie **Azure Active Directory** > **felhasználók** > **felhasználói beállításait**.
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** .

    ![Azure AD-engedélyek](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Ez egy alapértelmezett beállítás, amely nem érzékeny. [További információ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása

A bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét egy fiókhoz. [További információ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-azure-for-physical-server-migration"></a>Az Azure előkészítése a fizikai kiszolgálók áttelepítésére

Készítse elő az Azure-t a fizikai kiszolgálók áttelepítéséhez a kiszolgáló áttelepítése révén.

**Tevékenység** | **Részletek**
--- | ---
**Azure Migrate projekt létrehozása** | Az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie a projekt létrehozásához.
**Azure-fiók engedélyeinek ellenőrzése** | Az Azure-fióknak rendelkeznie kell a virtuális gép létrehozásához szükséges engedélyekkel, és írnia kell egy Azure-beli felügyelt lemezre.
**Azure-hálózat létrehozása** | Hozzon létre egy hálózatot az Azure-ban.


### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.


### <a name="assign-azure-account-permissions"></a>Azure-fiók engedélyeinek kiosztása

Rendelje hozzá a virtuális gépi közreműködő szerepkört az Azure-fiókhoz. Ez a következő engedélyekkel rendelkezik:

    - Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
    - Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
    - Írás egy Azure-beli felügyelt lemezre. 

### <a name="create-an-azure-network"></a>Azure-hálózat létrehozása

Azure-beli virtuális hálózat (VNet) [beállítása](../virtual-network/manage-virtual-network.md#create-a-virtual-network) . Az Azure-ba történő replikáláskor Azure-beli virtuális gépek jönnek létre és csatlakozva lesznek az áttelepítés beállításakor megadott Azure-VNet.


## <a name="prepare-for-physical-server-assessment"></a>Felkészülés a fizikai kiszolgáló értékelésére

A fizikai kiszolgáló értékelésének előkészítéséhez ellenőriznie kell a fizikai kiszolgáló beállításait, és ellenőriznie kell a berendezések központi telepítésének beállításait:

### <a name="verify-physical-server-settings"></a>Fizikai kiszolgáló beállításainak ellenőrzése

1. Ellenőrizze a [fizikai kiszolgálóra vonatkozó követelményeket](migrate-support-matrix-physical.md#physical-server-requirements) a kiszolgálók értékeléséhez.
2. Győződjön meg arról, hogy a [szükséges portok](migrate-support-matrix-physical.md#port-access) meg vannak nyitva a fizikai kiszolgálókon.


### <a name="verify-appliance-settings"></a>Berendezés beállításainak ellenőrzése

A Azure Migrate berendezés beállítása és az értékelés megkezdése előtt a következő oktatóanyagban készítse elő a berendezés üzembe helyezését.

1. [Ellenőrizze](migrate-appliance.md#appliance---physical) a fizikai kiszolgálók berendezésére vonatkozó követelményeket.
2. Tekintse át azokat az Azure URL-címeket, amelyeket a készüléknek a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell elérnie.
3. [Ellenőrizze](migrate-appliance.md#collected-data---vmware) , hogy a berendezés a felderítés és az értékelés során fog-e összegyűjteni.
4. [Megjegyzés:](migrate-support-matrix-physical.md#port-access) a port hozzáférési követelményeinek fizikai kiszolgáló értékelése.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Fiók beállítása a fizikai kiszolgáló felderítéséhez

Azure Migrate a helyszíni kiszolgálók felderítéséhez szükséges engedélyekkel.

- **Windows:** Állítson be egy helyi felhasználói fiókot az összes olyan Windows-kiszolgálón, amelyet fel szeretne venni a felderítésbe. A felhasználói fiókot fel kell venni a következő csoportokba:-távfelügyeleti felhasználók – Teljesítményfigyelő felhasználók – Teljesítménynapló felhasználói
- **Linux:** Szüksége lesz egy rendszergazdai fiókra a felderíteni kívánt Linux-kiszolgálókon.

## <a name="prepare-for-physical-server-migration"></a>Felkészülés a fizikai kiszolgálók áttelepítésére

Tekintse át a fizikai kiszolgálók áttelepítésére vonatkozó követelményeket.

> [!NOTE]
> Fizikai gépek áttelepítésekor Azure Migrate: a kiszolgáló áttelepítése ugyanazt a replikációs architektúrát használja, mint az ügynök-alapú vész-helyreállítás a Azure Site Recovery szolgáltatásban, és egyes összetevők ugyanazt a kódot használják. Előfordulhat, hogy egyes tartalmak Site Recovery dokumentációra hivatkoznak.

- [Tekintse át](migrate-support-matrix-physical-migration.md#physical-server-requirements) a fizikai kiszolgáló áttelepítésre vonatkozó követelményeit.
- Azure Migrate: a kiszolgáló áttelepítése replikációs kiszolgálót használ a fizikai kiszolgálók áttelepítéséhez:
    - [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a replikációs berendezés telepítési követelményeit, valamint a MySQL telepítésének [lehetőségeit](migrate-replication-appliance.md#mysql-installation) a készüléken.
    - Tekintse át a replikációs berendezés számára a nyilvános és a kormányzati felhők eléréséhez szükséges [Azure URL-címeket](migrate-appliance.md#url-access) .
    - Tekintse át a következőt: [port] (Migrálás-replikáció-berendezés. MD # port-Access) a replikációs berendezés hozzáférési követelményei.




## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure-fiók engedélyeinek beállítása.
> * Előkészített fizikai kiszolgálók az értékeléshez.

Folytassa a következő oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, és felméri a fizikai kiszolgálókat az Azure-ba való áttelepítésre

> [!div class="nextstepaction"]
> [Fizikai kiszolgálók felmérése](./tutorial-assess-physical.md)

---
title: Fizikai kiszolgálók előkészítése az azure-áttelepítéssel az értékelésre/áttelepítésre
description: Ismerje meg, hogyan készülhet fel a fizikai kiszolgálók értékelésére/áttelepítésére az Azure Migrate segítségével.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 539e25f8b6cc92674fef567de6e6de16d0a9394a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535281"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Felkészülés a fizikai kiszolgálók Azure-ba való értékelésére és áttelepítésére

Ez a cikk bemutatja, hogyan készülhet fel a helyszíni fizikai kiszolgálók értékelésére az [Azure Migrate](migrate-services-overview.md)segítségével.

[Az Azure Migrate](migrate-overview.md) olyan eszközök központi eszköze, amelyek segítségével felderítheti, felmérheti és áttelepítheti az alkalmazásokat, az infrastruktúrát és a számítási feladatokat a Microsoft Azure-ba. A központ tartalmazza az Azure Migrate eszközöket és a külső független szoftverszállítói (ISV) ajánlatokat. 

Ez az oktatóanyag az első egy sorozatban, amely bemutatja, hogyan értékelheti a fizikai kiszolgálók at Azure Migrate. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő az Azure-t. Állítsa be az Azure-fiókjához és az Azure Migrate szolgáltatással való együttműködésre szolgáló erőforrások engedélyeit.
> * Készítse elő a helyszíni fizikai kiszolgálókat a kiszolgáló értékeléséhez.


> [!NOTE]
> Az oktatóanyagok a forgatókönyv legegyszerűbb telepítési útvonalát mutatják be, így gyorsan beállíthat egy proof-of-concept-et. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Részletes utasításokért tekintse át az Útmutatók fizikai kiszolgálók értékelését.


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.


## <a name="prepare-azure-for-server-assessment"></a>Az Azure előkészítése a kiszolgálók értékelésére

Állítsa be az Azure-t az Azure Migrate szolgáltatással való együttműködésre. 

**Tevékenység** | **Részletek** 
--- | --- 
**Azure Migrate projekt létrehozása** | Az Azure-fiókjának közreműködői vagy tulajdonosi engedélyekre van szüksége a projekt létrehozásához. 
**Erőforrás-szolgáltatók regisztrálása (csak értékelés esetén)** | Az Azure Migrate egy könnyű Azure Migrate készüléket használ a gépek felderítéséhez és értékeléséhez az Azure Migrate:Server Assessment segítségével.<br/><br/> A készülék regisztrációja során az erőforrás-szolgáltatók regisztrálva vannak a készülékben kiválasztott előfizetéssel. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Az erőforrás-szolgáltatók regisztrálásához közreműködői vagy tulajdonosi szerepkörre van szüksége az előfizetéshez.
**Azure AD-alkalmazás létrehozása (csak értékelés esetén)** | A készülék regisztrálásakor az Azure Migrate létrehoz egy Azure Active Directory (Azure AD) alkalmazást, amely a készüléken futó ügynökök közötti kommunikációra szolgál az Azure-on futó megfelelő szolgáltatásokkal. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Engedélyeket kell létrehoznia az Azure AD-alkalmazások (az alkalmazásfejlesztői szerepkörben érhető el) létrehozásához.


### <a name="assign-permissions-to-create-project"></a>Engedélyek hozzárendelése projekt létrehozásához 

Ellenőrizze, hogy rendelkezik-e engedéllyel egy Azure Migrate projekt létrehozásához.

1. Az Azure Portalon nyissa meg az előfizetést, és válassza **a hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy tulajdonosi engedélyekkel kell **rendelkeznie.**
    - Ha most hozott létre egy ingyenes Azure-fiókot, ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együttműködve rendelje hozzá a szerepkört.


### <a name="assign-permissions-to-register-the-appliance"></a>Engedélyek hozzárendelése a készülék regisztrálásához 

Az Azure Migrate engedélyeket rendelhet az Azure AD alkalmazás létrehozásához a berendezés regisztrációja során, az alábbi módszerek egyikével:

- A bérlő/globális rendszergazda engedélyeket adhat a bérlői felhasználóknak az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- A bérlő/globális rendszergazda hozzárendelheti az alkalmazásfejlesztői szerepkört (amely rendelkezik az engedélyekkel) a fiókhoz.

> [!NOTE]
> - Az alkalmazás nem rendelkezik más hozzáférési engedélyekkel az előfizetéshez a fent leírtakon kívül.
> - Csak akkor van szüksége ezekre az engedélyekre, ha új készüléket regisztrál. A készülék beállítása után eltávolíthatja az engedélyeket.


#### <a name="grant-account-permissions"></a>Fiókengedélyek megadása

A bérlő/globális rendszergazda az alábbiak szerint adhat engedélyeket:

1. Az Azure AD-ben a bérlőnek/globális rendszergazdának az **Azure Active Directory** > **felhasználói** > **nak felhasználói beállításaira**kell navigálnia.
2. A rendszergazdának az **alkalmazásregisztrációkat** **Igen**beállításra kell állítania.

    ![Az Azure AD engedélyei](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Ez az alapértelmezett beállítás nem érzékeny. [További információ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Alkalmazásfejlesztői szerepkör hozzárendelése

A bérlő/globális rendszergazda hozzárendelheti az Alkalmazásfejlesztő szerepkört egy fiókhoz. [További információ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-azure-for-physical-server-migration"></a>Az Azure előkészítése a kiszolgálók fizikai áttelepítésére

Készítse elő az Azure-t a fizikai kiszolgálók áttelepítésére a Kiszolgáló áttelepítése használatával.

**Tevékenység** | **Részletek**
--- | ---
**Azure Migrate projekt létrehozása** | Az Azure-fiókjának közreműködői vagy tulajdonosi engedélyekre van szüksége a projekt létrehozásához.
**Az Azure-fiók engedélyeinek ellenőrzése** | Az Azure-fiók nak engedélyeket kell létrehoznia egy virtuális gép létrehozásához, és írniegy Azure felügyelt lemezre.
**Azure-hálózat létrehozása** | Hozzon létre egy hálózatot az Azure-ban.


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


## <a name="prepare-for-physical-server-assessment"></a>Felkészülés a fizikai kiszolgáló értékelésére

A fizikai kiszolgálók felmérésére való felkészüléshez ellenőriznie kell a kiszolgáló fizikai beállításait, és ellenőriznie kell a berendezés telepítésének beállításait:

### <a name="verify-physical-server-settings"></a>Fizikai kiszolgálóbeállítások ellenőrzése

1. Ellenőrizze a [kiszolgáló fizikai követelményeit](migrate-support-matrix-physical.md#physical-server-requirements) a kiszolgáló értékeléséhez.
2. Ellenőrizze, hogy a [szükséges portok](migrate-support-matrix-physical.md#port-access) meg vannak-e nyitva a fizikai kiszolgálókon.


### <a name="verify-appliance-settings"></a>A készülék beállításainak ellenőrzése

Az Azure Migrate készülék beállítása előtt, és a következő oktatóanyagban megkezdi a kiértékelést, készüljön fel a berendezés üzembe helyezésére.

1. [Ellenőrizze](migrate-appliance.md#appliance---physical) a fizikai kiszolgálók készülékre vonatkozó követelményeit.
2. Tekintse át az Azure URL-címeket, amelyeket a készüléknek a [nyilvános](migrate-appliance.md#public-cloud-urls) [és](migrate-appliance.md#government-cloud-urls) kormányzati felhőkben kell elérnie.
3. [Tekintse át,](migrate-appliance.md#collected-data---vmware) hogy a készülék a felfedezés és az értékelés során összegyűlik-e.
4. [Megjegyzés:](migrate-support-matrix-physical.md#port-access) a porthozzáférési követelmények fizikai kiszolgálóértékelése.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Fiók beállítása a kiszolgáló fizikai felderítéséhez

Az Azure Migrate-nek engedélyekre van szüksége a helyszíni kiszolgálók felderítéséhez.

- **Windows:** Állítson be egy helyi felhasználói fiókot a felderítésbe felvenni kívánt összes Windows-kiszolgálón. A felhasználói fiókot hozzá kell adni a következő csoportokhoz: - Távfelügyeleti felhasználók – Teljesítményfigyelő felhasználók – Teljesítménynapló-felhasználók
- **Linux:** Szüksége van egy gyökérfiókra a Linux-kiszolgálókon, amelyeket fel szeretne deríteni.

## <a name="prepare-for-physical-server-migration"></a>Felkészülés a kiszolgáló fizikai áttelepítésére

Tekintse át a fizikai kiszolgálók áttelepítésére vonatkozó követelményeket.

> [!NOTE]
> Fizikai gépek áttelepítésekor az Azure Migrate:Server Migration ugyanazt a replikációs architektúrát használja, mint az Azure Site Recovery szolgáltatás ügynökalapú vész-helyreállítási, és egyes összetevők ugyanazt a kódbázist használják. Előfordulhat, hogy egyes tartalmak a Site Recovery dokumentációjára hivatkoznak.

- [Tekintse át](migrate-support-matrix-physical-migration.md#physical-server-requirements) az áttelepítés fizikai kiszolgálói követelményeit.
- Azure Áttelepítés:A kiszolgálóáttelepítés replikációs kiszolgálót használ a kiszolgáló fizikai áttelepítéséhez:
    - [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a replikációs berendezés üzembe helyezési követelményeit, valamint a MySQL telepítésének [lehetőségeit](migrate-replication-appliance.md#mysql-installation) a készülékre.
    - Tekintse át a replikációs készülék nyilvános és kormányzati felhők eléréséhez szükséges [Azure-URL-címeket.](migrate-appliance.md#url-access)
    - Tekintse át a replikációs berendezés [port] (áttelepítés-replikáció-készülék.md#port-access) hozzáférési követelményeit.




## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Állítsa be az Azure-fiók engedélyeit.
> * Előkészített fizikai szerverek az értékeléshez.

Folytassa a következő oktatóanyaggal az Azure Migrate projekt létrehozásához és az Azure-ba való áttelepítés fizikai kiszolgálóinak felméréséhez

> [!div class="nextstepaction"]
> [Fizikai kiszolgálók felmérése](./tutorial-assess-physical.md)

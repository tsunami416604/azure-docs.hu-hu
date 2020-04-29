---
title: VMware virtuális gépek előkészítése értékeléshez/áttelepítéshez Azure Migrate
description: Ismerje meg, hogyan készítheti elő a VMware virtuális gépek értékelését és áttelepítését Azure Migrate használatával.
ms.topic: tutorial
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: a3f9716d6302c41f139d9a2a8b1f994b79afd199
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81677295"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>A VMware virtuális gépek előkészítése a felméréshez és az Azure-ba való migráláshoz

Ez a cikk segít felkészülni a helyszíni VMware virtuális gépek felmérésére és/vagy áttelepítésére az Azure-ban [Azure Migrate](migrate-services-overview.md)használatával.



Ez az oktatóanyag az első egy sorozatban, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő az Azure-t a Azure Migrate való együttműködéshez.
> * Készítse elő a VMware for VM értékelését a Azure Migrate: Server Assessment Tool eszközzel.
> * Készítse elő a VMware virtuális gépek áttelepítését a Azure Migrate: Server Migration Tool eszközzel. 

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ezek akkor hasznosak, ha megtudhatja, hogyan állíthatja be a központi telepítést, és hogyan hozhat létre gyors megvalósíthatósági koncepciót. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. 

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .


## <a name="prepare-azure"></a>Az Azure előkészítése

Ezeknek a feladatoknak az Azure-ban kell megadnia ezeket az engedélyeket a VMware virtuális gépek felmérése vagy áttelepítéséhez.

**Tevékenység** | **Részletek** 
--- | --- 
**Azure Migrate projekt létrehozása** | Az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie a projekt létrehozásához. 
**Erőforrás-szolgáltatók regisztrálása** | A Azure Migrate a VMware virtuális gépek felderítésére és értékelésére, valamint az Azure-ba való áttelepítésére szolgáló Azure Migrate berendezést használ a Azure Migrate: Server Assessment használatával.<br/><br/> A készülék regisztrálása során az erőforrás-szolgáltatók regisztrálva vannak a berendezésben kiválasztott előfizetésben. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Az erőforrás-szolgáltatók regisztrálásához közreműködői vagy tulajdonosi szerepkörre van szüksége az előfizetésben.
**Azure AD-alkalmazások létrehozása** | A készülék regisztrálása során a Azure Migrate Azure Active Directory (Azure AD) alkalmazásokat hoz létre. <br/><br/> – Az első alkalmazás a készüléken futó ügynökök és az Azure-on futó szolgáltatások közötti kommunikációhoz használatos.<br/><br/> – A második alkalmazás kizárólag az ügynök nélküli VMware VM-Migrálás felhasználói előfizetésében létrehozott kulcstartó elérésére szolgál. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Engedélyekre van szüksége az Azure AD-alkalmazások (az Application Developer) szerepkörben való létrehozásához.
**Kulcstartó létrehozása** | A VMware virtuális gépek ügynök nélküli áttelepítéssel való áttelepítéséhez Azure Migrate létrehoz egy Key Vault, amely a hozzáférési kulcsokat az előfizetésében lévő replikációs Storage-fiókhoz kezeli.<br/><br/> A tároló létrehozásához szerepkör-hozzárendelési engedélyekre van szükség ahhoz az erőforráscsoporthoz, amelyben az Azure Migrate-projekt található.




### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.

### <a name="assign-permissions-to-register-the-appliance"></a>Engedélyek kiosztása a készülék regisztrálásához

A készülék regisztrálásához engedélyeket kell rendelnie Azure Migrate az Azure AD-alkalmazások létrehozásához a készülék regisztrációja során. Az engedélyek az alábbi módszerek egyikével rendelhetők el:

- **Engedélyek megadása**: a bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói számára az Azure ad-alkalmazások létrehozásához és regisztrálásához.
- **Alkalmazás fejlesztői szerepkörének kiosztása**: a bérlői/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik engedélyekkel) a fiókhoz.

> [!NOTE]
> - Az alkalmazások nem rendelkeznek más hozzáférési engedélyekkel az előfizetéshez a fent leírtak kivételével.
> - Az új berendezések regisztrálásához csak ezekre az engedélyekre van szükség. Az engedélyeket a készülék beállítása után távolíthatja el.


#### <a name="grant-account-permissions"></a>Fiók engedélyeinek megadása

Ha azt szeretné, hogy a bérlő/globális rendszergazda engedélyeket adjon meg, tegye a következőket:

1. Az Azure ad-ben a bérlői/globális rendszergazdának meg kell keresnie **Azure Active Directory** > **felhasználók** > **felhasználói beállításait**.
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** . Ez egy alapértelmezett beállítás, amely nem érzékeny. [További információ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-engedélyek](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása

Azt is megteheti, hogy a bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkört egy fiókhoz. [További](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) információ a szerepkörök hozzárendeléséről.

### <a name="assign-permissions-to-create-a-key-vault"></a>Engedélyek kiosztása Key Vault létrehozásához

Ha engedélyezni szeretné, hogy a Azure Migrate Key Vault hozzon létre, rendeljen engedélyeket az alábbiak szerint:

1. A Azure Portal erőforráscsoporthoz válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.

    - A kiszolgáló-értékelés futtatásához a **közreműködői** engedélyek elégek.
    - Az ügynök nélküli kiszolgáló áttelepítésének futtatásához **tulajdonosi** (vagy **közreműködői** és **felhasználói hozzáférés-rendszergazdai**) engedélyekkel kell rendelkeznie.

3. Ha nem rendelkezik a szükséges engedélyekkel, kérje meg őket az erőforráscsoport tulajdonosától.



## <a name="prepare-for-vmware-vm-assessment"></a>Felkészülés a VMware virtuális gépek értékelésére

A VMware virtuális gépek értékelésének előkészítéséhez a következőket kell tennie:

- A **VMware-beállítások ellenőrzése**. Győződjön meg arról, hogy az áttelepíteni kívánt vCenter Server és virtuális gépek megfelelnek a követelményeknek.
- **Hozzon létre egy fiókot az értékeléshez**. Azure Migrate ezt a fiókot használja a vCenter Server eléréséhez, hogy felderítse a virtuális gépeket az értékeléshez.
- A **berendezésre vonatkozó követelmények ellenőrzése**. Az üzembe helyezés előtt ellenőrizze az Azure Migrate berendezés telepítési követelményeit.

### <a name="verify-vmware-settings"></a>VMware-beállítások ellenőrzése

1. [Ellenőrzési](migrate-support-matrix-vmware.md#vmware-requirements) A VMware-kiszolgálói követelmények az értékeléshez.
2. Győződjön meg [arról](migrate-support-matrix-vmware.md#port-access) , hogy a szükséges portok nyitva vannak vCenter Serveron.
3. VCenter Serveron ellenőrizze, hogy a fiókja rendelkezik-e engedéllyel egy virtuális gép létrehozásához PETESEJT-fájl használatával. A Azure Migrate berendezést VMware virtuális gépként helyezi üzembe a PETESEJT-fájl használatával.


### <a name="set-up-an-account-for-assessment"></a>Fiók beállítása az értékeléshez

Azure Migrate el kell érnie a vCenter Server, hogy felderítse a virtuális gépeket az értékeléshez és az ügynök nélküli áttelepítéshez.

- Ha ügynök nélküli módon szeretné felderíteni az alkalmazásokat, vagy a függőségeket szeretné megjeleníteni, hozzon létre egy vCenter Server fiókot írásvédett hozzáféréssel, valamint a **virtuális gépek** > **vendég műveleteihez**engedélyezett jogosultságokkal.

  ![vCenter Server fiók jogosultságai](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Ha nem tervezi az alkalmazások felderítését és az ügynök nélküli függőségek megjelenítését, állítson be írásvédett fiókot a vCenter Server számára.

### <a name="verify-appliance-settings-for-assessment"></a>A berendezés beállításainak ellenőrzése az értékeléshez

A Azure Migrate berendezés beállítása és az értékelés megkezdése előtt a következő oktatóanyagban készítse elő a berendezés üzembe helyezését.

1. [Ellenőrzés](migrate-appliance.md#appliance---vmware) Azure Migrate készülékre vonatkozó követelmények.
2. Tekintse át azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben.
3. [Tekintse át](migrate-appliance.md#collected-data---vmware) a készülék által a felderítés és az értékelés során gyűjtött adatokat.
4. [Jegyezze](migrate-support-matrix-vmware.md#port-access) fel a port hozzáférési követelményeit a készülékhez.




## <a name="prepare-for-agentless-vmware-migration"></a>Felkészülés az ügynök nélküli VMware-áttelepítésre

Tekintse át a VMware virtuális gépek [ügynök nélküli áttelepítésének](server-migrate-overview.md) követelményeit.

1. [Tekintse át](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) VMware-kiszolgálói követelmények.
2. [Tekintse át azokat az engedélyeket](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) , amelyekre Azure Migrate szüksége van a vCenter Server eléréséhez.
3. [Tekintse át](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) A VMware virtuális gépekre vonatkozó követelmények.
4. [Tekintse át](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) a Azure Migrate készülékre vonatkozó követelményeket.
5. Megjegyzés: a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkhez szükséges URL-hozzáférés.
6. Tekintse át a [port hozzáférési](migrate-support-matrix-vmware-migration.md#agentless-ports) követelményeit.

## <a name="prepare-for-agent-based-vmware-migration"></a>Felkészülés az ügynök alapú VMware-áttelepítésre

Tekintse át a VMware virtuális gépek [ügynök-alapú áttelepítésének](server-migrate-overview.md) követelményeit.

1. [Tekintse át](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) VMware-kiszolgálói követelmények.
2. [Az engedélyek áttekintése](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) A Azure Migratenak el kell érnie a vCenter Server.
2. [Tekintse át](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) A VMware virtuális gépekre vonatkozó követelmények, beleértve a mobilitási szolgáltatás telepítését minden olyan virtuális gépen, amelyet át szeretne telepíteni.
3. Az ügynök alapú áttelepítés replikációs készüléket használ:
    - [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a replikációs berendezés telepítési követelményeit.
    - [Tekintse át a](migrate-replication-appliance.md#mysql-installation) MySQL telepítésének lehetőségeit a készüléken.
    - Megjegyzés: a [nyilvános](migrate-replication-appliance.md#url-access) és a [kormányzati](migrate-replication-appliance.md#azure-government-url-access) felhőkhez szükséges URL-hozzáférés.
    - Tekintse át a replikációs berendezés [portra vonatkozó hozzáférési](migrate-replication-appliance.md#port-access) követelményeit.
    
## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure-engedélyek beállítása.
> * Előkészített VMware az értékeléshez és az áttelepítéshez.


Folytassa a második oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, és felméri a VMware virtuális gépeket az Azure-ba való Migrálás során.

> [!div class="nextstepaction"]
> [VMware virtuális gépek felmérése](./tutorial-assess-vmware.md)

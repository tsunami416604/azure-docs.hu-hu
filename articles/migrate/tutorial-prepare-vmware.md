---
title: VMware virtuális gépek előkészítése értékeléshez/áttelepítéshez Azure Migrate
description: Ismerje meg, hogyan készítheti elő a VMware virtuális gépek értékelését és áttelepítését Azure Migrate használatával.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: f00d5ba4841427098b0ab79ad1930e357008b6e0
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030795"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware virtuális gépek előkészítése az Azure-ba történő értékeléshez és áttelepítéshez

Ez a cikk segít felkészülni a helyszíni VMware virtuális gépek felmérésére és/vagy áttelepítésére az Azure-ban [Azure Migrate](migrate-services-overview.md)használatával.

[Azure Migrate](migrate-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.


Ez az oktatóanyag az első egy sorozatban, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket. Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Készítse elő az Azure-t a Azure Migrate való együttműködéshez.
> * Készítse elő a VMware for VM értékelését.
> * A VMware előkészítése a virtuális gépek áttelepítésére.

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Ezek akkor hasznosak, ha megtudhatja, hogyan állíthatja be a központi telepítést, és hogyan hozhat létre gyors megvalósíthatósági koncepciót. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a következő témakört: a VMware értékeléséhez és áttelepítéséhez szükséges útmutatók.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prepare-azure"></a>Az Azure előkészítése

Ezek az engedélyek szükségesek.

**Tevékenység** | **Engedélyek**
--- | ---
**Azure Migrate projekt létrehozása** | Az Azure-fióknak rendelkeznie kell a projekt létrehozásához szükséges engedélyekkel.
**A Azure Migrate berendezés regisztrálása** | A Azure Migrate egy egyszerűsített Azure Migrate berendezés használatával értékeli a VMware virtuális gépeket Azure Migrate Server Assessment használatával, valamint a VMware virtuális gépek [ügynök nélküli áttelepítését](server-migrate-overview.md) Azure Migrate kiszolgáló áttelepítésével. Ez a készülék felfedi a virtuális gépeket, és a VM-metaadatokat és teljesítményadatokat Azure Migrateba küldi.<br/><br/>A készülék regisztrálása során a következő erőforrás-szolgáltatók regisztrálva vannak a készülékben kiválasztott előfizetésben – Microsoft. OffAzure, Microsoft. Migrál és Microsoft. kulcstartó. Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre. Az erőforrás-szolgáltatók regisztrálásához közreműködői vagy tulajdonosi szerepkörre van szüksége az előfizetésben.<br/><br/> A bevezetés részeként Azure Migrate két Azure Active Directory (Azure AD) alkalmazást hoz létre:<br/> – Az első alkalmazás a készüléken futó ügynökök és az Azure-on futó szolgáltatások közötti kommunikációhoz (hitelesítéshez és engedélyezéshez) használatos. Az alkalmazásnak nincs jogosultsága ARM-hívások vagy RBAC elérésére bármely erőforráson.<br/> – A második alkalmazás kizárólag a felhasználó ügynök nélküli áttelepítésre vonatkozó előfizetésében létrehozott kulcstartó elérésére szolgál. A rendszer a RBAC hozzáférést biztosít a Azure Key Vault (az ügyfél bérlője alapján létrehozva), amikor a berendezésen keresztül kezdeményezi a felderítést.
**Key Vault létrehozása** | A VMware virtuális gépek Azure Migrate kiszolgáló áttelepítésével történő áttelepítéséhez Azure Migrate létrehoz egy Key Vault a hozzáférési kulcsok kezeléséhez az előfizetésében lévő replikációs Storage-fiókhoz. A tároló létrehozásához szerepkör-hozzárendelési engedélyekre van szükség ahhoz az erőforráscsoporthoz, amelyben az Azure Migrate-projekt található.






### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.

### <a name="assign-permissions-to-register-the-appliance"></a>Engedélyek kiosztása a készülék regisztrálásához

A készülék regisztrálásához engedélyeket kell rendelnie Azure Migrate az Azure AD-alkalmazások létrehozásához a készülék regisztrációja során. Az engedélyek az alábbi módszerek egyikével rendelhetők el:

- A bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói számára az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- A bérlői/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik engedélyekkel) a fiókhoz.

> [!NOTE]
> - Az alkalmazások nem rendelkeznek más hozzáférési engedélyekkel az előfizetéshez a fent leírtak kivételével.
> - Az új berendezések regisztrálásához csak ezekre az engedélyekre van szükség. Az engedélyeket a készülék beállítása után távolíthatja el.


#### <a name="grant-account-permissions"></a>Fiók engedélyeinek megadása

A bérlő/globális rendszergazda a következőképpen adhat meg engedélyeket.

1. Az Azure AD-ben a bérlői/globális rendszergazdának **Azure Active Directory** > **felhasználó** > **felhasználói beállításokat**kell megkeresnie.
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** . Ez egy alapértelmezett beállítás, amely nem érzékeny. [További információk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-engedélyek](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása

A bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét egy fiókhoz. [További információk](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

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
- **Hozzon létre egy értékelési fiókot**. Azure Migrate el kell érnie a vCenter Server, hogy felderítse a virtuális gépeket az értékeléshez.
- A **berendezésre vonatkozó követelmények ellenőrzése**. Ellenőrizze az értékeléshez használt Azure Migrate berendezés telepítési követelményeit.

### <a name="verify-vmware-settings"></a>VMware-beállítások ellenőrzése

1. [Ellenőrzési](migrate-support-matrix-vmware.md#vmware-requirements) A VMware-kiszolgálói követelmények az értékeléshez.
2. Győződjön meg [arról](migrate-support-matrix-vmware.md#port-access) , hogy a szükséges portok nyitva vannak vCenter Serveron.
3. VCenter Serveron ellenőrizze, hogy a fiókja rendelkezik-e engedéllyel egy virtuális gép létrehozásához PETESEJT-fájl használatával. A Azure Migrate berendezést VMware virtuális gépként helyezi üzembe a PETESEJT-fájl használatával.


### <a name="set-up-an-account-for-assessment"></a>Fiók beállítása az értékeléshez

Azure Migrate el kell érnie a vCenter Server, hogy felderítse a virtuális gépeket az értékeléshez és az ügynök nélküli áttelepítéshez.

- Ha ügynök nélküli módon szeretné felderíteni az alkalmazásokat, vagy a függőséget szeretné megjeleníteni, hozzon létre egy vCenter Server fiókot csak olvasási hozzáféréssel, valamint a **virtuális gépekhez** > **vendég műveletekhez**engedélyezett jogosultságokkal.

  ![vCenter Server fiók jogosultságai](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Ha nem tervezi az alkalmazások felderítését és az ügynök nélküli függőségek megjelenítését, állítson be írásvédett fiókot a vCenter Server számára.

### <a name="verify-appliance-settings-for-assessment"></a>A berendezés beállításainak ellenőrzése az értékeléshez

A Azure Migrate berendezés beállítása és az értékelés megkezdése előtt a következő oktatóanyagban készítse elő a berendezés üzembe helyezését.

1. [Ellenőrizze](migrate-appliance.md#appliance---vmware) , hogy a készüléken milyen követelmények vonatkoznak a VMWare virtuális gépek
2. [Tekintse át](migrate-appliance.md#url-access) azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie. Ha URL-alapú tűzfalat vagy proxyt használ, győződjön meg arról, hogy az lehetővé teszi a szükséges URL-címek elérését.
3. [Ellenőrizze](migrate-appliance.md#collected-data---vmware) , hogy a berendezés a felderítés és az értékelés során fog-e összegyűjteni.
4. [Jegyezze](migrate-support-matrix-vmware.md#port-access) fel a port hozzáférési követelményeit a készülékhez.




## <a name="prepare-for-agentless-vmware-migration"></a>Felkészülés az ügynök nélküli VMware-áttelepítésre

Tekintse át a VMware virtuális gépek ügynök nélküli áttelepítésének követelményeit.

1. [Tekintse át](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) A VMware Server követelményei, valamint az Azure Migrate számára szükséges [engedélyek](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) , amelyekkel az Azure Migrate Server áttelepítésével el kell érnie az ügynök nélküli áttelepítés vCenter Server.
2. [Tekintse át](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) azokat a VMWare virtuális gépekre vonatkozó követelményeket, amelyeket át szeretne telepíteni az Azure-ba az ügynök nélküli áttelepítés használatával.
4. [Tekintse át](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) az ügynök nélküli migrálás Azure Migrate berendezés használatára vonatkozó követelményeit.
5. Figyelje meg az ügynök nélküli áttelepítés számára szükséges [URL-címet](migrate-appliance.md#url-access) és a [porthoz való hozzáférést](migrate-support-matrix-vmware-migration.md#agentless-ports) .


## <a name="prepare-for-agent-based-vmware-migration"></a>Felkészülés az ügynök alapú VMware-áttelepítésre

Tekintse át a VMware virtuális gépek [ügynök-alapú áttelepítésének](server-migrate-overview.md) követelményeit.

1. [Tekintse át](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) A VMware-kiszolgáló követelményeinek, valamint a Azure Migrate kiszolgáló áttelepítését használó ügynök-alapú áttelepítés vCenter Server eléréséhez szükséges engedélyek Azure Migrate.
2. [Tekintse át](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) azokat a VMWare virtuális gépekre vonatkozó követelményeket, amelyeket az Azure-ba az ügynök-alapú áttelepítés használatával szeretne áttelepíteni, beleértve a mobilitási szolgáltatás telepítését minden áttelepíteni kívánt virtuális gépen.
3. Az ügynök alapú áttelepítések replikációs berendezést használnak:
    - [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a replikációs berendezés telepítési követelményeit, valamint a MySQL telepítésének [lehetőségeit](migrate-replication-appliance.md#mysql-installation) a készüléken.
    - Tekintse át a replikációs berendezés [URL-címét](migrate-replication-appliance.md#url-access) és a [port](migrate-replication-appliance.md#port-access) hozzáférési követelményeit.
    
## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban az alábbiakat végezte el:

> [!div class="checklist"]
> * Azure-engedélyek beállítása.
> * Előkészített VMware az értékeléshez és az áttelepítéshez.


Folytassa a második oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, és felméri a VMware virtuális gépeket az Azure-ba való Migrálás során.

> [!div class="nextstepaction"]
> [VMware virtuális gépek felmérése](./tutorial-assess-vmware.md)

---
title: VMware virtuális gépek előkészítése az Azure Migrate szolgáltatással az értékelésre/áttelepítésre
description: Ismerje meg, hogyan készülhet fel a VMware virtuális gépek értékelésére/áttelepítésére az Azure Migrate szolgáltatással.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 9f0729a3ddb2d8196a855557a6b8587940563984
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535264"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>A VMware virtuális gépek előkészítése a felméréshez és az Azure-ba való migráláshoz

Ez a cikk segít felkészülni a helyszíni VMware virtuális gépek értékelésére és/vagy áttelepítésére az Azure-ba az [Azure Áttelepítés](migrate-services-overview.md)használatával.



Ez az oktatóanyag az első olyan sorozat, amely bemutatja a VMware virtuális gépek értékelését és áttelepítését. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő az Azure-t az Azure Migrate szolgáltatással való együttműködésre.
> * Készítse elő a VMware-t a virtuális gép értékeléséhez az Azure Migrate:Server Assessment eszközzel.
> * Készítse elő a VMware-t a virtuális gépek áttelepítéséhez az Azure Áttelepítés:Kiszolgáló áttelepítése eszközzel. 

> [!NOTE]
> Oktatóanyagok megmutatja a legegyszerűbb telepítési útvonalat egy forgatókönyv. Ezek akkor hasznosak, ha megtanulja, hogyan kell beállítani egy központi telepítést, és egy gyors proof-of-concept. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.


## <a name="prepare-azure"></a>Az Azure előkészítése

Ezekre az engedélyekre az Azure-ban, a VMware virtuális gépek felmérése vagy áttelepítése előtt ezekre a feladatokra van szüksége.

**Tevékenység** | **Részletek** 
--- | --- 
**Azure Migrate projekt létrehozása** | Az Azure-fiókjának közreműködői vagy tulajdonosi engedélyekre van szüksége a projekt létrehozásához. 
**Erőforrás-szolgáltatók regisztrálása** | Az Azure Migrate egy könnyű Azure Migrate készüléket használ a VMware virtuális gépek felderítéséhez és értékeléséhez, valamint az Azure Migrate:Server Assessment használatával az Azure-ba való áttelepítéshez.<br/><br/> A készülék regisztrációja során az erőforrás-szolgáltatók regisztrálva vannak a készülékben kiválasztott előfizetéssel. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Az erőforrás-szolgáltatók regisztrálásához közreműködői vagy tulajdonosi szerepkörre van szüksége az előfizetéshez.
**Azure AD-alkalmazások létrehozása** | A készülék regisztrálásakor az Azure Migrate létrehozza az Azure Active Directory (Azure AD) alkalmazásokat. <br/><br/> - Az első alkalmazás a készüléken futó ügynökök és az Azure-on futó megfelelő szolgáltatások közötti kommunikációra szolgál.<br/><br/> - A második alkalmazás kizárólag a felhasználó ügynök nélküli VMware vm-áttelepítési előfizetésében létrehozott KeyVault eléréséhez használatos. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Engedélyeket kell létrehoznia az Azure AD-alkalmazások (az alkalmazásfejlesztői szerepkörben érhető el) létrehozásához.
**Kulcstartó létrehozása** | A VMware virtuális gépek ügynök nélküli áttelepítés használatával történő áttelepítéséhez az Azure Migrate létrehoz egy Key Vault-ot az előfizetés replikációs tárfiókjához való hozzáférési kulcsok kezeléséhez.<br/><br/> A tároló létrehozásához szerepkör-hozzárendelési engedélyeket kell létrehozni a szerepkör-hozzárendelési engedélyeket az erőforráscsoport, amelyben az Azure Migrate projekt található.




### <a name="assign-permissions-to-create-project"></a>Engedélyek hozzárendelése projekt létrehozásához

1. Az Azure Portalon nyissa meg az előfizetést, és válassza **a hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy tulajdonosi engedélyekkel kell **rendelkeznie.**
    - Ha most hozott létre egy ingyenes Azure-fiókot, ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együttműködve rendelje hozzá a szerepkört.

### <a name="assign-permissions-to-register-the-appliance"></a>Engedélyek hozzárendelése a készülék regisztrálásához

A készülék regisztrálásához engedélyeket kell hozzárendelnie az Azure Migrate számára az Azure AD-alkalmazások létrehozásához a készülék regisztrációja során. Az engedélyek az alábbi módszerek egyikével rendelhetők hozzá:

- **Engedélyek megadása:** A bérlő/globális rendszergazda engedélyeket adhat a bérlői felhasználóknak az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- **Alkalmazásfejlesztői szerepkör hozzárendelése:** A bérlői/globális rendszergazda hozzárendelheti az alkalmazásfejlesztői szerepkört (amely rendelkezik az engedélyekkel) a fiókhoz.

> [!NOTE]
> - Az alkalmazások a fent leírtakon kívül nem rendelkeznek más hozzáférési engedélyekkel az előfizetéshez.
> - Csak akkor van szüksége ezekre az engedélyekre, ha új készüléket regisztrál. A készülék beállítása után eltávolíthatja az engedélyeket.


#### <a name="grant-account-permissions"></a>Fiókengedélyek megadása

Ha azt szeretné, hogy a bérlő/globális rendszergazda engedélyeket adjon, tegye a következőket:

1. Az Azure AD-ben a bérlőnek/globális rendszergazdának az **Azure Active Directory** > **felhasználói** > **nak felhasználói beállításaira**kell navigálnia.
2. A rendszergazdának az **alkalmazásregisztrációkat** **Igen**beállításra kell állítania. Ez az alapértelmezett beállítás nem érzékeny. [További információ](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Az Azure AD engedélyei](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Alkalmazásfejlesztői szerepkör hozzárendelése

Másik lehetőségként a bérlő/globális rendszergazda hozzárendelheti az Alkalmazásfejlesztői szerepkört egy fiókhoz. [További információ](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) a szerepkör hozzárendeléséről.

### <a name="assign-permissions-to-create-a-key-vault"></a>Engedélyek hozzárendelése key vault létrehozásához

Ha engedélyezni szeretné, hogy az Azure Áttelepítés key vaultot hozzon létre, rendeljen hozzá engedélyeket az alábbiak szerint:

1. Az Azure Portal erőforráscsoportjában válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.**
2. A **Hozzáférés ellenőrzése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.

    - A kiszolgálófelmérés futtatásához **a közreműködői** engedélyek elegendőek.
    - Az ügynök nélküli kiszolgálóáttelepítés futtatásához **tulajdonosi** (vagy **közreműködői** és **felhasználói hozzáférés-rendszergazdai)** engedélyekkel kell rendelkeznie.

3. Ha nem rendelkezik a szükséges engedélyekkel, kérje őket az erőforráscsoport tulajdonosától.



## <a name="prepare-for-vmware-vm-assessment"></a>Felkészülés a VMware VM értékelésére

A VMware VM-felmérésre való felkészüléshez a következőket kell tenni:

- **Ellenőrizze a VMware beállításait**. Győződjön meg arról, hogy az áttelepíteni kívánt vCenter-kiszolgáló és virtuális gépek megfelelnek a követelményeknek.
- **Állítson be egy értékelést végző fiókot.** Az Azure Migrate ezt a fiókot használja a vCenter-kiszolgáló eléréséhez, a virtuális gépek felméréséhez.
- **A készülék követelményeinek ellenőrzése**. Ellenőrizze az Azure Migrate berendezés üzembe helyezési követelményeit, mielőtt üzembe helyezne.

### <a name="verify-vmware-settings"></a>A VMware-beállítások ellenőrzése

1. [Ellenőrzés](migrate-support-matrix-vmware.md#vmware-requirements) VMware kiszolgáló értékelési követelmények.
2. [Győződjön meg arról,](migrate-support-matrix-vmware.md#port-access) hogy a szükséges portok meg vannak nyitva a vCenter Server kiszolgálón.
3. A vCenter Server szolgáltatásban győződjön meg arról, hogy a fiók rendelkezik-e a virtuális gép OVA-fájl használatával történő létrehozásához szükséges engedélyekkel. Az Azure Migrate berendezést vmware virtuális gépként telepíti egy OVA-fájl használatával.


### <a name="set-up-an-account-for-assessment"></a>Fiók beállítása értékelésre

Az Azure Migrate-nek hozzá kell férnie a vCenter-kiszolgálóhoz, hogy felderítse a virtuális gépeket az értékeléshez és az ügynök nélküli áttelepítéshez.

- Ha azt tervezi, hogy felderíti az alkalmazásokat, vagy ügynök nélküli módon vizualizálja a függőséget, hozzon létre egy vCenter Server-fiókot írásvédett hozzáféréssel, valamint a **virtuális gépek** > **vendégműveleteihez**engedélyezett jogosultságokkal.

  ![vCenter Server-fiók jogosultságai](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Ha nem tervezi az alkalmazásfelderítést és az ügynök nélküli függőségi vizualizációt, állítson be egy írásvédett fiókot a vCenter-kiszolgálóhoz.

### <a name="verify-appliance-settings-for-assessment"></a>A készülék beállításainak ellenőrzése az értékeléshez

Az Azure Migrate készülék beállítása előtt, és a következő oktatóanyagban megkezdi a kiértékelést, készüljön fel a berendezés üzembe helyezésére.

1. [Ellenőrzés](migrate-appliance.md#appliance---vmware) Az Azure Migrate készülék követelményei.
2. Tekintse át az Azure URL-címeket, amelyeket a készüléknek a [nyilvános](migrate-appliance.md#public-cloud-urls) [és](migrate-appliance.md#government-cloud-urls) kormányzati felhőkben kell elérnie.
3. [Tekintse át](migrate-appliance.md#collected-data---vmware) a készülék által a felderítés és az értékelés során gyűjtött adatokat.
4. [Megjegyzés:](migrate-support-matrix-vmware.md#port-access) a készülék porthozzáférési követelményei.




## <a name="prepare-for-agentless-vmware-migration"></a>Felkészülés az ügynök nélküli VMware-áttelepítésre

Tekintse át a VMware virtuális gépek [ügynök nélküli áttelepítésének](server-migrate-overview.md) követelményeit.

1. [Véleményezés](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) VMware kiszolgáló követelmények.
2. Tekintse át az Azure Migrate által a vCenter-kiszolgáló eléréséhez szükséges [engedélyeket.](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers)
3. [Véleményezés](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) VMware virtuális gépek követelményei.
4. [Tekintse át](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) az Azure Migrate appliance követelményeit.
5. Jegyezze fel a [nyilvános](migrate-appliance.md#public-cloud-urls) és [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkhöz szükséges URL-hozzáférést.
6. Tekintse át [a porthozzáférési](migrate-support-matrix-vmware-migration.md#agentless-ports) követelményeket.

## <a name="prepare-for-agent-based-vmware-migration"></a>Felkészülés az ügynökalapú VMware-áttelepítésre

Tekintse át a vmware virtuális gépek [ügynökalapú áttelepítésének](server-migrate-overview.md) követelményeit.

1. [Véleményezés](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) VMware kiszolgáló követelmények.
2. [Az engedélyek áttekintése](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Az Azure Áttelepítési kell elérnie a vCenter Server.
2. [Véleményezés](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) VMware virtuális gépek követelményeinek, beleértve a mobilitási szolgáltatás telepítése minden virtuális gép áttelepíteni kívánt.
3. Az ügynökalapú áttelepítés replikációs berendezést használ:
    - [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a replikációs berendezés telepítési követelményeit.
    - Tekintse át a MySQL készülékre történő telepítésének [lehetőségeit.](migrate-replication-appliance.md#mysql-installation)
    - Jegyezze fel a [szükséges](migrate-replication-appliance.md#url-access)URL-hozzáférést.
    - Tekintse át a replikációs berendezés [porthozzáférési](migrate-replication-appliance.md#port-access) követelményeit.
    
## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Állítsa be az Azure-engedélyeket.
> * Előkészített VMware az értékelés és a migráció.


Folytassa a második oktatóanyagot egy Azure Migrate-projekt beállításához, és értékelje a VMware virtuális gépeket az Azure-ba való migráláshoz.

> [!div class="nextstepaction"]
> [VMware virtuális gépek értékelése](./tutorial-assess-vmware.md)

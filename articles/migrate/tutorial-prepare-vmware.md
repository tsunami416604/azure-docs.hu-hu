---
title: VMware virtuális gépek előkészítése értékeléshez/áttelepítéshez Azure Migrate
description: Ismerje meg, hogyan készítheti elő a VMware virtuális gépek értékelését és áttelepítését Azure Migrate használatával.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8d4d6ac1149c397442a8ca7dd01f46f04ffc89b4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927306"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>A VMware virtuális gépek előkészítése a felméréshez és az Azure-ba való migráláshoz

Ez a cikk segít felkészülni a helyszíni VMware virtuális gépek felmérésére és áttelepítésére az Azure-ban [Azure Migrate](migrate-services-overview.md)használatával.

Ez az oktatóanyag az első egy sorozatban, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő az Azure-t a Azure Migrate való együttműködéshez.
> * Felkészülés a VMware virtuális gépek felmérésére a Azure Migrate: Server Assessment Tool használatával.
> * Felkészülés a VMware virtuális gépek áttelepítésére a Azure Migrate: Server áttelepítési eszközzel. 

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek legegyszerűbb telepítési útvonalát mutatják be. Hasznosnak bizonyulnak a gyors próba-koncepció. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/), mielőtt hozzákezd.


## <a name="prepare-azure"></a>Az Azure előkészítése

A táblázat összefoglalja az Azure-ban elvégzendő feladatokat. Az egyes feladatokhoz tartozó utasítások a táblázatot követik.

**Tevékenység** | **Részletek** | **Engedélyek**
--- | --- | ---
**Azure Migrate projekt létrehozása** | Az Azure Migrate projektek központi helyet biztosítanak a felmérések és áttelepítések előkészítéséhez és kezeléséhez Azure Migrate eszközökkel, Microsoft-eszközökkel és harmadik féltől származó ajánlatokkal. | Az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie abban az erőforráscsoportban, amelyben a projekt található.
**Berendezés regisztrálása** | A Azure Migrate egy egyszerűsített Azure Migrate berendezéssel észleli a virtuális gépeket, értékeli azokat a kiszolgáló-értékelési eszközzel, és a kiszolgáló áttelepítési eszközével az ügynök nélküli Migrálás segítségével áttelepítheti azokat. [További](migrate-appliance-architecture.md#appliance-registration) információ a regisztrációról. | A készülék regisztrálásához az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az Azure-előfizetésben.
**Azure AD-alkalmazások létrehozása** | A berendezések regisztrálása Azure Migrate létrehozza az Azure két Active Directory (Azure AD) alkalmazást. <br/><br/> – Az első alkalmazás a készüléken futó ügynökök és Azure Migrate közötti kommunikációra szolgál. <br/><br/> – A második alkalmazás kizárólag az ügynök nélküli VMware VM-Migrálás felhasználói előfizetésében létrehozott kulcstartó elérésére szolgál.   | Az Azure-fióknak ezekre az [engedélyekre](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware#assign-permissions-to-create-azure-ad-apps) van szüksége az Azure ad-alkalmazások létrehozásához.
**Kulcstartó létrehozása** | – Az első Key Vault a berendezés regisztrációjának részeként jön létre, és a rendszer a készüléken a konfiguráció során letöltött tanúsítvány kezelésére szolgál. <br/><br/> – A VMware virtuális gépek ügynök nélküli áttelepítéssel történő áttelepítéséhez Azure Migrate létrehoz egy másik Key Vault az előfizetésben lévő replikációs fiók elérési kulcsainak kezeléséhez.| Ahhoz, hogy a Azure Migrate létrehozza a Key Vault, beállíthatja az engedélyeket (tulajdonos, közreműködő és felhasználói hozzáférés rendszergazdája) azon erőforráscsoporthoz, amelyben a Azure Migrate projekt található.


### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Engedélyek kiosztása Azure AD-alkalmazások létrehozásához

A készülék regisztrálásához az Azure-fióknak rendelkeznie kell az Azure AD-alkalmazások létrehozásához szükséges engedélyekkel. Rendelje hozzá az engedélyeket az alábbi módszerek egyikének használatával:

- **1. módszer: engedélyek megadása a fióknak**: a bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói fiókjainak az Azure ad-alkalmazások létrehozásához és regisztrálásához.
- **2. módszer: szerepkör társítása az engedélyekkel egy felhasználói fiókhoz**: a bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik a szükséges engedélyekkel) a felhasználói fiókhoz.

> [!NOTE]
> Az új berendezések regisztrálásához csak ezekre az engedélyekre van szükség. Az engedélyeket a készülék beállítása után távolíthatja el.


#### <a name="method-1-grant-permissions-to-the-account"></a>1. módszer: engedélyek megadása a fiók számára

A következőképpen adja meg az engedélyeket a fióknak:

1. Győződjön meg arról, hogy Ön bérlő vagy globális rendszergazda. Ezután az Azure ad-ben navigáljon **Azure Active Directory**  >  **felhasználók**  >  **felhasználói beállításait**.
2. Állítsa **App registrations** a Alkalmazásregisztrációk **értéket igen**értékre. Ez egy alapértelmezett beállítás, amely nem érzékeny. [További információ](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-engedélyek](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>2. módszer: az alkalmazás fejlesztői szerepkörének kiosztása

Azt is megteheti, hogy a bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkört egy fiókhoz. [További](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md) információ a szerepkörök hozzárendeléséről.

### <a name="assign-permissions-to-create-a-key-vault"></a>Engedélyek kiosztása Key Vault létrehozásához

Ha engedélyezni szeretné, hogy a Azure Migrate Key Vault hozzon létre, rendeljen engedélyeket az alábbiak szerint:

1. A Azure Portal erőforráscsoporthoz válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.

    - A kiszolgáló-értékelés futtatásához a **közreműködői** engedélyek elégek.
    - Az ügynök nélküli kiszolgáló áttelepítésének futtatásához **tulajdonosi** (vagy **közreműködői** és **felhasználói hozzáférés-rendszergazdai**) engedélyekkel kell rendelkeznie.

3. Ha nem rendelkezik a szükséges engedélyekkel, kérje meg őket az erőforráscsoport tulajdonosától.

## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

A VMware virtuális gépek értékelésének előkészítéséhez a következőket kell tennie:

1. A **VMware-beállítások ellenőrzése**. Győződjön meg arról, hogy az áttelepíteni kívánt vCenter Server és virtuális gépek megfelelnek a követelményeknek.
2. **Állítsa be az értékeléshez szükséges engedélyeket**. Azure Migrate egy vCenter-fiókot használ a vCenter Server eléréséhez a virtuális gépek felderítéséhez és értékeléséhez.
3. A **berendezésre vonatkozó követelmények ellenőrzése**. A következő oktatóanyagban a központi telepítés előtt ellenőrizze a Azure Migrate berendezés telepítési követelményeit.

### <a name="verify-vmware-settings"></a>VMware-beállítások ellenőrzése

1. [Vizsgálja](migrate-support-matrix-vmware.md#vmware-requirements) meg a VMware-követelményeket az értékeléshez.
2. Győződjön meg [arról](migrate-support-matrix-vmware.md#port-access-requirements) , hogy a szükséges portok nyitva vannak vCenter Serveron.
3. VCenter Server esetén győződjön meg arról, hogy a fiókja rendelkezik engedéllyel a virtuális gép létrehozásához PETESEJT-fájl használatával. Erre akkor van szükség, amikor a Azure Migrate készüléket VMware virtuális gépként telepíti, és egy PETESEJT-fájlt használ.
4. Az Azure-ba történő Migrálás előtt néhány módosításra van szükség a virtuális gépeken.

    - Egyes operációs rendszerek esetében a Azure Migrate automatikusan végrehajtja ezeket a módosításokat. 
    - Az áttelepítés megkezdése előtt fontos, hogy elvégezze ezeket a módosításokat. Ha a módosítás előtt áttelepíti a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.
    - Tekintse át a [Windows](prepare-for-migration.md#windows-machines) -és [Linux](prepare-for-migration.md#linux-machines) -módosításokat, amelyeket el kell végeznie.


### <a name="set-up-permissions-for-assessment"></a>Az értékeléshez szükséges engedélyek beállítása

Azure Migratenak hozzá kell férnie a vCenter Serverhoz, hogy a Azure Migrate készülék képes legyen felderíteni a virtuális gépeket az értékeléshez és az ügynök nélküli áttelepítéshez. A következőképpen állíthatja be a fiókot:

1. A vSphere webes ügyfélprogramban nyissa meg a **felügyeleti**  >  **hozzáférés**  >  **SSO-felhasználók és-csoportok**lehetőséget.
2. A **felhasználók**területen kattintson az **új felhasználó** ikonra.
3. Írja be az új felhasználó adatait.
4. Válassza az engedélyek lehetőséget a tábla értékeinek megfelelően.

    **Szolgáltatás** | **Fiók engedélyei**
    --- | ---
    [Virtuális gépek felmérése](tutorial-assess-vmware.md) | Az értékeléshez a fióknak csak olvasási hozzáférésre van szüksége.
    [VM-alkalmazások,-szerepkörök és-szolgáltatások felderítése](how-to-discover-applications.md) | Ha az App-Discovery szolgáltatást szeretné használni, az értékeléshez használt írásvédett fióknak engedélyezve kell lennie a **virtuális gépek**  >  **vendég műveleteihez**.
    [Virtuális gépek függőségeinek elemzése (ügynök nélküli)](how-to-create-group-machine-dependencies-agentless.md) | Ha szeretné elemezni a függőségeket, az értékeléshez használt írásvédett fióknak engedélyezve kell lennie a **virtuális gépek**  >  **vendég műveleteihez**.
    
> [!NOTE]
> Ha korlátozni szeretné a virtuális gépek felderítését egy adott hatókörre, tekintse át [ezt a cikket](set-discovery-scope.md#assign-a-role-for-assessment).

### <a name="verify-appliance-settings-for-assessment"></a>A berendezés beállításainak ellenőrzése az értékeléshez

A [következő oktatóanyagban](tutorial-assess-vmware.md) beállíthatja a Azure Migrate készüléket, és megkezdheti az értékelést. Mielőtt ezt megtenné, tekintse át a készülékre vonatkozó követelményeket a következő módon: 

1. [Tekintse át az](migrate-appliance.md#appliance---vmware) Azure Migrate berendezés üzembe helyezésének követelményeit.
2. Tekintse át azokat az Azure URL-címeket, amelyeket a készüléknek a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben kell elérnie.
3. [Jegyezze](migrate-support-matrix-vmware.md#port-access-requirements) fel a készülék által használt portokat.
4. [Tekintse át a](migrate-appliance.md#collected-data---vmware) berendezés által a felderítés és az értékelés során gyűjtött adatokat.

## <a name="prepare-for-agentless-vmware-migration"></a>Felkészülés az ügynök nélküli VMware-áttelepítésre

A VMware virtuális gépeket [ügynök nélküli vagy ügynök alapú áttelepítéssel](server-migrate-overview.md)is áttelepítheti. Ez a szakasz az ügynök nélküli áttelepítés követelményeit foglalja össze.

1. [Döntse el](server-migrate-overview.md#compare-migration-methods) , hogy az ügynök nélküli áttelepítést szeretné-e használni.
2. Az áttelepíteni kívánt gépekre vonatkozó hypervisor-követelmények [áttekintése](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) .
3. [Tekintse át](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) az áttelepíteni kívánt VMWare virtuális gépekre vonatkozó követelményeket az ügynök nélküli áttelepítés használatával.
4. [Tekintse át](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) az ügynök nélküli áttelepítés Azure Migrate készülékre vonatkozó követelményeit.
5. Megjegyzés: a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkhez szükséges URL-hozzáférés.
6. Tekintse át a [port hozzáférési](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) követelményeit.
7. Az ügynök nélküli áttelepítés engedélyeinek beállítása a következő eljárásban leírtak szerint.


### <a name="assign-permissions-to-an-account"></a>Engedélyek kiosztása egy fiókhoz

Azure Migrate készülék csatlakoztatja a vCenter Servert a virtuális gépek ügynök nélküli áttelepítéssel való felderítéséhez és áttelepítéséhez. Hozzárendelheti azokat az engedélyeket, amelyekre a készüléknek szüksége van a felhasználói fiókhoz, vagy létre kell hoznia egy szerepkört az engedélyekkel, és hozzá kell rendelnie a szerepkört egy felhasználói fiókhoz.

1. A vSphere webes ügyfélprogramban nyissa meg a **felügyeleti**  >  **hozzáférés**  >  **SSO-felhasználók és-csoportok**lehetőséget.
2. A **felhasználók**területen kattintson az **új felhasználó** ikonra.
3. Írja be az új felhasználó adatait.
4. [Engedélyek](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) kiosztása

#### <a name="create-a-role-and-assign-to-an-account"></a>Szerepkör létrehozása és hozzárendelés egy fiókhoz

Azt is megteheti, hogy létrehoz egy fiókot. Ezután hozzon létre egy szerepkört, és rendelje hozzá a fiókhoz az alábbiak szerint:

1. Jelentkezzen be a gömb webes ügyfelére vCenter Server rendszergazdaként
2. Válassza ki a vCenter Server példányt >  **hozzon létre szerepkört**.
3. Adja meg a szerepkör nevét, például <em>Azure_Migrate</em>, és rendelje hozzá a [szükséges engedélyeket](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) a szerepkörhöz.

    ![vCenter Server fiók jogosultságai](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. Most hozzon létre egy fiókot, és rendelje hozzá a szerepkört a fiókhoz.

> [!NOTE]
> Ha korlátozni szeretné az ügynök nélküli áttelepítés adott hatókörre való virtuálisgép-felderítését, tekintse át [ezt a cikket](set-discovery-scope.md#assign-a-role-for-agentless-migration).



## <a name="prepare-for-agent-based-vmware-migration"></a>Felkészülés az ügynök alapú VMware-áttelepítésre

A VMware virtuális gépeket [ügynök nélküli vagy ügynök alapú áttelepítéssel](server-migrate-overview.md)is áttelepítheti. Ez a szakasz az ügynök-alapú áttelepítés követelményeit foglalja össze.

1. [Döntse el](server-migrate-overview.md#compare-migration-methods) , hogy az ügynök-alapú áttelepítést szeretné-e használni.
1. Az áttelepíteni kívánt gépekre vonatkozó hypervisor-követelmények [áttekintése](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) .
2. [Tekintse át](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) A VMware virtuális gépekre vonatkozó követelmények, beleértve a mobilitási szolgáltatás telepítését minden olyan virtuális gépen, amelyet át szeretne telepíteni.
3. Az ügynök alapú áttelepítés replikációs készüléket használ:
    - [Tekintse át](migrate-replication-appliance.md#appliance-requirements) a replikációs berendezés telepítési követelményeit.
    - [Tekintse át a](migrate-replication-appliance.md#mysql-installation) MySQL telepítésének lehetőségeit a készüléken.
    - Megjegyzés: a [nyilvános](migrate-replication-appliance.md#url-access) és a [kormányzati](migrate-replication-appliance.md#azure-government-url-access) felhőkhez szükséges URL-hozzáférés.
    - Tekintse át a replikációs berendezés [portra vonatkozó hozzáférési](migrate-replication-appliance.md#port-access) követelményeit.
4. Néhány módosításra van szükség a virtuális gépeken az Azure-ba való Migrálás előtt. Tekintse át a [Windows](prepare-for-migration.md#windows-machines) -és [Linux](prepare-for-migration.md#linux-machines) -módosításokat, amelyeket el kell végeznie.



## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure-engedélyek beállítása.
> * Előkészített VMware az értékeléshez és az áttelepítéshez.


Folytassa a második oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, és felméri a VMware virtuális gépeket az Azure-ba való Migrálás során.

> [!div class="nextstepaction"]
> [VMware virtuális gépek felmérése](./tutorial-assess-vmware.md)

---
title: VMware virtuális gépek előkészítése az Azure-ba történő értékeléshez és áttelepítéshez Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan lehet felkészülni a helyszíni VMware virtuális gépek felmérésére és áttelepítésére az Azure-ban Azure Migrate használatával.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4cc04e9ab0acdc9d0cdff77ed1de7bea1c1362d4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498469"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware virtuális gépek előkészítése az Azure-ba történő értékeléshez és áttelepítéshez

Ez a cikk segít felkészülni a helyszíni VMware virtuális gépek felmérésére és/vagy áttelepítésére az Azure-ban [Azure Migrate](migrate-services-overview.md)használatával.

[Azure Migrate](migrate-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 


Ez az oktatóanyag az első egy sorozatban, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

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
--- | --- | ---
**Azure Migrate projekt létrehozása** | Az Azure-fióknak rendelkeznie kell a projekt létrehozásához szükséges engedélyekkel. 
**A Azure Migrate berendezés regisztrálása** | A Azure Migrate egy egyszerűsített Azure Migrate berendezés használatával értékeli a VMware virtuális gépeket Azure Migrate Server Assessment használatával, valamint a VMware virtuális gépek [ügynök nélküli áttelepítését](server-migrate-overview.md) Azure Migrate kiszolgáló áttelepítésével. Ez a készülék felfedi a virtuális gépeket, és a VM-metaadatokat és teljesítményadatokat Azure Migrateba küldi.<br/><br/>A regisztráció során Azure Migrate két Azure Active Directory-(Azure AD-) alkalmazást hoz létre, amelyek egyedileg azonosítják a készüléket, és engedélyekkel kell rendelkezniük ezeknek az alkalmazásoknak a létrehozásához.<br/> – Az első alkalmazás a Azure Migrate szolgáltatási végpontokkal kommunikál.<br/> – A második alkalmazás a regisztráció során létrehozott Azure Key Vault fér hozzá az Azure AD-alkalmazás adatai és a készülék konfigurációs beállításainak tárolásához.
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
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** . Ez egy alapértelmezett beállítás, amely nem érzékeny. [Részletek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD-engedélyek](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása 

A bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét egy fiókhoz. [Részletek](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-role-assignment-permissions"></a>Szerepkör-hozzárendelési engedélyek hozzárendelése

Ha engedélyezni szeretné, hogy a Azure Migrate egy Key Vault hozzon létre, rendeljen hozzá szerepkör-hozzárendelési engedélyeket a következő módon:

1. A Azure Portal erőforráscsoporthoz válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.

    - A kiszolgáló-értékelés futtatásához a **közreműködői** engedélyek elégek.
    - Az ügynök nélküli kiszolgáló áttelepítésének futtatásához **tulajdonosi** (vagy **közreműködői** és **felhasználói hozzáférés-rendszergazdai**) engedélyekkel kell rendelkeznie.

3. Ha nem rendelkezik a szükséges engedélyekkel, kérje meg őket az erőforráscsoport tulajdonosától. 



## <a name="prepare-for-vmware-vm-assessment"></a>Felkészülés a VMware virtuális gépek értékelésére

A VMware virtuális gépek értékelésének előkészítéséhez a következőket kell tennie:

- A **VMware-beállítások ellenőrzése**. Győződjön meg arról, hogy az áttelepíteni kívánt vCenter Server és virtuális gépek megfelelnek a követelményeknek.
- **Hozzon létre egy értékelési fiókot**. Azure Migrate el kell érnie a vCenter Server, hogy felderítse a virtuális gépeket az értékeléshez. Azure Migrate hozzáféréshez csak olvasható fiók szükséges.
- A **berendezésre vonatkozó követelmények ellenőrzése**. Ellenőrizze az értékeléshez használt Azure Migrate berendezés telepítési követelményeit.

### <a name="verify-vmware-settings"></a>VMware-beállítások ellenőrzése

1. [Ellenőrzési](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) A VMware-kiszolgálói követelmények az értékeléshez.
2. Győződjön meg [arról](migrate-support-matrix-vmware.md#assessment-port-requirements) , hogy a szükséges portok nyitva vannak vCenter Serveron.


### <a name="set-up-an-account-for-assessment"></a>Fiók beállítása az értékeléshez

Azure Migrate el kell érnie a vCenter Server, hogy felderítse a virtuális gépeket az értékeléshez és az ügynök nélküli áttelepítéshez. Csak az értékeléshez állítson be írásvédett fiókot a vCenter Server számára.

### <a name="verify-appliance-settings-for-assessment"></a>A berendezés beállításainak ellenőrzése az értékeléshez

A berendezés telepítésének megkezdése előtt tekintse meg a készülékre vonatkozó követelményeket.

1. [Ellenőrizze](migrate-support-matrix-vmware.md#assessment-appliance-requirements) a berendezések követelményeit és korlátozásait.
2. Ha URL-alapú tűzfal-proxyt használ, [tekintse át](migrate-support-matrix-vmware.md#assessment-url-access-requirements) azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie. Győződjön meg arról, hogy a proxy feloldja az URL-címek keresése során fogadott CNAME-rekordokat.
3. Tekintse át a [teljesítményadatokat](migrate-appliance.md#collected-performance-data-vmware)] és a [metaadatokat](migrate-appliance.md#collected-metadata-vmware) , amelyeket a készülék a felderítés és az értékelés során gyűjt.
4. [Jegyezze](migrate-support-matrix-vmware.md#assessment-port-requirements) fel a berendezés által elért portokat.
5. VCenter Serveron ellenőrizze, hogy a fiókja rendelkezik-e engedéllyel egy virtuális gép létrehozásához PETESEJT-fájl használatával. A Azure Migrate berendezést VMware virtuális gépként helyezi üzembe a PETESEJT-fájl használatával. 

Ha URL-alapú tűzfal. proxyt használ, engedélyezze a hozzáférést a szükséges [Azure URL-címekhez](migrate-support-matrix-vmware.md#assessment-url-access-requirements).




## <a name="prepare-for-agentless-vmware-migration"></a>Felkészülés az ügynök nélküli VMware-áttelepítésre

Tekintse át a VMware virtuális gépek ügynök nélküli áttelepítésének követelményeit.

1. [Tekintse át](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) VMware-kiszolgálói követelmények.
2. Állítson be egy fiókot a [szükséges engedélyekkel](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions), hogy a Azure Migrate hozzáférhessen az ügynök nélküli áttelepítés vCenter Server az Azure Migrate Server áttelepítésével.
3. [Tekintse át](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) azokat a VMWare virtuális gépekre vonatkozó követelményeket, amelyeket át szeretne telepíteni az Azure-ba az ügynök nélküli áttelepítés használatával.
4. [Tekintse át](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) az ügynök nélküli migrálás Azure Migrate berendezés használatára vonatkozó követelményeit.
5. Figyelje meg, hogy az [URL-cím](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) és a [port eléréséhez](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) az Azure Migrate készüléknek szüksége van az ügynök nélküli áttelepítéshez.


## <a name="prepare-for-agent-based-vmware-migration"></a>Felkészülés az ügynök alapú VMware-áttelepítésre

Tekintse át a VMware virtuális gépek [ügynök-alapú áttelepítésének](server-migrate-overview.md) követelményeit.

1. [Tekintse át](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware-kiszolgálói követelmények. 
2. Állítson be egy fiókot a [szükséges engedélyekkel](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions). annak érdekében, hogy vCenter Server a Azure Migrate hozzáférhessenek az ügynökön alapuló áttelepítéshez Azure Migrate Server áttelepítéssel.
3. [Tekintse át](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) azokat a VMWare virtuális gépekre vonatkozó követelményeket, amelyeket az Azure-ba az ügynök-alapú áttelepítés használatával szeretne áttelepíteni, beleértve a mobilitási szolgáltatás telepítését minden áttelepíteni kívánt virtuális gépen.
4. Megjegyzés [URL-hozzáférése](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Tekintse át a [porthoz való hozzáférést](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) , amely Azure Migrate az összetevőknek ügynök-alapú hozzáférésre van szüksége.

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:
 
> [!div class="checklist"] 
> * Azure-engedélyek beállítása.
> * Előkészített VMware az értékeléshez és az áttelepítéshez.


Folytassa a második oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, és felméri a VMware virtuális gépeket az Azure-ba való Migrálás során.

> [!div class="nextstepaction"] 
> [VMware virtuális gépek felmérése](./tutorial-assess-vmware.md) 


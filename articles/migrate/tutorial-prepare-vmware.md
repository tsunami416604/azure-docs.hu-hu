---
title: VMware virtuális gépek előkészítése az Azure-ba történő értékeléshez és áttelepítéshez Azure Migrate használatával | Microsoft Docs
description: Ismerteti, hogyan lehet felkészülni a helyszíni VMware virtuális gépek felmérésére és áttelepítésére az Azure-ban Azure Migrate használatával.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 5bd4926494e13c1dd0cfb6283292ade967471238
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013437"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware virtuális gépek előkészítése az Azure-ba történő értékeléshez és áttelepítéshez

Ez a cikk a helyszíni VMware virtuális gépek Azure-ba való előkészítését és áttelepítését ismerteti [Azure Migrate](migrate-services-overview.md)használatával.

[Azure Migrate](migrate-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz. 


Ez az oktatóanyag az első egy sorozatban, amely bemutatja, hogyan lehet felmérni és áttelepíteni a VMware virtuális gépeket. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő az Azure-t. Állítsa be az Azure-fiókra és-erőforrásokra vonatkozó engedélyeket a Azure Migrate való együttműködéshez.
> * Készítse elő a helyszíni VMware-kiszolgálókat és virtuális gépeket a VM-értékeléshez.
> * Készítse elő a helyszíni VMware-kiszolgálókat és virtuális gépeket a VM áttelepítéséhez.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a következő témakört: a VMware-értékeléshez és-áttelepítéshez használt TOS.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prepare-azure"></a>Az Azure előkészítése

Ezekre az Azure-engedélyekre van szüksége:

- Az Azure-fióknak engedélyre van szüksége ahhoz, hogy Azure Migrate projektet hozzon létre az értékeléshez és az áttelepítéshez. 
- A VMware virtuális gépek értékeléséhez és ügynök nélküli áttelepítéséhez Azure Migrate egy egyszerűsített berendezést futtat, amely felkeresi a virtuális gépeket, és elküldi a VM-metaadatokat és teljesítményadatokat a Azure Migrate. Az Azure-ban engedélyekkel kell rendelkeznie a Azure Migrate berendezés regisztrálásához.
- A VMware virtuális gépek Azure Migrate kiszolgáló áttelepítéssel történő áttelepítéséhez Azure Migrate létrehoz egy Key Vault az erőforráscsoporthoz, hogy a hozzáférési kulcsokat az előfizetésében lévő replikációs Storage-fiókhoz kezelhesse. A tároló létrehozásához szerepkör-hozzárendelési engedélyekre van szükség ahhoz az erőforráscsoporthoz, amelyben az Azure Migrate-projekt található. 


### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy tulajdonosi engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.

### <a name="assign-permissions-to-register-the-appliance"></a>Engedélyek kiosztása a készülék regisztrálásához

Ha a Azure Migrate készüléket üzembe helyezi a virtuális gépek ügynök nélküli áttelepítésének felméréséhez vagy futtatásához, regisztrálnia kell.

- A készülék regisztrálása során Azure Migrate két Azure Active Directory (Azure AD) alkalmazást hoz létre, amelyek egyedileg azonosítják a készüléket
    - Az első alkalmazás a Azure Migrate szolgáltatási végpontokkal kommunikál.
    - A második alkalmazás a regisztráció során létrehozott Azure Key Vault fér hozzá az Azure AD-alkalmazás adatainak és a készülék konfigurációs beállításainak tárolásához.
- Az Azure AD-alkalmazások létrehozásához a következő módszerek egyikével hozhatja létre a Azure Migrate engedélyeit:
    - A bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói számára az Azure AD-alkalmazások létrehozásához és regisztrálásához.
    - A bérlői/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik engedélyekkel) a fiókhoz.

Érdemes megjegyezni, hogy:

- Az alkalmazások nem rendelkeznek más hozzáférési engedélyekkel az előfizetéshez a fent leírtak kivételével.
- Az új berendezések regisztrálásához csak ezekre az engedélyekre van szükség. Az engedélyeket a készülék beállítása után távolíthatja el. 


#### <a name="grant-account-permissions"></a>Fiók engedélyeinek megadása

A bérlő/globális rendszergazda a következőképpen adhat meg engedélyeket.

1. Az Azure ad-ben a bérlői/globális rendszergazdának meg kell keresnie **Azure Active Directory** > **felhasználók** > **felhasználói beállításait**.
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** .

    ![Azure AD-engedélyek](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Ez egy alapértelmezett beállítás, amely nem érzékeny. [További információk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása 

A bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét egy fiókhoz. [További információk](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Szerepkör-hozzárendelési engedélyek hozzárendelése

Rendeljen szerepkör-hozzárendelési engedélyeket azon az erőforráscsoporthoz, amelyben az Azure Migrate-projekt található, a következőképpen:

1. A Azure Portal erőforráscsoporthoz válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.

    - A kiszolgáló-értékelés futtatásához a **közreműködői** engedélyek elégek.
    - Az ügynök nélküli kiszolgáló áttelepítésének futtatásához **tulajdonosi** (vagy **közreműködői** és **felhasználói hozzáférés-rendszergazdai**) engedélyekkel kell rendelkeznie.

3. Ha nem rendelkezik a szükséges engedélyekkel, kérje meg őket az erőforráscsoport tulajdonosától. 



## <a name="prepare-for-vmware-vm-assessment"></a>Felkészülés a VMware virtuális gépek értékelésére

A VMware virtuális gépek értékelésének előkészítéséhez ellenőriznie kell a VMware-gazdagép és a virtuális gép beállításait, és ellenőriznie kell a berendezések telepítésének beállításait.

### <a name="verify-vmware-settings"></a>VMware-beállítások ellenőrzése

1. [Ellenőrzés](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) A VMware-kiszolgálói követelmények a virtuális gépek értékeléséhez.
2. [Győződjön meg arról](migrate-support-matrix-vmware.md#assessment-port-requirements) , hogy a szükséges portok meg vannak nyitva a vCenter-kiszolgálókon.


### <a name="set-up-an-account-for-assessment"></a>Fiók beállítása az értékeléshez

Azure Migrate el kell érnie a vCenter Server, hogy felderítse a virtuális gépeket az értékeléshez és az ügynök nélküli áttelepítéshez. Csak az értékeléshez van szükség a vCenter Server írásvédett fiókjára.

Ha URL-alapú tűzfal. proxyt használ, engedélyezze a hozzáférést a szükséges [Azure URL](migrate-support-matrix-vmware.md#assessment-url-access-requirements)-címekhez.

Győződjön meg arról, hogy a proxy feloldja az URL-címek keresése során fogadott CNAME-rekordokat.


### <a name="verify-appliance-settings-for-assessment"></a>A berendezés beállításainak ellenőrzése az értékeléshez

A Azure Migrate berendezés beállítása és az értékelés megkezdése előtt a következő oktatóanyagban készítse elő a berendezés üzembe helyezését.

1. [Ellenőrizze](migrate-support-matrix-vmware.md#assessment-appliance-requirements) a Azure Migrate berendezés VMware környezetben való beállításának követelményeit.
2. [Tekintse át](migrate-support-matrix-vmware.md#assessment-url-access-requirements) azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie.
3. Tekintse át a berendezés által a felderítés és az értékelés során gyűjtött adatokat.
4. [Jegyezze](migrate-support-matrix-vmware.md#assessment-port-requirements) fel a port hozzáférési követelményeit a készülékhez.
5. A Azure Migrate berendezést VMware virtuális gépként helyezi üzembe egy PETESEJT-fájl használatával. VCenter Serveron ellenőrizze, hogy a fiókja rendelkezik-e engedéllyel egy virtuális gép létrehozásához PETESEJT-fájl használatával.


## <a name="prepare-for-agentless-vmware-migration"></a>Felkészülés az ügynök nélküli VMware-áttelepítésre

Tekintse át a VMware virtuális gépek ügynök nélküli áttelepítésének követelményeit.

1. [Tekintse át](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) VMware Server-követelmények ügynök nélküli Migrálás esetén.
2. Állítson be egy fiókot a vCenter Serverhoz való hozzáféréshez az ügynök nélküli áttelepítés számára [szükséges engedélyekkel](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) .
3. [Jegyezze](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) fel azokat a VMWare virtuális gépekre vonatkozó követelményeket, amelyeket át szeretne telepíteni az Azure-ba az ügynök nélküli áttelepítés használatával.
4. [Tekintse át](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) az ügynök nélküli Migrálás készülékre vonatkozó követelményeit.
5. Vegye figyelembe a készülék [URL-címének elérését](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) és a [portok hozzáférési](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) követelményeit az ügynök nélküli áttelepítés esetében.


## <a name="prepare-for-agent-based-vmware-migration"></a>Felkészülés az ügynök alapú VMware-áttelepítésre

Tekintse át a VMware virtuális gépek ügynök-alapú áttelepítésének követelményeit.

1. [Tekintse át](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware Server-követelmények ügynök nélküli Migrálás esetén. 
2. Állítson be egy fiókot a vCenter Serverhoz való hozzáféréshez az ügynök nélküli áttelepítés számára [szükséges engedélyekkel](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) .
3. [Jegyezze](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) fel azokat a VMWare virtuális gépekre vonatkozó követelményeket, amelyeket az Azure-ba az ügynök-alapú áttelepítés használatával szeretne áttelepíteni, beleértve a mobilitási szolgáltatás telepítését minden áttelepíteni kívánt virtuális gépen.
4. Megjegyzés [URL-hozzáférése](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Tekintse át az egyes virtuális gépeken futó mobilitási szolgáltatáshoz tartozó [port hozzáférési](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) követelményeit, valamint a Azure Migrate konfigurációs kiszolgálót.

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:
 
> [!div class="checklist"] 
> * Azure-engedélyek beállítása.
> * Előkészített VMware az értékeléshez és az áttelepítéshez.


Folytassa a második oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, és felméri a VMware virtuális gépeket az Azure-ba való Migrálás során.

> [!div class="nextstepaction"] 
> [VMware virtuális gépek felmérése](./tutorial-assess-vmware.md) 


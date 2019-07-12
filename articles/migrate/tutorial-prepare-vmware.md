---
title: VMware virtuális gépek előkészítése elemzésekhez és migráláshoz az Azure-bA az Azure Migrate |} A Microsoft Docs
description: Ismerteti, hogyan készíti elő az elemzésekhez és migráláshoz az Azure-ban az Azure Migrate a helyszíni VMware virtuális gépek.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 10f559295ff0598dea26fb30b089f020e2985889
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840341"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware virtuális gépek előkészítése elemzésekhez és migráláshoz az Azure-bA

Ez a cikk bemutatja, hogyan készíti elő az elemzésekhez és migráláshoz az Azure-bA a helyszíni VMware virtuális gépek használatával [Azure Migrate](migrate-services-overview.md).

[Az Azure Migrate](migrate-overview.md) hub eszközöket tartalmazza, amelyek segítséget nyújtanak a felderítése, értékelheti és migrálhatja az alkalmazásokat, az infrastruktúra és a számítási feladatok a Microsoft Azure biztosít. A hub tartalmaz, az Azure Migrate eszközökkel és külső független szoftverszállítók (ISV) szállító ajánlatok. 


Ez az oktatóanyag egy sorozat, amely bemutatja, hogyan mérje fel, és a VMware virtuális gépek áttelepítése az első olyan. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure előkészítése. Állítsa be az Azure-fiók és -erőforrások engedélyeit az Azure Migrate működjön.
> * Felkészülés a helyszíni VMware-kiszolgálók és virtuális gépek a virtuális gépek.
> * Készítse elő a helyszíni VMware-kiszolgálók és virtuális gépek virtuális gépek migrálása.

> [!NOTE]
> Az oktatóprogramok bemutatják, a legegyszerűbb telepítési út forgatókönyv esetén, hogy gyorsan beállíthat egy proof-of-concept. Az oktatóanyagok lehetőség szerint használja az alapértelmezett, és ne jelenjen meg az összes lehetséges beállítások és elérési út. Részletes útmutatásért tekintse át a hogyan Tos VMware elemzésekhez és migráláshoz.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prepare-azure"></a>Az Azure előkészítése

Ezek az Azure-engedélyek szükségesek:

- Az Azure-fiók létrehozása az Azure Migrate-projekt elemzésekhez és migráláshoz engedélyre. 
- Elemzésekhez és ügynök nélküli migrálást VMware virtuális gépek Azure Migrate fut egy egyszerűsített berendezés, amely felderíti a virtuális gépeket, és a virtuális gép metaadatainak és a teljesítmény adatokat küld az Azure Migrate. Az Azure-ban az Azure Migrate berendezés regisztráljon engedélyre van szükségük.
- Át a VMware virtuális gépek Azure Migrate-áttelepítés használatával, az Azure Migrate létrehoz egy Key Vaultot az erőforráscsoport, a replikációs tárfiókot az előfizetésében a hozzáférési kulcsok kezeléséhez. Hozza létre a tárolót, az erőforráscsoport, amelyben az Azure Migrate-projektben található a szerepkör-hozzárendelési engedéllyel kell. 


### <a name="assign-permissions-to-create-project"></a>Rendelje hozzá a projekt létrehozásához szükséges engedélyek

1. Az Azure Portalon nyissa meg az előfizetést, és válassza ki **hozzáférés-vezérlés (IAM)** .
2. A **hozzáférés ellenőrzése**, keresse meg a megfelelő fiók és engedélyek megtekintéséhez kattintson rá.
3. Rendelkeznie kell **közreműködői** vagy **tulajdonosa** engedélyeket.
    - Ha az imént létrehozott ingyenes Azure-fiókra, Ön az előfizetés tulajdonosa.
    - Ha Ön nem az előfizetés tulajdonosa, együttműködve rendelje hozzá a szerepkört a tulajdonosa.

### <a name="assign-permissions-to-register-the-appliance"></a>Adja meg a berendezés regisztráljon

Mérje fel, vagy futtassa az ügynök nélküli migrálást, virtuális gépek az Azure Migrate készülék végzi, ha szeretne regisztrálni.

- Készülék regisztráció során az Azure Migrate létrehoz két Azure Active Directory (Azure AD-) alkalmazások, a készülék egyedi azonosítására alkalmas
    - Az első alkalmazás Szolgáltatásvégpontok az Azure Migrate kommunikál.
    - A második alkalmazás hozzáfér az Azure Key Vault regisztrációja során létrehozott Azure AD alkalmazás adatai, valamint a berendezés konfigurációs beállítások tárolásához.
- Ezek a következő módszerek egyikével Azure AD-alkalmazások létrehozása az Azure Migrate engedélyeket rendelhet:
    - Egy bérlő globális rendszergazdai engedélyek megadásához a bérlő létrehozására és regisztrálására az Azure AD-alkalmazások a felhasználók számára.
    - A bérlő globális rendszergazdája rendelhet a fiók a alkalmazásfejlesztő szerepkör (amely engedélyekkel rendelkezik).

Érdemes megjegyezni, hogy:

- Az alkalmazások nem tartoznak semmilyen más hozzáférési engedélyt a fent leírtaktól eltérő előfizetés.
- Amikor regisztrál egy új készülék csak kell ezeket az engedélyeket. Az engedélyek a készülék beállítását követő távolíthatja el. 


#### <a name="grant-account-permissions"></a>Fiók engedélyek megadása

A bérlő globális rendszergazdája a következő engedélyek megadásához

1. Az Azure AD-ben a bérlő globális rendszergazdája kell lépjen **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
2. A rendszergazdának kell beállítania **alkalmazásregisztrációk** való **Igen**.

    ![Az Azure AD-engedélyekről](./media/tutorial-prepare-vmware/aad.png)

> [!NOTE]
> Ez a nem bizalmas alapértelmezett beállítás. [További információk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztője szerepkör hozzárendelése 

A bérlő globális rendszergazdája a alkalmazásfejlesztő szerepkört rendelhet egy fiókot. [További információk](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

## <a name="assign-role-assignment-permissions"></a>Szerepkör-hozzárendelés engedélyek hozzárendelése

Az az erőforráscsoport, amelyben az Azure Migrate-projektben található, a következő szerepkör-hozzárendelés engedélyek hozzárendelése:

1. Az erőforráscsoport az Azure Portalon, válassza ki **hozzáférés-vezérlés (IAM)** .
2. A **hozzáférés ellenőrzése**, keresse meg a megfelelő fiók és engedélyek megtekintéséhez kattintson rá.

    - A kiszolgáló felmérést **közreműködői** engedélyek vonatkoznak.
    - Ügynök nélküli kivételfigyelés kiszolgálóáttelepítés futtatásához rendelkeznie kell **tulajdonosa** (vagy **közreműködői** és **felhasználói hozzáférés rendszergazdája**) engedélyekkel.

3. Ha nem rendelkezik a szükséges engedélyekkel, kérjen őket az erőforráscsoport-tulajdonosnak. 



## <a name="prepare-for-vmware-vm-assessment"></a>A VMware virtuális gépek előkészítése

Felkészülés a VMware virtuális gépek, kell a Hyper-V-gazdagép és a virtuális gép beállításait, és ellenőrizze a beállításokat a berendezés üzembe helyezéshez.

### <a name="verify-vmware-settings"></a>VMware-beállítások ellenőrzése

1. [Győződjön meg arról](migrate-support-matrix-vmware.md#assessment-vmware-server-requirements) a virtuális gépek, VMware-kiszolgáló követelményeinek.
2. [Győződjön meg arról, hogy](migrate-support-matrix-vmware.md#assessment-port-requirements) , hogy a vCenter-kiszolgálók nyitva-e a szükséges portokat.


### <a name="set-up-an-account-for-assessment"></a>Értékelés fiók beállítása

Az Azure Migrate a vCenter-kiszolgáló virtuális gépek felderítéséhez elemzésekhez és ügynök nélküli migrálást hozzáféréssel kell rendelkeznie. Értékelés csak szüksége van egy csak olvasható fiók a vcenter-kiszolgálóhoz.

Egy URL-alapú firewall.proxy használja, ha hozzáférést szeretne a kívánt [Azure URL-címeinek](migrate-support-matrix-vmware.md#assessment-url-access-requirements).

Győződjön meg arról, hogy a proxy oldja fel az URL-címek keresése során kapott minden olyan CNAME-rekordokat.


### <a name="verify-appliance-settings-for-assessment"></a>Értékelés készülék beállítások ellenőrzése

A készülék az Azure Migrate és kezdő értékelése a következő oktatóanyagban beállítása előtt készítse elő a berendezés üzembe helyezéshez.

1. Győződjön meg arról [ellenőrizze](migrate-support-matrix-vmware.md#assessment-appliance-requirements) VMware-ben az Azure Migrate készülék beállításával kapcsolatos követelmények.
2. [Felülvizsgálat](migrate-support-matrix-vmware.md#assessment-url-access-requirements) az Azure URL-címeket, amelyeket a készülék kell hozzáférni.
3. Tekintse át az adatokat, hogy a berendezés gyűjt felderítés és értékelés során.
4. [Megjegyzés:](migrate-support-matrix-vmware.md#assessment-port-requirements) port a készülék való hozzáférés feltételeit.
5. Az OVA-fájl használatával VMware virtuális gépként az Azure Migrate berendezés üzembe helyezi. VCenter-kiszolgálóhoz győződjön meg arról, hogy a fiók rendelkezik-e a virtuális gép használatával az OVA-fájl létrehozásához szükséges engedélyek.


## <a name="prepare-for-agentless-vmware-migration"></a>Ügynök nélküli kivételfigyelés VMware-migrálás előkészítése

Tekintse át a VMware virtuális gépek az ügynök nélküli kivételfigyelés áttelepítéshez.

1. [Felülvizsgálat](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) ügynök nélküli migrálást VMware-kiszolgáló követelményeinek.
2. Állítsa be a fiókot a vCenter Server eléréséhez a a [szükséges engedélyek](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions) az ügynök nélküli migrálást.
3. [Megjegyzés:](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) VMware virtuális gépek áttelepítése az Azure-ban az ügynök nélküli migrálást kívánt követelményeinek.
4. [Felülvizsgálat](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) ügynök nélküli migrálást készülék követelményei.]
5. Megjegyzés: készülék [URL-hozzáférés](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) és [hozzáférési port](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) ügynök nélküli kivételfigyelés áttelepítésre vonatkozó követelmények.


## <a name="prepare-for-agent-based-vmware-migration"></a>VMware-alapú ügynök migrálásának előkészítése

Tekintse át a VMware virtuális gépek áttelepítéséhez az ügynök-alapú.

1. [Felülvizsgálat](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) ügynök nélküli migrálást VMware-kiszolgáló követelményeinek. 
2. Állítsa be a fiókot a vCenter Server eléréséhez a a [szükséges engedélyek](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions) az ügynök nélküli migrálást.
3. [Megjegyzés:](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) az ügynök-alapú áttelepítés, beleértve a telepíteni a mobilitási használatával Azure-bA migrálni kívánt VMware virtuális gépek követelményei szolgáltatást minden egyes áttelepíteni kívánt virtuális Gépet.
4. Megjegyzés: [URL-hozzáférés](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Felülvizsgálat [hozzáférési port](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) követelmények az egyes virtuális Gépeken futó mobilitási szolgáltatás, valamint az Azure Migrate konfigurációs kiszolgáló.

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:
 
> [!div class="checklist"] 
> * Állítsa be az Azure-engedélyeket.
> * Előkészített VMware elemzésekhez és migráláshoz.


A második oktatóanyagban beállítása az Azure Migrate-projektet, és felmérheti a VMware virtuális gépek az Azure-ba való migrálásra.

> [!div class="nextstepaction"] 
> [VMware virtuális gépek értékelése](./tutorial-migrate-vmware.md) 


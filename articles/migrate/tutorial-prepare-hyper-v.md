---
title: Hyper-V virtuális gépek előkészítése elemzésekhez és migráláshoz az Azure-bA az Azure Migrate |} A Microsoft Docs
description: Ismerteti, hogyan készíti elő az elemzésekhez és migráláshoz Hyper-V virtuális gépek Azure-bA az Azure Migrate használatával.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e0d29770aa36f8e79bf08b7c5435ea2dbc4ae38
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840372"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Elemzésekhez és a Hyper-V virtuális gépek az Azure-ba való migrálásának előkészítése

Ez a cikk bemutatja, hogyan készíti elő az elemzésekhez és a helyszíni Hyper-V virtuális gépeket az Azure-ba való migrálásának [Azure Migrate](migrate-services-overview.md).

[Az Azure Migrate](migrate-overview.md) hub eszközöket tartalmazza, amelyek segítséget nyújtanak a felderítése, értékelheti és migrálhatja az alkalmazásokat, az infrastruktúra és a számítási feladatok a Microsoft Azure biztosít. A hub tartalmaz, az Azure Migrate eszközökkel és külső független szoftverszállítók (ISV) szállító ajánlatok. 

Ez az oktatóanyag egy sorozat, amely bemutatja, hogyan mérje fel, és a Hyper-V virtuális gépek áttelepítése az Azure-bA az első olyan. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure előkészítése. Állítsa be az Azure-fiók és -erőforrások engedélyeit az Azure Migrate működjön.
> * Készítse elő a helyszíni Hyper-V gazdagépek és virtuális gépek server assessment.
> * Készítse elő a helyszíni Hyper-V gazdagépek és virtuális gépek áttelepítése.


> [!NOTE]
> Az oktatóprogramok bemutatják, a legegyszerűbb telepítési út forgatókönyv esetén, hogy gyorsan beállíthat egy proof-of-concept. Az oktatóanyagok lehetőség szerint használja az alapértelmezett, és ne jelenjen meg az összes lehetséges beállítások és elérési út. Részletes útmutatásért tekintse át a hogyan Tos Hyper-V elemzésekhez és migráláshoz.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prepare-azure"></a>Az Azure előkészítése

### <a name="azure-permissions"></a>Azure-engedélyeket

Engedélyek üzembe helyezéséhez az Azure Migrate néhány lesz szüksége:

- Az Azure-fiók létrehozása az Azure Migrate-projekt elemzésekhez és migráláshoz engedélyre. 
- Az Azure-fiókjával regisztráljon az Azure Migrate készülék engedélyre.
    - Értékelés az Azure Migrate fut egy egyszerűsített berendezés, amely felderíti a Hyper-V virtuális gépeket, és a virtuális gép metaadatainak és a teljesítmény adatokat küld az Azure Migrate.
    - Készülék regisztráció során az Azure Migrate létrehoz két Azure Active Directory (Azure AD-) alkalmazások, a készülék egyedi azonosítására alkalmas:
        - Az első alkalmazás Szolgáltatásvégpontok az Azure Migrate kommunikál.
        - A második alkalmazás hozzáfér az Azure Key Vault regisztrációja során létrehozott Azure AD alkalmazás adatai, valamint a berendezés konfigurációs beállítások tárolásához.
    - Ezek a következő módszerek egyikével Azure AD-alkalmazások létrehozása az Azure Migrate engedélyeket rendelhet:
        - Egy bérlő globális rendszergazdai engedélyek megadásához a bérlő létrehozására és regisztrálására az Azure AD-alkalmazások a felhasználók számára.
        - A bérlő globális rendszergazdája rendelhet a fiók a alkalmazásfejlesztő szerepkör (amely engedélyekkel rendelkezik).
    - Érdemes megjegyezni, hogy:
        - Az alkalmazások nem tartoznak semmilyen más hozzáférési engedélyt a fent leírtaktól eltérő előfizetés.
        - Amikor regisztrál egy új készülék csak kell ezeket az engedélyeket. Az engedélyek a készülék beállítását követő távolíthatja el. 


### <a name="assign-permissions-to-create-project"></a>Rendelje hozzá a projekt létrehozásához szükséges engedélyek

Ellenőrizze, hogy Ön jogosult az Azure Migrate-projekt létrehozása.

1. Az Azure Portalon nyissa meg az előfizetést, és válassza ki **hozzáférés-vezérlés (IAM)** .
2. A **hozzáférés ellenőrzése**, keresse meg a megfelelő fiók és engedélyek megtekintéséhez kattintson rá.
3. Rendelkeznie kell **közreműködői** vagy **tulajdonosa** engedélyeket.
    - Ha az imént létrehozott ingyenes Azure-fiókra, Ön az előfizetés tulajdonosa.
    - Ha Ön nem az előfizetés tulajdonosa, együttműködve rendelje hozzá a szerepkört a tulajdonosa.


### <a name="assign-permissions-to-register-the-appliance"></a>Adja meg a berendezés regisztráljon

Ha az Azure Migrate készülék annak ellenőrzéséhez, virtuális gépeket helyezi üzembe, kell regisztrálnia kell.

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

A bérlő globális rendszergazdája az engedélyeket a következő:

1. Az Azure AD-ben a bérlő globális rendszergazdája kell lépjen **Azure Active Directory** > **felhasználók** > **felhasználói beállítások**.
2. A rendszergazdának kell beállítania **alkalmazásregisztrációk** való **Igen**.

    ![Az Azure AD-engedélyekről](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Ez a nem bizalmas alapértelmezett beállítás. [További információk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztője szerepkör hozzárendelése 

A bérlő globális rendszergazdája a alkalmazásfejlesztő szerepkört rendelhet egy fiókot. [További információk](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-hyper-v-assessment"></a>Felkészülés a Hyper-V-értékelés

Készítse elő a Hyper-V értékeléséhez, a Hyper-V-gazdagép és a virtuális gép beállításainak ellenőrzése és a berendezés üzembe helyezési beállítások ellenőrzése.

### <a name="verify-hyper-v-host-settings"></a>Ellenőrizze a Hyper-V gazdagép beállításai

1. Győződjön meg arról [Hyper-V gazdagép követelmények](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) kiszolgáló értékeléshez.
2. Győződjön meg arról, hogy a [szükséges portok](migrate-support-matrix-hyper-v.md#assessment-port-requirements) Hyper-V-gazdagépek meg nyitva.

### <a name="enable-powershell-remoting-on-hosts"></a>A gazdagépeken PowerShell-távelérés engedélyezése

Az alábbiak szerint állíthatja PowerShell távoli eljáráshívás minden állomáson:

1. Minden gazdagépre nyisson meg egy PowerShell-konzolt rendszergazdaként.
2. Futtassa ezt a parancsot:

    ```
    Enable-PSRemoting -force
    ```

### <a name="enable-credssp-on-hosts"></a>Engedélyeznie kell a CredSSP-gazdagépeken

Ha Virtuálisgép-lemezek SMB-megosztásokban lévő, végezze el ezt a lépést minden releváns Hyper-V gazdagépen. Ebben a lépésben konfigurációs adatait a Hyper-V virtuális gépek lemezek az SMB-megosztások felderítésére szolgál. Ha nem rendelkezik Virtuálisgép-lemezek az SMB-megosztások, kihagyhatja a lépést.

1. Azonosítsa a Hyper-V-gazdagépeken futó Hyper-V virtuális gépek lemezek SMB-megosztások.
2. Futtassa a következő parancsot az egyes azonosított Hyper-V-gazdagép:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

- CredSSP-alapú hitelesítés lehetővé teszi, hogy a Hyper-V gazdagép delegálása az Azure Migrate-ügyfél nevében legyenek hitelesítő adatok.
- Ez a parancs távolról is futtathat a Hyper-V-gazdagépek.
- Új gazdagép-csomópontok hozzáadásakor egy fürtön a rendszer automatikusan felveszi a felderítése, de manuálisan engedélyeznie kell a CredSSP az új csomóponton szükség esetén kell.

### <a name="verify-appliance-settings"></a>Készülék-beállítások ellenőrzése

A készülék az Azure Migrate és kezdő értékelése a következő oktatóanyagban beállítása előtt készítse elő a berendezés üzembe helyezéshez.

1. [Győződjön meg arról](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) készülék követelményeinek.
2. [Felülvizsgálat](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) az Azure URL-címeket, amelyeket a készülék kell hozzáférni.
3. Tekintse át az adatokat, hogy a berendezés gyűjt felderítés és értékelés során.
4. [Megjegyzés:](migrate-support-matrix-hyper-v.md#assessment-port-requirements) port a készülék való hozzáférés feltételeit.


### <a name="set-up-an-account-for-vm-discovery"></a>A virtuális gépek felderítésének fiók beállítása

Az Azure Migrate engedélyre van szüksége a helyszíni virtuális gépek felderítéséhez.

- Állítsa be a tartomány vagy helyi felhasználói fiók rendszergazdai jogosultságokkal a Hyper-V gazdagép/fürt.

    - Egy olyan fiók valamennyi gazdagépére és fürtjére, amelyeket szeretne szerepeltetni a felderítés szükség van.
    - A fiók egy helyi vagy tartományi fiók is lehet. Azt javasoljuk, hogy a Hyper-V-gazdagépek és fürtök rendszergazdai engedélyekkel rendelkezik.
    - Azt is megteheti Ha nem szeretne rendszergazdai engedélyeket, a következő engedélyekre van szükség:
        - Rendszerfelügyeleti felhasználók
        - A Hyper-V-rendszergazdák
        - Teljesítményfigyelő felhasználók

### <a name="enable-integration-services-on-vms"></a>Integrációs szolgáltatásokat minden egyes virtuális gépek engedélyezése

Az integrációs szolgáltatások szabad engedélyezni az egyes virtuális Gépeken, hogy az Azure Migrate rögzítheti az operációs rendszer adatainak a virtuális gépen.

- A virtuális gépeken, amelyeket szeretne felderítéséhez és értékeléséhez, engedélyezéséhez [Hyper-V integrációs szolgáltatásokat](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) az egyes virtuális Gépeken. 

## <a name="prepare-for-hyper-v-migration"></a>Hyper-V áttelepítés előkészítése

1. [Felülvizsgálat](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) áttelepítési Hyper-V gazdagép-követelményei.
2. [Felülvizsgálat](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) , amely az Azure-bA migrálni kívánt Hyper-V virtuális gépek követelményeinek.
3. [Megjegyzés:](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) melyik Hyper-V-gazdagépek és fürtök hozzáférés szükséges a virtuális gépek migrálása Azure URL-címeket.

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:
 
> [!div class="checklist"] 
> * Azure-fiók engedélyek beállítása.
> * Előkészített Hyper-V-gazdagépek és virtuális gépek elemzésekhez és migráláshoz.

Továbbra is az Azure Migrate-projekt létrehozása a következő oktatóanyaggal, és a Hyper-V virtuális gépek értékelése az Azure-ba való migráláshoz

> [!div class="nextstepaction"] 
> [A Hyper-V virtuális gépek értékelése](./tutorial-assess-hyper-v.md) 

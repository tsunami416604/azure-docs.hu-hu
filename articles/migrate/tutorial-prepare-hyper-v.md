---
title: Hyper-V virtuális gépek előkészítése az Azure Migrate szolgáltatással az értékelésre/áttelepítésre
description: Ismerje meg, hogyan készülhet fel a Hyper-V virtuális gépek értékelésére/áttelepítésére az Azure Migrate szolgáltatással.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: e86e634812e9f2782a003a11b48cfecf91d95359
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535298"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Felkészülés a Hyper-V virtuális gépek Azure-ba való értékelésére és áttelepítésére

Ez a cikk bemutatja, hogyan készülhet fel a helyszíni Hyper-V virtuális gépek értékelésére az Azure Migrate:Server Assessment(áttelepítés-szolgáltatások áttekintése.md#azure-migrate-server-assessment-tool), valamint a Hyper-V virtuális gépek áttelepítése az [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool)segítségével.


Ez az oktatóanyag az első egy sorozatban, amely bemutatja, hogyan értékelheti és telepítheti át a Hyper-V virtuális gépeket az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő az Azure-t. Állítsa be az Azure-fiókjához és az Azure Migrate szolgáltatással való együttműködésre szolgáló erőforrások engedélyeit.
> * Készítse elő a helyszíni Hyper-V-állomásokat és a virtuális gépeket a kiszolgáló kiértékeléséhez. Előkészítheti egy konfigurációs parancsfájl használatával, vagy manuálisan.
> * Előkészítse az Azure Migrate-berendezés üzembe helyezését. A készülék helyszíni virtuális gépek felderítésére és értékelésére szolgál.
> * Készítse elő a helyszíni Hyper-V-állomásokat és virtuális gépeket a kiszolgáló áttelepítéséhez.


> [!NOTE]
> Az oktatóanyagok a forgatókönyv legegyszerűbb telepítési útvonalát mutatják be, így gyorsan beállíthat egy proof-of-concept-et. Az oktatóanyagok lehetőség szerint az alapértelmezett beállításokat használják, és nem jelenítik meg az összes lehetséges beállítást és elérési utat. Részletes utasításokért tekintse át a Hyper-V értékelésének és áttelepítésének útmutatóit.


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/pricing/free-trial/) mielőtt elkezdené.


## <a name="prepare-azure"></a>Az Azure előkészítése

### <a name="azure-permissions"></a>Azure-engedélyek

Be kell állítania az Azure Migrate üzembe helyezéséhez szükséges engedélyeket.

**Tevékenység** | **Részletek** 
--- | --- 
**Azure Migrate projekt létrehozása** | Az Azure-fiókjának közreműködői vagy tulajdonosi engedélyekre van szüksége a projekt létrehozásához. | 
**Erőforrás-szolgáltatók regisztrálása** | Az Azure Migrate egy könnyű Azure Migrate készüléket használ a Hyper-V virtuális gépek felderítéséhez és értékeléséhez az Azure Migrate Server Assessment segítségével.<br/><br/> A készülék regisztrációja során az erőforrás-szolgáltatók regisztrálva vannak a készülékben kiválasztott előfizetéssel. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Az erőforrás-szolgáltatók regisztrálásához közreműködői vagy tulajdonosi szerepkörre van szüksége az előfizetéshez.
**Azure AD alkalmazás létrehozása** | A készülék regisztrálásakor az Azure Migrate létrehoz egy Azure Active Directory (Azure AD) alkalmazást, amely a készüléken futó ügynökök közötti kommunikációra szolgál az Azure-on futó megfelelő szolgáltatásokkal. [További információ](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Engedélyeket kell létrehoznia az Azure AD-alkalmazások (az alkalmazásfejlesztői szerepkörben érhető el) létrehozásához.



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


## <a name="prepare-hyper-v-for-assessment"></a>Hyper-V előkészítése az értékelésre

A Hyper-V-t manuálisan vagy konfigurációs parancsfájl használatával készítheti elő a virtuális gép értékeléséhez. Az előkészítési lépések a következők:
- [Ellenőrzés](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) Hyper-V állomás beállításokat, és győződjön meg arról, hogy a [szükséges portok](migrate-support-matrix-hyper-v.md#port-access) meg vannak nyitva a Hyper-V gazdagépeken.
- Állítsa be a PowerShell-szolgáltatás minden állomáson, hogy az Azure Migrate-berendezés futtathassa a PowerShell-parancsokat az állomáson egy WinRM-kapcsolaton keresztül.
- Delegálja a hitelesítő adatokat, ha a virtuálisgép-lemezek távoli SMB-megosztásokon találhatók.
- Hozzon létre egy fiókot, amelyet a készülék a Hyper-V gazdagépeken lévő virtuális gépek felderítésére használ.
- Hyper-V integrációs szolgáltatások beállítása minden virtuális gép felszeretné deríteni és értékelni. Az integrációs szolgáltatások engedélyezéséhez az alapértelmezett beállítások elegendőek az Azure Migrate számára.

    ![Integrációs szolgáltatások engedélyezése](./media/tutorial-prepare-hyper-v/integrated-services.png)


## <a name="prepare-with-a-script"></a>Felkészülés parancsfájllal

A parancsfájl a következőket teszi:

- Ellenőrzi, hogy a parancsfájlt egy támogatott PowerShell-verzión futtatja-e.
- Ellenőrzi, hogy Ön (a parancsfájlt futtató felhasználó) rendelkezik-e rendszergazdai jogosultságokkal a Hyper-V gazdagépen.
- Lehetővé teszi, hogy hozzon létre egy helyi felhasználói fiókot (nem rendszergazda), amely az Azure Migrate szolgáltatás segítségével kommunikál a Hyper-V gazdagép. Ez a felhasználói fiók hozzáadódik ezekhez a csoportokhoz az állomáson:
    - Rendszerfelügyeleti felhasználók
    - Hyper-V-rendszergazdák
    - Teljesítményfigyelő felhasználói
- Ellenőrzi, hogy az állomás a Hyper-V és a Hyper-V szerepkör támogatott verzióját futtatja-e.
- Engedélyezi a WinRM-szolgáltatást, és megnyitja az 5985 -es (HTTP) és az 5986-os (HTTPS) portokat az állomáson (metaadat-gyűjtéshez szükséges).
- Engedélyezi a PowerShell-távírást az állomáson.
- Ellenőrzi, hogy a Hyper-V integrációs szolgáltatások engedélyezve vannak-e az állomás által kezelt összes virtuális gépen.
- Szükség esetén engedélyezi a CredSSP-t az állomáson.

Futtassa a parancsfájlt az alábbiak szerint:

1. Győződjön meg arról, hogy a PowerShell 4.0-s vagy újabb verzióját telepítette a Hyper-V gazdagépen.
2. Töltse le a parancsfájlt a [Microsoft letöltőközpontjából.](https://aka.ms/migrate/script/hyperv) A parancsfájlt a Microsoft kriptográfiailag írta alá.
3. Ellenőrizze a parancsfájl sértetlenségét Az MD5 vagy az SHA256 kivonatfájlok használatával. A hashtag értékek az alábbiakban találhatók. Futtassa ezt a parancsot a parancsfájl kivonatának létrehozásához:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Példa használat:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.    A parancsfájl integritásának érvényesítése után futtassa a parancsfájlt minden Hyper-V állomáson ezzel a PowerShell paranccsal:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Hashtag értékek

A kivonatértékek a következők:

| **Hash** | **Érték** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-manually"></a>Manuális előkészítés

Kövesse az ebben a szakaszban a Hyper-V manuális előkészítéséhez a parancsfájl használata helyett.

### <a name="verify-powershell-version"></a>A PowerShell-verzió ellenőrzése

Győződjön meg arról, hogy a PowerShell 4.0-s vagy újabb verzióját telepítette a Hyper-V gazdagépen.



### <a name="set-up-an-account-for-vm-discovery"></a>Fiók beállítása a virtuális gép felderítéséhez

Az Azure Migrate-nek engedélyekre van szüksége a helyszíni virtuális gépek felderítéséhez.

- Hozzon létre egy rendszergazdai engedélyekkel rendelkező tartományt vagy helyi felhasználói fiókot a Hyper-V állomásokon/fürtön.

    - A felderítésbe felvenni kívánt összes állomáshoz és fürthöz egyetlen fiókra van szükség.
    - A fiók lehet helyi vagy tartományi fiók. Azt javasoljuk, hogy rendszergazdai engedélyekkel rendelkezik a Hyper-V gazdagépeken vagy fürtökön.
    - Ha nem szeretne rendszergazdai engedélyeket hozzárendelni, a következő engedélyekre van szükség:
        - Rendszerfelügyeleti felhasználók
        - Hyper-V-rendszergazdák
        - Teljesítményfigyelő felhasználói

### <a name="verify-hyper-v-host-settings"></a>Hyper-V állomásbeállítások ellenőrzése

1. Ellenőrizze a [Hyper-V állomás követelményeit](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) a kiszolgáló értékeléséhez.
2. Győződjön meg arról, hogy a [szükséges portok](migrate-support-matrix-hyper-v.md#port-access) meg vannak nyitva a Hyper-V állomásokon.

### <a name="enable-powershell-remoting-on-hosts"></a>PowerShell-táv-és félreélők engedélyezése a gazdagépeken

Állítsa be a PowerShell-távadó-műveleteket az egyes gazdagépen az alábbiak szerint:

1. Minden gazdagépen nyisson meg egy PowerShell-konzolt rendszergazdaként.
2. Futtassa ezt a parancsot:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Integrációs szolgáltatások engedélyezése virtuális gépeken

Az integrációs szolgáltatásokat minden virtuális gépen engedélyezni kell, hogy az Azure Migrate rögzíthesse az operációs rendszer adatait a virtuális gépen.

A felderíteni és értékelni kívánt virtuális gépeken engedélyezze a [Hyper-V integrációs szolgáltatásokat](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) minden virtuális gépen.


### <a name="enable-credssp-on-hosts"></a>CredSSP engedélyezése állomásokon

Ha az állomás rendelkezik lemezekkel rendelkező virtuális gépekkel, az SMB-megosztásokon található, hajtsa végre ezt a lépést az állomáson.

- Ezt a parancsot távolról is futtathatja az összes Hyper-V gazdaszámítógépen.
- Ha új állomáscsomópontokat ad hozzá egy fürthöz, azok automatikusan hozzáadódnak a felderítéshez, de szükség esetén manuálisan kell engedélyeznie a CredSSP-t az új csomópontokon.

Engedélyezze az alábbiak szerint:

1. Azonosítsa a Hyper-V virtuális gépeket futtató Hyper-V virtuális gépeket sMB-megosztásokon lévő lemezekkel.
2. Futtassa a következő parancsot minden egyes azonosított Hyper-V állomáson:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

A készülék beállításakor a CredSSP beállítását úgy kell befejezni, [hogy engedélyezi azt a készüléken](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Ezt a sorozat következő oktatóanyaga ismerteti.


## <a name="prepare-for-appliance-deployment"></a>Felkészülés a berendezés telepítésére

Az Azure Migrate készülék beállítása előtt, és a következő oktatóanyagban megkezdi a kiértékelést, készüljön fel a berendezés üzembe helyezésére.

1. A készülék követelményeinek [ellenőrzése.](migrate-appliance.md#appliance---hyper-v)
2. Tekintse át az Azure URL-címeket, amelyeket a készüléknek a [nyilvános](migrate-appliance.md#public-cloud-urls) [és](migrate-appliance.md#government-cloud-urls) kormányzati felhőkben kell elérnie. HA URL-alapú tűzfalat vagy proxyt használ, győződjön meg arról, hogy engedélyezi a hozzáférést a szükséges URL-címekhez.
3. Tekintse át a készülék által a felderítés és az értékelés során gyűjtött adatokat.
4. [Tekintse át](migrate-appliance.md#collected-data---hyper-v) a készülék porthozzáférési követelményeit.




## <a name="prepare-for-hyper-v-migration"></a>Felkészülés a Hyper-V áttelepítésre

1. [Véleményezés](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) Hyper-V állomás követelmények áttelepítése, és az Azure URL-címek, amelyekhez a Hyper-V-állomások és -fürtök hozzáférést kell biztosítani a virtuális gép áttelepítése.
2. [Tekintse át](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) az Azure-ba áttelepíteni kívánt hyper-V virtuális gépek követelményeit.


## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Állítsa be az Azure-fiók engedélyeit.
> * Előkészített Hyper-V gazdagépek és virtuális gépek az értékelés hez és a migráció.
> * Az Azure Migrate készülék üzembe helyezésére előkészítve.

Folytassa a következő oktatóanyaggal egy Azure Migrate-projekt létrehozásához, a készülék üzembe helyezéséhez, valamint a Hyper-V virtuális gépek azure-ba való áttelepítéséhez való felderítéséhez és értékeléséhez.

> [!div class="nextstepaction"]
> [Hyper-V virtuális gépek értékelése](./tutorial-assess-hyper-v.md)

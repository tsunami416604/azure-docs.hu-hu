---
title: Hyper-V virtuális gépek előkészítése értékeléshez/áttelepítéshez Azure Migrate
description: Ismerje meg, hogyan készítheti elő a Hyper-V virtuális gépek értékelését és áttelepítését Azure Migrate használatával.
ms.topic: tutorial
ms.date: 01/01/2020
ms.custom: mvc
ms.openlocfilehash: a76c249f3d179a34fbb14e6c8bfb3666816fa160
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720208"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Felkészülés a Hyper-V virtuális gépek Azure-ba történő értékelésére és áttelepítésére

Ez a cikk bemutatja, hogyan lehet felkészülni a helyszíni Hyper-V virtuális gépeknek az Azure-ba [Azure Migrate](migrate-services-overview.md)használatával történő értékelésére és áttelepítésére.

[Azure Migrate](migrate-overview.md) olyan eszközöket biztosít, amelyek segítségével az alkalmazások, az infrastruktúra és a munkaterhelések felderíthető, mérhetők és áttelepíthetők a Microsoft Azure. A hub Azure Migrate eszközöket és külső gyártótól származó független szoftvergyártó (ISV) ajánlatokat tartalmaz.

Ez az oktatóanyag a Hyper-V virtuális gépek Azure-ba való értékelését és áttelepítését bemutató sorozat első része. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Készítse elő az Azure-t. Állítsa be az Azure-fiókra és-erőforrásokra vonatkozó engedélyeket a Azure Migrate való együttműködéshez.
> * Helyszíni Hyper-V-gazdagépek és virtuális gépek előkészítése a kiszolgálók értékeléséhez. Előkészítheti egy konfigurációs parancsfájlt vagy manuálisan is.
> * Felkészülés az Azure Migrate berendezés üzembe helyezésére. A berendezés a helyszíni virtuális gépek felderítésére és értékelésére szolgál.
> * Helyszíni Hyper-V-gazdagépek és virtuális gépek előkészítése a kiszolgálók áttelepítéséhez.


> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út. Részletes utasításokért tekintse át a Hyper-V felmérési és áttelepítési útmutatóit.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) a virtuális gép létrehozásának megkezdése előtt.


## <a name="prepare-azure"></a>Az Azure előkészítése

### <a name="azure-permissions"></a>Azure-engedélyek

Be kell állítania a Azure Migrate telepítéséhez szükséges engedélyeket.

- Az Azure-fiók engedélyei egy Azure Migrate projekt létrehozásához.
- A fiókra vonatkozó engedélyek a Azure Migrate berendezés regisztrálásához. A berendezést a migrált Hyper-V virtuális gépek felderítésére és értékelésére használjuk. A készülék regisztrálása során Azure Migrate két Azure Active Directory (Azure AD) alkalmazást hoz létre, amelyek egyedileg azonosítják a készüléket:
    - Az első alkalmazás a Azure Migrate szolgáltatási végpontokkal kommunikál.
    - A második alkalmazás a regisztráció során létrehozott Azure Key Vault fér hozzá az Azure AD-alkalmazás adatainak és a készülék konfigurációs beállításainak tárolásához.



### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

Győződjön meg arról, hogy rendelkezik Azure Migrate-projekt létrehozásához szükséges engedélyekkel.

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.


### <a name="assign-permissions-to-register-the-appliance"></a>Engedélyek kiosztása a készülék regisztrálásához

A következő módszerek egyikével hozhat létre engedélyeket a Azure Migrate számára a készülék regisztrálása során létrehozott Azure AD-alkalmazások létrehozásához:

- A bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói számára az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- A bérlői/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik engedélyekkel) a fiókhoz.

Érdemes megjegyezni, hogy:

- Az alkalmazások nem rendelkeznek más hozzáférési engedélyekkel az előfizetéshez a fent leírtak kivételével.
- Az új berendezések regisztrálásához csak ezekre az engedélyekre van szükség. Az engedélyeket a készülék beállítása után távolíthatja el.


#### <a name="grant-account-permissions"></a>Fiók engedélyeinek megadása

A bérlő/globális rendszergazda a következőképpen adhat meg engedélyeket:

1. Az Azure AD-ben a bérlői/globális rendszergazdának **Azure Active Directory** > **felhasználó** > **felhasználói beállításokat**kell megkeresnie.
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** .

    ![Azure AD-engedélyek](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Ez egy alapértelmezett beállítás, amely nem érzékeny. [További információk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása

A bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét egy fiókhoz. [További információk](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-hyper-v-for-assessment"></a>A Hyper-V előkészítése az értékeléshez

A Hyper-V-t manuálisan is előkészítheti a virtuális gépek felméréséhez, vagy konfigurációs parancsfájl használatával. Az alábbiakat kell előkészíteni:

- Ellenőrizze a Hyper-V gazdagép beállításait, és győződjön meg arról, hogy a szükséges portok meg vannak nyitva a Hyper-V-gazdagépeken.
- Állítsa be a PowerShell-távelérést minden gazdagépen, hogy az Azure Migrate készülék PowerShell-parancsokat futtasson a gazdagépen egy WinRM-kapcsolaton keresztül.
- Hitelesítő adatok delegálása, ha a VM-lemezek távoli SMB-megosztásokon találhatók.
- Állítson be egy fiókot, amelyet a berendezés a virtuális gépek felderítésére használ a Hyper-V-gazdagépeken.
- Állítsa be a Hyper-V integrációs szolgáltatásokat minden felderíteni és értékelni kívánt virtuális gépen.



## <a name="prepare-with-a-script"></a>Előkészítés parancsfájlokkal

A szkript a következő műveleteket végzi el:

- Ellenőrzi, hogy a parancsfájlt egy támogatott PowerShell-verzión futtatja-e.
- Ellenőrzi, hogy Ön (a parancsfájlt futtató felhasználó) rendelkezik-e rendszergazdai jogosultságokkal a Hyper-V-gazdagépen.
- Lehetővé teszi, hogy olyan helyi felhasználói fiókot hozzon létre (nem rendszergazda), amelyet a Azure Migrate szolgáltatás a Hyper-V-gazdagépkel való kommunikációhoz használ. Ezt a felhasználói fiókot a rendszer a gazdagépen adja hozzá a következő csoportokhoz:
    - Rendszerfelügyeleti felhasználók
    - Hyper-V-rendszergazdák
    - Teljesítményfigyelő felhasználói
- Ellenőrzi, hogy a gazdagép a Hyper-V támogatott verzióját és a Hyper-V szerepkört futtatja-e.
- Engedélyezi a WinRM szolgáltatást, és megnyitja a 5985 (HTTP) és a 5986 (HTTPS) portot a gazdagépen (a metaadat-gyűjteményhez szükséges).
- Engedélyezi a PowerShell távelérést a gazdagépen.
- Ellenőrzi, hogy a Hyper-V integrációs szolgáltatás engedélyezve van-e a gazdagép által kezelt összes virtuális gépen.
- Szükség esetén engedélyezi a CredSSP a gazdagépen.

Futtassa a szkriptet a következő módon:

1. Győződjön meg arról, hogy a PowerShell 4,0-es vagy újabb verziója telepítve van a Hyper-V-gazdagépen.
2. Töltse le a szkriptet a [Microsoft letöltőközpontból](https://aka.ms/migrate/script/hyperv). A parancsfájlt a Microsoft kriptográfiailag aláírja.
3. Ellenőrizze a parancsfájl integritását MD5 vagy SHA256 kivonatoló fájl használatával. A hashtag-értékek alább láthatók. Futtassa ezt a parancsot a szkript kivonatának létrehozásához:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Példa használata:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1
    SHA256
    ```

4.  A parancsfájl integritásának ellenőrzése után futtassa a parancsfájlt minden Hyper-V-gazdagépen a következő PowerShell-paranccsal:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

### <a name="hashtag-values"></a>Hashtag-értékek

A kivonatoló értékek a következők:

| **Kivonat** | **Érték** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |


## <a name="prepare-hyper-v-manually"></a>A Hyper-V manuális előkészítése

Kövesse az ebben a szakaszban ismertetett eljárásokat a Hyper-V manuális előkészítéséhez a parancsfájl használata helyett.

### <a name="verify-powershell-version"></a>PowerShell-verzió ellenőrzése

Győződjön meg arról, hogy a PowerShell 4,0-es vagy újabb verziója telepítve van a Hyper-V-gazdagépen.



### <a name="set-up-an-account-for-vm-discovery"></a>Fiók beállítása a virtuális gép felderítéséhez

Azure Migrate a helyszíni virtuális gépek felderítéséhez szükséges engedélyekkel.

- Hozzon létre egy tartományi vagy helyi felhasználói fiókot rendszergazdai engedélyekkel a Hyper-V-gazdagépeken/-fürtön.

    - A felderítésbe felvenni kívánt gazdagépekhez és fürtökhöz egyetlen fiókra van szükség.
    - A fiók lehet helyi vagy tartományi fiók is. Azt javasoljuk, hogy rendszergazdai engedélyekkel rendelkezik a Hyper-V-gazdagépeken vagy-fürtökön.
    - Ha nem szeretne rendszergazdai engedélyeket rendelni, akkor a következő engedélyek szükségesek:
        - Rendszerfelügyeleti felhasználók
        - Hyper-V-rendszergazdák
        - Teljesítményfigyelő felhasználói

### <a name="verify-hyper-v-host-settings"></a>Hyper-V gazdagép beállításainak ellenőrzése

1. Ellenőrizze a [Hyper-V gazdagépre vonatkozó követelményeket](migrate-support-matrix-hyper-v.md#assessment-hyper-v-host-requirements) a kiszolgálók értékeléséhez.
2. Győződjön meg arról, hogy a [szükséges portok](migrate-support-matrix-hyper-v.md#assessment-port-requirements) meg vannak nyitva a Hyper-V-gazdagépeken.

### <a name="enable-powershell-remoting-on-hosts"></a>A PowerShell távelérés engedélyezése a gazdagépeken

Állítsa be a PowerShell-távelérést az egyes gazdagépeken a következők szerint:

1. Minden gazdagépen nyisson meg egy PowerShell-konzolt rendszergazdaként.
2. Futtassa ezt a parancsot:

    ```
    Enable-PSRemoting -force
    ```
### <a name="enable-integration-services-on-vms"></a>Integrációs szolgáltatások engedélyezése a virtuális gépeken

Az integrációs szolgáltatásokat minden virtuális gépen engedélyezni kell, hogy Azure Migrate az operációs rendszer adatait rögzítse a virtuális gépen.

A felderíteni és értékelni kívánt virtuális gépeken engedélyezze a [Hyper-V integrációs szolgáltatásokat](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) minden egyes virtuális gépen.


### <a name="enable-credssp-on-hosts"></a>CredSSP engedélyezése a gazdagépeken

Ha a gazdagépen a lemezekkel rendelkező virtuális gépek találhatók az SMB-megosztásokon, hajtsa végre ezt a lépést a gazdagépen.

- Ezt a parancsot távolról is futtathatja az összes Hyper-V-gazdagépen.
- Ha új gazdagép-csomópontokat ad hozzá egy fürthöz, azokat a rendszer automatikusan hozzáadja a felderítéshez, de szükség esetén manuálisan kell engedélyeznie a CredSSP az új csomópontokon.

Engedélyezés a következőképpen:

1. Azonosítsa a Hyper-V virtuális gépeket futtató Hyper-v-gazdagépeket az SMB-megosztásokon lévő lemezekkel.
2. Futtassa az alábbi parancsot minden azonosított Hyper-V-gazdagépen:

    ```
    Enable-WSManCredSSP -Role Server -Force
    ```

A készülék beállításakor a CredSSP beállításával állíthatja be a [készüléket](tutorial-assess-hyper-v.md#delegate-credentials-for-smb-vhds). Ezt a sorozatot a következő oktatóanyag ismerteti.


## <a name="prepare-for-appliance-deployment"></a>Felkészülés a berendezések üzembe helyezésére

A Azure Migrate berendezés beállítása és az értékelés megkezdése előtt a következő oktatóanyagban készítse elő a berendezés üzembe helyezését.

1. A berendezésre vonatkozó követelmények [ellenőrzése](migrate-support-matrix-hyper-v.md#assessment-appliance-requirements) .
2. [Tekintse át](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie.
3. Tekintse át a berendezés által a felderítés és az értékelés során gyűjtött adatokat.
4. [Jegyezze](migrate-support-matrix-hyper-v.md#assessment-port-requirements) fel a port hozzáférési követelményeit a készülékhez.


## <a name="prepare-for-hyper-v-migration"></a>Felkészülés a Hyper-V áttelepítésére

1. [Tekintse át](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-requirements) A Hyper-V gazdagépre vonatkozó követelmények az áttelepítéshez.
2. [Tekintse át](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) az Azure-ba áttelepíteni kívánt Hyper-V virtuális gépekre vonatkozó követelményeket.
3. [Jegyezze](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access) fel azokat az Azure URL-címeket, amelyekhez a Hyper-V-gazdagépeknek és-fürtöknek hozzá kell férniük a virtuális gépek

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure-fiók engedélyeinek beállítása.
> * Előkészített Hyper-V-gazdagépek és virtuális gépek az értékeléshez és az áttelepítéshez.
> * Készen áll a Azure Migrate berendezés üzembe helyezésére.

Folytassa a következő oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, üzembe helyezi a készüléket, és felderíti és felméri a Hyper-V virtuális gépeket az Azure-ba való áttelepítéshez.

> [!div class="nextstepaction"]
> [Hyper-V virtuális gépek felmérése](./tutorial-assess-hyper-v.md)

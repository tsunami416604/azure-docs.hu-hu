---
title: Hyper-V virtuális gépek előkészítése értékeléshez/áttelepítéshez Azure Migrate
description: Ismerje meg, hogyan készítheti elő a Hyper-V virtuális gépek értékelését és áttelepítését Azure Migrate használatával.
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: 5f669de6bd8d767ca7b947fca883187dad9fe29d
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86109620"
---
# <a name="prepare-for-assessment-and-migration-of-hyper-v-vms-to-azure"></a>Felkészülés a Hyper-V virtuális gépek Azure-ba történő értékelésére és áttelepítésére

Ez a cikk segít felkészülni a helyszíni Hyper-V virtuális gépek Azure-ba történő értékelésére és áttelepítésére [Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool)és [Azure Migrate: Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool)használatával.


Ez az oktatóanyag a Hyper-V virtuális gépek Azure-ba való értékelését és áttelepítését bemutató sorozat első része. Az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Készítse elő az Azure-t a Azure Migrate való együttműködéshez.
> * Felkészülés a Hyper-V virtuális gépek értékelésére.
> * A Hyper-V virtuális gépek áttelepítésének előkészítése 

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Az oktatóanyagok az alapértelmezett beállításokat használják, ahol lehetséges, és nem jelennek meg az összes lehetséges beállítás és elérési út.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prepare-azure"></a>Az Azure előkészítése

A táblázat összefoglalja az Azure-ban elvégzendő feladatokat. Utasításokat követve a táblázat.

**Tevékenység** | **Részletek** | **Engedélyek**
--- | --- | ---
**Azure Migrate projekt létrehozása** | Az Azure Migrate projektek központi helyet biztosítanak a felmérések és áttelepítések előkészítéséhez és kezeléséhez Azure Migrate eszközökkel, Microsoft-eszközökkel és harmadik féltől származó ajánlatokkal. | Az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie abban az erőforráscsoportban, amelyben a projekt található.
**Berendezés regisztrálása** | A Azure Migrate a Hyper-V virtuális gépek felderítésére és értékelésére egy könnyű Azure Migrate berendezést használ. [További információk](migrate-appliance-architecture.md#appliance-registration). | A készülék regisztrálásához az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie az Azure-előfizetésben.
**Azure AD alkalmazás létrehozása** | A készülék regisztrálása során Azure Migrate létrehoz egy Azure Active Directory (Azure AD) alkalmazást, amelyet a készüléken futó ügynökök és a Azure Migrate között használ a kommunikációhoz. | Az Azure-fióknak rendelkeznie kell az Azure AD-alkalmazások létrehozásához szükséges engedélyekkel.
**Virtuális gép létrehozása** | Az erőforráscsoport és a virtuális hálózat létrehozásához, valamint az Azure-beli felügyelt lemezre való íráshoz engedélyek szükségesek. | Az Azure-fióknak szüksége van a virtuális gépi közreműködő szerepkörre.


### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához

Győződjön meg arról, hogy rendelkezik Azure Migrate-projekt létrehozásához szükséges engedélyekkel.

1. A Azure Portal nyissa meg az előfizetést, és válassza a **hozzáférés-vezérlés (iam)** lehetőséget.
2. A **hozzáférés engedélyezése**területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. **Közreműködői** vagy **tulajdonosi** engedélyekkel kell rendelkeznie.
    - Ha csak az ingyenes Azure-fiókot hozta létre, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal társítsa a szerepkört.


### <a name="assign-permissions-to-create-azure-ad-apps"></a>Engedélyek kiosztása Azure AD-alkalmazások létrehozásához

Az Azure AD-alkalmazás létrehozásához az alábbi módszerek egyikének használatával rendelhet engedélyeket Azure Migrate számára:

- A bérlők/globális rendszergazdák engedélyeket adhatnak a bérlő felhasználói számára az Azure AD-alkalmazások létrehozásához és regisztrálásához.
- A bérlői/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét (amely rendelkezik engedélyekkel) a fiókhoz.

> [!NOTE]
> - Az alkalmazás nem rendelkezik más hozzáférési engedélyekkel az előfizetéshez a fent leírtak kivételével.
> - Az új berendezések regisztrálásához csak ezekre az engedélyekre van szükség. Az engedélyeket a készülék beállítása után távolíthatja el.


#### <a name="grant-account-permissions"></a>Fiók engedélyeinek megadása

A bérlő/globális rendszergazda a következőképpen adhat meg engedélyeket:

1. Az Azure ad-ben a bérlői/globális rendszergazdának meg kell keresnie **Azure Active Directory**  >  **felhasználók**  >  **felhasználói beállításait**.
2. A rendszergazdának az **Igen**értékre kell állítania a **Alkalmazásregisztrációk** .

    ![Azure AD-engedélyek](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Ez egy alapértelmezett beállítás, amely nem érzékeny. [További információk](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).



#### <a name="assign-application-developer-role"></a>Alkalmazás fejlesztői szerepkörének kiosztása

A bérlő/globális rendszergazda hozzárendelheti az alkalmazás fejlesztői szerepkörét egy fiókhoz. [További információk](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md).

### <a name="assign-azure-account-permissions"></a>Azure-fiók engedélyeinek kiosztása

Rendelje hozzá a virtuális gépi közreműködő szerepkört a fiókhoz, hogy rendelkezzen a következő engedélyekkel:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás egy Azure-beli felügyelt lemezre. 


### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

[Hozzon létre egy Azure-hálózatot](../virtual-network/manage-virtual-network.md#create-a-virtual-network). A helyszíni gépek replikálódnak az Azure Managed Disks szolgáltatásba. Az Azure-ba történő áttelepítéskor az Azure-beli virtuális gépek ezekből a felügyelt lemezekről jönnek létre, és a beállított Azure-hálózathoz csatlakoznak.


## <a name="prepare-for-assessment"></a>Felkészülés az értékelésre

A Hyper-V-t manuálisan is előkészítheti a virtuális gépek felméréséhez, vagy konfigurációs parancsfájl használatával. Ezek az előkészítési lépések. Ha parancsfájlt készít elő, ezeket a rendszer automatikusan konfigurálja.

**Lépés** | **Parancsfájl** | **Kézi**
--- | --- | ---
**A Hyper-V gazdagépre vonatkozó követelmények ellenőrzése** | A parancsfájl ellenőrzi, hogy a gazdagép a Hyper-V támogatott verzióját és a Hyper-V szerepkört futtatja-e.<br/><br/> Engedélyezi a WinRM szolgáltatást, és megnyitja a 5985 (HTTP) és a 5986 (HTTPS) portot a gazdagépen (a metaadat-gyűjteményhez szükséges). | Ellenőrizze a [Hyper-V gazdagépre vonatkozó követelményeket](migrate-support-matrix-hyper-v.md#hyper-v-host-requirements) a kiszolgálók értékeléséhez.<br/><br/> Győződjön meg arról, hogy a [szükséges portok](migrate-support-matrix-hyper-v.md#port-access) meg vannak nyitva a Hyper-V-gazdagépeken.
**PowerShell-verzió ellenőrzése** | Ellenőrzi, hogy a parancsfájlt egy támogatott PowerShell-verzión futtatja-e. | Győződjön meg arról, hogy a PowerShell 4,0-es vagy újabb verzióját futtatja a Hyper-V-gazdagépen.
**Fiók létrehozása** | Ellenőrzi, hogy Ön (a parancsfájlt futtató felhasználó) rendelkezik-e rendszergazdai jogosultságokkal a Hyper-V-gazdagépen.<br/><br/>  Lehetővé teszi, hogy olyan helyi felhasználói fiókot hozzon létre (nem rendszergazda), amelyet a Azure Migrate szolgáltatás a Hyper-V-gazdagépkel való kommunikációhoz használ. Ezt a felhasználói fiókot a rendszer a gazdagépen adja hozzá a következő csoportokhoz:<br/><br/> -Távfelügyeleti felhasználók<br/><br/> -Hyper-V-rendszergazdák<br/><br/>– Teljesítményfigyelő felhasználók | Hozzon létre egy tartományi vagy helyi felhasználói fiókot rendszergazdai engedélyekkel a Hyper-V-gazdagépeken/-fürtön.<br/><br/> – A felderítésbe felvenni kívánt gazdagépekhez és fürtökhöz egyetlen fiókra van szükség.<br/><br/> – A fiók lehet helyi vagy tartományi fiók. Azt javasoljuk, hogy rendszergazdai engedélyekkel rendelkezik a Hyper-V-gazdagépeken vagy-fürtökön.<br/><br/> Ha nem szeretne rendszergazdai engedélyeket rendelni, akkor a következő engedélyek szükségesek: távfelügyeleti felhasználók; Hyper-V-rendszergazdák; Teljesítményfigyelő felhasználói.
**A PowerShell távelérésének engedélyezése** | Engedélyezi a PowerShell távelérést a gazdagépen, így a Azure Migrate készülék PowerShell-parancsokat futtathat a gazdagépen egy WinRM-kapcsolaton keresztül.| A beállításhoz minden gazdagépen nyisson meg egy PowerShell-konzolt rendszergazdaként, és futtassa a következő parancsot:<br/><br/>``` Enable-PSRemoting -force ```
**A Hyper-V integrációs szolgáltatások beállítása** | Ellenőrzi, hogy a Hyper-V integrációs szolgáltatások engedélyezve vannak-e a gazdagép által kezelt összes virtuális gépen. |  [Engedélyezze a Hyper-V integrációs szolgáltatásokat](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services) minden egyes virtuális gépen.<br/><br/> Ha a Windows Server 2003-et futtatja, [kövesse az alábbi utasításokat](prepare-windows-server-2003-migration.md).
**Hitelesítő adatok delegálása, ha a VM-lemezek távoli SMB-megosztásokon találhatók** | A parancsfájl delegálja a hitelesítő adatokat. | A hitelesítő adatok delegálásának [engedélyezése a CredSSP](#enable-credssp-to-delegate-credentials) számára.

### <a name="run-the-script"></a>A szkript futtatása

Futtassa a szkriptet a következő módon:

1. Győződjön meg arról, hogy a PowerShell 4,0-es vagy újabb verziója telepítve van a Hyper-V-gazdagépen.
2. Töltse le a szkriptet a [Microsoft letöltőközpontból](https://aka.ms/migrate/script/hyperv). A parancsfájlt a Microsoft kriptográfiailag aláírja.
3. Ellenőrizze a parancsfájl integritását MD5 vagy SHA256 kivonatoló fájl használatával. A hashtag-értékek alább láthatók. Futtassa ezt a parancsot a szkript kivonatának létrehozásához:
    ```
    C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]
    ```
    Példa használata:
    ```
    C:\>CertUtil -HashFile C:\Users\Administrators\Desktop\ MicrosoftAzureMigrate-Hyper-V.ps1 SHA256
    ```

4. A parancsfájl integritásának ellenőrzése után futtassa a parancsfájlt minden Hyper-V-gazdagépen a következő PowerShell-paranccsal:
    ```
    PS C:\Users\Administrators\Desktop> MicrosoftAzureMigrate-Hyper-V.ps1
    ```

#### <a name="hashtag-values"></a>Hashtag-értékek

A kivonatoló értékek a következők:

| **Hash** | **Érték** |
| --- | --- |
| **MD5** | 0ef418f31915d01f896ac42a80dc414e |
| **SHA256** | 0ad60e7299925eff4d1ae9f1c7db485dc9316ef45b0964148a3c07c80761ade2 |



### <a name="enable-credssp-to-delegate-credentials"></a>Hitelesítő adatok delegálásának engedélyezése a CredSSP számára

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

1. A berendezésre vonatkozó követelmények [ellenőrzése](migrate-appliance.md#appliance---hyper-v) .
2. Tekintse át azokat az Azure URL-címeket, amelyekhez a készüléknek hozzá kell férnie a [nyilvános](migrate-appliance.md#public-cloud-urls) és a [kormányzati](migrate-appliance.md#government-cloud-urls) felhőkben. Ha URL-alapú tűzfalat vagy proxyt használ, győződjön meg arról, hogy az lehetővé teszi a szükséges URL-címek elérését.
3. Tekintse át a berendezés által a felderítés és az értékelés során gyűjtött adatokat.
4. [Tekintse át](migrate-appliance.md#collected-data---hyper-v) a készülékhez tartozó portok hozzáférési követelményeit.


## <a name="prepare-for-hyper-v-migration"></a>Felkészülés a Hyper-V áttelepítésére

1. [Tekintse át](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) A Hyper-V gazdagépre vonatkozó követelmények az áttelepítéshez, valamint azon Azure URL-címek, amelyekhez a Hyper-V-gazdagépek és-fürtök hozzáférhetnek a virtuális gépek migrál
2. [Tekintse át](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) az Azure-ba áttelepíteni kívánt Hyper-V virtuális gépekre vonatkozó követelményeket.
3. Az Azure-ba történő Migrálás előtt néhány módosításra van szükség a virtuális gépeken.
    - Az áttelepítés megkezdése előtt fontos, hogy elvégezze ezeket a módosításokat. Ha a módosítás előtt áttelepíti a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.
    - Tekintse át a [Windows](prepare-for-migration.md#windows-machines) -és [Linux](prepare-for-migration.md#linux-machines) -módosításokat, amelyeket el kell végeznie.



## <a name="next-steps"></a>Következő lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Azure-fiók engedélyeinek beállítása.
> * Előkészített Hyper-V-gazdagépek és virtuális gépek az értékeléshez és az áttelepítéshez.
> * Készen áll a Azure Migrate berendezés üzembe helyezésére.

Folytassa a következő oktatóanyaggal, amely létrehoz egy Azure Migrate projektet, üzembe helyezi a készüléket, és felderíti és felméri a Hyper-V virtuális gépeket az Azure-ba való áttelepítéshez.

> [!div class="nextstepaction"]
> [Hyper-V virtuális gépek felmérése](./tutorial-assess-hyper-v.md)

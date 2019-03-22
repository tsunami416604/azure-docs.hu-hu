---
title: Gazdagép-készlet létrehozása az Azure Marketplace (előzetes verzió) – Azure
description: Hogyan lehet Windows virtuális asztali állomás-készlet létrehozása az Azure Marketplace-en.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: a1d172f219580b390ba4855928d6f6a68c8269b9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318112"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace-preview"></a>Oktatóanyag: Gazdagép-készlet létrehozása az Azure Marketplace-en (előzetes verzió)

Gazdagép-készletekre egy gyűjtemény egy vagy több egyforma virtuális gépek Windows virtuális asztal (előzetes verzió) bérlő környezetekben. Minden gazdagép címkészlet tartalmazhat egy alkalmazás-csoportot, amely a felhasználók használhatják, mint a fizikai számítógépen.

Ez a cikk ismerteti, hogyan hozhat létre egy gazdagép készlet használatával egy Microsoft Azure Marketplace-ajánlat egy virtuális asztali Windows-bérlőn belül. Ez magában foglalja a Windows virtuális asztal, egy erőforráscsoport létrehozása a virtuális gépek Azure-előfizetéssel, a virtuális gépek csatlakoztatása az Active Directory-tartományhoz, és a virtuális gépek regisztrálása a Windows virtuális asztal a gazdagép készletet hoz létre.

Mielőtt elkezdené, [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Futtassa az Azure piactéren, ajánlat, amellyel egy új gazdagép-készletet

Az Azure piactéren, ajánlat, amellyel egy új gazdagép-készletet futtatása:

1. Válassza ki **+** vagy **+ erőforrás létrehozása**.
2. Adja meg **Windows virtuális asztal** a Marketplace-en keresési ablakban.
3. Válassza ki **virtuális asztali Windows - állomás készlet kiépítése**, majd **létrehozás**.

Kövesse az útmutatást, írja be a megfelelő paneleket adatait.

### <a name="basics"></a>Alapvető beállítások

Íme, mire az alapvető beállítások panel:

1. Adja meg a gazdagép-készlet, amely a Windows virtuális asztal bérlőn belül egyedi nevét.
2. Válassza ki a megfelelő beállítást, a személyes asztal. Ha **Igen**, minden egyes felhasználó esetében a gazdagép készlet csatlakozik tartósan hozzá lesz rendelve egy virtuális géphez.
3. Adja meg a felhasználók számára is jelentkezzen be a Windows virtuális asztali ügyfelek és a egy asztali eléréséhez, az Azure Marketplace-ajánlat befejezése után vesszővel elválasztott listáját. Például, ha hozzá szeretné rendelni user1@contoso.com és user2@contoso.com elérni, írja be "user1@contoso.com,user2@contoso.com."
4. Válassza ki **új létrehozása** , és adja meg az új erőforráscsoport nevét.
5. A **hely**, válassza ki ugyanazt a helyet a virtuális hálózatnak, amely kapcsolódik az Active Directory-kiszolgáló.
6. Kattintson az **OK** gombra.

### <a name="configure-virtual-machines"></a>Virtuális gép konfigurálása

A virtuális gépek konfigurálás paneljén:

1. Elfogadhatja az alapértelmezett beállításokat, vagy testre szabhatja a számát és méretét, a virtuális gépeket.
2. Adja meg a virtuális gépek nevei előtag. Ha például az "előtag" nevet ad meg, ha a virtuális gépek neve "előtag-0," "előtag-1", és így tovább.
3. Kattintson az **OK** gombra.

### <a name="virtual-machine-settings"></a>A virtuális gép beállításai

A virtuális gép beállítás panel:

1. Válassza ki a **képforrás** , és adja meg a megfelelő információkat, hogyan találhatja meg, és hogyan kell tárolni. Ha nem használ felügyelt lemezeket, válassza ki azt a .vhd fájlt tartalmazó tárfiókot.
2. Adja meg az egyszerű felhasználónév és jelszó a tartományi fiók, amely az Active Directory-tartományhoz a virtuális gépek csatlakozni fognak. Ez ugyanaz a felhasználónév és jelszó létrejön a virtuális gépeken, amely a helyi fiók. Ezek a helyi fiókok később visszaállíthatja.
3. Válassza ki a virtuális hálózat, amely kapcsolódik az Active Directory-kiszolgáló, majd válasszon egy alhálózatot a virtuális gépek üzemeltetéséhez.
4. Kattintson az **OK** gombra.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows virtuális asztal bérlőinformációk

A Windows virtuális asztal bérlői információk panel:

1. Adja meg a **Windows virtuális asztal bérlői csoportnév** a bérlő csoport, amely tartalmazza a bérlő számára. Ha nem rendelkezik egy adott bérlővel csoportnevet tervezett, hagyja meg az alapértelmezett.
2. Adja meg a **Windows virtuális asztal bérlőnevet** a bérlő meg hoz létre a gazdagép-készletébe.
3. Adja meg a hitelesítő adatokat a Windows virtuális asztal bérlő RDS tulajdonosa nevében hitelesítsék magukat használni kívánt típusát. Ha **szolgáltatásnév**, meg kell adni a **az Azure AD-bérlő azonosítója** az egyszerű szolgáltatás társítva.
4. Adja meg a bérlői rendszergazdai fiókkal vagy a hitelesítő adatokat. Csak egy jelszavára vonatkozó hitelesítőadat-szolgáltatásnevek használata támogatott.
5. Kattintson az **OK** gombra.

## <a name="complete-setup-and-create-the-virtual-machine"></a>A beállítás befejezéséhez és a virtuális gép létrehozása

Az utolsó két a többi panelen:

1. Az a **összefoglalás** panelen tekintse át a telepítési információs. Ha valami módosítani szeretné, lépjen vissza a megfelelő panelt, és a módosítást, a folytatás előtt. Ha az információk megfelelő, válassza ki a **OK**.
2. Az a **vásárlása** panelen tekintse át a vásárlás Azure Marketplace-ről további információt.
3. Válassza ki **létrehozás** üzembe helyezéséhez a gazdagép-készlet.

Attól függően, hogy hány virtuális gépet hoz létre a folyamat 30 percig vagy tovább is eltarthat.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Nem kötelező) Hozzárendelhet további felhasználókat az asztali csoporthoz

Az Azure Marketplace-ajánlat befejezése után hozzárendelhet további felhasználókat az asztali csoporthoz a virtuális gépeken a teljes munkamenet asztalok tesztelés megkezdése előtt. Ha már felvett alapértelmezett a felhasználók az Azure Marketplace-ajánlat és továbbiak hozzáadásához nem szeretné, ezt a szakaszt kihagyhatja.

Felhasználók hozzárendelése az asztali alkalmazás csoporthoz, először nyissa meg egy PowerShell-ablakot. Ezt követően kell adja meg a következő két parancsmagokat.

Jelentkezzen be a Windows virtuális asztali környezetben a következő parancsmag futtatásával:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

A környezet beállítása a Windows virtuális asztal bérlői csoporthoz megadott ajánlat az alábbi parancsmagot az Azure Marketplace-en. Ha nem törli a virtuális asztali Windows-bérlő csoport érték az alapértelmezett érték az Azure Marketplace-en ajánlat, kihagyhatja ezt a lépést.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Ezt követően, ha ezeket a két dolgokat, az asztali alkalmazás csoportot ezzel a parancsmaggal is hozzáadhat felhasználókat:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A felhasználó egyszerű Felhasználónevének meg kell egyeznie a felhasználó identitását az Azure Active Directoryban (például user1@contoso.com). Ha azt szeretné, több felhasználó hozzáadásának, ez a parancsmag minden felhasználó számára kell futtatni.

Miután végrehajtotta ezeket a lépéseket, az asztali alkalmazás csoportba felvett felhasználók Windows virtuális asztal támogatott a távoli asztal ügyfelek jelentkezzen be, és tekintse meg a munkamenet asztali erőforrás.

Az alábbiakban a jelenlegi támogatott ügyfelek:

- [Távoli asztali ügyfél Windows 7 és Windows 10-es](connect-windows-7-and-10.md)
- [Windows virtuális asztal webes ügyféllel](connect-web.md)

## <a name="next-steps"></a>További lépések

Most, hogy a gazdagép-készlet és a hozzárendelt felhasználók férhetnek hozzá az asztali végzett, a gazdagép-készletet a távoli alkalmazások is fel lehet tölteni. Virtuális asztali Windows-alkalmazások kezeléséről további információkért tekintse meg a kezelés alkalmazás csoportok oktatóanyag.

> [!div class="nextstepaction"]
> [Útmutató alkalmazások csoportok kezelése](./manage-app-groups.md)

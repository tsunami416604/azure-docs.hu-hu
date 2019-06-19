---
title: Windows virtuális asztal előzetes gazdagép-készlet létrehozása az Azure Marketplace – az Azure használatával
description: Hogyan lehet Windows virtuális asztal előzetes gazdagép-készlet létrehozása az Azure piactér használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: f692303140db1441aa34aacef62523d7f596dba1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204734"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Oktatóanyag: Gazdagép-készlet létrehozása az Azure Marketplace használatával

Gazdagép-készletekre egy gyűjtemény egy vagy több egyforma virtuális gépek virtuális Windows Desktop előzetes verziójához bérlői környezetekben. Minden gazdagép címkészlet tartalmazhat egy alkalmazás-csoportot, amely a felhasználók használhatják, mint a fizikai számítógépen.

Ez az oktatóanyag leírja, hogyan hozhat létre egy Windows virtuális asztal bérlőn belül gazdagép készlet használatával egy Microsoft Azure Marketplace-ajánlat. A feladatok a következők:

> [!div class="checklist"]
> * Windows virtuális asztal a gazdagép-készlet létrehozása.
> * Hozzon létre egy erőforráscsoportot a virtuális gépek Azure-előfizetésben.
> * A virtuális gépek csatlakoztatása az Active Directory-tartományhoz.
> * Windows virtuális asztal regisztrálja a virtuális gépeket.

Mielőtt elkezdené, [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Futtassa az Azure piactéren, ajánlat, amellyel egy új gazdagép-készletet

Az Azure piactéren, ajánlat, amellyel egy új gazdagép-készletet futtatása:

1. Válassza ki **+** vagy **+ erőforrás létrehozása**.
2. Adja meg **Windows virtuális asztal** a Marketplace-en keresési ablakban.
3. Válassza ki **virtuális asztali Windows - állomás készlet kiépítése**, majd válassza ki **létrehozás**.

Kövesse az útmutatást, írja be a megfelelő paneleket adatait.

### <a name="basics"></a>Alapvető beállítások

Íme teendő a **alapjai** panelen:

1. Adja meg a gazdagép-készlet, amely a Windows virtuális asztal bérlőn belül egyedi nevét.
2. Válassza ki a személyes asztal megfelelő beállítást. Ha **Igen**, minden egyes felhasználó esetében a gazdagép készlet csatlakozik tartósan hozzá lesz rendelve egy virtuális géphez.
3. Adja meg a felhasználók, akik is jelentkezzen be a Windows virtuális asztali ügyfelek és a egy asztali eléréséhez az Azure piactéren ajánlat befejeztét követően vesszővel elválasztott listáját. Például, ha a hozzárendelni kívánt user1@contoso.com és user2@contoso.com elérni, írja be "user1@contoso.com,user2@contoso.com."
4. Válassza ki **új létrehozása** , és adja meg az új erőforráscsoport nevét.
5. A **hely**, válassza ki ugyanazt a helyet a virtuális hálózatnak, amely kapcsolódik az Active Directory-kiszolgáló.
6. Kattintson az **OK** gombra.

### <a name="configure-virtual-machines"></a>Virtuális gép konfigurálása

Az a **konfigurálja a virtuális gépek** panelen:

1. Elfogadhatja az alapértelmezett beállításokat, vagy testre szabhatja a számát és méretét, a virtuális gépeket.
2. Adja meg a virtuális gépek nevei előtag. Ha például az "előtag" nevet ad meg, ha a virtuális gépek neve "előtag-0," "előtag-1", és így tovább.
3. Kattintson az **OK** gombra.

### <a name="virtual-machine-settings"></a>A virtuális gép beállításai

Az a **virtuálisgép-beállítások** panelen:

>[!NOTE]
> Ha a virtuális gépek tartományhoz csatlakoztatja az Azure Active Directory Domain Services (Azure AD DS) környezetre, győződjön meg arról, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználó is tagja, a [AAD DC rendszergazdák csoport](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group).

1. A **képforrás**, válassza ki a forrás, és adja meg a megfelelő információkat, hogyan találhatja meg, és hogyan kell tárolni. Ha nem használ felügyelt lemezeket, válassza ki a tárfiókot, amely tartalmazza a .vhd fájlt.
2. Adja meg az egyszerű felhasználónév és jelszó a tartományi fiók, amely az Active Directory-tartományhoz a virtuális gépek csatlakozni fognak. Ez ugyanaz a felhasználónév és jelszó létrejön a virtuális gépeken, amely a helyi fiók. Ezek a helyi fiókok később visszaállíthatja.
3. Válassza ki a virtuális hálózatot, amely kapcsolódik az Active Directory-kiszolgáló, és válasszon ki egy alhálózatot a virtuális gépek üzemeltetéséhez.
4. Kattintson az **OK** gombra.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Windows virtuális asztal előzetes bérlőinformációk

Az a **Windows virtuális asztal bérlőinformációk** panelen:

1. A **Windows virtuális asztal bérlői csoportnév**, adja meg a bérlő csoport, amely tartalmazza a bérlő nevét. Hagyja meg az alapértelmezett, kivéve, ha meg van megadva egy adott bérlő csoport neve.
2. A **Windows virtuális asztal bérlőnevet**, írja be a bérlő nevét, ahol Ön hoz létre a gazdagép-készlet.
3. Adja meg a hitelesítő adatokat, amelyeket a Windows virtuális asztal bérlő RDS tulajdonosa nevében hitelesítsék magukat használni kívánt típusát. Ha befejezte a [egyszerű szolgáltatásnevekről és a szerepkör-hozzárendelések létrehozása a PowerShell-oktatóanyag](./create-service-principal-role-powershell.md)válassza **szolgáltatásnév**. Amikor **az Azure AD-bérlő azonosítója** jelenik meg, adja meg az Azonosítót, amely tartalmazza az egyszerű szolgáltatás Azure Active Directory-példány.
4. Adja meg a hitelesítő adatokat a bérlői rendszergazda fiókjával. Csak egy jelszavára vonatkozó hitelesítőadat-szolgáltatásnevek használata támogatott.
5. Kattintson az **OK** gombra.

## <a name="complete-setup-and-create-the-virtual-machine"></a>A beállítás befejezéséhez és a virtuális gép létrehozása

Az utolsó két a többi panelen:

1. Az a **összefoglalás** panelen tekintse át a telepítési információs. Ha valami módosítani szeretné, lépjen vissza a megfelelő panelt, és a módosítást, a folytatás előtt. Ha az információk megfelelő, válassza ki a **OK**.
2. Az a **vásárlása** panelen tekintse át a vásárlást az Azure Marketplace-ről további információt.
3. Válassza ki **létrehozás** üzembe helyezéséhez a gazdagép-készlet.

Attól függően, hogy hány virtuális gépet hoz létre a folyamat 30 percig vagy tovább is eltarthat.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Nem kötelező) Hozzárendelhet további felhasználókat az asztali csoporthoz

Után az Azure piactéren ajánlat befejeződik hozzárendelhet további felhasználókat az asztali csoporthoz a virtuális gépeken a teljes munkamenet asztalok tesztelés megkezdése előtt. Ha már felvett alapértelmezett a felhasználók az Azure Marketplace-ajánlat és továbbiak hozzáadásához nem szeretné, ezt a szakaszt kihagyhatja.

Felhasználók hozzárendelése az asztali alkalmazás csoporthoz, először nyissa meg egy PowerShell-ablakot. Ezt követően kell adja meg a következő két parancsmagokat.

Jelentkezzen be a Windows virtuális asztali környezetben a következő parancsmag futtatásával:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ez a parancsmag használatával adja hozzá a felhasználók az asztali csoporthoz:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A felhasználó egyszerű Felhasználónevének meg kell egyeznie a felhasználó identitását az Azure Active Directoryban (például user1@contoso.com). Ha azt szeretné, több felhasználó hozzáadásának, ez a parancsmag minden felhasználó számára kell futtatni.

Miután végrehajtotta ezeket a lépéseket, az asztali alkalmazás csoportba felvett felhasználók Windows virtuális asztal támogatott a távoli asztal ügyfelek jelentkezzen be, és tekintse meg a munkamenet asztali erőforrás.

Az alábbiakban a jelenlegi támogatott ügyfelek:

- [Távoli asztali ügyfél Windows 7 és Windows 10-es](connect-windows-7-and-10.md)
- [Windows virtuális asztal webes ügyféllel](connect-web.md)

>[!IMPORTANT]
>Védelme érdekében az Azure-ban, a Windows virtuális asztali környezetben ajánlott ne nyissa meg a 3389-es porton bejövő a virtuális gépeken. Windows virtuális asztal nyílt bejövő port felhasználók számára hozzáférést a gazdagép-készlet virtuális gépek a 3389-es nem igényel. Ha a 3389-es port hibaelhárítás céljából kell megnyitni, azt javasoljuk, használja [just-in-time VM access](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>További lépések

Most, hogy a gazdagép-készlet és a hozzárendelt felhasználók férhetnek hozzá az asztali végzett, a gazdagép-készletet a RemoteApp-programok fel lehet tölteni. Virtuális asztali Windows-alkalmazások kezeléséről további információkért lásd az ebben az oktatóanyagban:

> [!div class="nextstepaction"]
> [Útmutató alkalmazások csoportok kezelése](./manage-app-groups.md)

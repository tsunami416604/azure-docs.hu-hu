---
title: Windows rendszerű virtuális asztali címkészlet létrehozása az Azure piactér használatával – Azure
description: Windows rendszerű virtuális asztali címkészlet létrehozása az Azure Marketplace használatával.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: f5b40e59a4ed2393e3b9912f8e4caa06ee267428
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757518"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Oktatóanyag: állomáslista létrehozása az Azure Marketplace használatával

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Mindegyik gazdagép tartalmazhatja azt az alkalmazáscsoport-csoportot, amelyet a felhasználók a fizikai asztalon lévők használatával kezelhetnek.

Ez az oktatóanyag azt ismerteti, hogyan hozhat létre egy Windows rendszerű virtuális asztali bérlőn belüli címkészletet egy Microsoft Azure Marketplace ajánlat használatával. A feladatok a következők:

> [!div class="checklist"]
> * Hozzon létre egy gazdagépet a Windows Virtual Desktopban.
> * Hozzon létre egy erőforráscsoportot virtuális gépekkel egy Azure-előfizetésben.
> * Csatlakoztassa a virtuális gépeket a Active Directory tartományhoz.
> * Regisztrálja a virtuális gépeket a Windows Virtual Desktopban.

Mielőtt elkezdené, [töltse le és importálja a](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Az Azure Marketplace-ajánlat futtatása új címkészlet kiépítéséhez

Az Azure Marketplace ajánlatának futtatása új címkészlet kiépítéséhez:

1. Válassza **+** vagy **az erőforrás létrehozása**lehetőséget.
2. A piactér keresési ablakában adja meg a **Windows virtuális asztal** kifejezést.
3. Válassza **a Windows virtuális asztal – címkészlet kiépítése**lehetőséget, majd válassza a **Létrehozás**lehetőséget.

Ezután kövesse a következő szakaszban található utasításokat a megfelelő pengék adatainak megadásához.

### <a name="basics"></a>Alapvető beállítások

Az **alapvető beállítások** panel az alábbi műveleteket hajtja végre:

1. Adja meg a Windows rendszerű virtuális asztali bérlőn belül egyedi alkalmazáskészlet nevét.
2. Válassza ki a megfelelő lehetőséget a személyes asztal számára. Ha az **Igen**lehetőséget választja, a gazdagéphez csatlakozó összes felhasználó véglegesen hozzá lesz rendelve egy virtuális géphez.
3. Adja meg a Windows rendszerű virtuális asztali ügyfelekre bejelentkező felhasználók vesszővel tagolt listáját, és az Azure Marketplace-ajánlat befejezése után nyissa meg az asztalt. Ha például user1@contoso.com és user2@contoso.com hozzáférést szeretne hozzárendelni, írja be a "user1@contoso.com user2@contoso.com." értéket.
4. Válassza az **új létrehozása** lehetőséget, és adja meg az új erőforráscsoport nevét.
5. A **hely**mezőben válassza ki a virtuális hálózattal megegyező helyet, amely a Active Directory-kiszolgálóhoz kapcsolódik.
6. Kattintson az **OK** gombra.

>[!IMPORTANT]
>Ha tiszta Azure Active Directory Domain Services és Azure Active Directory megoldást használ, ügyeljen arra, hogy a gazdagépet ugyanabban a régióban telepítse, mint a Azure Active Directory Domain Services a tartományhoz való csatlakozás és a hitelesítő adatokkal kapcsolatos hibák elkerülése érdekében.

### <a name="configure-virtual-machines"></a>Virtuális gépek konfigurálása

A **virtuális gépek konfigurálása** panelen:

1. Fogadja el az alapértelmezett értékeket, vagy szabja testre a virtuális gépek számát és méretét.
2. Adja meg a virtuális gépek neveinek előtagját. Ha például a "prefix" nevet adja meg, akkor a virtuális gépek "előtag-0", "előtag-1" és így tovább.
3. Kattintson az **OK** gombra.

### <a name="virtual-machine-settings"></a>A virtuális gép beállításai

A **virtuális gép beállításai** panelen:

>[!NOTE]
> Ha a virtuális gépeket egy Azure Active Directory Domain Services (Azure AD DS) környezethez csatlakoztatja, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználója a [HRE DC-rendszergazdák csoport](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)tagja is.

1. A **képforráshoz**válassza ki a forrást, és adja meg a megfelelő információkat a megkereséséhez és tárolásához. Ha úgy dönt, hogy nem használja a felügyelt lemezeket, válassza ki azt a Storage-fiókot, amely a. vhd-fájlt tartalmazza.
2. Adja meg annak a tartományi fióknak a felhasználónevét és jelszavát, amely a virtuális gépeket a Active Directory tartományhoz fogja csatlakoztatni. Ugyanezt a felhasználónevet és jelszót a rendszer helyi fiókként hozza létre a virtuális gépeken. Ezeket a helyi fiókokat később is visszaállíthatja.
3. Válassza ki azt a virtuális hálózatot, amely a Active Directory-kiszolgálóhoz kapcsolódik, majd válasszon egy alhálózatot a virtuális gépek üzemeltetéséhez.
4. Kattintson az **OK** gombra.

### <a name="windows-virtual-desktop-tenant-information"></a>Windows rendszerű virtuális asztali bérlő adatai

A **Windows rendszerű virtuális asztali bérlő adatai** panelen:

1. A **Windows rendszerű virtuális asztali bérlői csoport neve**mezőbe írja be a bérlőt tartalmazó bérlői csoport nevét. Hagyja meg az alapértelmezett értéket, ha megadott egy bérlői csoport nevét.
2. A **Windows rendszerű virtuális asztali bérlő neve**mezőbe írja be annak a bérlőnek a nevét, ahol a gazdagépet létrehozza.
3. Adja meg, hogy milyen típusú hitelesítő adatokat kíván használni a Windows rendszerű virtuális asztali bérlői RDS-tulajdonosként való hitelesítéshez. Ha végrehajtotta az [egyszerű szolgáltatásnév és a szerepkör-hozzárendelések létrehozása a PowerShell-lel oktatóanyagot](./create-service-principal-role-powershell.md), válassza az **egyszerű szolgáltatásnév**lehetőséget. Amikor megjelenik az **Azure ad-bérlő azonosítója** , adja meg az Azure Active Directory-példány azonosítóját, amely tartalmazza az egyszerű szolgáltatásnevet.
4. Adja meg a bérlői rendszergazdai fiók hitelesítő adatait. Csak a jelszavas hitelesítő adatokkal rendelkező egyszerű szolgáltatások támogatottak.
5. Kattintson az **OK** gombra.

## <a name="complete-setup-and-create-the-virtual-machine"></a>A telepítés befejezése és a virtuális gép létrehozása

Az utolsó két penge esetében:

1. Az **Összefoglalás** panelen tekintse át a telepítési adatokat. Ha módosítania kell valamit, térjen vissza a megfelelő panelre, és a folytatás előtt végezze el a módosítást. Ha az adatok jobbra néznek, kattintson **az OK gombra**.
2. A **vásárlás** panelen tekintse át az Azure piactéren vásárolt további információkat.
3. Válassza a **Létrehozás** lehetőséget a gazdagép telepítéséhez.

Attól függően, hogy hány virtuális gépet hoz létre, ez a folyamat 30 percet vagy akár több időt is igénybe vehet.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Választható További felhasználók társítása az asztali alkalmazás csoportjához

Az Azure Marketplace-ajánlat befejezését követően több felhasználót is hozzárendelhet az asztali alkalmazás csoportjához, mielőtt megkezdené a teljes munkamenet-asztalok tesztelését a virtuális gépeken. Ha már hozzáadta az alapértelmezett felhasználókat az Azure Marketplace-ajánlatban, és nem szeretne további hozzáadást adni, akkor kihagyhatja ezt a szakaszt.

A felhasználók asztali alkalmazáscsoport hozzárendeléséhez először meg kell nyitnia egy PowerShell-ablakot. Ezután meg kell adnia a következő két parancsmagot.

Futtassa a következő parancsmagot a Windows rendszerű virtuális asztali környezetbe való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Adja hozzá a felhasználókat az asztali alkalmazás csoportjához a következő parancsmag használatával:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A felhasználó UPN-nek meg kell egyeznie a felhasználó identitásával Azure Active Directoryban (például user1@contoso.com). Ha több felhasználót szeretne felvenni, minden felhasználóhoz futtatnia kell ezt a parancsmagot.

A lépések elvégzése után az asztali alkalmazás csoportba felvett felhasználók bejelentkezhetnek a Windows rendszerű virtuális asztalra a támogatott Távoli asztal ügyfelekkel, és megtekinthetik a munkamenet-asztal erőforrásait.

A jelenleg támogatott ügyfelek:

- [Távoli asztal ügyfél a Windows 7 és a Windows 10 rendszerhez](connect-windows-7-and-10.md)
- [Windows rendszerű virtuális asztali webes ügyfél](connect-web.md)

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali környezet biztonságossá tételéhez az Azure-ban javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows rendszerű virtuális asztal nem igényel olyan nyitott bejövő portot 3389, amellyel a felhasználók hozzáférhetnek a gazdagép-készlet virtuális gépei számára. Ha hibaelhárítási célból meg kell nyitnia a 3389-as portot, javasoljuk, hogy használja a virtuális gépek igény szerinti [elérését](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozta a gazdagépet, és hozzárendelte a felhasználókat az asztalhoz való hozzáféréshez, feltöltheti a gazdagép-készletet a RemoteApp-programok használatával. Ha többet szeretne megtudni az alkalmazások kezeléséről a Windows Virtual Desktopban, tekintse meg ezt az oktatóanyagot:

> [!div class="nextstepaction"]
> [Alkalmazás-csoportok kezelése – oktatóanyag](./manage-app-groups.md)

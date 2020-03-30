---
title: Windows virtuális asztali gazdagépkészletének létrehozása PowerShell – Azure
description: Host készlet létrehozása a Windows Virtual Desktop ban PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: b390c0beb20b7557294c18f889a0f41023513e2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246959"
---
# <a name="create-a-host-pool-with-powershell"></a>Gazdagépcsoport létrehozása a PowerShell-lel

A gazdakészletek egy vagy több azonos virtuális gép gyűjteménye a Windows virtuális asztal bérlői környezetében. Minden gazdagépkészlet tartalmazhat egy alkalmazáscsoportot, amelyet a felhasználók ugyanúgy használhatnak, mint a fizikai asztalon.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>A PowerShell-ügyfél használatával hozzon létre egy gazdakészletet

Először [töltse le és importálja a Windows Virtual Desktop PowerShell modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg.

A windowsos virtuális asztal környezetbe való bejelentkezéshez futtassa a következő parancsmat

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ezután futtassa ezt a parancsmalapot egy új gazdagépkészlet létrehozásához a Windows virtuális asztal bérlőjében:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

A következő parancsmag futtatásával hozzon létre egy regisztrációs jogkivonatot, amely engedélyezi a munkamenet-gazdagép számára, hogy csatlakozzon a gazdakészlethez, és mentse azt egy új fájlba a helyi számítógépen. Megadhatja, hogy mennyi ideig érvényes a regisztrációs jogkivonat a -ExpirationHours paraméter használatával.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token | Out-File -FilePath <PathToRegFile>
```

Ezt követően futtassa ezt a parancsmasalapot az Azure Active Directory felhasználóinak hozzáadásához a gazdakészlet alapértelmezett asztali alkalmazáscsoportjához.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

Az **Add-RdsAppGroupUser** parancsmag nem támogatja a biztonsági csoportok hozzáadását, és egyszerre csak egy felhasználót ad hozzá az alkalmazáscsoporthoz. Ha több felhasználót szeretne hozzáadni az alkalmazáscsoporthoz, futtassa újra a parancsmagát a megfelelő egyszerű felhasználónevekkel.

Futtassa a következő parancsmast a regisztrációs jogkivonat változóba való exportálásához, amelyet [a virtuális gépek regisztrálása a Windows virtuális asztal gazdakészletére](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)című részben fog használni.

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Virtuális gépek létrehozása a gazdakészlethez

Most már létrehozhat egy Azure virtuális gépet, amely csatlakoztatható a Windows virtuális asztali gazdakészletéhez.

Virtuális gépet többféleképpen hozhat létre:

- [Virtuális gép létrehozása azure-galériaképből](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt lemezképből](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Virtuális gép létrehozása nem felügyelt lemezképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Ha egy virtuális gépet telepít a Windows 7-et gazdaoperációs rendszerként, a létrehozási és telepítési folyamat egy kicsit más lesz. További információt a [Windows 7 virtuális gép telepítése windowsos virtuális asztalon](deploy-windows-7-virtual-machine.md).

Miután létrehozta a munkamenetgazda virtuális gépeit, [alkalmazzon Windows-licencet egy munkamenetgazda virtuális gépre](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) a Windows vagy a Windows Server virtuális gépek futtatásához anélkül, hogy másik licencért fizetne. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>A virtuális gépek előkészítése a Windows Virtual Desktop ügynök telepítéséhez

A Windows virtuális asztali ügynökök telepítése és a virtuális gépek regisztrálása a Windows virtuális asztal gazdakészletében történő regisztrálása előtt a következő teendőkkel kell előkészítenie a virtuális gépeket:

- Tartományhoz kell csatlakoznia a géphez. Ez lehetővé teszi, hogy a bejövő Windows virtuális asztali felhasználók leképezve legyenek az Azure Active Directory-fiókjukból az Active Directory-fiókjukba, és sikeresen hozzáférhessenek a virtuális géphez.
- Ha a virtuális gépen Windows Server operációs rendszer fut, telepítenie kell a Távoli asztali munkamenetgazda (RDSH) szerepkört. Az RDSH szerepkör lehetővé teszi a Windows virtuális asztali ügynökök megfelelő telepítését.

A tartományhoz való sikeres csatlakozáshoz tegye a következő eket minden virtuális gépen:

1. [Csatlakozzon a virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. A virtuális gépen indítsa el a **Vezérlőpultot,** és válassza a **Rendszer**lehetőséget.
3. Válassza **a Számítógép név lehetőséget,** válassza a Beállítások **módosítása**lehetőséget, majd a **Módosítás...**
4. Válassza **a Tartomány lehetőséget,** majd írja be az Active Directory tartományt a virtuális hálózaton.
5. Hitelesítse a hitelesítést olyan tartományi fiókkal, amely jogosultságokkal rendelkezik a tartományhoz való csatlakozáshoz szükséges gépekhez.

    >[!NOTE]
    > Ha a virtuális gépeket egy Azure Active Directory tartományi szolgáltatások (Azure AD DS) környezethez csatlakozik, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználója is tagja az [AAD DC rendszergazdák csoportnak.](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>A virtuális gépek regisztrálása a Windows virtuális asztal gazdakészletében

A virtuális gépek regisztrálása egy Windows virtuális asztali gazdagép-készletbe olyan egyszerű, mint a Windows virtuális asztali ügynökök telepítése.

A Windows virtuális asztali ügynökök regisztrálásához tegye a következőket minden virtuális gépen:

1. [Csatlakozzon a virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Töltse le és telepítse a Windows virtuális asztali ügynököt.
   - Töltse le a [Windows virtuális asztali ügynökét](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Kattintson a jobb gombbal a letöltött telepítőre, válassza a **Tulajdonságok parancsot,** válassza **a Tiltás feloldása parancsot,** majd kattintson **az OK gombra.** Ez lehetővé teszi, hogy a rendszer megbízzon a telepítőben.
   - Indítsa el a telepítőt. Amikor a telepítő kéri a regisztrációs jogkivonatot, adja meg az **Export-RdsRegistrationInfo** parancsmagból kapott értéket.
3. Töltse le és telepítse a Windows Virtuális asztali ügynök rendszertöltőt.
   - Töltse le a [Windows virtuális asztali ügynök bootloader](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Kattintson a jobb gombbal a letöltött telepítőre, válassza a **Tulajdonságok parancsot,** válassza **a Tiltás feloldása parancsot,** majd kattintson **az OK gombra.** Ez lehetővé teszi, hogy a rendszer megbízzon a telepítőben.
   - Indítsa el a telepítőt.

>[!IMPORTANT]
>A Windows virtuális asztali környezet azure-beli védelméhez azt javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows virtuális asztal nem igényel nyitott bejövő port3389 a felhasználók számára, hogy hozzáférjenek a gazdakészlet virtuális gépek. Ha hibaelhárítási célból meg kell nyitnia a 3389-es portot, javasoljuk, hogy [just-in-time virtuális gép-hozzáférést használjon.](../security-center/security-center-just-in-time.md)

## <a name="next-steps"></a>További lépések

Most, hogy készített egy gazdakészletet, feltöltheti azt a RemoteApps alkalmazással. Ha többet szeretne tudni arról, hogyan kezelheti az alkalmazásokat a Windows Virtuális asztalon, olvassa el az Alkalmazáscsoportok kezelése oktatóanyagot.

> [!div class="nextstepaction"]
> [Alkalmazáscsoportok kezelése oktatóanyag](./manage-app-groups.md)

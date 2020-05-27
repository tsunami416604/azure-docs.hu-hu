---
title: Windows rendszerű virtuális asztali alkalmazáskészlet létrehozása PowerShell – Azure
description: Hogyan hozhat létre egy gazdagépet a Windows rendszerű virtuális asztalon PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0a4d0c22318399370b9ec11046c33a4eb5460eb3
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860120"
---
# <a name="create-a-host-pool-with-powershell"></a>Gazdagépcsoport létrehozása a PowerShell-lel

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/create-host-pools-powershell-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A gazdagép-készletek egy vagy több azonos virtuális gép gyűjteményei a Windows rendszerű virtuális asztali bérlői környezetekben. Mindegyik címkészlet több RemoteApp-csoporthoz, egy asztali alkalmazás csoporthoz és több munkamenet-gazdagéphez is társítható.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már követte a [PowerShell-modul beállítása](powershell-module.md)című témakör utasításait.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>A PowerShell-ügyfél használata a gazdagépek létrehozásához

Futtassa a következő parancsmagot a Windows rendszerű virtuális asztali környezetbe való bejelentkezéshez:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -HostPoolType <Pooled|Personal> -LoadBalancerType <BreadthFirst|DepthFirst|Persistent> -Location <region> -DesktopAppGroupName <appgroupname> 
```

Ez a parancsmag létrehozza a gazdagépet, a munkaterületet és az asztali alkalmazást. Emellett regisztrálja az asztali alkalmazás csoportját a munkaterületen. Létrehozhat egy munkaterületet ezzel a parancsmaggal, vagy használhat egy meglévő munkaterületet is. 

A következő parancsmag futtatásával hozzon létre egy regisztrációs jogkivonatot, amely engedélyezi, hogy a munkamenet-állomás csatlakozzon a gazdagéphez, és mentse a helyi számítógép egy új fájljába. Megadhatja, hogy a regisztrációs jogkivonat mennyi ideig érvényes a-ExpirationHours paraméter használatával.

>[!NOTE]
>A jogkivonat lejárati dátuma nem lehet kevesebb, mint egy óra, és legfeljebb egy hónap. Ha a megadott korláton kívül *expirationtime tulajdonságok* , a parancsmag nem hozza létre a jogkivonatot.

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddDays(1).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ'))
```

Ha például olyan tokent szeretne létrehozni, amely két órán belül lejár, futtassa a következő parancsmagot: 

```powershell
New-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> -ExpirationTime $((get-date).ToUniversalTime().AddHours(2).ToString('yyyy-MM-ddTHH:mm:ss.fffffffZ')) 
```

Ezután futtassa ezt a parancsmagot, hogy Azure Active Directory felhasználókat adjon hozzá az alapértelmezett asztali alkalmazás csoporthoz a gazdagéphez.

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+"-DAG"> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

A következő parancsmag futtatásával Azure Active Directory felhasználói csoportokat adhat hozzá az alapértelmezett asztali alkalmazás csoporthoz a gazdagépen:

```powershell
New-AzRoleAssignment -ObjectId <usergroupobjectid> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <hostpoolname+“-DAG”> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Futtassa a következő parancsmagot a regisztrációs jogkivonat egy változóba való exportálásához, amelyet később a [virtuális gépek a Windows rendszerű virtuális asztali készletbe való regisztrálásához](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool)fog használni.

```powershell
$token = Get-AzWvdRegistrationInfo -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> 
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Virtuális gépek létrehozása a gazdagéphez

Most létrehozhat egy Azure-beli virtuális gépet, amely csatlakoztatható a Windows rendszerű virtuális asztali készlethez.

Több módon is létrehozhat egy virtuális gépet:

- [Virtuális gép létrehozása Azure Gallery-rendszerképből](../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Virtuális gép létrehozása felügyelt rendszerképből](../virtual-machines/windows/create-vm-generalized-managed.md)
- [Virtuális gép létrehozása nem felügyelt rendszerképből](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-user-image-data-disks)

>[!NOTE]
>Ha a Windows 7 operációs rendszert futtató virtuális gépet a gazdagép operációs rendszereként telepíti, a létrehozási és a telepítési folyamat némileg eltérő lesz. További részletek: Windows 7 rendszerű virtuális [gépek üzembe helyezése Windows rendszerű virtuális asztalon](./virtual-desktop-fall-2019/deploy-windows-7-virtual-machine.md).

A munkamenet-gazdagép virtuális gépei létrehozása után Windows- [licencet alkalmazhat egy munkamenet-gazda](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) virtuális gépre, hogy a Windows vagy a Windows Server rendszerű virtuális gépeket egy másik licenc kifizetése nélkül futtassa. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>A virtuális gépek előkészítése a Windows rendszerű virtuális asztali ügynök telepítéséhez

A virtuális gépek előkészítéséhez a következő műveleteket kell elvégeznie, mielőtt telepítené a Windows rendszerű virtuális asztali ügynököket, és regisztrálja a virtuális gépeket a Windows rendszerű virtuális asztali alkalmazáskészletbe:

- A számítógépnek tartományhoz kell csatlakoznia. Ez lehetővé teszi, hogy a bejövő Windows rendszerű virtuális asztali felhasználók a Azure Active Directory fiókjából leképezhetők legyenek Active Directory-fiókjába, és sikeresen hozzáférhessenek a virtuális géphez.
- Ha a virtuális gép Windows Server operációs rendszert futtat, akkor telepítenie kell a Távoli asztal munkamenet-gazdagép (RDSH) szerepkört. A RDSH szerepkör lehetővé teszi a Windows rendszerű virtuális asztali ügynökök megfelelő telepítését.

A tartományhoz való csatlakozás sikeres végrehajtásához tegye a következőket az egyes virtuális gépeken:

1. [Kapcsolódjon a virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. A virtuális gépen indítsa el a **Vezérlőpultot** , és válassza a **rendszer**elemet.
3. Válassza ki a **számítógép nevét**, válassza a **beállítások módosítása**lehetőséget, majd válassza a **módosítás...** lehetőséget.
4. Válassza a **tartomány** lehetőséget, majd adja meg a Active Directory tartományt a virtuális hálózaton.
5. A hitelesítést olyan tartományi fiókkal végezze el, amely jogosultságokkal rendelkezik a tartományhoz csatlakozó gépekhez.

    >[!NOTE]
    > Ha a virtuális gépeket egy Azure Active Directory Domain Services (Azure AD DS) környezethez csatlakoztatja, győződjön meg arról, hogy a tartományhoz való csatlakozás felhasználója a [HRE DC-rendszergazdák csoport](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)tagja is.

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>A virtuális gépek regisztrálása a Windows rendszerű virtuális asztali gazdagépen

A virtuális gépek egy Windows rendszerű virtuális asztali készletbe való regisztrálása olyan egyszerű, mint a Windows rendszerű virtuális asztali ügynökök telepítése.

A Windows rendszerű virtuális asztali ügynökök regisztrálásához tegye a következőket az egyes virtuális gépeken:

1. [Kapcsolódjon a virtuális géphez](../virtual-machines/windows/quick-create-portal.md#connect-to-virtual-machine) a virtuális gép létrehozásakor megadott hitelesítő adatokkal.
2. Töltse le és telepítse a Windows rendszerű virtuális asztali ügynököt.
   - Töltse le a [Windows rendszerű virtuális asztali ügynököt](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Indítsa el a telepítőt. Ha a telepítő megkérdezi a regisztrációs jogkivonatot, adja meg az **export-AzWVDRegistrationInfo** parancsmagból kapott értéket.
3. Töltse le és telepítse a Windows rendszerű virtuális asztali ügynök rendszerbetöltőjét.
   - Töltse le a [Windows rendszerű virtuális asztali ügynök Rendszerbetöltőjét](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Indítsa el a telepítőt.

>[!IMPORTANT]
>A Windows rendszerű virtuális asztali környezet biztonságossá tételéhez az Azure-ban javasoljuk, hogy ne nyissa meg a 3389-es bejövő portot a virtuális gépeken. A Windows rendszerű virtuális asztal nem igényel olyan nyitott bejövő portot 3389, amellyel a felhasználók hozzáférhetnek a gazdagép-készlet virtuális gépei számára. Ha hibaelhárítási célból meg kell nyitnia a 3389-as portot, javasoljuk, hogy használja a virtuális gépek igény szerinti [elérését](../security-center/security-center-just-in-time.md). Javasoljuk továbbá, hogy a virtuális gépeket ne rendeljen nyilvános IP-címekhez.

## <a name="next-steps"></a>További lépések

Most, hogy létrehozott egy gazdagépet, feltöltheti azt a RemoteApps szolgáltatással. Ha többet szeretne megtudni az alkalmazások kezeléséről a Windows Virtual Desktopban, tekintse meg az alkalmazáscsoport kezelése oktatóanyagot.

> [!div class="nextstepaction"]
> [Alkalmazás-csoportok kezelése – oktatóanyag](./manage-app-groups.md)

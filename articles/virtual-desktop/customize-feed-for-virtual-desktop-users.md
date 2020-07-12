---
title: A Windows rendszerű virtuális asztali felhasználók hírcsatornáinak testreszabása – Azure
description: A Windows rendszerű virtuális asztali felhasználók hírcsatornáinak testreszabása PowerShell-parancsmagokkal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9c2ad99a59e548ff7793455dac21748dd057c5fc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86248696"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Csatorna személyre szabása Windows Virtual Desktop-felhasználók számára

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Testreszabhatja a hírcsatornát, hogy a RemoteApp-és távoli asztali erőforrások felismerhető módon jelenjenek meg a felhasználók számára.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már letöltötte és telepítette a Windows rendszerű virtuális asztali PowerShell-modult. Ha még nem tette meg, kövesse a [PowerShell-modul beállítása](powershell-module.md)című témakör utasításait.

## <a name="customize-the-display-name-for-a-remoteapp"></a>A RemoteApp megjelenítendő nevének testreszabása

A közzétett RemoteApp megjelenített nevét megváltoztathatja a felhasználóbarát név beállításával. Alapértelmezés szerint a felhasználóbarát név megegyezik a RemoteApp program nevével.

Az alkalmazáscsoport közzétett RemoteAppinak listájának lekéréséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Ha rövid nevet szeretne hozzárendelni egy RemoteApp-hoz, futtassa a következő parancsmagot a szükséges paraméterekkel:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Tegyük fel például, hogy az aktuális alkalmazásokat a következő példa parancsmaggal kérdezte le:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

A kimenet így néz ki:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
A rövid név frissítéséhez futtassa a következő parancsmagot:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

A rövid név sikeres frissítésének megerősítéséhez futtassa a következő parancsmagot:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

A parancsmagnak a következő kimenetet kell adnia:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Távoli asztal megjelenítendő nevének testreszabása

Egy közzétett távoli asztal megjelenítendő nevét a felhasználóbarát név beállításával módosíthatja. Ha manuálisan hozott létre egy gazdagép-készletet és egy asztali alkalmazást a PowerShell használatával, az alapértelmezett felhasználóbarát név a "munkamenet-asztal". Ha a GitHub Azure Resource Manager sablonnal vagy az Azure Marketplace-ajánlaton keresztül hozott létre egy gazdagép-készletet és egy asztali alkalmazást, az alapértelmezett felhasználóbarát név ugyanaz, mint a gazdagép-készlet neve.

A távoli asztal erőforrásának lekéréséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Ha rövid nevet szeretne rendelni a távoli asztali erőforráshoz, futtassa a következő PowerShell-parancsmagot:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Megjelenítendő név testreszabása Azure Portal

A közzétett távoli asztal megjelenítendő nevét úgy módosíthatja, hogy egy rövid nevet állít be a Azure Portal használatával.

1. Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen.

2. Keresse meg a **Windows rendszerű virtuális asztalt**.

3. A szolgáltatások területen válassza a **Windows virtuális asztal**elemet.

4. A Windows rendszerű virtuális asztal lapon válassza ki az **alkalmazáscsoport** elemet a képernyő bal oldalán, majd válassza ki a szerkeszteni kívánt alkalmazáscsoport nevét.

5. A képernyő bal oldalán található menüben válassza az **alkalmazások** lehetőséget.

6. Válassza ki a frissíteni kívánt alkalmazást, majd adjon meg egy új **megjelenítendő nevet**.

7. Válassza a **Mentés** lehetőséget. A szerkesztett alkalmazásnak ekkor meg kell jelennie a frissített névnek.

## <a name="next-steps"></a>Következő lépések

Most, hogy testre szabta a hírcsatornát a felhasználók számára, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe a teszteléshez. Ehhez folytassa a Kapcsolódás a Windows rendszerű virtuális asztali környezetekhez:

 * [Windows 10 vagy Windows 7 rendszerű kapcsolat](connect-windows-7-10.md)
 * [Kapcsolódás a webügyféllel](connect-web.md)
 * [Kapcsolódás az Android-ügyféllel](connect-android.md)
 * [Kapcsolódás az iOS-ügyfélhez](connect-ios.md)
 * [Kapcsolódás a macOS-ügyfélhez](connect-macos.md)

---
title: Windows rendszerű virtuális asztali személyes asztal hozzárendelésének típusa – Azure
description: A hozzárendelés típusának konfigurálása a Windows rendszerű virtuális asztali személyes asztali készlethez.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158ac92a930b53e02ee81570c62711ca27dc4ae8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85200392"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>A személyes asztali címkészlet-hozzárendelés típusának konfigurálása

>[!IMPORTANT]
>Ez a tartalom a Spring 2020 frissítésre vonatkozik Azure Resource Manager Windows rendszerű virtuális asztali objektumokkal. Ha a Windows rendszerű virtuális 2019 asztalt Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).
>
> A Windows rendszerű virtuális asztali Spring 2020 frissítése jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és nem javasoljuk, hogy éles számítási feladatokhoz használja azt. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A személyes asztali címkészlet hozzárendelési típusának konfigurálásával beállíthatja a Windows rendszerű virtuális asztali környezetét, hogy jobban megfeleljen az igényeinek. Ebben a témakörben bemutatjuk, hogyan konfigurálhatja az automatikus vagy közvetlen hozzárendelést a felhasználók számára.

>[!NOTE]
> A cikkben szereplő utasítások csak a személyes asztali gazdagépekre érvényesek, nem készletezett gazdagépek, mivel a készletezett gazdagépek felhasználói nem vannak hozzárendelve adott munkamenet-gazdagépekhez.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már letöltötte és telepítette a Windows rendszerű virtuális asztali PowerShell-modult. Ha még nem tette meg, kövesse a [PowerShell-modul beállítása](powershell-module.md)című témakör utasításait.

## <a name="configure-automatic-assignment"></a>Automatikus hozzárendelés konfigurálása

Az automatikus hozzárendelés az alapértelmezett hozzárendelési típus a Windows rendszerű virtuális asztali környezetben létrehozott új személyes asztali gazdagépek számára. A felhasználók automatikus hozzárendeléséhez nincs szükség egy adott munkamenet-gazdagépre.

A felhasználók automatikus hozzárendeléséhez először rendelje hozzá azokat a személyes asztali gazdagéphez, hogy azok a hírcsatornában lássák az asztalt. Amikor egy hozzárendelt felhasználó elindítja az asztalt a hírcsatornájában, a rendszer egy rendelkezésre álló munkamenet-gazdagépet igényel, ha még nem csatlakoztak a gazdagéphez, amely befejezi a hozzárendelési folyamatot.

A következő PowerShell-parancsmag futtatásával konfigurálhatja, hogy a gazdagépek automatikusan rendeljenek felhasználókat a virtuális gépekhez:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

A következő PowerShell-parancsmag futtatásával rendelhet hozzá felhasználót a személyes asztali gazdagéphez:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Közvetlen hozzárendelés konfigurálása

Az automatikus hozzárendeléstől eltérően a közvetlen hozzárendelés használatakor a felhasználót a személyes asztali gazdagéphez és egy adott munkamenet-gazdagéphez is hozzá kell rendelnie, mielőtt csatlakozni tudnak a személyes asztalhoz. Ha a felhasználó csak a munkamenetgazda-hozzárendelés nélküli gazdagépekhez van hozzárendelve, nem férhetnek hozzá az erőforrásokhoz.

A következő PowerShell-parancsmag futtatásával konfigurálhatja a gazdagépeket, hogy a felhasználók közvetlen hozzárendelését igénylik a munkamenet-gazdagépekhez:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

A következő PowerShell-parancsmag futtatásával rendelhet hozzá felhasználót a személyes asztali gazdagéphez:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Ha egy felhasználót egy adott munkamenet-gazdagéphez szeretne rendelni, futtassa a következő PowerShell-parancsmagot:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>További lépések

Most, hogy konfigurálta a személyes asztal hozzárendelési típusát, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, és tesztelheti azt egy felhasználói munkamenet részeként. A következő két útmutató bemutatja, hogyan csatlakozhat egy munkamenethez az Ön által választott ügyfél használatával:

- [Kapcsolódás a Windows asztali ügyféllel](connect-windows-7-and-10.md)
- [Kapcsolódás a webügyféllel](connect-web.md)
- [Kapcsolódás az Android-ügyféllel](connect-android.md)
- [Kapcsolódás az iOS-ügyfélhez](connect-ios.md)
- [Kapcsolódás a macOS-ügyfélhez](connect-macos.md)
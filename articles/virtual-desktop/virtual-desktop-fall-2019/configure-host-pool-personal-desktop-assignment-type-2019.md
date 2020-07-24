---
title: Windows rendszerű virtuális asztali személyes asztal hozzárendelése (Fall 2019) – Azure
description: Megtudhatja, hogyan konfigurálhatja a Windows rendszerű virtuális asztalok hozzárendelési típusát (2019-es kiadás) a személyes asztali gazdaszámítógépen.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 036e144922fcb49c486813b8668d944dd771536e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087186"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-fall-2019-release"></a>A személyes asztali címkészlet hozzárendelési típusának konfigurálása (2019-es kiadás)

>[!IMPORTANT]
>Ez a tartalom a Fall 2019 kiadásra vonatkozik, amely nem támogatja a Windows rendszerű virtuális asztali objektumokat Azure Resource Manager. Ha a Spring 2020 Update szolgáltatásban bevezetett Azure Resource Manager Windows rendszerű virtuális asztali objektumokat szeretne felügyelni, tekintse meg [ezt a cikket](../configure-host-pool-personal-desktop-assignment-type.md).

A személyes asztali címkészlet hozzárendelési típusának konfigurálásával beállíthatja a Windows rendszerű virtuális asztali környezetét, hogy jobban megfeleljen az igényeinek. Ebben a témakörben bemutatjuk, hogyan konfigurálhatja az automatikus vagy közvetlen hozzárendelést a felhasználók számára.

>[!NOTE]
> A cikkben szereplő utasítások csak a személyes asztali gazdagépekre érvényesek, nem készletezett gazdagépek, mivel a készletezett gazdagépek felhasználói nem vannak hozzárendelve adott munkamenet-gazdagépekhez.

## <a name="configure-automatic-assignment"></a>Automatikus hozzárendelés konfigurálása

Az automatikus hozzárendelés az alapértelmezett hozzárendelési típus a Windows rendszerű virtuális asztali környezetben létrehozott új személyes asztali gazdagépek számára. A felhasználók automatikus hozzárendeléséhez nincs szükség egy adott munkamenet-gazdagépre.

A felhasználók automatikus hozzárendeléséhez először rendelje hozzá azokat a személyes asztali gazdagéphez, hogy azok a hírcsatornában lássák az asztalt. Amikor egy hozzárendelt felhasználó elindítja az asztalt a hírcsatornájában, a rendszer egy rendelkezésre álló munkamenet-gazdagépet igényel, ha még nem csatlakoztak a gazdagéphez, amely befejezi a hozzárendelési folyamatot.

Mielőtt elkezdené, [töltse le és importálja a Windows rendszerű virtuális asztali PowerShell-modult](/powershell/windows-virtual-desktop/overview/) , ha még nem tette meg.

> [!NOTE]
> Az utasítások követése előtt győződjön meg arról, hogy telepítette a Windows rendszerű virtuális asztali PowerShell-modul 1.0.1534.2001 vagy újabb verzióját.

Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

A következő PowerShell-parancsmag futtatásával konfigurálhatja, hogy a gazdagépek automatikusan rendeljenek felhasználókat a virtuális gépekhez:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

A következő PowerShell-parancsmag futtatásával rendelhet hozzá felhasználót a személyes asztali gazdagéphez:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Közvetlen hozzárendelés konfigurálása

Az automatikus hozzárendeléstől eltérően a közvetlen hozzárendelés használatakor a felhasználót a személyes asztali gazdagéphez és egy adott munkamenet-gazdagéphez is hozzá kell rendelnie, mielőtt csatlakozni tudnak a személyes asztalhoz. Ha a felhasználó csak a munkamenetgazda-hozzárendelés nélküli gazdagépekhez van hozzárendelve, nem férhetnek hozzá az erőforrásokhoz.

A következő PowerShell-parancsmag futtatásával konfigurálhatja a gazdagépeket, hogy a felhasználók közvetlen hozzárendelését igénylik a munkamenet-gazdagépekhez:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

A következő PowerShell-parancsmag futtatásával rendelhet hozzá felhasználót a személyes asztali gazdagéphez:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Ha egy felhasználót egy adott munkamenet-gazdagéphez szeretne rendelni, futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>Felhasználói hozzárendelés eltávolítása

Érdemes lehet eltávolítani egy felhasználói hozzárendelést, mivel a felhasználónak már nincs szüksége a személyes asztalra, a felhasználó elhagyta a vállalatot, vagy ha valaki másnak szeretné használni az asztalt.

Jelenleg csak a személyes asztal felhasználói hozzárendelésének eltávolítására van lehetőség, hogy teljes mértékben eltávolítsa a munkamenet-gazdagépet. A munkamenet-gazdagép eltávolításához futtassa a következő parancsmagot:

```powershell
Remove-RdsSessionHost
```

Ha újra hozzá kell adnia a munkamenet-gazdagépet a személyes asztali gazdagéphez, távolítsa el a Windows rendszerű virtuális asztalt a gépen, majd kövesse a [gazdagép létrehozása a PowerShell](create-host-pools-powershell-2019.md) -lel a munkamenet-gazdagép újbóli regisztrálásához című témakör lépéseit.

## <a name="next-steps"></a>További lépések

Most, hogy konfigurálta a személyes asztal hozzárendelési típusát, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, és tesztelheti azt egy felhasználói munkamenet részeként. A következő két útmutató bemutatja, hogyan csatlakozhat egy munkamenethez az Ön által választott ügyfél használatával:

- [Kapcsolódás a Windows asztali ügyféllel](connect-windows-7-10-2019.md)
- [Kapcsolódás a webügyféllel](connect-web-2019.md)

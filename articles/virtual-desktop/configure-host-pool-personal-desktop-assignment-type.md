---
title: Windows Virtual Desktop személyes asztali feladattípusa – Azure
description: A Windows Virtual Desktop személyes asztali gazdakészlet hozzárendeléstípusának konfigurálása.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 41b24a94d36b21fe5d5f539e056abb535bda433a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128284"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>A személyes asztali gazdagépkészlet hozzárendeléstípusának konfigurálása

Beállíthatja a személyes asztali gazdakészlet hozzárendelési típusát úgy, hogy a Windows virtuális asztal környezetét az igényeinek megfelelően állítsa be. Ebben a témakörben bemutatjuk, hogyan konfigurálhatja az automatikus vagy közvetlen hozzárendelést a felhasználók számára.

>[!NOTE]
> A cikkben szereplő utasítások csak a személyes asztali gazdakészletekre vonatkoznak, a készletezett gazdakészletekre nem, mivel a készletezett gazdakészletekfelhasználói nincsenek hozzárendelve adott munkamenet-gazdagépekhez.

## <a name="configure-automatic-assignment"></a>Automatikus hozzárendelés konfigurálása

Az automatikus hozzárendelés a Windows virtuális asztal környezetben létrehozott új személyes asztali gazdakészletek alapértelmezett hozzárendelési típusa. A felhasználók automatikus hozzárendeléséhez nincs szükség adott munkamenet-gazdagépre.

Ha automatikusan hozzá szeretne rendelni a felhasználókat, először rendelje hozzá őket a személyes asztali gazdakészlethez, hogy azok a hírcsatornájukban láthassák az asztalt. Amikor egy hozzárendelt felhasználó elindítja az asztalt a hírcsatornájában, akkor egy elérhető munkamenet-gazdatornokat igényel, ha még nem csatlakozott a gazdakészlethez, amely befejezi a hozzárendelési folyamatot.

Mielőtt elkezdené, [töltse le és importálja a Windows Virtual Desktop PowerShell modult,](/powershell/windows-virtual-desktop/overview/) ha még nem tette meg. 

> [!NOTE]
> Az alábbi utasítások bekerülése előtt győződjön meg arról, hogy telepítette a Windows Virtual Desktop PowerShell 1.0.1534.2001-es vagy újabb verzióját.

Ezután futtassa a következő parancsmast a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ha úgy szeretne konfigurálni egy gazdakészletet, hogy a felhasználók automatikusan hozzárendeljenek a virtuális gépekhez, futtassa a következő PowerShell-parancsmacamot:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Ha egy felhasználót hozzá szeretne rendelni a személyes asztali gazdakészlethez, futtassa a következő PowerShell-parancsmackét:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Közvetlen hozzárendelés konfigurálása

Az automatikus hozzárendeléssel ellentétben a közvetlen hozzárendelés használatakor a felhasználót mind a személyes asztali gazdakészlethez, mind egy adott munkamenet-gazdagéphez hozzá kell rendelnie ahhoz, hogy a személyes asztalhoz csatlakozhassanak. Ha a felhasználó csak munkamenet-gazdagép-hozzárendelés nélküli gazdakészlethez van hozzárendelve, nem fog tudni hozzáférni az erőforrásokhoz.

Ha úgy szeretne konfigurálni egy gazdakészletet, hogy a felhasználók közvetlen hozzárendelését követelje meg a munkamenet-állomásokhoz, futtassa a következő PowerShell-parancsmacsat:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Ha egy felhasználót hozzá szeretne rendelni a személyes asztali gazdakészlethez, futtassa a következő PowerShell-parancsmackét:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Ha egy felhasználót egy adott munkamenet-állomáshoz szeretne hozzárendelni, futtassa a következő PowerShell-parancsmast:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>További lépések

Most, hogy konfigurálta a személyes asztal-hozzárendelés típusát, bejelentkezhet egy Windows virtuális asztali ügyfélbe, hogy tesztelje azt egy felhasználói munkamenet részeként. A következő két útmutató megmutatja, hogyan csatlakozhat egy munkamenethez az Ön által választott ügyfél segítségével:

- [Kapcsolódás a Windows asztali ügyféllel](connect-windows-7-and-10.md)
- [Kapcsolódás a webügyféllel](connect-web.md)

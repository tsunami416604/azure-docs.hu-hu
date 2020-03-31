---
title: A Windows virtuális asztali terheléselosztás konfigurálása – Azure
description: A terheléselosztási módszer konfigurálása Windows Virtuális asztali környezetben.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128313"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>A Windows Virtual Desktop terheléselosztási módjának beállítása

A gazdakészlet terheléselosztási módjának konfigurálása lehetővé teszi a Windows virtuális asztal környezetének az igényeinek megfelelő beállítását.

>[!NOTE]
> Ez nem vonatkozik az állandó asztali gazdakészletre, mert a felhasználók mindig 1:1 arányban vannak leképezve a gazdakészlet munkamenet-gazdagépéhez.

## <a name="configure-breadth-first-load-balancing"></a>Szélesség-első terheléselosztás konfigurálása

A szélesség-első terheléselosztás az új, nem állandó állomáskészletek alapértelmezett konfigurációja. A szélesség-első terheléselosztás új felhasználói munkameneteket oszt el a gazdakészlet összes elérhető munkamenet-állomása között. A szélesség-első terheléselosztás konfigurálásakor beállíthatja a munkamenetgazda-állomásonkénti maximális munkamenetkorlátot a gazdakészletben.

Először [töltse le és importálja a Windows Virtual Desktop PowerShell modult](/powershell/windows-virtual-desktop/overview/) a PowerShell-munkamenetben való használatra, ha még nem tette meg. Ezután futtassa a következő parancsmast a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ha egy gazdakészletet úgy szeretne beállítani, hogy a maximális munkamenet-korlát módosítása nélkül végezze el a szélesség-első terheléselosztást, futtassa a következő PowerShell-parancsmacsa:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Ha egy gazdakészletet a széles körű és első terheléselosztás végrehajtására és egy új maximális munkamenetkorlát használatára szeretne beállítani, futtassa a következő PowerShell-parancsmacsat:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Mélység-első terheléselosztás konfigurálása

A mélység-első terheléselosztás új felhasználói munkameneteket oszt ki egy elérhető munkamenet-állomásnak, amely a legtöbb kapcsolattal rendelkezik, de nem érte el a maximális munkamenet-korlát küszöbértékét. A mélység-első terheléselosztás konfigurálásakor a gazdagépkészletben munkamenet-gazdagépenként be **kell** állítania a munkamenet-korlátot.

Ha egy gazdakészletet a mélység-első terheléselosztás végrehajtására szeretne beállítani, futtassa a következő PowerShell-parancsmacsat:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```

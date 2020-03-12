---
title: A Windows rendszerű virtuális asztali terheléselosztás konfigurálása – Azure
description: A terheléselosztási módszer konfigurálása Windows rendszerű virtuális asztali környezetekhez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8670994791e360f5e3b30b90b4bea5d55464b5
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128313"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>A Windows Virtual Desktop terheléselosztási módjának beállítása

A gazdagép terheléselosztási módszerének konfigurálása lehetővé teszi a Windows rendszerű virtuális asztali környezet beállítását, hogy jobban megfeleljen az igényeinek.

>[!NOTE]
> Ez nem vonatkozik egy állandó asztali gazdagépre, mert a felhasználók mindig rendelkeznek 1:1-hozzárendeléssel a gazdagépen belüli munkamenet-gazdagéphez.

## <a name="configure-breadth-first-load-balancing"></a>A szélesség beállítása – első terheléselosztás

Szélesség – az első terheléselosztás az új, nem állandó gazdagép-készletek alapértelmezett konfigurációja. Szélesség – az első terheléselosztás az új felhasználói munkameneteket az összes elérhető munkamenet-gazdagépen elosztja a gazdagépen. A szélesség – első terheléselosztás konfigurálásakor a gazdagép-készletben beállíthatja a munkamenetek maximális számát.

Először [töltse le és importálja a](/powershell/windows-virtual-desktop/overview/) PowerShell-munkamenetben használni kívánt Windows virtuális asztali PowerShell-modult, ha még nem tette meg. Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Ha úgy szeretné konfigurálni a gazdagépet, hogy a maximális munkamenet-korlát módosítása nélkül végezze el az első terheléselosztást, futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

A következő PowerShell-parancsmag futtatásával konfigurálhat egy gazdagépet a szélesség – első terheléselosztás végrehajtásához és egy új maximális munkamenet-korlát használatához:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>A mélység beállítása – első terheléselosztás

Mélység – az első terheléselosztás új felhasználói munkameneteket oszt ki egy rendelkezésre álló, a legnagyobb számú kapcsolattal rendelkező munkamenet-gazdagépre, de nem érte el a maximális munkamenet-korlátot. A mélység beállításakor – az első terheléselosztáshoz a gazdagép-készletben **kell** beállítani a munkamenetek maximális számát.

A következő PowerShell-parancsmag futtatásával állíthatja be, hogy a gazdagép mélysége – első terheléselosztást végezzen:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```

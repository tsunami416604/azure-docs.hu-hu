---
title: Windows virtuális asztal terheléselosztási módszer konfigurálása (előzetes verzió) – Azure
description: Hogyan lehet egy Windows virtuális asztali környezetben terheléselosztási módszer konfigurálása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 10a1066b85b16749fe95e373e696d486b0e7bafa
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318344"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>Windows virtuális asztal terheléselosztási módszer konfigurálása

A terhelés terheléselosztási módszer egy gazdagép készlet konfigurálása lehetővé teszi, módosíthatja a Windows virtuális asztal (előzetes verzió) környezet, hogy jobban megfeleljen az igényeinek.

>[!NOTE]
> Ez nem vonatkozik állandó asztali gazdagéphez címkészlethez, mert a felhasználók mindig rendelkezzenek 1:1 leképezés egy munkamenet-gazdagépre a gazdagépen készleten belül.

## <a name="configure-breadth-first-load-balancing"></a>Szélesség-és felhőközpontú terheléselosztás beállítása

Szélesség-és felhőközpontú load balancing szolgáltatás az alapértelmezett konfiguráció új állomás nem állandó készletek esetében. Új felhasználói munkamenetek szélesség-és felhőközpontú terheléselosztó osztja el a gazdagép-készlet az összes elérhető munkamenet gazdagépeken. Szélesség-és felhőközpontú terheléselosztási konfigurálásakor adhatja meg a maximális munkamenet időkorlátjának munkamenet gazdagépenként a gazdagép-készletben.

Először [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

Egy gazdagép erőforráskészlet tagjai végzik a maximális munkamenet korlát módosításával anélkül szélesség-és felhőközpontú terheléselosztási konfigurálásához futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Szélesség-és felhőközpontú terheléselosztás és új maximális munkamenet használja a gazdagép készlet konfigurálásához futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Várólistamélység-és felhőközpontú terheléselosztás beállítása

Terheléselosztás mélysége-és felhőközpontú osztja el egy elérhető munkamenetgazda kapcsolatok számát vesszük figyelembe az új felhasználói munkamenetek, de nem érte el a maximális munkamenet korlát küszöbértéket. Terheléselosztás mélysége-és felhőközpontú, konfigurálásakor, **kell** munkamenet gazdagépenként egy maximális munkamenet-korlátjának beállítása a gazdagép-készletben.

Egy gazdagép erőforráskészlet tagjai végzik a mélység-és felhőközpontú terheléselosztás konfigurálásához futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```

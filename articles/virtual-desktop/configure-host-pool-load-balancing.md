---
title: A Windows rendszerű virtuális asztali terheléselosztás konfigurálása – Azure
description: A terheléselosztási módszer konfigurálása Windows rendszerű virtuális asztali környezetekhez.
author: Heidilohr
ms.topic: how-to
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 07eae73a36bf4051925547fa375f46963a162881
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010106"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>A Windows Virtual Desktop terheléselosztási módjának beállítása

A gazdagép terheléselosztási módszerének konfigurálása lehetővé teszi a Windows rendszerű virtuális asztali környezet beállítását, hogy jobban megfeleljen az igényeinek.

>[!NOTE]
> Ez nem vonatkozik egy állandó asztali gazdagépre, mert a felhasználók mindig rendelkeznek 1:1-hozzárendeléssel a gazdagépen belüli munkamenet-gazdagéphez.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy követte a [Windows rendszerű virtuális asztali PowerShell-modul beállítása](powershell-module.md) a PowerShell-modul letöltéséhez és telepítéséhez, valamint az Azure-fiókba való bejelentkezéshez szükséges lépéseket.

## <a name="configure-breadth-first-load-balancing"></a>A szélesség beállítása – első terheléselosztás

Szélesség – az első terheléselosztás az új, nem állandó gazdagép-készletek alapértelmezett konfigurációja. Szélesség – az első terheléselosztás az új felhasználói munkameneteket az összes elérhető munkamenet-gazdagépen elosztja a gazdagépen. A szélesség – első terheléselosztás konfigurálásakor a gazdagép-készletben beállíthatja a munkamenetek maximális számát.

Ha úgy szeretné konfigurálni a gazdagépet, hogy a maximális munkamenet-korlát módosítása nélkül végezze el az első terheléselosztást, futtassa a következő PowerShell-parancsmagot:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

Ezt követően, hogy meggyőződjön arról, hogy beállította a szélesség-első terheléselosztási módszert, futtassa a következő parancsmagot:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

A következő PowerShell-parancsmag futtatásával konfigurálhat egy gazdagépet a szélesség – első terheléselosztás végrehajtásához és egy új maximális munkamenet-korlát használatához:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>A mélység beállítása – első terheléselosztás

Mélység – az első terheléselosztás új felhasználói munkameneteket oszt ki egy rendelkezésre álló, a legnagyobb számú kapcsolattal rendelkező munkamenet-gazdagépre, de nem érte el a maximális munkamenet-korlátot. A mélység beállításakor – az első terheléselosztáshoz a gazdagép-készletben kell beállítani a munkamenetek maximális számát.

A következő PowerShell-parancsmag futtatásával állíthatja be, hogy a gazdagép mélysége – első terheléselosztást végezzen:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

A beállítás frissítése előtt futtassa a következő parancsmagot:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Terheléselosztás konfigurálása a Azure Portal

A terheléselosztást a Azure Portal is konfigurálhatja.

Terheléselosztás konfigurálása:

1. Jelentkezzen be Azure Portal a következő címen: https://portal.azure.com .
2. Keresse meg és válassza ki a **Windows rendszerű virtuális asztali** szolgáltatások elemet.
3. A Windows rendszerű virtuális asztal lapon válassza a **gazdagép készletek**elemet.
4. Válassza ki a szerkeszteni kívánt gazdagép-készlet nevét.
5. Válassza ki a **Tulajdonságok** elemet.
6. Adja meg a **maximális munkamenet-korlátot** a mezőbe, és válassza ki a használni kívánt **terheléselosztási algoritmust** a legördülő menüben.
7. Kattintson a **Mentés** gombra. Ez a terheléselosztás új beállításait alkalmazza.
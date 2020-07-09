---
title: Azure Cloud Service méretezése a Windows PowerShellben | Microsoft Docs
description: klasszikus Ismerje meg, hogyan méretezheti a PowerShellt egy webes szerepkör vagy feldolgozói szerepkör az Azure-ban való méretezéséhez.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "68359040"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Felhőalapú szolgáltatás méretezése a PowerShellben

A Windows PowerShell használatával a-példányok hozzáadásával vagy eltávolításával méretezheti a webes szerepköröket vagy feldolgozói szerepköröket.  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Mielőtt műveleteket hajt végre az előfizetésen a PowerShellen keresztül, be kell jelentkeznie:

```powershell
Add-AzureAccount
```

Ha több előfizetése van társítva a fiókjához, előfordulhat, hogy módosítania kell a jelenlegi előfizetést attól függően, hogy hol található a felhőalapú szolgáltatás. Az aktuális előfizetés a következő futtatásával ellenőrizhető:

```powershell
Get-AzureSubscription -Current
```

Ha módosítania kell a jelenlegi előfizetést, futtassa a következőt:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>A szerepkör aktuális példányszámának keresése

A szerepkör aktuális állapotának vizsgálatához futtassa a következőt:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Vissza kell olvasnia a szerepkörre vonatkozó információkat, beleértve az operációs rendszer aktuális verzióját és a példányszámot. Ebben az esetben a szerepkör egyetlen példánnyal rendelkezik.

![A szerepkörre vonatkozó információk](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>A szerepkör felskálázása további példányok hozzáadásával

A szerepkör felskálázásához adja meg a példányok kívánt számát a **set-AzureRole** parancsmagnak a **Count** paraméterként:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

A parancsmag egy pillanatra blokkolja az új példányok üzembe helyezését és elindítását. Ebben az időszakban, ha megnyit egy új PowerShell-ablakot, és a korábban bemutatott módon hívja meg a **Get-AzureRole-** t, akkor megjelenik az új célként megadott példányok száma. Ha megvizsgálja a szerepkör állapotát a portálon, akkor az új példány indul:

![Virtuálisgép-példány indítása a portálon](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Az új példányok elindítása után a parancsmag sikeresen visszatér:

![A szerepkör-példány nagyobb sikert mutat](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Méretezés a szerepkörben a példányok eltávolításával

A szerepköröket úgy méretezheti, hogy a példányokat ugyanúgy távolítja el. Állítsa be a **Count** paramétert a **set-AzureRole** értékre azon példányok számának megadásához, amelyeket a művelet befejezését követően szeretne használni.

## <a name="next-steps"></a>További lépések

Nem lehet konfigurálni a Cloud Services automatikus méretezését a PowerShellből. Ehhez tekintse meg [a felhőalapú szolgáltatás automatikus méretezését](cloud-services-how-to-scale-portal.md)ismertető témakört.

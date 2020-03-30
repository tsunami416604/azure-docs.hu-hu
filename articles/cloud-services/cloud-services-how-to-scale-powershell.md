---
title: Azure-felhőszolgáltatás méretezése a Windows PowerShellben | Microsoft dokumentumok
description: (klasszikus) Megtudhatja, hogyan használhatja a PowerShellt egy webes szerepkör vagy feldolgozói szerepkör méretezésére az Azure-ban vagy az Azure-ban.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: a1945aad12eb34bad1b593878779e1ceb0dae686
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359040"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Felhőszolgáltatás méretezése a PowerShellben

A Windows PowerShell segítségével skálázhatja a webes szerepkör vagy feldolgozó szerepkör példányok hozzáadásával vagy eltávolításával.  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Mielőtt bármilyen műveletet végrehajthatna az előfizetésen a PowerShellen keresztül, be kell jelentkeznie:

```powershell
Add-AzureAccount
```

Ha több előfizetést társított a fiókjához, előfordulhat, hogy módosítania kell az aktuális előfizetést attól függően, hogy hol található a felhőszolgáltatás. Az aktuális előfizetés ellenőrzéséhez futtassa a következőket:

```powershell
Get-AzureSubscription -Current
```

Ha módosítania kell az aktuális előfizetést, futtassa a következőket:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>A szerepkör aktuális példányszámának ellenőrzése

A szerepkör aktuális állapotának ellenőrzéséhez futtassa a következőket:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Vissza kell kapnia a szerepköradatait, beleértve az operációs rendszer aktuális verzióját és példányszámát. Ebben az esetben a szerepkör egyetlen példányt.

![Információ a szerepről](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>A szerepkör kiszélesedése további példányok hozzáadásával

A szerepkör horizontális felskálázásához adja át a kívánt példányszámot a **Set-AzureRole** parancsmagnak a **Count** paraméterként:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

A parancsmag blokkolja a pillanatban, amíg az új példányok vannak kiépítve, és elindult. Ez idő alatt, ha megnyit egy új PowerShell-ablakot, és hívja meg a **Get-AzureRole-t,** ahogy korábban látható, látni fogja az új célpéldányok számát. Ha pedig megvizsgálja a szerepkör állapotát a portálon, látnia kell az új példány indítását:

![Virtuálisgép-példány indítása a portálon](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Az új példányok megkezdése után a parancsmag sikeresen visszatér:

![A szerepkörpéldány sikeressé](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>A szerepkör méretezése példányok eltávolításával

Egy szerepkörben a példányok ugyanúgy való eltávolításával méretezhető. Állítsa be a **Count** paraméter **set-AzureRole** a példányok számát szeretné, hogy a méretezés befejezése után.

## <a name="next-steps"></a>További lépések

Nem lehet konfigurálni a PowerShell felhőszolgáltatások automatikus méretezését. Ehhez olvassa el [A felhőszolgáltatások automatikus méretezése](cloud-services-how-to-scale-portal.md)című témakört.

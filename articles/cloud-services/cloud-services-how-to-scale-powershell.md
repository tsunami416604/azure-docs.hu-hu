---
title: "Azure-felhőszolgáltatás méretezése a Windows PowerShell |} Microsoft Docs"
description: "(klasszikus) Megtudhatja, hogyan lehet egy webes vagy feldolgozói szerepkör bejövő vagy kimenő skálázása az Azure PowerShell használatával."
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: mmccrory
ms.openlocfilehash: a7ae8ff202d403dff19b8c9a6a09492235db27ac
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Egy felhőalapú szolgáltatás méretezése a PowerShellben

A Windows PowerShell segítségével egy webes vagy feldolgozói szerepkör bejövő vagy kimenő hozzáadásával vagy eltávolításával a példány méretezése.  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Az előfizetés, a PowerShell segítségével bármilyen műveletet elvégzése előtt be kell jelentkeznie:

```powershell
Add-AzureAccount
```

Ha a fiókjához társított több előfizetéssel rendelkezik, szükség lehet attól függően, hogy hol helyezkedik el a felhőalapú szolgáltatás jelenlegi módosítása. A jelenlegi előfizetés ellenőrzéséhez futtassa:

```powershell
Get-AzureSubscription -Current
```

Ha módosítania kell a jelenlegi előfizetés, futtassa:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Ellenőrizze a szerepkör az aktuális példányszám

A szerepkör aktuális állapotának ellenőrzéséhez futtassa:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Kell visszakapnia információt a szerepkör, beleértve az aktuális operációs rendszer verziója és a példány száma. Ebben az esetben a szerepkörhöz egyetlen példányt.

![A szerepkör kapcsolatos információk](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>A szerepkör horizontális felskálázása ismételt felvételével

A szerepkör horizontális, továbbítja a példányok, a kívánt számát a **száma** paramétert a **Set-AzureRole** parancsmagot:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

A parancsmag blokkolja az új példányok közben rövid ideig üzembe helyezve, és elindult. Ebben az időszakban, ha megnyit egy új PowerShell-ablakot, és hívás **Get-AzureRole** mint korábban, megjelenik az új cél-példányok száma. És a szerepkör állapota a portálon vizsgálja meg, ha az új példány elindítása láthatja:

![Virtuálisgép-példány a portál indítása](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Miután az új példány indult el, a parancsmag sikeresen vissza:

![Szerepkör példány növekedése sikeres](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Bővítse a szerepkör a példányok eltávolítása

Példányok ugyanúgy eltávolításával szerepkör méretezheti. Állítsa be a **száma** paraméter **Set-AzureRole** kíván használni a méretezési művelet befejeződése után példányok száma.

## <a name="next-steps"></a>Következő lépések

Nincs automatikus méretezése a powershellből felhőszolgáltatások konfigurálása lehetséges. Ehhez tekintse meg a [automatikus méretezési egy felhőalapú szolgáltatás hogyan](cloud-services-how-to-scale-portal.md).

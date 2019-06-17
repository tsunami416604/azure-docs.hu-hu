---
title: Az Azure cloud Services méretezése a Windows PowerShellben |} A Microsoft Docs
description: (klasszikus) Ismerje meg, hogyan használható a PowerShell használatával egy webes szerepkör és a feldolgozói szerepkör vagy az Azure-ban.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: ee37dd8c-6714-4c61-adb8-03d6bbf76c9a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.openlocfilehash: 6c013687faaca33938d0b27303e9b1252482fcb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963314"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Cloud service skálázása a PowerShellben

Windows PowerShell használatával méretezheti a webes szerepkör vagy a feldolgozói szerepkör vagy hozzáadásával vagy eltávolításával példányok.  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Az előfizetés Powershellen keresztül műveleteket elvégzése előtt be kell jelentkeznie:

```powershell
Add-AzureAccount
```

Ha a fiókjához társított több előfizetéssel rendelkezik, szükség lehet az aktuális előfizetést, attól függően, ahol a felhőszolgáltatás található. Az aktuális előfizetésben ellenőrzéséhez futtassa:

```powershell
Get-AzureSubscription -Current
```

Ha módosítania kell az aktuális előfizetésben, futtassa:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Ellenőrizze a szerepkör a jelenlegi példányszám

A szerepkör aktuális állapotának ellenőrzéséhez futtassa:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Szerezheti vissza a szerepkört, beleértve a jelenlegi operációs rendszer verziója és a példány szám kapcsolatos információkat. Ebben az esetben a szerepkör rendelkezik egyetlen példányt.

![A szerepkör kapcsolatos információk](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>A szerepkör horizontális felskálázása további példányok hozzáadása révén

Horizontális felskálázás a szerepköre, át kell adnia a kívánt számú példányok, a **száma** paramétert a **Set-AzureRole** parancsmagot:

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

A parancsmag blokkok rövid ideig során az új példányok üzembe helyezett és használatába. Ebben az időszakban, ha megnyit egy új PowerShell-ablakot, és az első hívás **Get-AzureRole** , ahogy ezt korábban, látni fogja az új cél-példányok száma. Nézze meg a szerepkör állapota a portálon, ha, és megjelenik az új példány indítása folyamatban:

![Virtuálisgép-példány indítása a portálon](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Miután elindította az új példányok, a parancsmag sikeresen adja vissza:

![Szerepkör-példány növekedés sikeres](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>A szerepkör eltávolításával példányok skálázása

Példányok ugyanúgy eltávolításával skálázhatja a szerepkör. Állítsa be a **száma** paraméterrel **Set-AzureRole** szeretne biztosítani a horizontális leskálázási művelet befejeződése után példányok száma.

## <a name="next-steps"></a>További lépések

Nem alkalmas konfigurálása az automatikus méretezés a cloud services, a Powershellből. Ehhez lásd [automatikus méretezése egy felhőalapú szolgáltatás](cloud-services-how-to-scale-portal.md).

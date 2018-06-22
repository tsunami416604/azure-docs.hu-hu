---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d4d370e6b76fcfc502366642842bfeb923a13991
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313676"
---
Ez a konfiguráció megkezdése előtt meg kell bejelentkezni az Azure-fiókjával. A parancsmag kéri a bejelentkezési hitelesítő adatait az Azure-fiókjával. Történő bejelentkezés után az tölti le a fiókbeállításoknál, hogy az Azure PowerShell elérhetők legyenek. További információ: [A Windows PowerShell használata a Resource Managerrel](../articles/powershell-azure-resource-manager.md).

A bejelentkezéshez nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Connect-AzureRmAccount
```

Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

```powershell
Get-AzureRmSubscription
```

Válassza ki a használni kívánt előfizetést.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```

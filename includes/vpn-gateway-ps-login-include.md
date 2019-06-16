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
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157461"
---
Mielőtt hozzálát, jelentkezzen be Azure-fiókjába. A parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adataival. Bejelentkezés után letölti a fiók beállításait így elérhetők legyenek az Azure PowerShell-lel. További információ: [A Windows PowerShell használata a Resource Managerrel](../articles/powershell-azure-resource-manager.md).

Jelentkezzen be, nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Connect-AzAccount
```

Ha több Azure-előfizetéssel is rendelkezik, ellenőrizze a fiók előfizetéseit.

```powershell
Get-AzSubscription
```

Válassza ki a használni kívánt előfizetést.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```

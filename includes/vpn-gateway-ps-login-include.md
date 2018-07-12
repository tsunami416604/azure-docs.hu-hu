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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38732674"
---
Mielőtt hozzálát, jelentkezzen be Azure-fiókjába. A parancsmag kéri az Azure-fiók bejelentkezési hitelesítő adataival. Bejelentkezés után letölti a fiók beállításait így elérhetők legyenek az Azure PowerShell-lel. További információ: [A Windows PowerShell használata a Resource Managerrel](../articles/powershell-azure-resource-manager.md).

Jelentkezzen be, nyissa meg a PowerShell-konzolt emelt szintű jogosultságokkal, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

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

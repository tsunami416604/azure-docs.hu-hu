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
ms.openlocfilehash: 188f70d17531a3a7da12444ce5f1c97385ea33bc
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418041"
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

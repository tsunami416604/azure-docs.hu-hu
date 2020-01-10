---
title: Exchange-társ létrehozása vagy módosítása a PowerShell-lel
titleSuffix: Azure
description: Exchange-társ létrehozása vagy módosítása a PowerShell-lel
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774405"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Exchange-társ létrehozása vagy módosítása a PowerShell-lel

Ez a cikk azt ismerteti, hogyan hozható létre Microsoft Exchange-társ a PowerShell-parancsmagok és a Resource Manager-alapú üzemi modell használatával. A cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, ezt az útmutatót a [portálon](howto-exchange-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [Előfeltételek](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítás áttekintése című témakört.
* Ha már rendelkezik olyan Exchange-partnerekkel, amelyek nem az Azure-erőforrásokra vannak konvertálva, tekintse át az [örökölt Exchange-társítás átalakítása Azure-erőforrásra a PowerShell használatával](howto-legacy-exchange-powershell.md) című témakört.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társ létrehozása és kiépítése

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=exchange-location></a>Az Exchange-társítás támogatott társi helyeinek listájának beolvasása
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name=create></a>Exchange-társ létrehozása
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name=get></a>Exchange-társítás beolvasása
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify"></a>Exchange-társak módosítása
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name=delete></a>Exchange-társ kiépítése

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>Következő lépések

* [Közvetlen társ létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md)
* [Örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```

További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.

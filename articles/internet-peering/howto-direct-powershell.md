---
title: Közvetlen társ létrehozása vagy módosítása a PowerShell használatával
titleSuffix: Azure
description: Közvetlen társ létrehozása vagy módosítása a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 69031550bdab1535213c78f81426fa76e8ea62ad
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774236"
---
# <a name="create-or-modify-a-direct-peering-using-powershell"></a>Közvetlen társ létrehozása vagy módosítása a PowerShell használatával

Ez a cikk azt ismerteti, hogyan hozható létre Microsoft Direct-társ a PowerShell-parancsmagok és a Resource Manager-alapú üzemi modell használatával. A cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, ezt az útmutatót a [portálon](howto-direct-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [Előfeltételek](prerequisites.md) és a közvetlen társítási [útmutató](walkthrough-direct-all.md) témakört.
* Abban az esetben, ha a Microsofttal való közvetlen együttműködés már megtörtént, amelyek nem az Azure-erőforrásokra vannak átalakítva, tekintse át a következő témakört: [örökölt közvetlen társítás átalakítása Azure-erőforrásra PowerShell](howto-legacy-direct-powershell.md)

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Közvetlen társítás létrehozása és kiépítése

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be az Azure-fiókjába, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name=direct-location></a>A támogatott peering-alapú telephelyek listájának beolvasása a közvetlen társításhoz
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name=create></a>Közvetlen társ létrehozása
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name=get></a>Közvetlen társítás ellenőrzése
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify"></a>Közvetlen társítás módosítása
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="delete"></a>Közvetlen társak kiépítése
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md).
* [Örökölt Exchange-társítás átalakítása Azure-erőforrásra a PowerShell használatával](howto-legacy-exchange-powershell.md).

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```

További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.

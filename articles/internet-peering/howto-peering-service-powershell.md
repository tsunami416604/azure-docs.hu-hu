---
title: A peering Service engedélyezése közvetlenül a PowerShell használatával
titleSuffix: Azure
description: A peering Service engedélyezése közvetlenül a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774184"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>A peering Service engedélyezése közvetlenül a PowerShell használatával

Ez a cikk azt ismerteti, hogyan engedélyezhető a társ-kezelő szolgáltatás közvetlen kiosztásban PowerShell-parancsmagok és a Resource Manager-alapú [üzemi](overview-peering-service.md) modell használatával.

Ha szeretné, ezt az útmutatót a [portálon](howto-peering-service-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) .
* Válasszon közvetlen társítást az előfizetésben, amelyen engedélyezni szeretné a társ-szolgáltatási szolgáltatást. Ha nem rendelkezik ilyennel, alakítsa át az örökölt közvetlen társítást, vagy hozzon létre egy új közvetlen társítást.
    * Örökölt közvetlen társítás átalakításához kövesse az [örökölt közvetlen társítás átalakítása Azure-erőforrásra a PowerShell használatával](howto-legacy-direct-powershell.md)című témakör utasításait.
    * Új közvetlen társítás létrehozásához kövesse a [közvetlen társítás létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md)című témakör utasításait.

### <a name="working-with-azure-powershell"></a>A Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Egyenrangú szolgáltatás engedélyezése közvetlen társításon

### <a name= get></a>Közvetlen társak megtekintése
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name= get></a>A közvetlen társítás engedélyezése a partneri szolgáltatás számára

Miután lekéri a közvetlen társítást az előző lépésben, engedélyezze a társítási szolgáltatáshoz.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Közvetlen társas kapcsolatok módosítása

Ha módosítania kell a kapcsolódási beállításokat, tekintse át a közvetlen társítás [létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md) című témakör **közvetlen társítás módosítása** című szakaszát.

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a PowerShell-lel](howto-exchange-powershell.md)
* [Örökölt Exchange-társ átalakítása Azure-erőforrásra a PowerShell használatával](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```

A gyakori kérdésekért lásd: a [peering Service gyakori](service-faqs.md)kérdései.
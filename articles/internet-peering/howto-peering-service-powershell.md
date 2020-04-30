---
title: Azure-alapú társ-kezelés engedélyezése közvetlenül a PowerShell használatával
titleSuffix: Azure
description: Azure-alapú társ-kezelés engedélyezése közvetlenül a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686967"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Azure-alapú társ-kezelés engedélyezése közvetlenül a PowerShell használatával

Ez a cikk azt ismerteti, hogyan engedélyezhető az Azure- [partneri szolgáltatás](overview-peering-service.md) közvetlen társítása PowerShell-parancsmagok és a Azure Resource Manager telepítési modell használatával.

Ha szeretné, ezt az útmutatót az Azure [Portalon](howto-peering-service-portal.md)végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) .
* Válasszon közvetlen társítást az előfizetésben, amelyhez engedélyezni szeretné a társ-szolgáltatást. Ha még nem rendelkezik ilyennel, alakítsa át az örökölt közvetlen társítást, vagy hozzon létre egy új közvetlen társat:
    * Örökölt közvetlen társítás átalakításához kövesse az [örökölt közvetlen társ konvertálása Azure-erőforrásra a PowerShell használatával](howto-legacy-direct-powershell.md)című témakör utasításait.
    * Új közvetlen társ létrehozásához kövesse a [közvetlen társítás létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md)című témakör utasításait.

### <a name="work-with-azure-powershell"></a>Azure PowerShell használata
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>A Peering Service engedélyezése közvetlen társviszony-létesítés esetében

### <a name="view-direct-peering"></a><a name= get></a>Közvetlen társak megtekintése
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>A közvetlen társítás engedélyezése a partneri szolgáltatás számára

Miután megszerezte a közvetlen társítást az előző lépésben, engedélyezze a társítási szolgáltatáshoz.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Közvetlen társas kapcsolatok módosítása

Ha módosítania kell a kapcsolódási beállításokat, tekintse meg a [közvetlen társítás létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md)című témakör "közvetlen társítás módosítása" című szakaszát.

## <a name="next-steps"></a>További lépések

* [Exchange-társ létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md)
* [Örökölt Exchange-társ konvertálása Azure-erőforrásra a PowerShell használatával](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>További háttéranyagok
Az összes paraméter részletes leírását a következő parancs futtatásával érheti el:

```powershell
Get-Help Get-AzPeering -detailed
```

A gyakori kérdésekért tekintse meg a következő témakört: [peering Service – gyakori](service-faqs.md)kérdések.
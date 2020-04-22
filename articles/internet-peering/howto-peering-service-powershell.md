---
title: Az Azure-társviszony-létesítési szolgáltatás engedélyezése közvetlen társviszony-létesítésen a PowerShell használatával
titleSuffix: Azure
description: Az Azure-társviszony-létesítési szolgáltatás engedélyezése közvetlen társviszony-létesítésen a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686967"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Az Azure-társviszony-létesítési szolgáltatás engedélyezése közvetlen társviszony-létesítésen a PowerShell használatával

Ez a cikk bemutatja, hogyan engedélyezheti az [Azure-társviszony-létesítési szolgáltatást](overview-peering-service.md) közvetlen társviszony-létesítésen a PowerShell-parancsmagok és az Azure Resource Manager telepítési modell használatával.

Ha szeretné, ezt az útmutatót az Azure Portal használatával is [elvégezheti.](howto-peering-service-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket.](prerequisites.md)
* Válasszon közvetlen társviszony-létesítést az előfizetésében, amelyhez engedélyezni szeretné a társviszony-létesítési szolgáltatást. Ha még nem rendelkezik ilyentel, konvertáljon egy örökölt közvetlen társviszony-létesítést, vagy hozzon létre egy új közvetlen társviszony-létesítést:
    * Örökölt közvetlen társviszony-létesítés konvertálásához kövesse az [örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá](howto-legacy-direct-powershell.md)a PowerShell használatával című útmutató utasításait.
    * Új közvetlen társviszony-létesítés létrehozásához kövesse a [Közvetlen társviszony-létesítés létrehozása vagy módosítása](howto-direct-powershell.md)a PowerShell használatával című útmutató utasításait.

### <a name="work-with-azure-powershell"></a>Az Azure PowerShell munkája
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>A Peering Service engedélyezése közvetlen társviszony-létesítés esetében

### <a name="view-direct-peering"></a><a name= get></a>Közvetlen társviszony-létesítés megtekintése
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Társviszony-létesítés engedélyezése társviszony-létesítéshez

Miután közvetlen társviszony-létesítésaz előző lépésben, engedélyezze azt a társviszony-létesítési szolgáltatás.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Közvetlen társviszony-létesítési kapcsolat módosítása

Ha módosítania kell a kapcsolatbeállításokat, olvassa el a "Közvetlen társviszony-létesítés módosítása" című szakaszát, a [Közvetlen társviszony-létesítést a PowerShell használatával](howto-direct-powershell.md)című témakörben.

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md)
* [Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```powershell
Get-Help Get-AzPeering -detailed
```

A gyakori kérdésekről a [Társviszony-létesítési szolgáltatás gyIK című témakörben talál.](service-faqs.md)
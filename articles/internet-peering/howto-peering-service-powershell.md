---
title: Társviszony-létesítésengedélyezése közvetlen társviszony-létesítésen a PowerShell használatával
titleSuffix: Azure
description: Társviszony-létesítésengedélyezése közvetlen társviszony-létesítésen a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774184"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Társviszony-létesítésengedélyezése közvetlen társviszony-létesítésen a PowerShell használatával

Ez a cikk bemutatja, hogyan engedélyezheti [a társviszony-létesítési szolgáltatást](overview-peering-service.md) egy közvetlen társviszony-létesítésen a PowerShell-parancsmagok és az Erőforrás-kezelő telepítési modell használatával.

Ha szeretné, ezt az útmutatót a portál segítségével is [elvégezheti.](howto-peering-service-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az előfeltételeket.](prerequisites.md)
* Válasszon egy közvetlen társviszony-létesítést az előfizetésében, amelyen engedélyezni szeretné a társviszony-létesítési szolgáltatást. Ha nem rendelkezik ilyen, konvertáljon egy örökölt közvetlen társviszony-létesítést, vagy hozzon létre egy új közvetlen társviszony-létesítést.
    * Örökölt közvetlen társviszony-létesítés konvertálásához kövesse az [örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrásba](howto-legacy-direct-powershell.md)a PowerShell használatával című útmutató utasításait.
    * Új közvetlen társviszony-létesítés létrehozásához kövesse a [PowerShell használatával közvetlen társviszony-létesítés létrehozása vagy módosítása](howto-direct-powershell.md)című részben található utasításokat.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>A Peering Service engedélyezése közvetlen társviszony-létesítés esetében

### <a name="view-direct-peering"></a><a name= get></a>Közvetlen társviszony-létesítés megtekintése
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Társviszony-létesítés engedélyezése társviszony-létesítéshez

Miután az előző lépésben közvetlen társviszony-létesítést kapott, engedélyezze azt társviszony-létesítési szolgáltatáshoz.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Közvetlen társviszony-létesítési kapcsolat módosítása

Ha módosítania kell a kapcsolatbeállításokat, olvassa el a **Közvetlen társviszony-létesítés módosítása szakaszt** [a Közvetlen társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával című szakaszban.](howto-direct-powershell.md)

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md)
* [Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```powershell
Get-Help Get-AzPeering -detailed
```

A gyakori kérdésekről a [Társviszony-létesítési szolgáltatás gyIK -ja](service-faqs.md)látható.
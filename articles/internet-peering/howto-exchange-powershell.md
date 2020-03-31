---
title: Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával
titleSuffix: Azure
description: Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 4fd7d345e5efbe6e4e86e5bb410e2df4dd917047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774405"
---
# <a name="create-or-modify-an-exchange-peering-using-powershell"></a>Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával

Ez a cikk azt ismerteti, hogyan hozhat létre Microsoft Exchange-társviszony-létesítést a PowerShell-parancsmagok és az Erőforrás-kezelő telepítési modell használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az erőforrás állapotát, frissítheti, illetve törölheti és megszüntetheti azt.

Ha szeretné, ezt az útmutatót a portál segítségével is [elvégezheti.](howto-exchange-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az Előfeltételek](prerequisites.md) és [az Exchange-társviszony-létesítési forgatókönyvet.](walkthrough-exchange-all.md)
* Abban az esetben, ha már rendelkezik Exchange-társviszony-létesítéssel a Microsofttal, amely nem konvertálódik Azure-erőforrásokká, tekintse meg [egy örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával](howto-legacy-exchange-powershell.md)

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társviszony-létesítés létrehozása és kiépítése

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-exchange-peering"></a><a name=exchange-location></a>Az Exchange-társviszony-létesítés támogatott társviszony-létesítési helyeinek listájának beszereznie
[!INCLUDE [exchange-location](./includes/exchange-powershell-create-location.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange-társviszony-létesítés létrehozása
[!INCLUDE [exchange-peering](./includes/exchange-powershell-create-connection.md)]

### <a name="get-exchange-peering"></a><a name=get></a>Az Exchange-társviszony-létesítés beszereznie
[!INCLUDE [peering-exchange-get](./includes/exchange-powershell-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange-társviszony-létesítés módosítása
[!INCLUDE [peering-exchange-modify](./includes/exchange-powershell-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name=delete></a>Exchange-társviszony-létesítés megszüntetése

[!INCLUDE [peering-exchange-delete](./includes/delete.md)]

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával](howto-direct-powershell.md)
* [Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrásba a PowerShell használatával](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```powershell
Get-Help Get-AzPeering -detailed
```

További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)

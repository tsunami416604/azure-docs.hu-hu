---
title: Közvetlen társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával
titleSuffix: Azure
description: Közvetlen társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 7639499aaef8d479c2552849b2124e709c46fd36
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680787"
---
# <a name="create-or-modify-a-direct-peering-by-using-powershell"></a>Közvetlen társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával

Ez a cikk ismerteti, hogyan hozhat létre egy Microsoft Direct társviszony-létesítés a PowerShell-parancsmagok és az Azure Resource Manager telepítési modell használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az erőforrás állapotát, frissítheti, illetve törölheti és megszüntetheti azt.

Ha szeretné, ezt az útmutatót az Azure Portal használatával is [elvégezheti.](howto-direct-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen társviszony-létesítési forgatókönyvet.](walkthrough-direct-all.md)
* Ha már rendelkezik közvetlen társviszony-létesítési kapcsolatokkal a Microsofttal, amelyek nincsenek azure-erőforrásokká konvertálva, olvassa el [az Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá](howto-legacy-direct-powershell.md)a PowerShell használatával című témakört.

### <a name="work-with-azure-powershell"></a>Az Azure PowerShell munkája
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-and-provision-a-direct-peering"></a>Közvetlen társviszony-létesítés létrehozása és kiépítése

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Jelentkezzen be Azure-fiókjába, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-the-list-of-supported-peering-locations-for-direct-peering"></a><a name=direct-location></a>A támogatott társviszony-létesítési helyek listájának beszereznie a közvetlen társviszony-létesítéshez
[!INCLUDE [direct-location](./includes/direct-powershell-create-location.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Közvetlen társviszony-létesítés létrehozása
[!INCLUDE [direct-peering](./includes/direct-powershell-create-connection.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társviszony-létesítés ellenőrzése
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Közvetlen társviszony-létesítés módosítása
[!INCLUDE [peering-direct-modify](./includes/direct-powershell-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Közvetlen társviszony-létesítés megszüntetése
[!INCLUDE [peering-direct-delete](./includes/delete.md)]

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával](howto-exchange-powershell.md)
* [Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```powershell
Get-Help Get-AzPeering -detailed
```

További információt az [Internetes társviszony-létesítés – gyakori kérdések című témakörben talál.](faqs.md)

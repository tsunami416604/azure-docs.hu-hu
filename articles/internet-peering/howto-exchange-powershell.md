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
ms.openlocfilehash: 23c905f148da614c7785b61b76abed191206cd90
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678667"
---
# <a name="create-or-modify-an-exchange-peering-by-using-powershell"></a>Exchange-társviszony-létesítés létrehozása vagy módosítása a PowerShell használatával

Ez a cikk azt ismerteti, hogyan hozhat létre Microsoft Exchange-társviszony-létesítést a PowerShell-parancsmagok és az Erőforrás-kezelő telepítési modell használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az erőforrás állapotát, frissítheti, illetve törölheti és megszüntetheti azt.

Ha szeretné, ezt az útmutatót az Azure Portal használatával is [elvégezheti.](howto-exchange-portal.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange-társviszony-létesítési forgatókönyvet.](walkthrough-exchange-all.md)
* Ha már rendelkezik Exchange-társviszony-létesítéssel a Microsofttal, amely nem konvertálva Azure-erőforrásokká, olvassa el [az Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával](howto-legacy-exchange-powershell.md)lehetőséget.

### <a name="work-with-azure-powershell"></a>Az Azure PowerShell munkája
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
* [Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá a PowerShell használatával](howto-legacy-direct-powershell.md)

## <a name="additional-resources"></a>További források
Az összes paraméter részletes leírását a következő parancs futtatásával kaphatja meg:

```powershell
Get-Help Get-AzPeering -detailed
```

További információt az [Internetes társviszony-létesítés – gyakori kérdések című témakörben talál.](faqs.md)

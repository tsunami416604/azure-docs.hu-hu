---
title: Közvetlen társviszony létesítése vagy módosítása a portál használatával
titleSuffix: Azure
description: Közvetlen társviszony létesítése vagy módosítása a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775328"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Közvetlen társviszony létesítése vagy módosítása a portál használatával

Ez a cikk azt ismerteti, hogyan hozhat létre Microsoft Direct társviszony-létesítést a portál használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az erőforrás állapotát, frissítheti, illetve törölheti és megszüntetheti azt.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-direct-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az előfeltételeket](prerequisites.md) és [a közvetlen társviszony-létesítési forgatókönyvet.](walkthrough-direct-all.md)
* Abban az esetben, ha már közvetlen társviszony-létesítést folytat a Microsofttal, amely nem konvertálódik Azure-erőforrásokká, olvassa el [az Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrásba a portál használatával című részt.](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Közvetlen társviszony-létesítés létrehozása és kiépítése

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Közvetlen társviszony-létesítés létrehozása

Új társviszony-létesítési kérelmet **társviszony-létesítési** erőforrás használatával hozhat létre.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás indítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társviszony-létesítés ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Közvetlen társviszony-létesítés módosítása
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Közvetlen társviszony-létesítés megszüntetése
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a portálon keresztül.](howto-exchange-portal.md)
* [A portálon örökölt Exchange-társviszony-létesítést alakítát Azure-erőforrássá.](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)

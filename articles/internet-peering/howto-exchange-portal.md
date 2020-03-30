---
title: Exchange-társviszony létesítése vagy módosítása a portál használatával
titleSuffix: Azure
description: Exchange-társviszony létesítése vagy módosítása a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774574"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Exchange-társviszony létesítése vagy módosítása a portál használatával

Ez a cikk azt ismerteti, hogy miként hozhat létre Microsoft Exchange társviszony-létesítést a portál használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizheti az erőforrás állapotát, frissítheti, illetve törölheti és megszüntetheti azt.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-exchange-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az Előfeltételek](prerequisites.md) és [az Exchange-társviszony-létesítési forgatókönyvet.](walkthrough-exchange-all.md)
* Abban az esetben, ha már rendelkezik Exchange-társviszony-létesítéssel a Microsofttal, amely nem konvertálódik Azure-erőforrásokká, olvassa el [az Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával című részt.](howto-legacy-exchange-portal.md)

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társviszony-létesítés létrehozása és kiépítése

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange-társviszony-létesítés létrehozása

Új társviszony-létesítési kérelmet **társviszony-létesítési** erőforrás használatával hozhat létre.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás indítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Exchange-társviszony-létesítés ellenőrzése
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange-társviszony-létesítés módosítása
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Exchange-társviszony-létesítés megszüntetése
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony létesítése vagy módosítása a portál használatával](howto-direct-portal.md)
* [Örökölt közvetlen társviszony konvertálása Azure-erőforrássá a portál használatával](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>További források

További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)

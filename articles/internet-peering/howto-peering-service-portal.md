---
title: A Peering Service engedélyezése közvetlen társviszony-létesítés esetében a portál használatával
titleSuffix: Azure
description: A Peering Service engedélyezése közvetlen társviszony-létesítés esetében a portál használatával
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129960"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>A Peering Service engedélyezése közvetlen társviszony-létesítés esetében a portál használatával

Ez a cikk azt ismerteti, hogyan engedélyezheti a [társviszony-létesítési szolgáltatást](overview-peering-service.md) egy közvetlen társviszony-létesítésen a portál használatával.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-peering-service-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az előfeltételeket.](prerequisites.md)
* Válasszon egy közvetlen társviszony-létesítést az előfizetésében, amelyen engedélyezni szeretné a társviszony-létesítési szolgáltatást. Ha nem rendelkezik ilyen, konvertáljon egy örökölt közvetlen társviszony-létesítést, vagy hozzon létre egy új közvetlen társviszony-létesítést.
    * Örökölt közvetlen társviszony-létesítés konvertálásához kövesse az [örökölt közvetlen társviszony-létesítés átalakítása azure-erőforrásba a portál használatával](howto-legacy-direct-portal.md)című útmutató utasításait.
    * Új közvetlen társviszony-létesítés létrehozásához kövesse a [Közvetlen társviszony-létesítés létrehozása vagy módosítása a portálhasználatával című](howto-direct-portal.md)részben található utasításokat.

## <a name="enable-peering-service-on-a-direct-peering"></a>A Peering Service engedélyezése közvetlen társviszony-létesítés esetében

### <a name="view-direct-peering"></a><a name= get></a>Közvetlen társviszony-létesítés megtekintése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Társviszony-létesítés engedélyezése társviszony-létesítéshez

Az előző lépésközvetlen társviszony-létesítésének megnyitása után engedélyezze azt társviszony-létesítési szolgáltatáshoz.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Közvetlen társviszony-létesítési kapcsolat módosítása

Ha módosítania kell a kapcsolatbeállításokat, olvassa el a **Közvetlen társviszony-létesítés módosítása** szakaszát [a Közvetlen társviszony-létesítés létrehozása vagy módosítása a portálhasználatával című szakaszban.](howto-direct-portal.md)

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társviszony konvertálása Azure-erőforrássá a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

A gyakori kérdésekről a [Társviszony-létesítési szolgáltatás gyIK -ja](service-faqs.md)látható.
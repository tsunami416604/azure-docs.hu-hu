---
title: Az Azure-társviszony-létesítési szolgáltatás engedélyezése közvetlen társviszony-létesítésen az Azure Portal használatával
titleSuffix: Azure
description: Az Azure-társviszony-létesítési szolgáltatás engedélyezése közvetlen társviszony-létesítésen az Azure Portal használatával
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687058"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Az Azure-társviszony-létesítési szolgáltatás engedélyezése közvetlen társviszony-létesítésen az Azure Portal használatával

Ez a cikk ismerteti, hogyan engedélyezheti az [Azure-társviszony-létesítési szolgáltatás](overview-peering-service.md) közvetlen társviszony-létesítés az Azure Portal használatával.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-peering-service-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket.](prerequisites.md)
* Válasszon közvetlen társviszony-létesítést az előfizetésében, amelyhez engedélyezni szeretné a társviszony-létesítési szolgáltatást. Ha még nem rendelkezik ilyentel, konvertáljon egy örökölt közvetlen társviszony-létesítést, vagy hozzon létre egy új közvetlen társviszony-létesítést:
    * Örökölt közvetlen társviszony-létesítés konvertálásához kövesse az [örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával](howto-legacy-direct-portal.md)című útmutató utasításait.
    * Új közvetlen társviszony-létesítés létrehozásához kövesse a [Közvetlen társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)című útmutató utasításait.

## <a name="enable-peering-service-on-a-direct-peering"></a>A Peering Service engedélyezése közvetlen társviszony-létesítés esetében

### <a name="view-direct-peering"></a><a name= get></a>Közvetlen társviszony-létesítés megtekintése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>Társviszony-létesítés engedélyezése társviszony-létesítéshez

Miután megnyitotta a közvetlen társviszony-létesítésaz előző lépésben, engedélyezze azt társviszony-létesítési szolgáltatás.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Közvetlen társviszony-létesítési kapcsolat módosítása

A kapcsolatbeállítások módosításához olvassa el a Közvetlen [társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)című szakasz "Közvetlen társviszony-létesítés módosítása" című szakaszát.

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társviszony-létesítés átalakítása Azure-erőforrássá a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

A gyakori kérdésekről a [Társviszony-létesítési szolgáltatás gyIK című témakörben talál.](service-faqs.md)
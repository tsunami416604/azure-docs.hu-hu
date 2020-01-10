---
title: Peering Service engedélyezése közvetlen társon a portál használatával
titleSuffix: Azure
description: Peering Service engedélyezése közvetlen társon a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774977"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Peering Service engedélyezése közvetlen társon a portál használatával

Ez a cikk azt ismerteti, hogyan engedélyezheti a [társ-szolgáltatási szolgáltatást](overview-peering-service.md) a portálon keresztüli közvetlen társon.

Ha szeretné, a [PowerShell](howto-peering-service-powershell.md)használatával is elvégezheti ezt az útmutatót.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) .
* Válasszon közvetlen társítást az előfizetésben, amelyen engedélyezni szeretné a társ-szolgáltatási szolgáltatást. Ha nem rendelkezik ilyennel, alakítsa át az örökölt közvetlen társítást, vagy hozzon létre egy új közvetlen társítást.
    * Örökölt közvetlen társak konvertálásához kövesse a [örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)című témakör utasításait.
    * Új közvetlen társítás létrehozásához kövesse a [közvetlen társítás létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)című témakör utasításait.

## <a name="enable-peering-service-on-a-direct-peering"></a>Egyenrangú szolgáltatás engedélyezése közvetlen társításon

### <a name= get></a>Közvetlen társak megtekintése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>A közvetlen társítás engedélyezése a partneri szolgáltatás számára

Miután megnyitotta a közvetlen társítást az előző lépésben, engedélyezze a társítási szolgáltatás számára.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Közvetlen társas kapcsolatok módosítása

Ha módosítania kell a kapcsolódási beállításokat, tekintse át a közvetlen társítás [létrehozása vagy módosítása a portál használatával](howto-direct-portal.md) című témakör **közvetlen társítás módosítása** című szakaszát.

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társ átalakítása az Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További források

A gyakori kérdésekért lásd: a [peering Service gyakori](service-faqs.md)kérdései.
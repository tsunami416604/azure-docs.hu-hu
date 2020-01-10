---
title: Közvetlen társ létrehozása vagy módosítása a portál használatával
titleSuffix: Azure
description: Közvetlen társ létrehozása vagy módosítása a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 67a4944082d3ebc14fd564eedee0310afe6e3ff5
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775328"
---
# <a name="create-or-modify-a-direct-peering-using-the-portal"></a>Közvetlen társ létrehozása vagy módosítása a portál használatával

Ez a cikk bemutatja, hogyan hozhat létre Microsoft Direct-társat a portál használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, a [PowerShell](howto-direct-powershell.md)használatával is elvégezheti ezt az útmutatót.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [Előfeltételek](prerequisites.md) és a közvetlen társítási [útmutató](walkthrough-direct-all.md) témakört.
* Ha már rendelkezik a Microsofttal való közvetlen együttműködéssel, amelyek nem az Azure-erőforrásokra vannak átalakítva, tekintse át a következőt: [örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)

## <a name="create-and-provision-a-direct-peering"></a>Közvetlen társítás létrehozása és kiépítése

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Közvetlen társ létrehozása

Hozzon létre egy új társ-létrehozási kérést a **társ** -erőforrás használatával.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás elindítása és alapszintű beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name=get></a>Közvetlen társítás ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify"></a>Közvetlen társítás módosítása
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="delete"></a>Közvetlen társak kiépítése
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Következő lépések

* [Exchange-társ létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md).
* [Örökölt Exchange-társ átalakítása az Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md).

## <a name="additional-resources"></a>További források

További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.

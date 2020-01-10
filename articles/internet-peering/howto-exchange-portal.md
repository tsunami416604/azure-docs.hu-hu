---
title: Exchange-társ létrehozása vagy módosítása a portál használatával
titleSuffix: Azure
description: Exchange-társ létrehozása vagy módosítása a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 2c186decf68d167ab2c5ab7696c2dfb51d77a071
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774574"
---
# <a name="create-or-modify-an-exchange-peering-using-the-portal"></a>Exchange-társ létrehozása vagy módosítása a portál használatával

Ez a cikk azt ismerteti, hogyan hozható létre Microsoft Exchange-társ a portál használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, a [PowerShell](howto-exchange-powershell.md)használatával is elvégezheti ezt az útmutatót.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [Előfeltételek](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítás áttekintése című témakört.
* Ha már rendelkezik Exchange-partnerekkel a Microsofttal, amelyek nem az Azure-erőforrásokra vannak átalakítva, tekintse át az [örökölt Exchange-társítás átalakítása az Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md) című témakört.

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társ létrehozása és kiépítése

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Exchange-társ létrehozása

Hozzon létre egy új társ-létrehozási kérést a **társ** -erőforrás használatával.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás elindítása és alapszintű beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name=get></a>Exchange-társak ellenőrzése
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify"></a>Exchange-társak módosítása
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="delete"></a>Exchange-társ kiépítése
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>Következő lépések

* [Közvetlen társ létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)
* [Örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>További források

További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.

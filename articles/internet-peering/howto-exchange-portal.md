---
title: Exchange-társ létrehozása vagy módosítása a Azure Portal használatával
titleSuffix: Azure
description: Exchange-társ létrehozása vagy módosítása a Azure Portal használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e4f2ee72cbe17c094567aab5c7cc4720b02cde68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680951"
---
# <a name="create-or-modify-an-exchange-peering-by-using-the-azure-portal"></a>Exchange-társ létrehozása vagy módosítása a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan hozható létre Microsoft Exchange-társ a Azure Portal használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, az útmutatót a [PowerShell](howto-exchange-powershell.md)használatával végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és az [Exchange](walkthrough-exchange-all.md) -társítási útmutatót.
* Ha már rendelkezik olyan Exchange-partnerekkel, amelyek nem az Azure-erőforrásokra lettek konvertálva, tekintse meg a [örökölt Exchange-társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md)című témakört.

## <a name="create-and-provision-an-exchange-peering"></a>Exchange-társ létrehozása és kiépítése

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-an-exchange-peering"></a><a name=create></a>Exchange-társ létrehozása

Hozzon létre egy **új társ-** létrehozási kérést a társítási erőforrás használatával.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Az erőforrás elindítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration.md)]

### <a name="verify-an-exchange-peering"></a><a name=get></a>Exchange-társak ellenőrzése
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="modify-an-exchange-peering"></a><a name="modify"></a>Exchange-társak módosítása
[!INCLUDE [peering-exchange-modify-portal](./includes/exchange-portal-modify.md)]

## <a name="deprovision-an-exchange-peering"></a><a name="delete"></a>Exchange-társ kiépítése
[!INCLUDE [peering-exchange-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>További lépések

* [Közvetlen társ létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)
* [Örökölt közvetlen társak konvertálása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)

## <a name="additional-resources"></a>További háttéranyagok

További információ: [internetes peering GYIK](faqs.md).

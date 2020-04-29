---
title: Közvetlen társ létrehozása vagy módosítása a Azure Portal használatával
titleSuffix: Azure
description: Közvetlen társ létrehozása vagy módosítása a Azure Portal használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: dcd6aaf584691005dd071a7aba5958070f598978
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81681068"
---
# <a name="create-or-modify-a-direct-peering-by-using-the-azure-portal"></a>Közvetlen társ létrehozása vagy módosítása a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan hozható létre a Microsoft Direct peering a Azure Portal használatával. Ez a cikk azt is bemutatja, hogyan ellenőrizhető az erőforrás állapota, hogyan frissíthető vagy törölhető és kiépíthető.

Ha szeretné, ezt az útmutatót az Azure [PowerShell](howto-direct-powershell.md)használatával végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen](walkthrough-direct-all.md) társítási útmutatót.
* Ha már rendelkezik olyan közvetlen kapcsolódási kapcsolattal a Microsofttal, amely nem az Azure-erőforrásokra lett konvertálva, tekintse meg a [örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával](howto-legacy-direct-portal.md)című témakört.

## <a name="create-and-provision-a-direct-peering"></a>Közvetlen társítás létrehozása és kiépítése

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="create-a-direct-peering"></a><a name=create></a>Közvetlen társ létrehozása

Hozzon létre egy **új társ-** létrehozási kérést a társítási erőforrás használatával.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás elindítása és alapszintű beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társítás ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="modify-a-direct-peering"></a><a name="modify"></a>Közvetlen társítás módosítása
[!INCLUDE [peering-direct-modify-portal](./includes/direct-portal-modify.md)]

## <a name="deprovision-a-direct-peering"></a><a name="delete"></a>Közvetlen társak kiépítése
[!INCLUDE [peering-direct-delete-portal](./includes/delete.md)]

## <a name="next-steps"></a>További lépések

* [Exchange-társ létrehozása vagy módosítása a portál használatával](howto-exchange-portal.md)
* [Örökölt Exchange-társ átalakítása Azure-erőforrásra a portál használatával](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>További háttéranyagok

További információ: [internetes peering GYIK](faqs.md).

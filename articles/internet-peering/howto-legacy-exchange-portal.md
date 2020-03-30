---
title: Örökölt közvetlen társviszony konvertálása Azure-erőforrássá a portál használatával
titleSuffix: Azure
description: Örökölt közvetlen társviszony konvertálása Azure-erőforrássá a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a40c7bbc9f37135814b7bba3396d368faf97a166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775198"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-the-portal"></a>Örökölt Exchange-társviszony konvertálása Azure-erőforrássá a portál használatával

Ez a cikk ismerteti, hogyan konvertálhatja a meglévő örökölt Exchange-társviszony-létesítés az Azure-erőforrás a portál használatával.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-legacy-exchange-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az Előfeltételek](prerequisites.md) és [az Exchange-társviszony-létesítési forgatókönyvet.](walkthrough-exchange-all.md)

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Örökölt Exchange-társviszony konvertálása Azure-erőforrássá

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-exchange-peering"></a><a name=create></a>Örökölt Exchange-társviszony-létesítés konvertálása

Az örökölt társviszony-létesítési kapcsolatok at **társviszony-létesítési** erőforrás sal konvertálhatja.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás indítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [exchange-peering-configuration](./includes/exchange-portal-configuration-legacy.md)]

### <a name="verify-exchange-peering"></a><a name=get></a>Exchange-társviszony-létesítés ellenőrzése
[!INCLUDE [peering-exchange-get-portal](./includes/exchange-portal-get.md)]

## <a name="additional-resources"></a>További források

További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)

## <a name="next-steps"></a>További lépések

* [Exchange-társviszony létesítése vagy módosítása a portál használatával](howto-exchange-portal.md)

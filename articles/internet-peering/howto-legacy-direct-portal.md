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
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775055"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Örökölt közvetlen társviszony konvertálása Azure-erőforrássá a portál használatával

Ez a cikk ismerteti, hogyan konvertálhatja a meglévő örökölt közvetlen társviszony-létesítés az Azure-erőforrás a portál használatával.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-legacy-direct-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át [az előfeltételeket](prerequisites.md) és [a közvetlen társviszony-létesítési forgatókönyvet.](walkthrough-direct-all.md)


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-legacy-direct-peering"></a><a name=create></a>Örökölt közvetlen társviszony-létesítés konvertálása

Az örökölt társviszony-létesítési kapcsolatok at **társviszony-létesítési** erőforrás sal konvertálhatja.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás indítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társviszony-létesítés ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>További források

További információkért látogasson el [az internetes társviszony-létesítési GYIK-be](faqs.md)

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony-létesítés létrehozása vagy módosítása a portál használatával.](howto-direct-portal.md)

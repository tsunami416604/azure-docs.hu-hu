---
title: Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá az Azure Portal használatával
titleSuffix: Azure
description: Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá az Azure Portal használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678824"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá az Azure Portal használatával

Ez a cikk ismerteti, hogyan konvertálhatja a meglévő örökölt közvetlen társviszony-létesítés egy Azure-erőforrás az Azure Portalhasználatával.

Ha szeretné, ezt az útmutatót a PowerShell használatával is [elvégezheti.](howto-legacy-direct-powershell.md)

## <a name="before-you-begin"></a>Előkészületek
* A konfiguráció megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen társviszony-létesítési forgatókönyvet.](walkthrough-direct-all.md)


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Örökölt közvetlen társviszony-létesítés átalakítása Azure-erőforrássá

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetést
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Örökölt közvetlen társviszony-létesítés konvertálása

Az örökölt társviszony-létesítési kapcsolatok at a **Társviszony-létesítési** erőforrás használatával konvertálhatja.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Az erőforrás elindítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társviszony-létesítés ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>További források

További információt az [Internetes társviszony-létesítés – gyakori kérdések című témakörben talál.](faqs.md)

## <a name="next-steps"></a>További lépések

* [Közvetlen társviszony-létesítés létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)

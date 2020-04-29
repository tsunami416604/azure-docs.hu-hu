---
title: Örökölt közvetlen társak konvertálása Azure-erőforrásra a Azure Portal használatával
titleSuffix: Azure
description: Örökölt közvetlen társak konvertálása Azure-erőforrásra a Azure Portal használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e49e4d5debe63b99039bbafbc14f7788367314f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678824"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-the-azure-portal"></a>Örökölt közvetlen társak konvertálása Azure-erőforrásra a Azure Portal használatával

Ez a cikk bemutatja, hogyan alakíthat át egy meglévő örökölt közvetlen társítást egy Azure-erőforrásra a Azure Portal használatával.

Ha szeretné, az útmutatót a [PowerShell](howto-legacy-direct-powershell.md)használatával végezheti el.

## <a name="before-you-begin"></a>Előkészületek
* A konfigurálás megkezdése előtt tekintse át az [előfeltételeket](prerequisites.md) és a [közvetlen](walkthrough-direct-all.md) társítási útmutatót.


## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Örökölt közvetlen társak konvertálása Azure-erőforrásra

### <a name="sign-in-to-the-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="convert-a-legacy-direct-peering"></a><a name=create></a>Örökölt közvetlen társak konvertálása

A **régi társ-** összekapcsolási kapcsolatokat a társítási erőforrás használatával alakíthatja át.

#### <a name="launch-the-resource-and-configure-basic-settings"></a>Az erőforrás elindítása és az alapvető beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name="verify-direct-peering"></a><a name=get></a>Közvetlen társítás ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>További háttéranyagok

További információ: [internetes peering GYIK](faqs.md).

## <a name="next-steps"></a>További lépések

* [Közvetlen társ létrehozása vagy módosítása a portál használatával](howto-direct-portal.md)

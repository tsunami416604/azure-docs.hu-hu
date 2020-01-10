---
title: Örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával
titleSuffix: Azure
description: Örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 9900414d38bd597d08a80d15e908228c06ce06ea
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775055"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-the-portal"></a>Örökölt közvetlen társítás átalakítása Azure-erőforrásra a portál használatával

Ez a cikk bemutatja, hogyan alakíthat át egy meglévő örökölt közvetlen társítást az Azure-erőforrásra a portál használatával.

Ha szeretné, a [PowerShell](howto-legacy-direct-powershell.md)használatával is elvégezheti ezt az útmutatót.

## <a name="before-you-begin"></a>Előzetes teendők
* A konfigurálás megkezdése előtt tekintse át az [Előfeltételek](prerequisites.md) és a közvetlen társítási [útmutató](walkthrough-direct-all.md) témakört.


## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Örökölt közvetlen társítás átalakítása Azure-erőforrásra

### <a name="sign-in-to-portal-and-select-your-subscription"></a>Jelentkezzen be a portálra, és válassza ki az előfizetését
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name=create></a>Örökölt közvetlen társak konvertálása

Az örökölt kapcsolati kapcsolatokat az **egyenrangú** erőforrással alakíthatja át.

#### <a name="launch-resource-and-configure-basic-settings"></a>Erőforrás elindítása és alapszintű beállítások konfigurálása
[!INCLUDE [direct-peering-basic](./includes/direct-portal-basic.md)]

#### <a name="configure-connections-and-submit"></a>Kapcsolatok konfigurálása és küldés
[!INCLUDE [direct-peering-configuration](./includes/direct-portal-configuration-legacy.md)]

### <a name=get></a>Közvetlen társítás ellenőrzése
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

## <a name="additional-resources"></a>További források

További információért látogasson el az internetes kereséssel kapcsolatos [Gyakori kérdések](faqs.md) oldalra.

## <a name="next-steps"></a>Következő lépések

* [Hozzon létre vagy módosítson egy közvetlen társat a portál használatával](howto-direct-portal.md).

---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8f511eafda70fca997a08b5907e606e572b8e219
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50973817"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

A mintaalkalmazásnak hitelesítenie kell a tárfiókhoz való hozzáférést. A hitelesítéshez adja meg az alkalmazásnak a tárfiókja hitelesítő adatait kapcsolati sztring formájában. A tárfiók hitelesítő adatainak megtekintéséhez kövesse az alábbi lépéseket:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Itt megtekintheti a fiókhoz tartozó hozzáférési kulcsokat, valamint az egyes kulcsokhoz tartozó teljes kapcsolati sztringeket.   
4. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához. A kapcsolati sztring értékét hozzáadja egy környezeti változóhoz a következő lépés során.

    ![A kapcsolati sztring az Azure Portalról történő másolását bemutató képernyőkép](media/storage-copy-connection-string-portal/portal-connection-string.png)

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
ms.openlocfilehash: 51bdb7e3a84cbd176ac1a26747a627a6c2c4fa5b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30920582"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>A hitelesítési adatok másolása az Azure Portalról

A mintaalkalmazásnak hitelesítenie kell a tárfiókhoz való hozzáférést. A hitelesítéshez meg kell adnia az alkalmazásnak a tárfiókjához tartozó, kapcsolati karakterlánc formátumú hitelesítő adatait. A tárfiók hitelesítő adatainak megtekintéséhez a következőt kell tennie:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati karakterláncok.
4. Jegyezze fel a tárfiók nevét, mert a hitelesítéshez meg kell majd adnia.   
5. Keresse meg a **Kulcs** értéket a **key1** területen, majd kattintson a **Másolás** gombra a fiókkulcs másolásához.

    ![A fiókkulcs Azure Portalról történő másolását bemutató képernyőkép](media/storage-copy-account-key-portal/portal-account-key.png)

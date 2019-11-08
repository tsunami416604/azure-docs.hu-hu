---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e3b3d944508a4261b78def0b3bee13f7395a8bf0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748997"
---
A Storage-fiók hozzáférési kulcsainak vagy kapcsolati karakterláncának megtekintése és másolása a Azure Portalből:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
2. Keresse meg a Storage-fiókját.
3. A tárfiók áttekintésének **Beállítások** szakaszában válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
4. Keresse meg a **Kulcs** értéket a **key1** területen, majd kattintson a **Másolás** gombra a fiókkulcs másolásához.
5. Másik lehetőségként átmásolhatja a teljes kapcsolatok sztringjét. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához.

    ![A Azure Portal elérési kulcsainak megtekintését bemutató képernyőkép](media/storage-view-keys-include/portal-connection-string.png)

Bármelyik kulcsot használhatja az Azure Storage eléréséhez, de általánosságban ajánlott az első kulcs használata, és a második kulcs használatának fenntartása a kulcsok elforgatásakor.

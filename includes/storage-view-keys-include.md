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
ms.openlocfilehash: 9333bb36971fc28a23a443e50d191abeef05b758
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208193"
---
A tárfiók hozzáférési kulcsainak vagy kapcsolati karakterláncának megtekintése és másolása az Azure Portalról:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Keresse meg a Storage-fiókját.
3. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
4. Keresse meg a **Kulcs** értéket a **key1** területen, majd kattintson a **Másolás** gombra a fiókkulcs másolásához.
5. Másik lehetőségként átmásolhatja a teljes kapcsolati karakterláncot. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához.

    ![Képernyőkép a hozzáférési kulcsok azure-portálon való megtekintéséről](media/storage-view-keys-include/portal-connection-string.png)

Bármelyik kulcs használatával elérheti az Azure Storage-t, de általában célszerű az első kulcsot használni, és a kulcsok elforgatása kor lefoglalhatja a második kulcs használatát.

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
ms.openlocfilehash: 57368331e83111836e13b6c3796cacad9816e318
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521286"
---
A tárfiók hozzáférési kulcsainak vagy kapcsolati karakterláncának megtekintése és másolása az Azure Portalról:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
2. Keresse meg a Storage-fiókját.
3. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
4. Keresse meg a **Kulcs** értéket a **key1** területen, majd kattintson a **Másolás** gombra a fiókkulcs másolásához.
5. Másik lehetőségként átmásolhatja a teljes kapcsolati karakterláncot. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához.

    ![Képernyőkép a hozzáférési kulcsok azure-portálon való megtekintéséről](media/storage-view-keys-include/portal-connection-string.png)

Bármelyik kulcs használatával elérheti az Azure Storage-t, de általában célszerű az első kulcsot használni, és a kulcsok elforgatása kor lefoglalhatja a második kulcs használatát.

Egy fiók hozzáférési kulcsainak megtekintéséhez vagy olvasásához a felhasználónak szolgáltatás-rendszergazdának kell lennie, vagy olyan RBAC szerepkört kell hozzárendelnie, amely tartalmazza a **Microsoft.Storage/storageAccounts/listkeys/action műveletet.** Néhány beépített RBAC szerepkör, amely tartalmazza ezt a műveletet a **tulajdonos,** **közreműködő**és **a tárfiók kulcsoperátori szolgáltatás szerepkör** szerepkörei. A Service Administrator szerepkörrel kapcsolatos további információkért lásd: [Klasszikus előfizetéses rendszergazdai szerepkörök, Azure RBAC-szerepkörök és Azure AD-szerepkörök.](../articles/role-based-access-control/rbac-and-directory-admin-roles.md) Az Azure Storage beépített szerepköreiről **az** Azure [RBAC beépített szerepkörei tárolószakaszában](../articles/role-based-access-control/built-in-roles.md#storage)talál részletes információt.

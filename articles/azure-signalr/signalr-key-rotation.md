---
title: Hozzáférés az Azure SignalR Service kulcsrotálás
description: Áttekintés arról, hogy miért kell ügyfél rendszeresen a a hozzáférési kulcsainak rotálása, és megtudhatja, hogyan teheti a portál a grafikus felhasználói felület és a CLI használatával.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 2c0f60b0ef3a90372fc4a095c830f39bc148f354
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53636155"
---
# <a name="access-key-rotation-for-azure-signalr-service"></a>Hozzáférés az Azure SignalR Service kulcsrotálás

Minden egyes Azure SignalR Service-példány két tárelérési kulcsok rendelkezik: Elsődleges és másodlagos kulcsok. SignalR-ügyfelek hitelesítésére, ha a szolgáltatás-kérelem indítására szolgálnak. A kulcsok társítva a példány végpont URL-címe. A kulcsok biztonsága, és rendszeresen elforgatása őket. Két kulcsot, így egy kulcs használatával, amíg az egyiket a kapcsolatok fenntartásához rendelkezésre.

## <a name="why-rotate-access-keys"></a>Miért elforgatása a hozzáférési kulcsok?

A biztonsági és megfelelőségi miatt követelmény a fejlesztők számára ajánlott, hogy rendszeresen a a hozzáférési kulcsainak rotálása.

## <a name="how-to-regenerate-access-keys"></a>Elérési kulcsok újragenerálása hogyan?

1. Az [Azure Portalon](https://portal.azure.com/) jelentkezzen be a hitelesítő adataival.

1. Keresse meg a **kulcsok** szakaszban az Azure SignalR Service-példányban, hogy újragenerálja a kulcsokat.

1. Kattintson a **kulcsok** a navigációs menüben.

1. Kattintson a **elsődleges kulcs újragenerálása** vagy **másodlagos kulcs újragenerálása**.

Új kulcs és a hozzá tartozó kapcsolati karakterlánc létrejön és jelenik meg.

 ![Kulcsok újragenerálása](media/signalr-key-rotation/regenerate-keys.png)

Kulcsok használatával is létrehozhatja [Azure CLI-vel](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Az új kapcsolati karakterláncok frissítése konfigurációk

1. Másolja ki az újonnan létrehozott kapcsolati karakterláncot.

1. Frissítse az összes használandó konfigurációk, az új kapcsolati karakterláncban.

1. Indítsa újra az alkalmazást, igény szerint.

## <a name="forced-access-key-regeneration"></a>A kényszerített elérési kulcs újragenerálása

Az Azure SignalR Service léptethet egy kötelező elérési kulcs újragenerálása bizonyos helyzet alapján. A szolgáltatás értesíti ügyfeleit e-mailben és a portál értesítései. Ha ez a kommunikáció kap, vagy szolgáltatás hibája miatt a hozzáférési kulcsot észlel, a kulcsok rotálására a ezt az útmutatót.

## <a name="next-steps"></a>További lépések

Azt javasoljuk ajánlott biztonsági eljárásként rendszeresen elforgatása a hozzáférési kulcsait.

Ebben az útmutatóban megismerhette tárelérési kulcsok újragenerálásával. Folytassa a következő oktatóanyagok hitelesítés OAuth vagy az Azure Functions használatával kapcsolatos.

> [!div class="nextstepaction"]
> [Integráció az ASP.NET Core-identitás](./signalr-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Kiszolgáló nélküli, hitelesítéssel rendelkező valós idejű alkalmazás összeállítása](./signalr-authenticate-azure-functions.md)
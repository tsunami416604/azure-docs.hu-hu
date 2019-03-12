---
title: Hozzáférési kulcs rotálása az Azure SignalR Service
description: Miért kell az ügyfél rendszeresen a a hozzáférési kulcsainak rotálása és az Azure Portalon grafikus felhasználói felület és az Azure CLI áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 15b0ff0bbb96e5fa96d81cfa265e83abf749cf85
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555142"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Hozzáférési kulcs rotálása az Azure SignalR Service

Minden egyes Azure SignalR Service-példány egy elsődleges és másodlagos kulcsok nevű hozzáférés kulcspárra rendelkezik. Ha a kérelmeket a szolgáltatás a SignalR-ügyfelek hitelesítésére használhatók. A kulcsok társítva a példány végpont URL-címe. A kulcsok biztonsága, és rendszeresen elforgatása őket. Most már a megadott két hozzáférési kulcsokkal, így a kapcsolatok fenntartásához amíg az egyiket egy kulcs használatával.

## <a name="why-rotate-access-keys"></a>Miért elforgatása a hozzáférési kulcsok?

Biztonsági okokból és megfelelőségi követelmények rendszeresen a hozzáférési kulcsainak rotálása.

## <a name="regenerate-access-keys"></a>Elérési kulcsok újragenerálása

1. Nyissa meg a [az Azure portal](https://portal.azure.com/), és jelentkezzen be a hitelesítő adataival.

1. Keresse meg a **kulcsok** szakasz újragenerálja a kulcsot az Azure SignalR Service-példányában.

1. Válassza ki **kulcsok** a navigációs menüben.

1. Válassza ki **elsődleges kulcs újragenerálása** vagy **másodlagos kulcs újragenerálása**.

   Egy új kulcsot, és a megfelelő kapcsolati karakterláncot létrehozni és jelenik meg.

   ![Kulcsok újragenerálása](media/signalr-howto-key-rotation/regenerate-keys.png)

Emellett létrehozhatja kulcsok használatával a [Azure CLI-vel](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Az új kapcsolati karakterláncok frissítése konfigurációk

1. Másolja ki az újonnan létrehozott kapcsolati karakterláncot.

1. Frissítse az összes használandó konfigurációk, az új kapcsolati karakterláncban.

1. Indítsa újra az alkalmazást, igény szerint.

## <a name="forced-access-key-regeneration"></a>A kényszerített elérési kulcs újragenerálása

Az Azure SignalR Service egy kötelező elérési kulcs újragenerálása bizonyos helyzetekben előfordulhat, hogy kényszerítése. A szolgáltatás értesítést küld e-mailben és a portál értesítései ügyfelek. Ha ez a kommunikáció kap, vagy szolgáltatás hibája miatt egy hozzáférési kulcsot észlel, a kulcsok rotálására a Ez az útmutató utasításait követve.

## <a name="next-steps"></a>További lépések

A hozzáférési kulcsainak rotálása rendszeresen ajánlott biztonsági eljárás.

Ebben az útmutatóban útmutatóból megtudhatta, hogyan újragenerálni a hozzáférési kulcsokat. Folytassa a következő oktatóanyagok hitelesítés OAuth vagy az Azure Functions használatával kapcsolatos.

> [!div class="nextstepaction"]
> [Integráció az ASP.NET core-identitás](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Hitelesítés a valós idejű kiszolgáló nélküli alkalmazás készítése](./signalr-tutorial-authenticate-azure-functions.md)
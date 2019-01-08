---
title: Hozzáférés az Azure SignalR Service kulcsrotálás
description: Miért kell az ügyfél rendszeresen a a hozzáférési kulcsainak rotálása és az Azure Portalon grafikus felhasználói felület és az Azure CLI áttekintése.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 04321e62ea41b58e9ee4314b600c77e6c39b7ade
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065834"
---
# <a name="access-key-rotation-for-azure-signalr-service"></a>Hozzáférés az Azure SignalR Service kulcsrotálás

Minden egyes Azure SignalR Service-példány egy elsődleges és másodlagos kulcsok nevű hozzáférés kulcspárra rendelkezik. Ha a kérelmeket a szolgáltatás a SignalR-ügyfelek hitelesítésére használhatók. A kulcsok társítva a példány végpont URL-címe. A kulcsok biztonsága, és rendszeresen elforgatása őket. Most már a megadott két hozzáférési kulcsokkal, így a kapcsolatok fenntartásához amíg az egyiket egy kulcs használatával.

## <a name="why-rotate-access-keys"></a>Miért elforgatása a hozzáférési kulcsok?

Biztonsági okokból és megfelelőségi követelmények rendszeresen a hozzáférési kulcsainak rotálása.

## <a name="regenerate-access-keys"></a>Elérési kulcsok újragenerálása

1. Nyissa meg a [az Azure portal](https://portal.azure.com/), és jelentkezzen be a hitelesítő adataival.

1. Keresse meg a **kulcsok** szakasz újragenerálja a kulcsot az Azure SignalR Service-példányában.

1. Válassza ki **kulcsok** a navigációs menüben.

1. Válassza ki **elsődleges kulcs újragenerálása** vagy **másodlagos kulcs újragenerálása**.

   Egy új kulcsot, és a megfelelő kapcsolati karakterláncot létrehozni és jelenik meg.

   ![Kulcsok újragenerálása](media/signalr-key-rotation/regenerate-keys.png)

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
> [Integráció az ASP.NET core-identitás](./signalr-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Hitelesítés a valós idejű kiszolgáló nélküli alkalmazás készítése](./signalr-authenticate-azure-functions.md)
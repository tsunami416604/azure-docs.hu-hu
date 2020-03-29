---
title: Hozzáférési kulcs rotálása az Azure SignalR Service esetében
description: Áttekintés arról, hogy az ügyfélnek miért kell rendszeresen elforgatnia a hozzáférési kulcsokat, és hogyan kell ezt megtenni az Azure Portal gui és az Azure CLI használatával.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 133edc64ac2f858a397a4a184c24497dae8af333
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67565730"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Hozzáférési kulcs rotálása az Azure SignalR Service esetében

Minden Egyes Azure SignalR-szolgáltatáspéldány rendelkezik egy elsődleges és másodlagos kulcsok nevű hozzáférési kulcsokkal. A dirti ügyfelek hitelesítésére szolgálnak, amikor a szolgáltatásnak kéréseket tesznek. A kulcsok a példányvégpont URL-címéhez vannak társítva. Tartsa biztonságban a billentyűit, és rendszeresen forgassa őket. Két hozzáférési kulcsot kap, így az egyik kulcs használatával karbantarthatja a kapcsolatokat, miközben újragenerálja a másikat.

## <a name="why-rotate-access-keys"></a>Miért érdemes elforgatni a hozzáférési kulcsokat?

Biztonsági okokból és megfelelőségi követelmények miatt rendszeresen forgassa el a hozzáférési kulcsokat.

## <a name="regenerate-access-keys"></a>Hozzáférési kulcsok újragenerálása

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/)és jelentkezzen be a hitelesítő adataival.

1. Keresse meg a **kulcsok** szakaszt az Azure SignalR service-példányban a újralétrehozni kívánt kulcsokkal.

1. Válassza a navigációs menü **Billentyűparancs-parancsát.**

1. Válassza **az elsődleges kulcs újragenerálása** vagy a másodlagos kulcs **újragenerálása**lehetőséget.

   Egy új kulcs és a megfelelő kapcsolati karakterlánc jön létre és jelenik meg.

   ![Kulcsok újragenerálása](media/signalr-howto-key-rotation/regenerate-keys.png)

A kulcsokat az Azure [CLI](/cli/azure/signalr/key?view=azure-cli-latest#az-signalr-key-renew)használatával is újragenerálhatja.

## <a name="update-configurations-with-new-connection-strings"></a>Konfigurációk frissítése új kapcsolati karakterláncokkal

1. Másolja az újonnan létrehozott kapcsolati karakterláncot.

1. Frissítse az összes konfigurációt az új kapcsolati karakterlánc használatához.

1. Szükség szerint indítsa újra az alkalmazást.

## <a name="forced-access-key-regeneration"></a>Kényszerített hozzáférésű kulcs regenerálása

Az Azure SignalR-szolgáltatás bizonyos helyzetekben kényszerítheti a kötelező hozzáférési kulcs regenerálását. A szolgáltatás e-mailben és portálértesítéssel értesíti az ügyfeleket. Ha ezt a kommunikációt kapja, vagy egy hozzáférési kulcs miatt szolgáltatáshibát tapasztal, forgassa el a kulcsokat az útmutató utasításainak megfelelően.

## <a name="next-steps"></a>További lépések

A hozzáférési kulcsokat rendszeresen forgassa el, mint jó biztonsági gyakorlatot.

Ebben az útmutatóban megtanulta a hozzáférési kulcsok újragenerálását. Folytassa a következő oktatóanyagokat az OAuth vagy az Azure Functions hitelesítésével kapcsolatban.

> [!div class="nextstepaction"]
> [Integrálható ASP.NET alapvető identitással](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Kiszolgáló nélküli, hitelesítéssel rendelkező valós idejű alkalmazás összeállítása](./signalr-tutorial-authenticate-azure-functions.md)
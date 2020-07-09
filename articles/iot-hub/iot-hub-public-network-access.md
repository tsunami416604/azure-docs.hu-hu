---
title: Nyilvános hálózati hozzáférés kezelése az Azure IoT hub-hoz
description: Az IoT hub nyilvános hálózati hozzáférésének letiltására és engedélyezésére vonatkozó dokumentáció
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: c82f98df8fb79fa10f2e30b219c1a02bb646e2de
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85937521"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>A IoT hub nyilvános hálózati hozzáférésének kezelése

A nyilvános hálózati hozzáférés letiltásával korlátozhatja a hozzáférést a [VNet IoT hub-beli privát végponthoz](virtual-network-support.md). Ehhez használja a Azure Portal vagy az API-t `publicNetworkAccess` . 

## <a name="turn-off-public-network-access-using-azure-portal"></a>Nyilvános hálózati hozzáférés kikapcsolása Azure Portal használatával

1. Látogasson el [Azure Portal](https://portal.azure.com)
2. Keresse meg az IoT-központot.
3. A bal oldali menüben válassza a **hálózatkezelés** lehetőséget.
4. A "nyilvános hálózati hozzáférés engedélyezése" területen válassza a **Letiltva** lehetőséget.
5. Kattintson a **Mentés** gombra.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="A nyilvános hálózati hozzáférés kikapcsolását Azure Portal bemutató kép" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

A nyilvános hálózati hozzáférés bekapcsolásához válassza az **engedélyezve**, majd a **Mentés**lehetőséget.

## <a name="ip-filter"></a>IP-szűrő 

Ha a nyilvános hálózati hozzáférés le van tiltva, az összes [IP-szűrési](iot-hub-ip-filtering.md) szabályt figyelmen kívül hagyja a rendszer. Ennek az az oka, hogy a nyilvános internetről érkező összes IP-cím le van tiltva. Az IP-szűrő használatához használja a **kiválasztott IP-címtartományok** lehetőséget.

## <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>Hibajavítás beépített Event hub-kompatibilis végponttal

Hiba történt a IoT Hub, ahol a [beépített Event hub-kompatibilis végpont](iot-hub-devguide-messages-read-builtin.md) továbbra is elérhető lesz nyilvános interneten keresztül, ha a nyilvános hálózati hozzáférés a IoT hub le van tiltva. Ha többet szeretne megtudni erről a hibáról, tekintse meg a [nyilvános hálózati hozzáférés letiltása IoT hub letiltja a hozzáférést a beépített Event hub-végponthoz](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix).
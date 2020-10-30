---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.openlocfilehash: 15c29648e42ba190991d51188489883e29bee165
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041592"
---
>[!NOTE]
>Rendszergazdai jogosultsággal kell rendelkeznie azon a Windows ügyfélszámítógépen, ahonnan csatlakozik.
>

1. A VNet való csatlakozáshoz az ügyfélszámítógépen navigáljon a VPN-beállítások elemre, és keresse meg a létrehozott VPN-kapcsolatot. Neve megegyezik a virtuális hálózat nevével. Válassza a **Kapcsolódás** lehetőséget. Megjelenhet egy előugró üzenet, amely a tanúsítvány használatára utal. Kattintson a **tovább** gombra emelt szintű jogosultságok használatához.

1. **A kapcsolat állapota lapon** válassza a **Kapcsolódás** lehetőséget a kapcsolat indításához. Ha megjelenik a **Tanúsítvány kiválasztása** képernyő, ellenőrizze, hogy az a csatlakozáshoz használni kívánt ügyféltanúsítványt mutatja-e. Ha nem, a legördülő menüben válassza ki a megfelelő tanúsítványt, majd kattintson az **OK gombra** .

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connection-status.png" alt-text="Kapcsolat Windows rendszerű számítógépről":::

1. A kapcsolat létrejött.

   :::image type="content" source="./media/vpn-gateway-p2s-connect-windows-client/connected.png" alt-text="Kapcsolat Windows rendszerű számítógépről":::

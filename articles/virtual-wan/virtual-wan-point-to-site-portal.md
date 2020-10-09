---
title: Az Azure-ba irányuló pont–hely típusú kapcsolat létrehozása az Azure Virtual WAN használatával | Microsoft Docs
description: Ez az oktatóanyag az Azure-ba irányuló pont–hely VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: cherylmc
ms.openlocfilehash: 84f8563a6b03f10f4cbc647426c350d9fac52780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812684"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Oktatóanyag: felhasználói VPN-kapcsolat létrehozása az Azure Virtual WAN használatával

Ez az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv2) vagy OpenVPN VPN-kapcsolaton keresztül. Ehhez a kapcsolattípushoz konfigurálni kell egy ügyfelet az ügyfélszámítógépen. A virtuális WAN-ról további információt a [virtuális WAN áttekintése](virtual-wan-about.md) című témakörben talál.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * WAN létrehozása
> * Pont–hely konfiguráció létrehozása
> * Elosztó létrehozása
> * DNS-kiszolgálók meghatározása
> * VPN-ügyfél profiljának letöltése
> * A virtuális WAN megtekintése

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtuális WAN létrehozása

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Pont–hely konfiguráció létrehozása

A pont – hely (P2S) konfiguráció határozza meg a távoli ügyfelek csatlakoztatásának paramétereit.

[!INCLUDE [Create client profiles](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-hub-with-point-to-site-gateway"></a><a name="hub"></a>Hub létrehozása pont – hely átjáróval

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>DNS-kiszolgáló meghatározása

A virtuális WAN felhasználói VPN-átjárók legfeljebb 5 DNS-kiszolgáló megadását teszik lehetővé. Ezt konfigurálhatja a hub létrehozási folyamata során, vagy később is módosíthatja. Ehhez keresse meg a virtuális hubot. A **felhasználói VPN (pont – hely)** területen válassza a **Konfigurálás** lehetőséget, majd adja meg a DNS-kiszolgáló IP-címét (i) az **Egyéni DNS-kiszolgálók** szövegmezőben (i).

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="Egyéni DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="download-vpn-profile"></a><a name="download"></a>VPN-profil letöltése

A VPN-profillal konfigurálhatja az ügyfeleket.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

### <a name="configure-user-vpn-clients"></a>Felhasználói VPN-ügyfelek konfigurálása

A letöltött profillal konfigurálhatja a távelérésű ügyfeleket. Az egyes operációs rendszerekre vonatkozó eljárás eltérő, kövesse a rendszeren alkalmazott utasításokat.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
1. Az **Áttekintés** oldalon a térképen lévő minden pont egy hubot jelöl.
1. A **hubok és kapcsolatok** szakaszban megtekintheti a hub állapotát, a helyet, a régiót, a VPN-kapcsolat állapotát és a bejövő és kimenő bájtokat.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Az erőforrások eltávolítása

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintő](virtual-wan-about.md) lapját.

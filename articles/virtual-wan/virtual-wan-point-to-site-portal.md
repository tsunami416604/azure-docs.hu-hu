---
title: 'Oktatóanyag: az Azure Virtual WAN használata pont – hely kapcsolat létrehozásához az Azure-hoz'
description: Ez az oktatóanyag az Azure-ba irányuló pont–hely VPN-kapcsolat létrehozását ismerteti az Azure Virtual WAN használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7e65d5d2941765df98b3bf3b7fb8ff2e89b7e9f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411201"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>Oktatóanyag: felhasználói VPN-kapcsolat létrehozása az Azure Virtual WAN használatával

Ez az oktatóanyag bemutatja, hogyan kapcsolódhat a Virtual WAN használatával az Azure-ban lévő erőforrásaihoz IPsec/IKE (IKEv2) vagy OpenVPN VPN-kapcsolaton keresztül. Az ilyen típusú kapcsolathoz a VPN-ügyfél konfigurálására van szükség az ügyfélszámítógépen. A Virtual WAN-nal kapcsolatos további információkért lásd a [Virtual WAN áttekintését](virtual-wan-about.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális WAN létrehozása
> * Pont–hely konfiguráció létrehozása
> * Virtuális központ létrehozása
> * DNS-kiszolgálók meghatározása
> * VPN-ügyfél profiljának konfigurációs csomagjának előállítása
> * VPN-ügyfelek konfigurálása
> * A virtuális WAN megtekintése

![Virtuális WAN ábrája](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [Before beginning](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtuális WAN létrehozása

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-p2s-configuration"></a><a name="p2sconfig"></a>Pont–hely konfiguráció létrehozása

A pont – hely (P2S) konfiguráció határozza meg a távoli ügyfelek csatlakoztatásának paramétereit.

[!INCLUDE [Create P2S configuration](../../includes/virtual-wan-p2s-configuration-include.md)]

## <a name="create-virtual-hub-and-gateway"></a><a name="hub"></a>Virtuális központ és átjáró létrehozása

[!INCLUDE [Create hub](../../includes/virtual-wan-p2s-hub-include.md)]

## <a name="specify-dns-server"></a><a name="dns"></a>DNS-kiszolgáló meghatározása

Ezt a beállítást konfigurálhatja a hub létrehozásakor, vagy később is módosíthatja. A módosításhoz keresse meg a virtuális hubot. A **felhasználói VPN (pont – hely)** területen válassza a **Konfigurálás** lehetőséget, majd adja meg a DNS-kiszolgáló IP-címét (i) az **Egyéni DNS-kiszolgálók** szövegmezőben (i). Akár 5 DNS-kiszolgálót is megadhat.

   :::image type="content" source="media/virtual-wan-point-to-site-portal/custom-dns.png" alt-text="Egyéni DNS" lightbox="media/virtual-wan-point-to-site-portal/custom-dns-expand.png":::

## <a name="generate-vpn-client-profile-package"></a><a name="download"></a>VPN-ügyfél profil csomagjának előállítása

A VPN-ügyfelek konfigurálásához adja meg és töltse le a VPN-ügyfél profilját tartalmazó csomagot.

[!INCLUDE [Download profile](../../includes/virtual-wan-p2s-download-profile-include.md)]

## <a name="configure-vpn-clients"></a><a name="configure-client"></a>VPN-ügyfelek konfigurálása

A letöltött profil csomag használatával konfigurálja a távelérési VPN-ügyfeleket. Az egyes operációs rendszerek eljárásai eltérőek. Kövesse a rendszeren alkalmazandó utasításokat.
Miután befejezte az ügyfél konfigurálását, csatlakozhat.

[!INCLUDE [Configure clients](../../includes/virtual-wan-p2s-configure-clients-include.md)]

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>A virtuális WAN megtekintése

1. Lépjen a virtuális WAN-ra.
1. Az **Áttekintés** oldalon a térképen lévő minden pont egy hubot jelöl.
1. A **hubok és kapcsolatok** szakaszban megtekintheti a hub állapotát, a helyet, a régiót, a VPN-kapcsolat állapotát és a bejövő és kimenő bájtokat.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Erőforrások felszabadítása

Ha már nincs szükség ezekre az erőforrásokra, a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölheti az erőforráscsoportot és az összes benne található erőforrást. A „myResourceGroup” helyére írja be az erőforráscsoport nevét, és futtassa a következő PowerShell-parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>További lépések

A virtuális WAN-ról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> * [Virtuális WAN – GYIK](virtual-wan-faq.md)

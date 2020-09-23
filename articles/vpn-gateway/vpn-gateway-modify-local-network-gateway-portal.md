---
title: 'VPN Gateway: az átjáró IP-címének beállításainak módosítása: Azure Portal'
description: Ez a cikk végigvezeti a helyi hálózati átjáró IP-címének előtagjainak a Azure Portal használatával történő módosításán.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90983181"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Helyi hálózati átjáró beállításainak módosítása az Azure Portal használatával

A helyi hálózati átjáró AddressPrefix vagy GatewayIPAddress vonatkozó beállítások néha változnak. Ez a cikk bemutatja, hogyan módosíthatja a helyi hálózati átjáró beállításait. Ezeket a beállításokat más módszer használatával is módosíthatja, ha a következő listából választ egy másik lehetőséget:

A kapcsolat törlése előtt érdemes lehet letölteni a csatlakoztatott eszközök konfigurációját a megadott PSK beszerzéséhez. Így nem kell újradefiniálni a másik oldalon.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Azure CLI](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Helyi hálózati átjáró konfigurációja

Az alábbi képernyőképen egy helyi hálózati átjáró erőforrásának **konfigurációs** lapja látható nyilvános IP-cím végpont használatával:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Helyi hálózati átjáró konfigurálása – IP-cím":::

Ez ugyanaz a konfigurációs oldal, amely FQDN-végponttal rendelkezik:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Helyi hálózati átjáró konfigurálása – FQDN":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Átjáró IP-címének módosítása

Ha a VPN-eszköz, amelyhez csatlakozni akar, megváltoztatta nyilvános IP-címét, a változtatásnak megfelelően módosítania kell a helyi hálózati átjárót.

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. Az **IP-cím** mezőben módosítsa az IP-címet.
3. Kattintson a **Mentés** gombra a beállítások mentéséhez.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>Az átjáró teljes tartománynevének módosítása

Ha a VPN-eszköz, amelyhez csatlakozni szeretne, megváltoztatta annak FQDN-jét (teljes tartománynév), módosítania kell a helyi hálózati átjárót, hogy tükrözze a változást.

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. A **teljes** tartománynév mezőben módosítsa a tartománynevet.
3. Kattintson a **Mentés** gombra a beállítások mentéséhez.

> ! Megjegyzés Helyi hálózati átjáró nem módosítható FQDN-végpont és IP-cím végpontja között. Törölnie kell az ehhez a helyi hálózati átjáróhoz társított összes kapcsolatot, létre kell hoznia egy újat az új végponttal (IP-cím vagy teljes tartománynév), majd újra létre kell hoznia a kapcsolatokat.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>IP-cím előtagjainak módosítása

### <a name="to-add-additional-address-prefixes"></a>További címelőtagok felvétele:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. Adja hozzá az IP-címtartományt a *további címtartomány hozzáadása* mezőben.
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

### <a name="to-remove-address-prefixes"></a>Címelőtagok eltávolítása:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. Kattintson a **"..."** elemre az eltávolítani kívánt előtagot tartalmazó sorban.
3. Kattintson az **Eltávolítás** elemre.
4. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="modify-bgp-settings"></a><a name="bgp"></a>BGP-beállítások módosítása

### <a name="to-add-or-update-bgp-settings"></a>BGP-beállítások hozzáadása vagy frissítése:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. A **"BGP-beállítások konfigurálása"** lehetőség kiválasztásával megjelenítheti vagy frissítheti a helyi hálózati átjáró BGP-konfigurációit
3. Az autonóm rendszerszám vagy BGP-társ IP-cím hozzáadása vagy frissítése a megfelelő mezőkben
4. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

### <a name="to-remove-bgp-settings"></a>A BGP-beállítások eltávolítása:

1. A helyi hálózati átjáró erőforrás **Beállítások** szakaszában kattintson a **konfiguráció**elemre.
2. A **"BGP-beállítások konfigurálása"** lehetőség kiválasztásával távolítsa el a meglévő BGP ASN-és BGP-társ IP-címét
3. A beállítások mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="next-steps"></a>Következő lépések

Ellenőrizheti az átjáró-kapcsolatokat. Lásd: [átjáró-kapcsolatok ellenőrzése](vpn-gateway-verify-connection-resource-manager.md).

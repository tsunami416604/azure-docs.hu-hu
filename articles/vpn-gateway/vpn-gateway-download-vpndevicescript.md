---
title: 'Töltse le a VPN-eszközök konfigurációs szkriptjeinek S2S VPN-kapcsolatok: Az Azure Resource Manager |} A Microsoft Docs'
description: Ez a cikk végigvezeti az Azure Resource Manager használatával Azure VPN-átjárókkal VPN-eszközök konfigurációs szkriptjeinek S2S VPN-kapcsolatok letöltése.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: 0b0a7ce63fa2d0154300dd2e8f9cf88d985a8a0a
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200523"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Töltse le a VPN-eszközök konfigurációs szkriptjeinek S2S VPN-kapcsolatok

Ez a cikk végigvezeti az Azure Resource Manager használatával Azure VPN-átjárókkal VPN-eszközök konfigurációs szkriptjeinek S2S VPN-kapcsolatok letöltése. Az alábbi ábrán látható, a magas szintű munkafolyamat.

![parancsfájl-letöltése](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

A következő eszközökön a parancsfájlokat van:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>Tudnivalók a VPN-eszközkonfigurációs szkriptek

Létesítmények közötti VPN-kapcsolatot az Azure VPN gateway, egy a helyszíni VPN-eszköz és a egy IPsec S2S VPN-alagutat a két összekötő áll. A szokásos munkahelyi folyamat a következő lépésekből áll:

1. Létrehozása és konfigurálása az Azure VPN gateway (virtuális hálózati átjáró)
2. Létrehozhat és konfigurálhat egy Azure helyi hálózati átjáró, amely a helyszíni hálózat és VPN-eszköz
3. Hozzon létre, és az Azure VPN gateway és a helyi hálózati átjáró közötti Azure VPN-kapcsolat konfigurálása
4. Az Azure VPN gateway a tényleges S2S VPN-alagutat létesíteni a helyi hálózati átjáró által képviselt a helyszíni VPN-eszköz konfigurálása

1 – az Azure 3 lépést is elvégezheti [portál](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), vagy [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Az utolsó lépés a helyszíni VPN-eszközök Azure-on kívül konfigurálását igényli. Ez a funkció lehetővé teszi, hogy töltse le a konfigurációs parancsfájlt a VPN-eszközhöz, a megfelelő értékekkel, az Azure VPN-átjáró, a virtuális hálózathoz, és a helyszíni hálózat címelőtagokat, és VPN-kapcsolat tulajdonságait, már kitöltött stb. A parancsfájlt használhatja kiindulási pontként, vagy a parancsfájl vonatkoznak közvetlenül a konfigurációs konzolon keresztül a helyszíni VPN-eszközök.

> [!IMPORTANT]
> * Minden egyes VPN-eszközkonfigurációs szkript szintaxisa különböző, és a modellek és belső vezérlőprogrammal nagymértékben függ. A modell és verzió eszközadatokat ellen a rendelkezésre álló sablonok külön figyelmet szentelnie.
> * Néhány paraméterértékeket az eszköz egyedinek kell lennie, és nem lehet megállapítani az eszköz való hozzáférés nélkül. Az Azure által létrehozott konfigurációs szkripteket előre adja meg ezeket az értékeket, de győződjön meg arról, a megadott értékek érvényesek az eszközön. Példák:
>    * Csatoló számok
>    * Ellenőrző lista telefonszámok
>    * A házirend nevét, vagy számokat, stb.
> * Keresse meg a kulcsszó "**cserélje le**" beágyazott a parancsfájlban keresse meg a paramétert, a szkript alkalmazása előtt ellenőriznie kell.
> * Bizonyos sablonok tartalmaznak egy "**karbantartása**" szakasz távolítja el a konfigurációkat alkalmazhat. A Lemezkarbantartó szakaszok jelenleg megjegyzésként szerepelnek alapértelmezés szerint.

## <a name="download-the-configuration-script-from-azure-portal"></a>A konfigurációs parancsfájl letöltése az Azure Portal használatával

Az Azure VPN gateway, a helyi hálózati átjáró és a egy összekötő kapcsolati erőforrás létrehozásához. A következő oldalon végigvezeti a lépéseken:

* [Helyek közötti kapcsolat létrehozása az Azure Portalon](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

A kapcsolati erőforrás létrehozása után kövesse az alábbi utasításokat, hogy a VPN-eszközök konfigurációs szkriptjeinek letöltése:

1. Egy böngészőből keresse fel a [az Azure portal](http://portal.azure.com) , és ha szükséges, jelentkezzen be az Azure-fiókkal
2. Nyissa meg a létrehozott kapcsolat-erőforrást. Megtalálhatja az összes kapcsolat erőforrások listájában kattintson "Az összes szolgáltatás", majd a "Hálózat" és a "Kapcsolatok."

    ![kapcsolat-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Kattintson a konfigurálni kívánt kapcsolatot.

    ![kapcsolat – áttekintés](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Kattintson a "Konfiguráció letöltése" hivatkozásra, kiemelve a vörös színnel; a kapcsolat – áttekintés oldalra Ekkor megnyílik a "Letöltés konfiguráció" oldala.

    ![letöltési-szkript – 1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Válassza ki a VPN-eszköz modell termékcsalád és a belső vezérlőprogram verzióját, majd kattintson a "Letöltés konfiguráció" gombra.

    ![download66-szkript – 2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Mentse a letöltött szkript (szövegfájl) böngészőből történő kéri.
7. Miután letöltötte a konfigurációs parancsfájl, nyissa meg egy szövegszerkesztőben és keresse meg a "REPLACE" kulcsszó azonosíthatók és megvizsgálhatók a paramétereket, előfordulhat, hogy kell helyettesíteni.

    ![parancsfájl-szerkesztése](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Az Azure PowerShell-lel konfigurációs parancsfájl letöltése

A konfigurációs parancsfájl Azure PowerShell használatával is letöltheti, az alábbi példában látható módon:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>A konfigurációs parancsfájl alkalmazni a VPN-eszköz

Miután letöltötte és a konfigurációs parancsfájl érvényesítve, a következő lépés a alkalmazni a parancsfájl a VPN-eszköz. A tényleges eljárás a VPN-eszköz megkönnyíti és a modellek függően változik. Tekintse át a VPN-eszközök a művelet szoftverhez vagy a utasítás lapokon.

## <a name="next-steps"></a>További lépések

Konfigurálás folytatása a [helyek közötti kapcsolat](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

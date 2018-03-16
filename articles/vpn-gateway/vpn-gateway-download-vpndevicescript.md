---
title: "Töltse le a VPN-eszköz konfigurációs parancsfájlokat S2S VPN-kapcsolatok: Azure Resource Manager |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan letöltése VPN eszköz konfigurációs parancsfájlokat S2S VPN-kapcsolatok az Azure VPN Gatewayek Azure Resource Manager használatával."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2018
ms.author: yushwang
ms.openlocfilehash: 2ec428bc5d2cdfb376db3c27b9899014c7ffa2af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Töltse le a VPN-eszköz konfigurációs parancsfájlokat S2S VPN-kapcsolatokhoz

Ez a cikk bemutatja, hogyan letöltése VPN eszköz konfigurációs parancsfájlokat S2S VPN-kapcsolatok az Azure VPN Gatewayek Azure Resource Manager használatával. Az alábbi ábrán látható, a magas szintű munkafolyamat.

![parancsfájllal](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

## <a name="about"></a>VPN-eszköz konfigurációs parancsfájlok

A létesítmények közötti VPN-kapcsolat az Azure VPN gateway, egy a helyszíni VPN-eszköz és az IPsec S2S VPN-alagút összekötő áll. A tipikus munkafolyamata a következő lépésekből áll:

1. Hozza létre és konfigurálja az Azure VPN-átjáró (virtuális hálózati átjáró)
2. Létrehozhat és konfigurálhat egy Azure helyi hálózati átjáró, amely a helyszíni hálózat és a VPN-eszköz jelöli
3. Hozza létre és konfigurálja az Azure VPN kapcsolatot az Azure VPN gateway és a helyi hálózati átjáró
4. A helyszíni VPN-eszközön, a tényleges, az Azure VPN gateway az S2S VPN csatornát szeretne létrehozni a helyi hálózati átjáró által képviselt konfigurálása

Című témakör 1 – 3 az Azure használatával hajthatja végre [portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), vagy [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Az utolsó lépés a helyszíni VPN-eszközök Azure-on kívüli konfigurálását jelenti. Ez a funkció lehetővé teszi letölteni egy konfigurációs parancsfájl esetében a VPN-eszköz a megfelelő értékekkel, az Azure VPN gateway, virtuális hálózati és a helyszíni hálózati címelőtagokat, és VPN-kapcsolat tulajdonságait, már kitöltötte stb. A parancsfájllal kiindulási pontként, vagy közvetlenül a helyszíni VPN-eszközök a konfigurációs konzolon keresztül alkalmazza a parancsfájlt.

> [!IMPORTANT]
> * Minden egyes VPN-eszköz konfigurációs parancsprogram szintaxisa a következő különböző, és nagymértékben függ a modellek és belső vezérlőprogramjának következő verziójával. Nagy figyelmet fordítani az eszköz modellt és verziót adatokat a rendelkezésre álló sablonok ellen.
> * Néhány paraméterértékeket az eszköz egyedinek kell lennie, és nem lehet megállapítani az eszköz elérése nélkül. Az Azure által létrehozott parancsfájlokat előre töltse ki ezeket az értékeket, de győződjön meg arról, a megadott értékek érvényesek az eszközön. Példák:
>    * Csatoló számok
>    * Ellenőrző lista telefonszámok
>    * Házirend neve vagy számok, stb.
> * Keresse meg a kulcsszó "**cseréje**" beágyazott keresse meg a paramétert, a parancsfájl alkalmazása előtt ellenőrizze a parancsfájl.
> * Néhány-sablonjai tartalmazzák a "**tisztítás**" szakaszban, távolítsa el a konfigurációkat alkalmazhat. A Lemezkarbantartó szakaszok jelenleg megjegyzésként szerepelnek alapértelmezés szerint.

## <a name="download-the-configuration-script-from-azure-portal"></a>A konfigurációs parancsprogram letöltése Azure-portálon

Hozzon létre egy Azure VPN gateway, a helyi hálózati átjáró és az összekötő kapcsolat erőforrás. A következő lap végigvezeti a lépéseken:

* [Pont-pont kapcsolat létrehozása az Azure-portálon](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Ha létrejött a kapcsolati erőforrást, kövesse az utasításokat az alábbi töltse le a VPN-eszköz konfigurációs parancsfájlokat:

1. Egy böngészőből keresse meg a [Azure-portálon](http://portal.azure.com) és szükség esetén jelentkezzen be az Azure-fiókjával
2. Nyissa meg a létrehozott kapcsolat-erőforráshoz. A lista az összes kapcsolat erőforrás található "Az összes szolgáltatás", majd a "Hálózat" majd "Kapcsolatokat."

    ![kapcsolat-lista](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Kattintson a konfigurálni kívánt kapcsolat.

    ![kapcsolat-áttekintés](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. A kapcsolat – áttekintés lapon; pirossal kiemelt mint "Konfigurációs letöltése" hivatkozásra kattintva Ekkor megnyílik a "Letöltés konfiguráció" lapot.

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Válassza ki a VPN-eszköz modell termékcsalád és a belső vezérlőprogram verzióját, majd kattintson a "Letöltés konfiguráció" gombra.

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Mentse a letöltött parancsfájlt (szövegfájlba) a böngészőből kéri.
7. Miután letöltötte a konfigurációs parancsfájl, nyissa meg szövegszerkesztőben és keressen rá a "REPLACE" kulcsszó azonosításához, és vizsgálja meg a paramétereket, előfordulhat, hogy le kell cserélni kell.

    ![Szerkesztés-parancsfájl](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>A konfigurációs parancsprogram használata az Azure PowerShell letöltése

Emellett letöltheti a konfigurációs parancsfájl használata az Azure PowerShell, a következő példában látható módon:

```powershell
$Sub         = "<YourSubscriptionName>"
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite5"

Login-AzureRmAccount
Set-AzureRmContext -Subscription $Sub

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>A konfigurációs parancsfájl alkalmazása a VPN-eszköz

Miután letöltött és a konfigurációs parancsfájl érvényesítése, a következő lépés a parancsfájl alkalmazhat a VPN-eszköz. A tényleges eljárás attól függően változik, a VPN-eszköz teszi és -modellek. A művelet laborútmutatókkal vagy utasítás oldalak olvassa el a VPN-eszközök.

## <a name="next-steps"></a>További lépések

További konfigurálásához a [pont-pont kapcsolat](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

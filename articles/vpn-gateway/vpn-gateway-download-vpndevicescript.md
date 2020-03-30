---
title: Az S2S VPN-kapcsolatok VPN-eszközkonfigurációs parancsfájljainak letöltése
description: Ez a cikk végigvezeti az S2S VPN-kapcsolatok Hoz az Azure Resource Manager használatával az S2S VPN-kapcsolatok VPN-eszközkonfigurációs parancsfájljainak letöltésén.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f905e27f48a0bf9181625bbba07549a13d9420cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162136"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Az S2S VPN-kapcsolatok VPN-eszközkonfigurációs parancsfájljainak letöltése

Ez a cikk végigvezeti az S2S VPN-kapcsolatok Hoz az Azure Resource Manager használatával az S2S VPN-kapcsolatok VPN-eszközkonfigurációs parancsfájljainak letöltésén. Az alábbi ábra a magas szintű munkafolyamatot mutatja be.

![letöltés-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

A következő eszközök rendelkeznek elérhető parancsfájlokkal:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Vpn-eszközkonfigurációs parancsfájlok – ismertető

A létesítmények közötti VPN-kapcsolat egy Azure VPN-átjáróból, egy helyszíni VPN-eszközből és egy IPsec S2S VPN-alagútból áll, amely összeköti a kettőt. A tipikus munkafolyamat a következő lépéseket tartalmazza:

1. Azure VPN-átjáró (virtuális hálózati átjáró) létrehozása és konfigurálása
2. A helyszíni hálózatot és vpn-eszközt képviselő Helyi Azure-hálózati átjáró létrehozása és konfigurálása
3. Azure VPN-kapcsolat létrehozása és konfigurálása az Azure VPN-átjáró és a helyi hálózati átjáró között
4. Konfigurálja a helyi hálózati átjáró által képviselt helyszíni VPN-eszközt a tényleges S2S VPN-alagút létrehozásához az Azure VPN-átjáróval

Az Azure [Portalon,](vpn-gateway-howto-site-to-site-resource-manager-portal.md)a [PowerShellen](vpn-gateway-create-site-to-site-rm-powershell.md)vagy a [CLI-n](vpn-gateway-howto-site-to-site-resource-manager-cli.md)az 1–3. Az utolsó lépés a helyszíni VPN-eszközök konfigurálása az Azure-on kívül. Ez a funkció lehetővé teszi, hogy letöltse a VPN-eszköz konfigurációs parancsfájlját az Azure VPN-átjáró, a virtuális hálózat és a helyszíni hálózati cím előtagok, valamint a VPN-kapcsolat tulajdonságai nak megfelelő értékeivel, stb. Használhatja a parancsfájlt kiindulási pontként, vagy alkalmazhatja a parancsfájlt közvetlenül a helyszíni VPN-eszközökre a konfigurációs konzolon keresztül.

> [!IMPORTANT]
> * Az egyes VPN-eszközkonfigurációs parancsfájlok szintaxisa eltérő, és nagymértékben függ a modellektől és a belső vezérlőprogram-verzióktól. Fordítson különös figyelmet az eszköz modell- és verzióinformációira a rendelkezésre álló sablonok alapján.
> * Egyes paraméterértékeknek egyedinek kell lenniük az eszközön, és nem határozhatók meg az eszköz elérése nélkül. Az Azure által létrehozott konfigurációs parancsfájlok előre kitölti ezeket az értékeket, de meg kell győződnie arról, hogy a megadott értékek érvényesek az eszközön. Példák:
>    * Kapcsolati pontok száma
>    * Hozzáférés-vezérlési lista számai
>    * Házirend nevek vagy számok stb.
> * Keresse meg a **"REPLACE"** kulcsszót, amely a parancsfájlba ágyazva található, hogy megtalálja azokat a paramétereket, amelyeket ellenőriznie kell a parancsfájl alkalmazása előtt.
> * Egyes sablonok közé tartozik a konfigurációk eltávolításához alkalmazható "**CLEANUP**" szakasz. A törlési szakaszok at alapértelmezés szerint megjegyzésekkel elkell végezni.

## <a name="download-the-configuration-script-from-azure-portal"></a>A konfigurációs parancsfájl letöltése az Azure Portalról

Hozzon létre egy Azure VPN-átjárót, helyi hálózati átjárót és egy kapcsolati erőforrást, amely összeköti a kettőt. A következő oldal végigvezeti a lépéseken:

* [Helyek közötti kapcsolat létrehozása az Azure Portalon](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

A kapcsolati erőforrás létrehozása után kövesse az alábbi utasításokat a VPN-eszköz konfigurációs parancsfájljainak letöltéséhez:

1. Böngészőből keresse meg az [Azure-portált,](https://portal.azure.com) és szükség esetén jelentkezzen be Azure-fiókjával
2. Nyissa meg a létrehozott kapcsolati erőforrást. Az összes kapcsolati erőforrás listáját a "Minden szolgáltatás", majd a "HÁLÓZATKEZELÉS" és a "Kapcsolatok" gombra kattintva találja meg.

    ![kapcsolatlista](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Kattintson a konfigurálni kívánt kapcsolatra.

    ![kapcsolat áttekintése](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Kattintson a "Konfiguráció letöltése" linkre, amint az pirossal van kiemelve a Kapcsolat áttekintése oldalon; ez megnyitja a "Konfiguráció letöltése" oldalt.

    ![letöltés-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Válassza ki a VPN-eszköz modellcsalád- és firmware-verzióját, majd kattintson a "Konfiguráció letöltése" gombra.

    ![letöltés66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. A program kéri, hogy mentse a letöltött parancsfájlt (egy szövegfájlt) a böngészőből.
7. Miután letöltötte a konfigurációs parancsfájlt, nyissa meg egy szövegszerkesztővel, és keresse meg a "REPLACE" kulcsszót, hogy azonosítsa és megvizsgálja azokat a paramétereket, amelyeket esetleg ki kell cserélni.

    ![szerkesztés-parancsfájl](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>A konfigurációs parancsfájl letöltése az Azure PowerShell használatával



A konfigurációs parancsfájlt az Azure PowerShell használatával is letöltheti, ahogy az a következő példában látható:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>A konfigurációs parancsfájl alkalmazása a VPN-eszközre

Miután letöltötte és érvényesítette a konfigurációs parancsfájlt, a következő lépés a parancsfájl alkalmazása a VPN-eszközre. A tényleges eljárás a VPN-eszköz gyártmányáttól és modelljeitől függ. Tekintse meg a VPN-eszközök kezelési útmutatóját vagy útmutatólapjait.

## <a name="next-steps"></a>További lépések

Folytassa a [helyek közötti kapcsolat](vpn-gateway-howto-site-to-site-resource-manager-portal.md)konfigurálását.

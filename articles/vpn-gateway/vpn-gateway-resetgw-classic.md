---
title: Alaphelyzetbe állítja az Azure VPN gateway IPsec-alagutak közti |} Microsoft Docs
description: Ez a cikk végigvezeti az Azure VPN Gateway IPsec-alagutak közti alaphelyzetbe állítását. A cikk a klasszikus, és a Resource Manager üzembe helyezési modellel a VPN-átjárók vonatkozik.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: cherylmc
ms.openlocfilehash: 604af54d034b6b51a96ff098827e2352a163be18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884551"
---
# <a name="reset-a-vpn-gateway"></a>VPN Gateway alaphelyzetbe állítása

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. Ez a cikk segítséget nyújt a VPN-átjárót alaphelyzetbe.

### <a name="what-happens-during-a-reset"></a>Mi történik, amikor egy alaphelyzetbe áll?

VPN-átjáró két Virtuálisgép-példány fut egy aktív-készenléti állapotban lévő konfigurációs tevődik össze. Az átjárót alaphelyzetbe állításakor az átjáró újraindul, és majd újra alkalmazza a létesítmények közötti konfigurálható, hogy azt. Az átjáró megtartja azt a nyilvános IP-címet, amellyel rendelkezik. Ez azt jelenti, hogy a VPN-útválasztó konfigurációját nem kell frissíteni új nyilvános IP-címmel az Azure VPN Gatewayhez.

A parancs az átjárót alaphelyzetbe kell elküldésekor az aktuális active példányát az Azure VPN gateway azonnal újraindítása után. Nem lesznek rövid hiány során a feladatátvételt, az aktív példány (újraindítása folyamatban), az a készenléti kiszolgálói példányra. Ez a szünet nem tart tovább egy percnél.

Ha a kapcsolat nem áll vissza az első újraindítás után, adja ki ugyanezt a parancsot újra a második virtuálisgép-példány (az új aktív átjáró) újraindításához. Ha a két, újraindításra vonatkozó kérelem egymás után van kiadva, hosszabb szünet következhet be, amíg az aktív és a készenléti virtuálisgép-példány is újraindul. Ebben az esetben hosszabb szünet következik be a VPN-kapcsolatban – akár 2–4 perc –, amíg a virtuális gépek újraindulnak.

Után két újraindításra Ha olyan továbbra is problémákat tapasztal, létesítmények közötti kapcsolatot, nyisson egy támogatási kérést Azure-portálról.

## <a name="before"></a>Előkészületek

Az átjáró alaphelyzetbe állítása előtt ellenőrizze az alábbi listán szereplő legfontosabb elemeket mindegyik IPsec helyek közötti (S2S) VPN-alagúthoz. Ha bármilyen eltérés van az elemek között, az S2S VPN-alagutak kapcsolata meg fog szakadni. Ellenőrzése és javítása a helyszíni és az Azure VPN gatewayek konfigurációi menti, a szükségtelen újraindítások és az egyéb működő engedélyezi az átjáró üzemzavarokhoz vezethet.

Ellenőrizze az alábbiakat az átjárót alaphelyzetbe állítása előtt:

* Az Azure VPN Gateway és a helyszíni VPN-átjáró internetes IP-címei (VIP-k) helyesen vannak konfigurálva az Azure és a helyszíni VPN-házirendekben.
* Az előre megosztott kulcsnak meg kell egyeznie az Azure VPN Gateway átjáróban és a helyszíni VPN-átjáróban.
* Ha adott IPsec/IKE-konfigurációt alkalmaz – például titkosítást, kivonatoló algoritmust vagy sérülés utáni titkosságvédelmet (PFS) – ügyeljen arra, hogy az Azure VPN Gateway és a helyszíni VPN-átjárók ugyanezzel a konfigurációval rendelkezzenek.

## <a name="portal"></a>Azure-portálon

Alaphelyzetbe állíthatja a Resource Manager VPN-átjáró az Azure portál használatával. Ha szeretne alaphelyzetbe állítani a klasszikus átjáró, tekintse meg a [PowerShell](#resetclassic) lépéseket.

### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

1. Nyissa meg a [Azure-portálon](https://portal.azure.com) , és keresse meg az erőforrás-kezelő virtuális hálózati átjáró, amely alaphelyzetbe állítja.
2. A virtuális hálózati átjáró panelen kattintson az "Új".

  ![Alaphelyzetbe állítja a VPN-átjáró panel](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. A visszaállítási paneljén kattintson a **alaphelyzetbe** gombra.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

A parancsmag egy átjárót alaphelyzetbe állítása **alaphelyzetbe állítása-AzureRmVirtualNetworkGateway**. A visszaállítás elvégzése előtt győződjön meg arról, hogy a legújabb verzióját a [Resource Manager PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0). Az alábbi példa alaphelyzetbe állítja a virtuális hálózati átjáró VNet1GW nevű TestRG1 az erőforráscsoporthoz tartozik:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Eredmény:

Amikor megjelenik a visszaadandó eredmény, akkor feltételezheti, sikeres volt-e az átjáró alaphelyzetbe állítása. Azonban nincs semmi a visszaadandó eredmény, amely explicit módon, hogy sikeres volt-e az alaphelyzetbe állítás. Ha szeretné meg arról, hogy pontosan Ha az átjáró-visszaállítás történt előzményeit, megtekintheti ezt az információt a [Azure-portálon](https://portal.azure.com). A portálon lépjen a **(GatewayName) -> erőforrás állapotának**.

### <a name="resetclassic"></a>Klasszikus telepítési modell

A parancsmag egy átjárót alaphelyzetbe állítása **alaphelyzetbe állítása-AzureVNetGateway**. A visszaállítás elvégzése előtt győződjön meg arról, hogy a legújabb verzióját a [Service Management (SM) PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0). Az alábbi példában a "ContosoVNet" nevű virtuális hálózati átjáró alaphelyzetbe állítása:

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

Eredmény:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Az Azure parancssori felület

Az átjáró visszaállításához használja a [az hálózati vnet-átjáró alaphelyzetbe](https://docs.microsoft.com/cli/azure/network/vnet-gateway#az_network_vnet_gateway_reset) parancsot. Az alábbi példa alaphelyzetbe állítja a virtuális hálózati átjáró VNet5GW nevű TestRG5 az erőforráscsoporthoz tartozik:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Eredmény:

Amikor megjelenik a visszaadandó eredmény, akkor feltételezheti, sikeres volt-e az átjáró alaphelyzetbe állítása. Azonban nincs semmi a visszaadandó eredmény, amely explicit módon, hogy sikeres volt-e az alaphelyzetbe állítás. Ha szeretné meg arról, hogy pontosan Ha az átjáró-visszaállítás történt előzményeit, megtekintheti ezt az információt a [Azure-portálon](https://portal.azure.com). A portálon lépjen a **(GatewayName) -> erőforrás állapotának**.
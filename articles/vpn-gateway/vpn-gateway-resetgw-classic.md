---
title: IPsec-alagutak megszüntette az Azure VPN gateway alaphelyzetbe állítása |} A Microsoft Docs
description: Ez a cikk bemutatja az Azure VPN Gateway IPsec-alagutak megszüntette alaphelyzetbe állításához. A cikk a VPN-átjárókhoz a klasszikus és a Resource Manager-alapú üzemi modellek vonatkozik.
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
ms.openlocfilehash: 8db17b92208bd956bd5f9b855249f03ecd5e2c59
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756699"
---
# <a name="reset-a-vpn-gateway"></a>VPN Gateway alaphelyzetbe állítása

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. Ez a cikk segítséget nyújt a VPN gateway alaphelyzetbe állítása.

### <a name="what-happens-during-a-reset"></a>Mi történik, a visszaállítás során?

Egy VPN-átjárót egy aktív-készenléti konfigurációban futó két Virtuálisgép-példányok tevődik össze. Az átjáró alaphelyzetbe állítása, ha újraindítja az átjárót, és majd újra alkalmazza arra a létesítmények közötti konfigurációkat. Az átjáró megtartja azt a nyilvános IP-címet, amellyel rendelkezik. Ez azt jelenti, hogy a VPN-útválasztó konfigurációját nem kell frissíteni új nyilvános IP-címmel az Azure VPN Gatewayhez.

Amikor a parancsot az átjáró alaphelyzetbe állítása, az Azure VPN gateway jelenleg aktív példánya azonnal újraindul. Lesz olyan rövid szünet készenléti példány (újraindítása), az aktív példányról a feladatátvétel során. Ez a szünet nem tart tovább egy percnél.

Ha a kapcsolat nem áll vissza az első újraindítás után, adja ki ugyanezt a parancsot újra a második virtuálisgép-példány (az új aktív átjáró) újraindításához. Ha a két, újraindításra vonatkozó kérelem egymás után van kiadva, hosszabb szünet következhet be, amíg az aktív és a készenléti virtuálisgép-példány is újraindul. Ebben az esetben hosszabb szünet következik be a VPN-kapcsolatban – akár 2–4 perc –, amíg a virtuális gépek újraindulnak.

Után két újraindításra Ha továbbra is létesítmények közötti csatlakozási problémákat tapasztal, nyisson egy támogatási kérést az Azure Portalról.

## <a name="before"></a>Előkészületek

Az átjáró alaphelyzetbe állítása előtt ellenőrizze az alábbi listán szereplő legfontosabb elemeket mindegyik IPsec helyek közötti (S2S) VPN-alagúthoz. Ha bármilyen eltérés van az elemek között, az S2S VPN-alagutak kapcsolata meg fog szakadni. Ellenőrzésével és javításával a helyszíni és az Azure VPN-átjárók konfigurációi veszi szükségtelen újraindítások és más, működő kapcsolatainak az átjárókon megszakadása.

Ellenőrizze a következő elemeket az átjáró alaphelyzetbe állítása előtt:

* Az Azure VPN Gateway és a helyszíni VPN-átjáró internetes IP-címei (VIP-k) helyesen vannak konfigurálva az Azure és a helyszíni VPN-házirendekben.
* Az előre megosztott kulcsnak meg kell egyeznie az Azure VPN Gateway átjáróban és a helyszíni VPN-átjáróban.
* Ha adott IPsec/IKE-konfigurációt alkalmaz – például titkosítást, kivonatoló algoritmust vagy sérülés utáni titkosságvédelmet (PFS) – ügyeljen arra, hogy az Azure VPN Gateway és a helyszíni VPN-átjárók ugyanezzel a konfigurációval rendelkezzenek.

## <a name="portal"></a>Azure Portal

Alaphelyzetbe állíthatja a Resource Manager VPN-átjáró, az Azure portal használatával. Ha azt szeretné, a klasszikus átjáró alaphelyzetbe állítása, tekintse meg a [PowerShell](#resetclassic) lépéseket.

### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

1. Nyissa meg a [az Azure portal](https://portal.azure.com) , és keresse meg a Resource Manager virtuális hálózati átjáró, amely alaphelyzetbe állítja.
2. A virtuális hálózati átjáró paneljén kattintson a "Új" elemre.

  ![VPN-átjáró panel visszaállítása](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. A visszaállítás panelen kattintson a **alaphelyzetbe** gombra.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

Ha szeretne alaphelyzetbe állítani egy átjárót a parancsmag **Reset-AzureRmVirtualNetworkGateway**. A alaphelyzetbe állítása előtt, ellenőrizze, hogy a legújabb verzióját a [Resource Manager PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.0.0). A következő példa alaphelyzetbe állítja a virtuális hálózati átjáró nevű VNet1GW TestRG1 az erőforráscsoportban:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Eredmény:

Visszatérési eredményt kapunk, akkor feltételezheti, az átjáró alaphelyzetbe állítása sikeres volt. Azonban nincs semmi a visszaadott eredmény, amely azt jelzi, hogy explicit módon, hogy a alaphelyzetbe állítása sikeres volt-e. Ha szeretné a példakódot előzményeinek megtekintéséhez a pontosan az átjáró alaphelyzetbe állítása fellépésének, ezt az információt is megtekintheti a [az Azure portal](https://portal.azure.com). A portálon lépjen a **(GatewayName) -> a Resource Health**.

### <a name="resetclassic"></a>Klasszikus üzemi modellben

Ha szeretne alaphelyzetbe állítani egy átjárót a parancsmag **Reset-AzureVNetGateway**. A alaphelyzetbe állítása előtt, ellenőrizze, hogy a legújabb verzióját a [Service Management (SM) PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). A következő példa alaphelyzetbe állítja a "ContosoVNet" nevű virtuális hálózati átjáró:

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

## <a name="cli"></a>Azure CLI

Az átjáró alaphelyzetbe állítása, használja a [az network vnet-átjáró alaphelyzetbe állítása](https://docs.microsoft.com/cli/azure/network/vnet-gateway) parancsot. A következő példa alaphelyzetbe állítja a virtuális hálózati átjáró nevű VNet5GW TestRG5 az erőforráscsoportban:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Eredmény:

Visszatérési eredményt kapunk, akkor feltételezheti, az átjáró alaphelyzetbe állítása sikeres volt. Azonban nincs semmi a visszaadott eredmény, amely azt jelzi, hogy explicit módon, hogy a alaphelyzetbe állítása sikeres volt-e. Ha szeretné a példakódot előzményeinek megtekintéséhez a pontosan az átjáró alaphelyzetbe állítása fellépésének, ezt az információt is megtekintheti a [az Azure portal](https://portal.azure.com). A portálon lépjen a **(GatewayName) -> a Resource Health**.

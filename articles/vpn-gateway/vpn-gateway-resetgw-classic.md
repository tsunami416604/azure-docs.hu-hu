---
title: Azure VPN-átjáró alaphelyzetbe állítása az IPsec-alagút visszaállításához
description: Ez a cikk végigvezeti az Azure VPN-átjáró alaphelyzetbe állításán az IPsec-alagutak újbóli létrehozásához. A cikk a klasszikus és az erőforrás-kezelő telepítési modellek VPN-átjárókra vonatkozik.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e3a5807a0ccfa39cc80acacedaa5fb4d3afaaed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244419"
---
# <a name="reset-a-vpn-gateway"></a>VPN Gateway alaphelyzetbe állítása

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. Ez a cikk segít a VPN-átjáró alaphelyzetbe állításához.

### <a name="what-happens-during-a-reset"></a>Mi történik az alaphelyzetbe állítás során?

A VPN-átjáró két virtuálisgép-példányból áll, amelyek aktív készenléti konfigurációban futnak. Amikor alaphelyzetbe állítja az átjárót, újraindítja az átjárót, majd újra alkalmazza a telephelyek közötti konfigurációkat. Az átjáró megtartja azt a nyilvános IP-címet, amellyel rendelkezik. Ez azt jelenti, hogy a VPN-útválasztó konfigurációját nem kell frissíteni új nyilvános IP-címmel az Azure VPN Gatewayhez.

Amikor kiadja az átjáró alaphelyzetbe állítására a parancsot, az Azure VPN-átjáró aktuális aktív példánya azonnal újraindul. Lesz egy rövid rés a feladatátvétel során az aktív példány (újraindítás alatt), a készenléti példány. Ez a szünet nem tart tovább egy percnél.

Ha a kapcsolat nem áll vissza az első újraindítás után, adja ki ugyanezt a parancsot újra a második virtuálisgép-példány (az új aktív átjáró) újraindításához. Ha a két, újraindításra vonatkozó kérelem egymás után van kiadva, hosszabb szünet következhet be, amíg az aktív és a készenléti virtuálisgép-példány is újraindul. Ez hosszabb rést okoz a VPN-kapcsolaton, akár 30–45 percig a virtuális gépek számára az újraindítások befejezéséhez.

Két újraindítás után, ha továbbra is tapasztal több telephelyen átnyúló kapcsolódási problémákat, nyisson meg egy támogatási kérelmet az Azure Portalon.

## <a name="before-you-begin"></a><a name="before"></a>Mielőtt elkezdené

Az átjáró alaphelyzetbe állítása előtt ellenőrizze az alábbi listán szereplő legfontosabb elemeket mindegyik IPsec helyek közötti (S2S) VPN-alagúthoz. Ha bármilyen eltérés van az elemek között, az S2S VPN-alagutak kapcsolata meg fog szakadni. A helyszíni és az Azure VPN-átjárók konfigurációinak ellenőrzése és javítása szükségtelen újraindításokat és megszakításokat takarít meg az átjárók on-the más működő kapcsolataiszámára.

Az átjáró alaphelyzetbe állítása előtt ellenőrizze a következő elemeket:

* Az Azure VPN Gateway és a helyszíni VPN-átjáró internetes IP-címei (VIP-k) helyesen vannak konfigurálva az Azure és a helyszíni VPN-házirendekben.
* Az előre megosztott kulcsnak meg kell egyeznie az Azure VPN Gateway átjáróban és a helyszíni VPN-átjáróban.
* Ha adott IPsec/IKE-konfigurációt alkalmaz – például titkosítást, kivonatoló algoritmust vagy sérülés utáni titkosságvédelmet (PFS) – ügyeljen arra, hogy az Azure VPN Gateway és a helyszíni VPN-átjárók ugyanezzel a konfigurációval rendelkezzenek.

## <a name="azure-portal"></a><a name="portal"></a>Azure-portál

Az Azure Portal használatával alaphelyzetbe állíthatja a Resource Manager VPN-átjáróját. Ha azt szeretné, hogy állítsa alaphelyzetbe egy klasszikus átjáró, tekintse meg a [PowerShell](#resetclassic) lépéseit.

### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com) és keresse meg az alaphelyzetbe állítani kívánt Erőforrás-kezelő virtuális hálózati átjárót.
2. A virtuális hálózati átjáró paneljén kattintson az "Alaphelyzet" gombra.

   ![Vpn-átjáró visszaállítása panel](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Az Alaphelyzet panelen kattintson az **Alaphelyzet** gombra.

## <a name="powershell"></a><a name="ps"></a>Powershell

### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az átjáró alaphelyzetbe állításához szükséges parancsmag a **Reset-AzVirtualNetworkGateway**. Az alaphelyzetbe állítás végrehajtása előtt győződjön meg arról, hogy a [PowerShell Az parancsmagok legújabb verziójával rendelkezik.](https://docs.microsoft.com/powershell/module/az.network) A következő példa visszaállítja a VNet1GW nevű virtuális hálózati átjárót a TestRG1 erőforráscsoportban:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Eredmény:

Amikor visszatérési eredményt kap, feltételezheti, hogy az átjáró alaphelyzetbe állítása sikeres volt. A visszatérési eredményben azonban nincs semmi, ami kifejezetten jelezne, hogy az alaphelyzetbe állítás sikeres volt. Ha közelebbről meg szeretné tekinteni az előzményeket, hogy pontosan mikor történt az átjáró alaphelyzetbe állítása, megtekintheti ezeket az információkat az [Azure Portalon.](https://portal.azure.com) A portálon keresse meg a **"GatewayName" -> Erőforrás állapota**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klasszikus üzembe helyezési modell

Az átjáró alaphelyzetbe állításához szükséges parancsmag az **Alaphelyzetbe állítás-AzureVNetGateway**. Az Azure PowerShell-parancsmagokat a szolgáltatáskezelés helyileg kell telepíteni az asztalon. Az Azure Cloud Shell nem használható. Az alaphelyzetbe állítás végrehajtása előtt győződjön meg arról, hogy a [Service Management (SM) PowerShell-parancsmagok legújabb verziójával rendelkezik.](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) Ha ezt a parancsot használja, győződjön meg arról, hogy a virtuális hálózat teljes nevét használja. A portál használatával létrehozott klasszikus virtuális hálózatok hosszú névvel rendelkeznek, amely szükséges a PowerShell. A hosszú név megtekintéséhez használja a "Get-AzureVNetConfig -ExportToFile C:\Myfoldername\NetworkConfig.xml" parancsot.

A következő példa visszaállítja a "Group TestRG1 TestVNet1" nevű virtuális hálózat átjáróját (amely egyszerűen "TestVNet1" értéket mutat a portálon):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
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

## <a name="azure-cli"></a><a name="cli"></a>Azure CLI

Az átjáró alaphelyzetbe állításához használja az [az hálózati vnet-átjáró alaphelyzetbe parancs.](https://docs.microsoft.com/cli/azure/network/vnet-gateway) A következő példa visszaállítja a VNet5GW nevű virtuális hálózati átjárót a TestRG5 erőforráscsoportban:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Eredmény:

Amikor visszatérési eredményt kap, feltételezheti, hogy az átjáró alaphelyzetbe állítása sikeres volt. A visszatérési eredményben azonban nincs semmi, ami kifejezetten jelezne, hogy az alaphelyzetbe állítás sikeres volt. Ha közelebbről meg szeretné tekinteni az előzményeket, hogy pontosan mikor történt az átjáró alaphelyzetbe állítása, megtekintheti ezeket az információkat az [Azure Portalon.](https://portal.azure.com) A portálon keresse meg a **"GatewayName" -> Erőforrás állapota**.

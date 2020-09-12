---
title: Azure VPN Gateway alaphelyzetbe állítása az IPsec-alagút újralétesítéséhez
description: Az Azure-VPN Gateway alaphelyzetbe állításával visszaállíthatja az IPsec-alagutakat a klasszikus és a Resource Manager-alapú üzemi modellben található VPN-átjárók számára.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: c0313c74b046d4c93c8625eab8659df392041059
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89419809"
---
# <a name="reset-a-vpn-gateway"></a>VPN Gateway alaphelyzetbe állítása

Az Azure VPN Gateway alaphelyzetbe állítása akkor hasznos, ha egy vagy több helyek közötti VPN-alagúton elveszíti a létesítmények közötti VPN-kapcsolatot. Ebben az esetben a helyszíni VPN-eszközei megfelelően működnek, de nem tudnak Ipsec-alagutakat létesíteni az Azure VPN Gateway átjárókkal. Ez a cikk segítséget nyújt a VPN-átjáró alaphelyzetbe állításához.

### <a name="what-happens-during-a-reset"></a>Mi történik az Alaphelyzetbe állítás során?

A VPN-átjáró két virtuálisgép-példányból áll, amelyek aktív-készenléti konfigurációban futnak. Az átjáró alaphelyzetbe állításakor újraindítja az átjárót, majd újra alkalmazza a létesítmények közötti konfigurációkat. Az átjáró megtartja azt a nyilvános IP-címet, amellyel rendelkezik. Ez azt jelenti, hogy a VPN-útválasztó konfigurációját nem kell frissíteni új nyilvános IP-címmel az Azure VPN Gatewayhez.

Amikor a parancsot az átjáró alaphelyzetbe állítására állítja ki, az Azure VPN Gateway aktuális aktív példánya azonnal újraindul. Az aktív példány feladatátvétele (újraindításkor) a készenléti példányra rövid hézagot eredményez. Ez a szünet nem tart tovább egy percnél.

Ha a kapcsolat nem áll vissza az első újraindítás után, adja ki ugyanezt a parancsot újra a második virtuálisgép-példány (az új aktív átjáró) újraindításához. Ha a két, újraindításra vonatkozó kérelem egymás után van kiadva, hosszabb szünet következhet be, amíg az aktív és a készenléti virtuálisgép-példány is újraindul. Ez a művelet a VPN-kapcsolaton túl hosszú időt eredményez, ami akár 30 – 45 percet is igénybe vehet, amíg a virtuális gépek újraindulnak.

Ha a két újraindítást követően továbbra is a létesítmények közötti kapcsolati problémák léptek fel, nyisson meg egy támogatási kérést a Azure Portal.

## <a name="before-you-begin"></a><a name="before"></a>Kezdés előtt

Az átjáró alaphelyzetbe állítása előtt ellenőrizze az alábbi listán szereplő legfontosabb elemeket mindegyik IPsec helyek közötti (S2S) VPN-alagúthoz. Ha bármilyen eltérés van az elemek között, az S2S VPN-alagutak kapcsolata meg fog szakadni. A helyszíni és az Azure-beli VPN-átjárók konfigurációjának ellenőrzése és javítása a szükségtelen újraindítások és az átjárók egyéb munkakapcsolatainak megszakadásai miatt megszakad.

Az átjáró alaphelyzetbe állítása előtt ellenőrizze a következő elemeket:

* Az Azure VPN Gateway és a helyszíni VPN-átjáró internetes IP-címei (VIP-k) helyesen vannak konfigurálva az Azure és a helyszíni VPN-házirendekben.
* Az előre megosztott kulcsnak meg kell egyeznie az Azure VPN Gateway átjáróban és a helyszíni VPN-átjáróban.
* Ha adott IPsec/IKE-konfigurációt alkalmaz – például titkosítást, kivonatoló algoritmust vagy sérülés utáni titkosságvédelmet (PFS) – ügyeljen arra, hogy az Azure VPN Gateway és a helyszíni VPN-átjárók ugyanezzel a konfigurációval rendelkezzenek.

## <a name="azure-portal"></a><a name="portal"></a>Azure Portal

A Azure Portal segítségével alaphelyzetbe állíthatja a Resource Manager VPN-átjárót. Ha alaphelyzetbe szeretné állítani a klasszikus átjárót, tekintse meg a [PowerShell](#resetclassic) lépéseit.

### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

1. Nyissa meg a [Azure Portalt](https://portal.azure.com) , és navigáljon az alaphelyzetbe állítani kívánt Resource Manager-beli virtuális hálózati átjáróhoz.
2. A virtuális hálózati átjáró paneljén kattintson az Alaphelyzetbe állítás elemre.

   ![VPN Gateway panel alaphelyzetbe állítása](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. Az Alaphelyzetbe állítás panelen kattintson az **Alaphelyzetbe állítás** gombra.

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Resource Manager-alapú üzemi modell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az átjáró **alaphelyzetbe**állítására szolgáló parancsmag a következőt állítja be: AzVirtualNetworkGateway. Az Alaphelyzetbe állítás előtt győződjön meg arról, hogy a PowerShell legújabb verziója van telepítve a [parancsmagok használatával](https://docs.microsoft.com/powershell/module/az.network). A következő példa egy VNet1GW nevű virtuális hálózati átjárót állít alaphelyzetbe a TestRG1 erőforráscsoporthoz:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Eredmény:

Ha visszatérési eredményt kap, akkor feltételezhető, hogy az átjáró alaphelyzetbe állítása sikeres volt. A visszatérési eredmény azonban semmis, ami explicit módon jelzi, hogy az Alaphelyzetbe állítás sikeres volt. Ha azt szeretné, hogy az előzmények alapján pontosan megtekinthető legyen az átjáró alaphelyzetbe állítása, megtekintheti ezeket az információkat a [Azure Portal](https://portal.azure.com). A portálon navigáljon a **"átjáró neve"-> Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Klasszikus üzemi modell

Az átjáró **alaphelyzetbe**állítására szolgáló parancsmag a következőt állítja be: AzureVNetGateway. A Service Management Azure PowerShell-parancsmagjai helyileg kell telepíteni az asztalra. A Azure Cloud Shell nem használható. Az Alaphelyzetbe állítás előtt ellenőrizze, hogy rendelkezik-e a [Service Management (SM) PowerShell-parancsmagok](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets)legújabb verziójával. A parancs használatakor győződjön meg arról, hogy a virtuális hálózat teljes nevét használja. A portál használatával létrehozott klasszikus virtuális hálózatok hosszú nevet igényelnek a PowerShell számára. A hosszú nevet a "Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml" paranccsal tekintheti meg.

Az alábbi példa alaphelyzetbe állítja a "Group TestRG1 TestVNet1" nevű virtuális hálózat átjáróját (amely egyszerűen "TestVNet1" a portálon):

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

Az átjáró alaphelyzetbe állításához használja az az [Network vnet-Gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway) parancsot. A következő példa egy VNet5GW nevű virtuális hálózati átjárót állít alaphelyzetbe a TestRG5 erőforráscsoporthoz:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Eredmény:

Ha visszatérési eredményt kap, akkor feltételezhető, hogy az átjáró alaphelyzetbe állítása sikeres volt. A visszatérési eredmény azonban semmis, ami explicit módon jelzi, hogy az Alaphelyzetbe állítás sikeres volt. Ha azt szeretné, hogy az előzmények alapján pontosan megtekinthető legyen az átjáró alaphelyzetbe állítása, megtekintheti ezeket az információkat a [Azure Portal](https://portal.azure.com). A portálon navigáljon a **"átjáró neve"-> Resource Health**.

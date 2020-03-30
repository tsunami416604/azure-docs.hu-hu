---
title: 'Virtuális hálózati átjáró törlése: Klasszikus Azure'
description: Virtuális hálózati átjáró törlése a PowerShell használatával a klasszikus telepítési modellben.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7283f5e28edc6f7beaad3a2743aa155f6ea6e14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198649"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Virtuális hálózati átjáró törlése a PowerShell használatával (klasszikus)

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasszikus - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Ez a cikk segít a VPN-átjáró törlésében a klasszikus központi telepítési modellben a PowerShell használatával. A virtuális hálózati átjáró törlése után módosítsa a hálózati konfigurációs fájlt a már nem használt elemek eltávolításához.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>1. lépés: Csatlakozás az Azure-hoz

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Telepítse a legújabb PowerShell-parancsmagokat.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Csatlakozzon Azure-fiókjához.

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

1. Nyissa meg emelt szintű jogosultságokkal rendelkező PowerShell-konzolját. A szolgáltatáskezelésre való váltáshoz használja ezt a parancsot:

   ```powershell
   azure config mode asm
   ```
2. Csatlakozás a fiókhoz. A következő példa segít a kapcsolódásban:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>2. lépés: A hálózati konfigurációs fájl exportálása és megtekintése

Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ezzel a fájllal megtekintheti az aktuális konfigurációs adatokat, és módosíthatja a hálózati konfigurációt is.

Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Nyissa meg a fájlt egy szövegszerkesztővel, és tekintse meg a klasszikus virtuális hálózat nevét. Amikor virtuális hálózatot hoz létre az Azure Portalon, az Azure által használt teljes név nem látható a portálon. Például egy virtuális hálózat, amely úgy tűnik, hogy "ClassicVNet1" az Azure Portalon, lehet, hogy sokkal hosszabb nevet a hálózati konfigurációs fájlban. A név a következőre hasonlíthat: "Group ClassicRG1 ClassicVNet1". A virtuális hálózatok nevei **a "VirtualNetworkSite name ="** néven jelennek meg. Használja a neveket a hálózati konfigurációs fájlban a PowerShell-parancsmagok futtatásakor.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>3. lépés: A virtuális hálózati átjáró törlése

Virtuális hálózati átjáró törlésekor a virtuális hálózathoz az átjárón keresztül létesített összes kapcsolat lelesz választva. Ha a Virtuális hálózathoz p2S-ügyfelek csatlakoznak, azok figyelmeztetés nélkül megszakadnak.

Ez a példa törli a virtuális hálózati átjárót. Ügyeljen arra, hogy a hálózati konfigurációs fájlból származó virtuális hálózat teljes nevét használja.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Ha sikeres, a visszatérés a következőket mutatja:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>4. lépés: A hálózati konfigurációs fájl módosítása

Virtuális hálózati átjáró törlésekor a parancsmag nem módosítja a hálózati konfigurációs fájlt. A már nem használt elemek eltávolításához módosítania kell a fájlt. A következő szakaszok segítenek a letöltött hálózati konfigurációs fájl módosításában.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Helyi hálózati hely hivatkozásai

A helyhivatkozási információk eltávolításához módosítsa a **ConfigurationToLocalNetwork/LocalNetworkSiteRef**konfigurációt. A helyi helyhivatkozás eltávolítása elindítja az Azure-t egy alagút törléséhez. A létrehozott konfigurációtól függően előfordulhat, hogy nincs **listálva LocalNetworkSiteRef.**

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Példa:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>Helyi hálózati helyek

Távolítsa el azokat a helyi webhelyeket, amelyeket már nem használ. A létrehozott konfigurációtól függően előfordulhat, hogy nincs listázva **LocalNetworkSite.**

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

Ebben a példában csak a Site3 webhelyet távolítottuk el.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Ügyfél címkészlete

Ha P2S-kapcsolattal rendelkezik a virtuális hálózattal, akkor **egy VPNClientAddressPool programot fog létesíteni.** Távolítsa el a törölt virtuális hálózati átjárónak megfelelő ügyfélcímkészleteket.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Példa:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Törölje a virtuális hálózatnak megfelelő **GatewaySubnet-et.**

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Példa:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>5. lépés: A hálózati konfigurációs fájl feltöltése

Mentse a módosításokat, és töltse fel a hálózati konfigurációs fájlt az Azure-ba. Győződjön meg arról, hogy a környezethez szükségesen módosítja a fájl elérési útját.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Ha sikeres, a visszatérés a következő höz hasonló tmutat:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```

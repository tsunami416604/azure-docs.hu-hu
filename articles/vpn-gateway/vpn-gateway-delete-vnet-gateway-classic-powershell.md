---
title: "A virtuális hálózati átjáró törlése: PowerShell: klasszikus Azure portál |} Microsoft Docs"
description: "A PowerShell használatával a klasszikus üzembe helyezési modellel virtuális hálózati átjáró törlése."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: b1bc18307227a728e2bc8fd95e30fdc1cbdb8c59
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>A powershellel (klasszikus) virtuális hálózati átjáró törlése
> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasszikus - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Ez a cikk segítséget nyújt a VPN-átjáró a klasszikus üzembe helyezési modellel törlése a PowerShell használatával. Után a virtuális hálózati átjáró törölve lett, távolítsa el az elemeket, amelyek már nem használ a hálózati konfigurációs fájl módosításával.

##<a name="connect"></a>1. lépés: Csatlakozás az Azure-bA

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Telepítse a legújabb PowerShell-parancsmagokat.

Töltse le és telepítse a legújabb verziót az Azure Service Management (SM) PowerShell-parancsmagokat. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

### <a name="2-connect-to-your-azure-account"></a>2. Csatlakozás az Azure-fiókjával. 

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Add-AzureAccount
```

## <a name="export"></a>2. lépés: Exportálása és a hálózati konfiguráció megtekintése

Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ezt a fájlt, mindkét nézetben meg a konfigurációs információkat, valamint a hálózati konfiguráció módosításához használja.

Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Nyissa meg a fájlt egy szövegszerkesztőben, és megtekintése a klasszikus virtuális hálózat nevét. VNet létrehozása az Azure portálon, az Azure által használt teljes név esetén nem látható a portálon. Például egy VNet neve "ClassicVNet1" az Azure-portálon megjelenő lehetséges, hogy mennyi hosszabb nevet adni a hálózati konfigurációs fájlban. A név lehet, hogy alábbihoz hasonló: "Csoport ClassicRG1 ClassicVNet1". Virtuális hálózati nevek felsorolt **"VirtualNetworkSite name ="**. Amikor a PowerShell-parancsmagok futtatását lévő nevek használata a hálózati konfigurációs fájlt.

## <a name="delete"></a>3. lépés: A virtuális hálózati átjáró törlése

A virtuális hálózati átjáró törlése, a virtuális hálózat az átjárón keresztül létesített összes kapcsolat megszakad. Ha a virtuális hálózat csatlakozik P2S-ügyféllel, akkor le lesz választva figyelmeztetés nélkül.

Ebben a példában a virtuális hálózati átjáró törlése. Győződjön meg arról, hogy a virtuális hálózati teljes nevét használja a hálózati konfigurációs fájlból.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Ha sikeres, a visszatérési jeleníti meg:

```
Status : Successful
```

## <a name="modify"></a>4. lépés: Módosítsa a hálózati konfigurációs fájlt.

A virtuális hálózati átjáró törlése, a parancsmag nem módosítja a hálózati konfigurációs fájlt. Módosítania kell a fájlt, és távolítsa el az elemeket, már nem használt. Az alábbi szakaszok segítségével módosítsa a letöltött hálózati konfigurációs fájlt.

### <a name="lnsref"></a>Helyi hálózati telephelyekre való hivatkozások

Helyadatok hivatkozás eltávolításához konfigurációs módosítások elvégzéséhez **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. A helyi webhelyhez hivatkozás eseményindítók törlése egy alagúton Azure eltávolítása. A létrehozott konfigurációjától függően lehet, hogy nincs egy **LocalNetworkSiteRef** szerepel a listában.

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

###<a name="lns"></a>Helyi hálózati helyek

Távolítsa el a helyi telephely, amely már nem használ. A létrehozott konfigurációjától függően lehetséges, hogy nincs egy **LocalNetworkSite** szerepel a listában.

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

Ebben a példában csak Site3 eltávolítása.

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

### <a name="clientaddresss"></a>Ügyfél címkészlete

Ha a virtuális hálózat kellett P2S kapcsolatot, akkor egy **VPNClientAddressPool**. Távolítsa el az ügyfél-címkészleteket, amelyek a virtuális hálózati átjáró törölt felelnek meg.

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

### <a name="gwsub"></a>A GatewaySubnet

Törölje a **GatewaySubnet** , amely megfelel a virtuális hálózat.

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

## <a name="upload"></a>5. lépés: A hálózati konfigurációs fájl feltöltése

A módosítások mentéséhez és a hálózati konfigurációs fájl feltöltése az Azure-bA. Győződjön meg arról, hogy a fájl elérési útját a környezet szükség szerint módosíthatja.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Ha sikeres, a visszatérési valami hasonló ebben a példában látható:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
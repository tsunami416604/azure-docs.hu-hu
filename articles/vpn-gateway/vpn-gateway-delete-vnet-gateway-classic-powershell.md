---
title: 'Virtuális hálózati átjáró törlése: PowerShell: Klasszikus Azure portál |} A Microsoft Docs'
description: PowerShell-lel a klasszikus üzemi modellben virtuális hálózati átjáró törlése.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: ca014e4f5fbc4a5695dbc5fedc85826c71a2a906
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767770"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>PowerShell (klasszikus) használatával virtuális hálózati átjáró törlése

> [!div class="op_single_selector"]
> * [Resource Manager – Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager – PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasszikus – PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Ez a cikk segít a klasszikus üzemi modellben a VPN gateway törlése a PowerShell használatával. Miután a virtuális hálózati átjáró törölve lett, módosítsa a hálózati konfigurációs fájlt, amely már nem használt elemek eltávolítása.

## <a name="connect"></a>1. lépés: Csatlakozás az Azure szolgáltatáshoz

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Telepítse a legújabb PowerShell-parancsmagokat.

Töltse le és telepítse az Azure Service Management (SM) PowerShell-parancsmagjainak legújabb verzióját. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/overview) foglalkozó témakörben talál.

### <a name="2-connect-to-your-azure-account"></a>2. Csatlakozás az Azure-fiókjával. 

Nyissa meg emelt szintű jogosultságokkal a PowerShell konzolt, és csatlakozzon a fiókjához. A következő példa segít a kapcsolódásban:

```powershell
Add-AzureAccount
```

## <a name="export"></a>2. lépés: Exportálás, és megtekintheti a hálózati konfigurációs fájlt

Hozzon létre egy könyvtárat a számítógépén, majd exportálja a hálózati konfigurációs fájlt a könyvtárba. Ez a fájl mindkét nézetben a jelenlegi konfigurációs adatokat és a hálózati konfiguráció módosításához használhatja.

Ebben a példában a hálózati konfigurációs fájlt a C:\AzureNet helyre exportálja.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Nyissa meg a fájlt egy szövegszerkesztőben, és tekintse meg a nevet a klasszikus virtuális hálózat számára. Az Azure Portalon hoz létre egy virtuális hálózathoz, amikor a teljes Azure használó név nem látható a portálon. Virtuális hálózat neve "ClassicVNet1" az Azure Portalon megjelenő például rendelkezhet egy sokkal hosszabb nevet a hálózat konfigurációs fájljában. Neve a következőhöz hasonló: 'Csoport ClassicRG1 ClassicVNet1'. Virtuális hálózat nevei jelennek meg **"VirtualNetworkSite name ="**. A hálózati konfigurációs fájlt a neveket használja, ha futtatja a PowerShell-parancsmagokat.

## <a name="delete"></a>3. lépés: A virtuális hálózati átjáró törlése

Ha töröl egy virtuális hálózati átjárót, a virtuális hálózathoz, az átjárón keresztüli összes kapcsolat megszakadt a kapcsolat. Ha a virtuális hálózathoz kapcsolódó P2S-ügyfelekhez, akkor le lesz választva figyelmeztetés nélkül.

Ebben a példában a virtuális hálózati átjáró törlése. Ellenőrizze, hogy a virtuális hálózat teljes nevét használja a hálózati konfigurációs fájlból.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Ha sikeres, a visszatérési jeleníti meg:

```
Status : Successful
```

## <a name="modify"></a>4. lépés: A hálózati konfigurációs fájl módosítása

Ha töröl egy virtuális hálózati átjárót, a parancsmag nem módosítja a hálózati konfigurációs fájlt. Módosítania kell a fájlt, és távolítsa el az elemeket, amely már nem használ. Az alábbi szakaszok segítségével módosítsa a hálózati konfigurációs fájlt, ahonnan letöltötte.

### <a name="lnsref"></a>Helyi hálózati telephelyekre való hivatkozások

Távolítsa el a hely kapcsolódó információk, győződjön meg arról, konfigurációs módosítások **ConnectionsToLocalNetwork/LocalNetworkSiteRef**. A helyi hely referencia eseményindítók törölni egy alagutat az Azure eltávolítása. Az Ön által létrehozott konfigurációtól függően lehet, hogy nincs egy **LocalNetworkSiteRef** szerepel.

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

### <a name="lns"></a>Helyi hálózati helyek

Távolítsa el minden olyan helyek, amely már nem használ. A létrehozott konfigurációtól függően lehetséges, hogy nem rendelkezik az egy **LocalNetworkSite** szerepel.

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

Ebben a példában csak Site3 eltávolítottuk.

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

Ha a P2S-kapcsolat a virtuális hálózathoz, hogy egy **VPNClientAddressPool**. Távolítsa el az ügyfél-címkészleteket, amelyek megfelelnek a virtuális hálózati átjárót, amelyet törölt.

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

### <a name="gwsub"></a>GatewaySubnet

Törölje a **GatewaySubnet** , amely megfelel a virtuális hálózathoz.

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

Mentse a módosításokat, és töltse fel a hálózati konfigurációs fájlt az Azure-bA. Ellenőrizze, hogy a fájl elérési útját a környezetben szükség szerint módosíthatja.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Ha ez sikeres, a visszatérési valami hasonló ebben a példában látható:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```

---
title: fájl belefoglalása
description: fájl belefoglalása
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178853"
---
> [!NOTE]
> Ezek a példák nem vonatkoznak az S2S/ExpressRoute egymás mellett létező konfigurációkra.
> Az átjárók egymás mellett létező konfigurációban való munkáról a [Egymás mellett létező kapcsolatok konfigurálása című](../articles/expressroute/expressroute-howto-coexist-classic.md#gw) témakörben talál további információt.

## <a name="add-a-gateway"></a>Átjáró hozzáadása

Ha átjárót ad hozzá egy virtuális hálózathoz a klasszikus erőforrásmodell használatával, az átjáró létrehozása előtt közvetlenül módosítja a hálózati konfigurációs fájlt. Az alábbi példákban lévő értékeknek meg kell jelenniük a fájlban az átjáró létrehozásához. Ha a virtuális hálózathoz korábban átjáró volt társítva, ezen értékek némelyike már jelen lesz. Módosítsa a fájlt az alábbi értékeknek megfelelően.

### <a name="download-the-network-configuration-file"></a>A hálózati konfigurációs fájl letöltése

1. Töltse le a hálózati konfigurációs fájlt a [hálózati konfigurációs fájl](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) cikkében leírt lépésekkel. Nyissa meg a fájlt egy szövegszerkesztő vel.
2. Helyi hálózati hely hozzáadása a fájlhoz. Bármilyen érvényes címelőtagot használhat. A VPN-átjáróhoz bármilyen érvényes IP-címet hozzáadhat. Az ebben a szakaszban szereplő címértékek nem expressroute-műveletekhez használatosak, de szükségesek a fájlérvényesítéshez. A példában a "branch1" a hely neve. Használhat másik nevet, de ne felejtse el ugyanazt az értéket használni a fájl Átjáró szakaszában.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Keresse meg a VirtualNetworkSites és módosítsa a mezőket.

   * Ellenőrizze, hogy létezik-e átjáróalhálózat a virtuális hálózathoz. Ha nem, akkor adjunk hozzá egy ebben az időben. A névnek "GatewaySubnet" névnek kell lennie.
   * Ellenőrizze, hogy a fájl átjáró szakasza létezik-e. Ha nem, add hozzá. Erre azért van szükség, hogy a virtuális hálózatot a helyi hálózati helyhez társítsa (amely azt a hálózatot jelöli, amelyhez csatlakozik).
   * Ellenőrizze, hogy a kapcsolat típusa = Dedikált. Ez az ExpressRoute-kapcsolatok esetén szükséges.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Mentse a fájlt, és töltse fel az Azure-ba.

### <a name="create-the-gateway"></a>Az átjáró létrehozása

Az alábbi paranccsal hozzon létre átjárót. Helyettesítsen bármilyen értéket a sajátjának.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Az átjáró létrehozásának ellenőrzése

Az alábbi paranccsal ellenőrizze, hogy az átjáró létrejött-e. Ez a parancs az átjáróazonosítót is lekéri, amelyre más műveletekhez is szüksége van.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Átjáró átméretezése

Számos [átjáró-ska létezik.](../articles/expressroute/expressroute-about-virtual-network-gateways.md) A következő paranccsal bármikor módosíthatja az átjáró termékváltozatát.

> [!IMPORTANT]
> Ez a parancs nem működik az UltraPerformance átjárónál. Az átjáró UltraPerformance-átjáróvá való módosításához először távolítsa el a meglévő ExpressRoute-átjárót, majd hozzon létre egy új UltraPerformance-átjárót. Az átjáró UltraPerformance-átjáróról való visszaminősítéséhez először távolítsa el az UltraPerformance átjárót, majd hozzon létre egy új átjárót.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása

Átjáró eltávolítása az alábbi paranccsal

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
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
ms.openlocfilehash: 2457ef2843b0d16359b7e47fc54c58e2ef5e6034
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53429974"
---
> [!NOTE]
> Ezek a példák nem vonatkoznak a S2S/ExpressRoute párhuzamos telephelyközi konfigurációkat.
> Átjárók coexist konfigurációban használatáról további információkért lásd: [egyidejű kapcsolatok konfigurálása.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Átjáró hozzáadása

Amikor hozzáad egy átjárót egy virtuális hálózathoz a klasszikus erőforrások modellel, közvetlenül a gateway létrehozása előtt módosítsa a hálózati konfigurációs fájlt. Az értékeket a következő példa létrehoz egy átjárót a fájlban jelen kell lennie. A virtuális hálózathoz társított átjáró rendelkezett, alábbi értékek némelyikét a rendszer már elérhetőnek lennie. A fájl az alábbi értékeknek megfelelően módosítsa.

### <a name="download-the-network-configuration-file"></a>A hálózati konfigurációs fájl letöltése

1. Töltse le a hálózati konfigurációs fájlt a lépések használatával [hálózati konfigurációs fájlt](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) cikk. Nyissa meg a fájlt egy szövegszerkesztőben.
2. Adja hozzá a fájlt egy helyi hálózati telephelyre. Használhat bármilyen érvényes cím előtagja. Bármilyen érvényes IP-címet a VPN-átjáró is hozzáadhat. Ebben a szakaszban szereplő címet értékek ExpressRoute műveletek nem használhatók, de a fájl érvényesítéséhez szükséges. A példában a "fiók1" a webhely nevét. Előfordulhat, hogy a másik nevet, de ügyeljen arra, hogy a fájl az átjáró szakaszban ugyanazt az értéket használja.

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
3. Keresse meg a VirtualNetworkSites, és módosítsa a mezőket.

  * Gondoskodjon arról, hogy az átjáró-alhálózatot a virtuális hálózat. Ha nem jelenik meg, hozzáadhat egyet most. A neve "GatewaySubnet" kell lennie.
  * Ellenőrizze, hogy az átjáró a szakasz a fájl létezik-e. Ha nem, adja hozzá. Ez azért szükséges, a virtuális hálózat társítása a helyi hálózati telephely (amely jelöli, amely kapcsolódik a hálózathoz).
  * Győződjön meg arról, hogy a kapcsolat típusa = dedikált. Ez azért szükséges, az ExpressRoute-kapcsolatok esetében.

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
4. Mentse a fájlt, és töltse fel az Azure-bA.

### <a name="create-the-gateway"></a>Az átjáró létrehozása

Az átjáró létrehozásához használja az alábbi parancsot. Helyettesítse be minden olyan értékeket saját értékekre.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Az átjáró létrehozásának ellenőrzéséhez

Az alábbi parancs segítségével győződjön meg arról, hogy az átjáró létrehozása. A parancs segítségével lekérdezhető az átjáró-azonosító szükséges egyéb műveleteket is.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Az átjáró átméretezése

A következő számú [átjáró-termékváltozatok](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Az alábbi parancs segítségével bármikor módosíthatja az átjáró-Termékváltozat.

> [!IMPORTANT]
> Ez a parancs az UltraPerformance átjáró nem működik. Ha módosítani szeretné az átjárót az UltraPerformance átjáró, először távolítsa el a meglévő ExpressRoute-átjárót, és hozzon létre egy új UltraPerformance átjáró. Alacsonyabb szolgáltatásszintre váltásához az átjáró az UltraPerformance átjáró, először távolítsa el az UltraPerformance átjáró, és hozzon létre egy új átjárót.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Átjáró eltávolítása

Használja az alábbi parancsot az átjáró eltávolítása

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
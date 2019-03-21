---
title: 'Virtuális hálózat kapcsolódhat több helyhez, a VPN Gateway és a PowerShell használatával: Klasszikus |} A Microsoft Docs'
description: Több helyszíni webhely csatlakoztatása egy klasszikus virtuális hálózat VPN-átjáró használatával.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: yushwang
ms.openlocfilehash: 77f8b7094c96e507eef1d360a26240627bc0e350
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57994025"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Helyek közötti kapcsolat hozzáadása egy virtuális hálózatot egy meglévő VPN gateway-kapcsolattal (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
>

Ez a cikk végigvezeti Site-to-Site (S2S) kapcsolat hozzáadása VPN-átjáró, amely rendelkezik egy meglévő kapcsolatot a PowerShell használatával. Ez a kapcsolattípus egy "többhelyes" konfigurációs van néven. A jelen cikkben ismertetett lépések a klasszikus üzemi modellben (más néven szolgáltatásfelügyelet) használatával létrehozott virtuális hálózatokat a alkalmazni. Ezeket a lépéseket az ExpressRoute és Site-to-Site egyidejű kapcsolat konfigurációk nem vonatkoznak.

### <a name="deployment-models-and-methods"></a>Üzemi modellek és módszerek

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ezt a táblázatot frissítjük, ahogy új cikkek és további eszközök válnak elérhetővé ehhez a konfigurációhoz. Új cikk érhető el, ha arra mutató közvetlen hivatkozás, ebből a táblázatból.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Csatlakozás

Több helyszíni hely kapcsolódhat egyetlen virtuális hálózaton. Ez akkor különösen vonzó, hibrid felhőalapú megoldások kiépítéséhez. Többhelyes kapcsolat az Azure virtuális hálózati átjáró létrehozása a hasonló más helyek közötti kapcsolatok létrehozására. Sőt, használhat egy meglévő Azure VPN gateway mindaddig, amíg az átjáró nem dinamikus (útvonalalapú).

Ha már rendelkezik a virtuális hálózathoz csatlakozó statikus átjáró, módosíthatja az átjáró típusa dinamikus anélkül, hogy építse újra a virtuális hálózathoz való megfelelés érdekében többhelyes. Útválasztási típusának módosítása előtt győződjön meg arról, hogy a helyszíni VPN-átjáró támogatja-e az útvonalalapú VPN-konfigurációk.

![többhelyes diagram](./media/vpn-gateway-multi-site/multisite.png "többhelyes")

## <a name="points-to-consider"></a>Megfontolandó szempontok

**Akkor hajtsa végre a módosításokat a virtuális hálózaton a portál használatával.** Módosítania kell a hálózati konfigurációs fájlt a portál használata helyett. Ha módosítja a portálon, a többhelyes referencia virtuális hálózatra vonatkozó beállításainak fog felülírja.

Érdemes úgy gondolja, hogy tisztában a hálózati konfigurációs fájlt használ a Ön teljesítette a többhelyes eljárás ideje. Azonban ha több felhasználó dolgozik a hálózati konfigurációt, szüksége annak biztosításához, hogy mindenki ismeri az ezt a korlátozást. Ez nem jelenti azt, hogy egyáltalán nem használható a portálon. Minden más, kivéve a konfigurációs módosítások az adott virtuális hálózaton használható.

## <a name="before-you-begin"></a>Előkészületek

Mielőtt elkezdené a konfigurációt, győződjön meg arról, hogy rendelkezik az alábbiakkal:

* Az egyes kompatibilis VPN-hardverre a helyszíni helyre. Ellenőrizze [VPN-eszközeit virtuális hálózati kapcsolat](vpn-gateway-about-vpn-devices.md) ellenőrizze, hogy az eszközt, hogy a használni kívánt-e valamit, ismert, hogy kompatibilis legyen.
* Egy kifelé irányuló nyilvános IPv4 IP-cím minden egyes VPN-eszköz számára. Az IP-cím nem található a NAT mögötti. Ez a követelmény.
* Az Azure PowerShell-parancsmagok legújabb verzióit kell telepítenie. Ellenőrizze, hogy a Service Management (SM) verzióját mellett a Resource Managerre vonatkozó verziójának telepítését. Lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) további információt.
* Valaki, aki értelemben haladó szinten konfigurálása a VPN-hardverrel álljanak. Rendelkezik egy szilárd ismeretekkel a VPN-eszköz konfigurálásához, vagy egy olyan személlyel, aki használni kell.
* (Ha Ön még nem hozott létre egyet) a virtuális hálózatához használni kívánt IP-címtartományok.
* Az IP-címtartományok egyes meg fog csatlakozni a helyi hálózati helyek. Győződjön meg arról, hogy az IP-címtartományát, hogy ne legyenek átfedésben csatlakozni szeretne a helyi hálózati helyek mindegyike kell. A portálon vagy a REST API ellenkező esetben elutasítja az a konfiguráció feltöltése folyamatban.<br>Például ha két helyi hálózati helyek, hogy egyaránt tartalmazza az IP-cím tartományt 10.2.3.0/24, és rendelkezik a cél címe 10.2.3.3 csomagot, Azure nem tudja, melyik helyen is szeretne küldeni a csomag, mert a címtartomány átfedésben. Útválasztási problémák elkerülése érdekében az Azure nem engedélyezi, hogy egy átfedő tartományt tartalmazó konfigurációs fájl feltöltése.

## <a name="1-create-a-site-to-site-vpn"></a>1. Helyek közötti VPN létrehozása
Ha már rendelkezik egy dinamikus útválasztású átjárót, a Site-to-Site VPN nagyszerű! Folytassa [a virtuális hálózat konfigurációs beállítások exportálása](#export). Ha nem, tegye a következőket:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Ha már rendelkezik egy helyek közötti virtuális hálózat, de rendelkezik statikus (házirendalapú) útválasztási átjáró:
1. Módosítsa az átjáró típusa dinamikus útválasztást. Többhelyes VPN dinamikus (más néven útválasztó-alapú) útválasztó átjáró szükséges. Ha módosítani szeretné az átjáró típusa, először törölje a meglévő átjárót, majd hozzon létre egy új szüksége.
2. Az új átjáró konfigurálása és a VPN-alagút létrehozása. Útmutatásért útmutatásért lásd: [adja meg a Termékváltozat és a VPN típusát](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Győződjön meg arról, adja meg a "Dinamikus", az Útválasztás típusa.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Ha helyek közötti virtuális hálózat nem rendelkezik:
1. Az alábbi utasításokat követve Site-to-Site virtuális hálózat létrehozása: [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolaton keresztül](vpn-gateway-site-to-site-create.md).  
2. Ezek az utasítások használatával dinamikus útválasztású átjáró konfigurálása: [VPN-átjáró konfigurálása](vpn-gateway-configure-vpn-gateway-mp.md). Ügyeljen arra, hogy válasszon **dinamikus útválasztású** az átjáró típusához.

## <a name="export"></a>2. Exportálhatja a hálózati konfigurációs fájlt
Exportálhatja az Azure hálózati konfigurációs fájlt a következő parancs futtatásával. Módosíthatja egy másik helyre, szükség esetén exportálhatja a fájl helyét.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Nyissa meg a hálózati konfigurációs fájlt
Nyissa meg a letöltött hálózati konfigurációs fájlt az előző lépésben. Minden olyan xml-szerkesztőt, amelynek használata. A fájl az alábbihoz hasonlóan kell kinéznie:

        <NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <LocalNetworkSites>
              <LocalNetworkSite name="Site1">
                <AddressSpace>
                  <AddressPrefix>10.0.0.0/16</AddressPrefix>
                  <AddressPrefix>10.1.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.2.3.4</VPNGatewayAddress>
              </LocalNetworkSite>
              <LocalNetworkSite name="Site2">
                <AddressSpace>
                  <AddressPrefix>10.2.0.0/16</AddressPrefix>
                  <AddressPrefix>10.3.0.0/16</AddressPrefix>
                </AddressSpace>
                <VPNGatewayAddress>131.4.5.6</VPNGatewayAddress>
              </LocalNetworkSite>
            </LocalNetworkSites>
            <VirtualNetworkSites>
              <VirtualNetworkSite name="VNet1" AffinityGroup="USWest">
                <AddressSpace>
                  <AddressPrefix>10.20.0.0/16</AddressPrefix>
                  <AddressPrefix>10.21.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="FE">
                    <AddressPrefix>10.20.0.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="BE">
                    <AddressPrefix>10.20.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="GatewaySubnet">
                    <AddressPrefix>10.20.2.0/29</AddressPrefix>
                  </Subnet>
                </Subnets>
                <Gateway>
                  <ConnectionsToLocalNetwork>
                    <LocalNetworkSiteRef name="Site1">
                      <Connection type="IPsec" />
                    </LocalNetworkSiteRef>
                  </ConnectionsToLocalNetwork>
                </Gateway>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

## <a name="4-add-multiple-site-references"></a>4. Több telephelyre hozzáadása
Hozzáadásakor, vagy távolítsa el a hely kapcsolódó információk, meg fogjuk konfigurációs módosítások ConnectionsToLocalNetwork/LocalNetworkSiteRef. Egy új helyi hely hivatkozást hozhat létre egy új alagutat az Azure eseményindítók hozzáadása. Az alábbi példában a hálózati konfigurációs van egyetlen helyen kapcsolat. Miután végzett a módosításokkal, mentse a fájlt.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

Adjon hozzá további helyrendszer-hivatkozások (a többhelyes konfiguráció létrehozása), "LocalNetworkSiteRef" további sorokat, egyszerűen adja meg az alábbi példában látható módon:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. A hálózati konfigurációs fájl importálása
Importálja a hálózati konfigurációs fájlt. Amikor importálja ezt a fájlt a módosításokkal, az új alagutak fog bővülni. Az alagutak a korábban létrehozott dinamikus átjáró fogja használni. Importálja a fájlt a PowerShell használatával is.

## <a name="6-download-keys"></a>6. Töltse le a kulcsok
Az új alagutak hozzáadása után az IPsec/IKE előmegosztott kulcsok minden egyes bújtatás használhatják a "Get-AzureVNetGatewayKey" PowerShell-parancsmagot.

Példa:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Ha szeretné, akkor is használhatja a *első virtuális hálózati átjáró a megosztott kulcs* REST API-t az előmegosztott kulcsok beolvasása.

## <a name="7-verify-your-connections"></a>7. Kapcsolatok ellenőrzése
Többhelyes kapcsolat bújtatási állapotának ellenőrzéséhez. A kulcsok minden egyes bújtatás a letöltés után érdemes kapcsolatok ellenőrzése. Ezen "Get-AzureVnetConnection" virtuálishálózat-alagút, listáját az alábbi példában látható módon. Vnet1 felől a virtuális hálózat neve.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Visszatérési. példa:

```
    ConnectivityState         : Connected
    EgressBytesTransferred    : 661530
    IngressBytesTransferred   : 519207
    LastConnectionEstablished : 5/2/2014 2:51:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site1' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site1
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7f68a8e6-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded

    ConnectivityState         : Connected
    EgressBytesTransferred    : 789398
    IngressBytesTransferred   : 143908
    LastConnectionEstablished : 5/2/2014 3:20:40 PM
    LastEventID               : 23401
    LastEventMessage          : The connectivity state for the local network site 'Site2' changed from Not Connected to Connected.
    LastEventTimeStamp        : 5/2/2014 2:51:40 PM
    LocalNetworkSiteName      : Site2
    OperationDescription      : Get-AzureVNetConnection
    OperationId               : 7893b329-51e9-9db4-88c2-16b8067fed7f
    OperationStatus           : Succeeded
```

## <a name="next-steps"></a>További lépések

VPN-átjárókkal kapcsolatos további információkért lásd: [információk a VPN-Átjárókról](vpn-gateway-about-vpngateways.md).

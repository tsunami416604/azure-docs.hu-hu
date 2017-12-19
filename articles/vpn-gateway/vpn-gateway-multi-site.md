---
title: "A virtuális hálózati csatlakozás több hely VPN Gateway és a PowerShell használatával: klasszikus |} Microsoft Docs"
description: "Ez a cikk végigvezeti több helyi helyszíni hely csatlakozik egy virtuális hálózat VPN-átjáró a klasszikus üzembe helyezési modell."
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: azure-service-management
ms.assetid: b043df6e-f1e8-4a4d-8467-c06079e2c093
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/20/2017
ms.author: yushwang
ms.openlocfilehash: 434f84dc6244eddce9b172a617722b218360ffc2
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Pont-pont kapcsolat hozzáadása a virtuális hálózat meglévő VPN-átjáró kapcsolattal (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
>

Ez a cikk bemutatja, hogyan, amely rendelkezik egy létező kapcsolat VPN-átjáró webhelyek (közötti S2S) kapcsolatok hozzáadása a PowerShell használatával. Ilyen típusú kapcsolat gyakran egy "több" Helykonfiguráció nevezik. A cikkben leírt lépéseket a klasszikus üzembe helyezési modellel (más néven szolgáltatásfelügyelet) használatával létrehozott virtuális hálózatokat vonatkozik. Ezeket a lépéseket nem vonatkoznak a ExpressRoute/pont-pont vizsgálatát a kísérő kapcsolati beállításokat.

### <a name="deployment-models-and-methods"></a>Üzemi modellek és módszerek

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ebben a táblázatban, új cikket frissítjük, és további eszközök ehhez a konfigurációhoz elérhetővé válnak. Egy cikk nem érhető el, hogy csatolása közvetlenül ebből a táblázatból.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Csatlakozás

Több helyszíni hely csatlakozhatnak egy virtuális hálózaton. Ez egy különösen vonzó hibrid felhőalapú megoldás felépítéséhez. A többhelyes kapcsolat az Azure virtuális hálózati átjáró létrehozása hasonlít más webhelyek kapcsolatok létrehozására. Valójában használhatja egy meglévő Azure VPN gateway mindaddig, amíg az átjáró dinamikus (útválasztó-alapú).

Ha már rendelkezik a virtuális hálózathoz csatlakozó statikus átjárók, módosíthatja az átjáró típusa dinamikus anélkül, hogy a virtuális hálózati ahhoz, hogy megfeleljen a többhelyes. Útválasztási típusának módosítása előtt győződjön meg arról, hogy a helyszíni VPN-átjáró támogatja-e az útvonalalapú VPN-konfigurációk.

![többhelyes diagram](./media/vpn-gateway-multi-site/multisite.png "többhelyes")

## <a name="points-to-consider"></a>Megfontolandó szempontok

**Ön nem fog tudni a portál használatával módosíthatja a virtuális hálózat.** Módosítania kell a hálózati konfigurációs fájl helyett a portál használatával. Ha módosítja a portálon, a többhelyes hivatkozás beállításai a virtuális hálózat fog felülírja.

Úgy véli kényelmes befejezte a többhelyes eljárás ideje szerint a hálózati konfigurációs fájl használatával. Azonban ha több személy dolgozik a hálózati konfiguráció van, szüksége lesz győződjön meg arról, hogy mindenki ismer ezt a korlátozást. Ez nem jelenti azt, hogy egyáltalán nem használható a portálon. Minden más, kivéve a konfigurációs módosítások az adott virtuális hálózati használhatja.

## <a name="before-you-begin"></a>Előkészületek

A konfigurálás elkezdése előtt győződjön meg arról, hogy rendelkezik-e a következő:

* Az egyes hardver VPN helyszíni hely. Ellenőrizze [kapcsolatos VPN-eszközök a virtuális hálózati kapcsolatra](vpn-gateway-about-vpn-devices.md) az eszközt, hogy a használni kívánt-e, amelyet ismert való kompatibilitás ellenőrzése.
* Külsőleg irányuló nyilvános IPv4-alapú IP-címet minden egyes VPN-eszközön. Az IP-cím nem található a NAT mögött Ez a követelmény.
* Az Azure PowerShell-parancsmagok legújabb verzióit kell telepítenie. Ellenőrizze, hogy telepíteni a Service Management (SM) mellett a Resource Manager verzióra. Lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) további információt.
* Személynek értelemben haladó szinten konfigurálása a VPN-hardver álljanak. Bemutatja, hogyan konfigurálhatja a VPN-eszköz, vagy tartalmazó valakinek, aki erős megértése kell.
* Az IP-címtartományok, amelyet a virtuális hálózat használata (Ha még nem már létre).
* Az IP-címtartományt, az egyes a helyi hálózati helyeket, akkor fog csatlakozni. Győződjön meg arról, hogy az IP-címtartományát a helyi hálózati helyek nincsenek átfedésben való csatlakozáshoz használni kívánt egyes lesz szüksége. Ellenkező esetben a portál vagy a REST API-t fogják utasítani a feltöltendő konfigurációs.<br>Például ha két helyi hálózati helyek, hogy mindkettő rendelkezik, az IP-cím tartomány 10.2.3.0/24, és rendelkezik a cél címe 10.2.3.3 számára, Azure nem tudja, melyik helyhez szeretne küldeni a csomag, mert a címtartomány átfedésben. Útválasztási problémák elkerülése érdekében Azure nem engedélyezi, hogy átfedő tartományokat egy konfigurációs fájl feltöltése.

## <a name="1-create-a-site-to-site-vpn"></a>1. Helyek közötti VPN létrehozása
Ha már rendelkezik egy dinamikus útválasztási átjáró, pont-pont VPN nagy! Lépne [a virtuális hálózati konfigurációs beállítások exportálása](#export). Ha nem, tegye a következőket:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Ha a pont-pont virtuális hálózat már rendelkezik, de statikus (házirend-alapú) útválasztó átjáró rendelkezik:
1. Módosítsa az átjáró típusa dinamikus útválasztást. Többhelyes VPN dinamikus (más néven útválasztó-alapú) útválasztó átjáró szükséges. Ha módosítani szeretné az átjáró típusa, szüksége, először törölje a meglévő átjárót, majd hozzon létre egy újat.
2. Az új átjáró konfigurálása, és a VPN-alagút létrehozása. Az útmutatáshoz útmutatásért lásd: [adja meg az SKU- és VPN-típus](vpn-gateway-howto-site-to-site-classic-portal.md#sku). Ellenőrizze, hogy megadja a útválasztási típusú, mint "Dinamikus".

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Ha még nem rendelkezik pont-pont virtuális hálózat:
1. A pont-pont virtuális hálózat létrehozása ezeket az utasításokat: [hozzon létre egy virtuális hálózati helyek a VPN-kapcsolat](vpn-gateway-site-to-site-create.md).  
2. A jelen útmutatást dinamikus útválasztó átjáró konfigurálása: [VPN-átjáró konfigurálása](vpn-gateway-configure-vpn-gateway-mp.md). Ügyeljen arra, hogy válasszon **dinamikus útválasztási** az átjáró típusának.

## <a name="export"></a>2. A hálózati konfigurációs fájl exportálása
Az Azure-hálózat konfigurációs fájl exportálása a következő parancs futtatásával. Módosíthatja a fájlt egy másik helyre, szükség esetén helyét.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Nyissa meg a hálózati konfigurációs fájlját.
Nyissa meg a hálózati konfigurációs fájl letöltött az előző lépésben. Bármilyen XML-szerkesztő, amely tetszés használja. A fájl az alábbihoz hasonlóan kell kinéznie:

        <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

## <a name="4-add-multiple-site-references"></a>4. Több mutató hivatkozások hozzáadása
Amikor hozzá, vagy távolítsa el a webhely referencia jellegű információt, konfigurációs módosításokat végezni a ConnectionsToLocalNetwork/LocalNetworkSiteRef lesz. Hozzáadása egy új helyi hivatkozás eseményindítók Azure egy új alagút létrehozásához. Az alábbi példában a hálózati konfigurációt a egyhelyes kapcsolat van. Mentse a fájlt, a változtatások befejezése után.

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

(A többhelyes konfiguráció létrehozása) további telephelyekre való hivatkozások hozzáadásához egyszerűen adja hozzá a további "LocalNetworkSiteRef" sorokat, az alábbi példában látható módon:

```
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. A hálózati konfigurációs fájl importálása
Importálja a hálózati konfigurációs fájlt. Amikor importálja ezt a fájlt a változtatásokat, az új alagutak lesz hozzáadva. Az alagutat a korábban létrehozott dinamikus átjáró fogja használni. PowerShell segítségével importálja a fájlt.

## <a name="6-download-keys"></a>6. Töltse le a kulcsok
Az új alagutak hozzáadása után a "Get-AzureVNetGatewayKey" PowerShell-parancsmag segítségével lekérni a IPsec/IKE minden alagút előmegosztott kulcsokat.

Példa:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Tetszés szerint használhatja a *első virtuális hálózati átjáró megosztott kulcsos* REST API-t az előmegosztott kulcs beszerzése.

## <a name="7-verify-your-connections"></a>7. Kapcsolatok ellenőrzése
A többhelyes alagút állapotának. A kulcsok minden egyes alagúthoz a letöltés után érdemes kapcsolatok ellenőrzése. Az alábbi példában látható módon használja a "Get-AzureVnetConnection" virtuális hálózati alagutak listájának. VNet1 a virtuális hálózat nevét.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

A példa visszatérési:

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

## <a name="next-steps"></a>Következő lépések

VPN-átjárók kapcsolatos további információkért lásd: [VPN-átjárók](vpn-gateway-about-vpngateways.md).

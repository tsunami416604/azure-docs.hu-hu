---
title: 'Virtuális hálózat csatlakoztatása több webhelyhez VPN-átjáró használatával: Klasszikus'
description: Több helyi helyszíni hely csatlakoztatása egy klasszikus virtuális hálózathoz VPN-átjáró használatával.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/11/2020
ms.author: yushwang
ms.openlocfilehash: a95cd6ea85a16b0e0bf5f67f5dfc20d57f11463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198095"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection-classic"></a>Helyek közötti kapcsolat hozzáadása meglévő VPN-átjárókapcsolattal rendelkező virtuális hálózathoz (klasszikus)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

> [!div class="op_single_selector"]
> * [Azure-portál](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasszikus)](vpn-gateway-multi-site.md)
>
>

Ez a cikk bemutatja, hogy a PowerShell használatával webhelyről webhelyre (S2S) kapcsolatokat adhat hozzá egy meglévő kapcsolattal rendelkező VPN-átjáróhoz. Ezt a típusú kapcsolatot gyakran nevezik "többhelyes" konfigurációnak. A cikkben ismertetett lépések a klasszikus üzembe helyezési modell (más néven Szolgáltatáskezelés) használatával létrehozott virtuális hálózatokra vonatkoznak. Ezek a lépések nem vonatkoznak az ExpressRoute/A helyek közötti kapcsolati konfigurációkra.

### <a name="deployment-models-and-methods"></a>Üzemi modellek és módszerek

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Frissítjük ezt a táblázatot, amint új cikkek és további eszközök válnak elérhetővé ehhez a konfigurációhoz. Amikor egy cikk elérhető, közvetlenül erre a táblára hivatkozunk.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="about-connecting"></a>Csatlakozás –

Több helyszíni hely is csatlakoztatható egyetlen virtuális hálózathoz. Ez különösen vonzó hibrid felhőalapú megoldások készítéséhez. Többhelyes kapcsolat létrehozása az Azure virtuális hálózati átjáróhoz hasonló más helyek közötti kapcsolatok létrehozásához. Valójában használhatja a meglévő Azure VPN-átjáró, mindaddig, amíg az átjáró dinamikus (útvonal-alapú).

Ha már rendelkezik statikus átjáróval a virtuális hálózathoz, módosíthatja az átjáró típusát dinamikusra anélkül, hogy újra kellene építenie a virtuális hálózatot a többhelyhez való hozzáférés érdekében. Az útválasztási típus módosítása előtt győződjön meg arról, hogy a helyszíni VPN-átjáró támogatja az útvonalalapú VPN-konfigurációkat.

![többhelyes diagram](./media/vpn-gateway-multi-site/multisite.png "többtelephelyes")

## <a name="points-to-consider"></a>Megfontolandó szempontok

**A portál nem fogja tudni használni a virtuális hálózat módosításait.** A portál használata helyett módosítania kell a hálózati konfigurációs fájlt. Ha módosításokat hajt végre a portálon, azok felülírják a virtuális hálózat többwebhelyre vonatkozó referenciabeállításait.

A többhelyes eljárás befejezésekor nyugodtan használja a hálózati konfigurációs fájlt. Ha azonban többen dolgoznak a hálózati konfiguráción, gondoskodnia kell arról, hogy mindenki tudjon erről a korlátozásról. Ez nem jelenti azt, hogy egyáltalán nem használhatja a portált. Használhatja minden mást, kivéve, hogy a konfigurációs módosításokat az adott virtuális hálózat.

## <a name="before-you-begin"></a>Előkészületek

A konfiguráció megkezdése előtt ellenőrizze, hogy rendelkezik-e a következőkkel:

* Kompatibilis VPN-hardver ek minden helyszíni helyhez. [A virtuális hálózati kapcsolat VPN-eszközei című kérdésben](vpn-gateway-about-vpn-devices.md) ellenőrizze, hogy a használni kívánt eszköz kompatibilis-e.
* Külsőleg néző nyilvános IPv4-IP-cím minden EGYES VPN-eszközhöz. Az IP-cím nem található nat mögött. Ez a követelmény.
* Valaki, aki jártas a VPN-hardver konfigurálása. Meg kell, hogy egy erős megértése, hogyan kell beállítani a VPN-eszköz, vagy dolgozni valakivel, aki nem.
* A virtuális hálózathoz használni kívánt IP-címtartományok (ha még nem hozott létre egyet).
* Az IP-címtartományok minden olyan helyi hálózati helyhez, amelyhez csatlakozni fog. Meg kell győződnie arról, hogy a csatlakoztatni kívánt helyi hálózati helyek IP-címtartományai nem fedik át egymást. Ellenkező esetben a portál vagy a REST API elutasítja a feltöltött konfigurációt.<br>Ha például két helyi hálózati hellyel rendelkezik, amelyek mindegyike tartalmazza a 10.2.3.0/24 IP-címtartományt, és rendelkezik egy 10.2.3.3 célcímmel rendelkező csomaggal, az Azure nem tudja, melyik helyre szeretné elküldeni a csomagot, mert a címtartományok átfedésben vannak. Az útválasztási problémák megelőzése érdekében az Azure nem teszi lehetővé az egymást átfedő tartományokkal rendelkező konfigurációs fájl feltöltését.

### <a name="working-with-azure-powershell"></a>Az Azure PowerShell együttműködése

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="1-create-a-site-to-site-vpn"></a>1. Helyek közötti VPN létrehozása
Ha már rendelkezik egy hely-to-site VPN dinamikus útválasztási átjáró, nagy! A virtuális [hálózat konfigurációs beállításainak exportálása](#export)című segédprogramot továbbviheti. Ha nem, tegye a következőket:

### <a name="if-you-already-have-a-site-to-site-virtual-network-but-it-has-a-static-policy-based-routing-gateway"></a>Ha már rendelkezik helyek közötti virtuális hálózattal, de statikus (házirend-alapú) útválasztási átjáróval rendelkezik:
1. Módosítsa az átjáró típusát dinamikus útválasztásra. A többhelyes VPN-hez dinamikus (más néven útvonalalapú) útválasztási átjáró szükséges. Az átjáró típusának módosításához először törölnie kell a meglévő átjárót, majd létre kell hoznia egy újat.
2. Konfigurálja az új átjárót, és hozza létre a VPN-alagutat. További utasítások: A [Termékváltozat és a VPN típusának megadása](vpn-gateway-howto-site-to-site-classic-portal.md#sku)című témakörben talál. Győződjön meg arról, hogy a Műveletterv típust "Dinamikus" néven adja meg.

### <a name="if-you-dont-have-a-site-to-site-virtual-network"></a>Ha nem rendelkezik helyek közötti virtuális hálózattal:
1. Hozza létre a helyek közötti virtuális hálózatot az alábbi utasítások használatával: [Virtuális hálózat létrehozása helyek közötti VPN-kapcsolattal.](vpn-gateway-site-to-site-create.md)  
2. Dinamikus útválasztási átjáró konfigurálása a következő utasítások kal: [VPN-átjáró konfigurálása](vpn-gateway-configure-vpn-gateway-mp.md). Ügyeljen arra, hogy **dinamikus útválasztást** válasszon az átjáró típusához.

## <a name="2-export-the-network-configuration-file"></a><a name="export"></a>2. A hálózati konfigurációs fájl exportálása

Nyissa meg emelt szintű jogosultságokkal rendelkező PowerShell-konzolját. A szolgáltatáskezelésre való váltáshoz használja ezt a parancsot:

```powershell
azure config mode asm
```

Csatlakozás a fiókhoz. A következő példa segít a kapcsolódásban:

```powershell
Add-AzureAccount
```

Exportálja az Azure hálózati konfigurációs fájlját a következő parancs futtatásával. Szükség esetén módosíthatja a fájl helyét, hogy egy másik helyre exportálja.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

## <a name="3-open-the-network-configuration-file"></a>3. Nyissa meg a hálózati konfigurációs fájlt
Nyissa meg az utolsó lépésben letöltött hálózati konfigurációs fájlt. Használjon bármilyen XML-szerkesztőt, amit szeret. A fájlnak a következőhöz hasonlóan kell kinéznie:

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

## <a name="4-add-multiple-site-references"></a>4. Több webhelyhivatkozás hozzáadása
A helyhivatkozási adatok hozzáadásakor vagy eltávolításakor a ConnectionsToLocalNetwork/LocalNetworkSiteRef konfigurációs módosításokat hajt végre. Egy új helyi hely hivatkozás iaktiválja az Azure-t egy új alagút létrehozásához. Az alábbi példában a hálózati konfiguráció egyhelyes kapcsolatra szól. Mentse a fájlt, miután befejezte a módosításokat.

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

További helyhivatkozások hozzáadásához (többhelyes konfiguráció létrehozásához) egyszerűen adjon hozzá további "LocalNetworkSiteRef" sorokat, amint az az alábbi példában látható:

```xml
  <Gateway>
    <ConnectionsToLocalNetwork>
      <LocalNetworkSiteRef name="Site1"><Connection type="IPsec" /></LocalNetworkSiteRef>
      <LocalNetworkSiteRef name="Site2"><Connection type="IPsec" /></LocalNetworkSiteRef>
    </ConnectionsToLocalNetwork>
  </Gateway>
```

## <a name="5-import-the-network-configuration-file"></a>5. A hálózati konfigurációs fájl importálása
Importálja a hálózati konfigurációs fájlt. Amikor importálja ezt a fájlt a módosításokkal, az új alagutak hozzáadódnak. Az alagutak a korábban létrehozott dinamikus átjárót fogják használni. A PowerShell segítségével importálhatja a fájlt.

## <a name="6-download-keys"></a>6. Letöltési kulcsok
Az új alagutak hozzáadása után használja a PowerShell "Get-AzureVNetGatewayKey" parancsmagját az IPsec/IKE minden alagúthoz előre megosztott kulcsok lekérni.

Példa:

```powershell
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site1"
Get-AzureVNetGatewayKey –VNetName "VNet1" –LocalNetworkSiteName "Site2"
```

Ha szeretné, *használhatja* a Virtuális hálózati átjáró megosztott kulcs REST API-t is az előre megosztott kulcsok lehívásához.

## <a name="7-verify-your-connections"></a>7. Ellenőrizze kapcsolatait
Ellenőrizze a többhelyes alagút állapotát. Az egyes alagutak kulcsainak letöltése után ellenőriznie kell a kapcsolatokat. Használja a "Get-AzureVnetConnection" a virtuális hálózati alagutak listájának leéséhez, amint az az alábbi példában látható. VNet1 a virtuális hálózat neve.

```powershell
Get-AzureVnetConnection -VNetName VNET1
```

Példa visszatérésre:

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

A VPN-átjárókról a [VPN-átjárók – ismertető](vpn-gateway-about-vpngateways.md)további tudnivalókat olvashat.

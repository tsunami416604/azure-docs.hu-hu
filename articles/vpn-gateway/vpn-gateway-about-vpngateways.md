<properties 
   pageTitle="Információk a VPN Gateway-ről | Microsoft Azure"
   description="Megismerkedhet az Azure Virtual Network virtuális hálózatok számára készült VPN Gateway átjárókkal."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Információk a VPN Gateway-ről

A VPN Gateway átjárók a virtuális hálózatok és a helyszínek közötti hálózati adatforgalom elküldésére használatosak. Másik használati módja az Azure virtuális hálózatai közötti (VNet–VNet) adatküldés. Az alábbi szakasz a VPN Gateway-re vonatkozó elemeket tárgyalja.

A VPN-átjáró létrehozásához kapcsolódó utasítások a virtuális hálózat üzembe helyezésekor használt modell alapján vannak megadva. Ha például a virtuális hálózatot a klasszikus üzembe helyezési modellel hozta létre, a VPN-átjáró létrehozására és konfigurálására a klasszikus üzembe helyezési modell irányelvei és utasításai vonatkoznak. A klasszikus üzembe helyezési modellt használó virtuális hálózatokhoz nem hozhat létre erőforrás-kezelői VPN-átjárót. 

További információk az üzembe helyezési modellekről: [Az erőforrás-kezelői és a klasszikus üzembe helyezési modellek ismertetése](../resource-manager-deployment-model.md).


## <a name="gwsub"></a>Átjáró alhálózata

A VPN-átjáró konfigurálásához először is létre kell hozni egy átjáró-alhálózatot a virtuális hálózathoz. A megfelelő működéshez az átjáró-alhálózat neve *GatewaySubnet* kell legyen. A név alapján az Azure tudni fogja, hogy az átjáróhoz ezt az alhálózatot kell használnia.<BR>Ha a klasszikus portált használja, annak felületén az átjáró-alhálózat neve automatikusan *Gateway* lesz. A fentiek kizárólag az átjáró-alhálózat klasszikus portálon történő megtekintésére vonatkoznak. Ebben az esetben az alhálózat ténylegesen *GatewaySubnet* néven jön létre az Azure-ban, és így jelenik meg az Azure portálon és a PowerShellben is.

Az átjáró-alhálózat minimális mérete teljes mértékben a létrehozni kívánt konfigurációtól függ. Ugyan egyes konfigurációkhoz létrehozhat kicsi, akár /29-es méretű átjáró-alhálózatot is, ajánlott /28-ast vagy nagyobbat létrehozni (/28, /27, /26 stb.). 

Nagyobb méretű átjáró létrehozásával kiküszöbölhető a méretkorlátozásnak való megfelelés problémája. Ha például az átjárót /29-es méretű átjáró-alhálózattal hozta létre, és Helyek közötti/ExpressRoute párhuzamos konfigurációt kíván megadni, törölnie kell az átjárót, az átjáró-alhálózatot, /28-as vagy nagyobb méretű átjáró-alhálózatot kell létrehoznia, majd újra üzembe kell állítania az átjárót. 

Ha már kezdetben nagyobb méretű átjáró-alhálózatot hoz létre, azzal időt takaríthat meg, amikor utólag ad új konfigurációs szolgáltatásokat a hálózati környezethez. 

Az alábbi példán egy GatewaySubnet nevű átjáró-alhálózat látható. Ahogy láthatja, a CIDR-jelölésrendszer /27-es méretet ad meg, amely a jelenlegi konfigurációk zöméhez elegendő IP-címet tesz lehetővé.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Győződjön meg arról, hogy a GatewaySubnet átjáró-alhálózaton nincs alkalmazva Hálózati biztonsági csoport (NSG), ami a kapcsolódás meghiúsulását okozhatja.

## <a name="gwtype"></a>Átjárótípusok

Az átjáró típusa az átjáró kapcsolódási módját adja meg, és az Erőforrás-kezelő üzembe helyezési modelljének kötelező konfigurációs beállításai közé tartozik. Ne keverje össze az átjáró és a VPN típusát, ahol utóbbi a VPN-hez tartozó útválasztás típusát adja meg. A `-GatewayType` lehetséges értékei a következők: 

- Vpn
- ExpressRoute


Az Erőforrás-kezelő üzembe helyezési modelljének jelen példájában a -GatewayType értéke: *Vpn*. Egy átjáró létrehozásakor biztosítania kell, hogy annak típusa megfeleljen a konfigurációnak. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>Gateway termékváltozatok

Egy VPN-átjáró létrehozásakor meg kell adni a használni kívánt termékváltozatot. A VPN Gateway három termékváltozatban létezik:

- Alapszintű
- Standard
- HighPerformance

A `-GatewaySku` értéke az alábbi példában: *Standard*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

###  <a name="aggthroughput"></a>A termékváltozat és az átjáró típusának becsült összesített átviteli sebessége


Az alábbi táblában az átjárótípusok és azok becsült összesített átviteli sebessége tekinthetők meg. A díjszabás a különböző átjáró-termékváltozatok esetében nem változik. Díjszabással kapcsolatos információk: [VPN Gateway Pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/) (A VPN Gateway díjszabása). Ez a tábla a Resource Managerre és a klasszikus üzembe helyezési modellre is érvényes.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 

## <a name="vpntype"></a>VPN-típusok

Az üzembe állításhoz minden egyes konfiguráció esetében adott típusú VPN-re van szükség. Kétféle konfiguráció kombinálásakor (például Helyek közötti és Pont–hely kapcsolat létesítésekor ugyanazon a virtuális hálózaton) olyan VPN-típust kell használni, amely mindkét kapcsolat követelményeit kielégíti. 

Pont–hely és Helyek közötti kapcsolat együttes létesítésekor útvonalalapú VPN-típust kell használni, ha Azure Resource Manager-alapú üzemi modellt használ, illetve dinamikus átjárót kell alkalmazni, ha klasszikus üzembe helyezési módban dolgozik.

A konfiguráció létrehozásakor a kapcsolat számára szükséges VPN-típust fogja kiválasztani. 

Kétféle VPN-típus létezik:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Az Erőforrás-kezelő üzembe helyezési modelljének jelen példájában a `-VpnType` értéke: *RouteBased*. Egy átjáró létrehozásakor biztosítania kell, hogy -VpnType megfeleljen a konfigurációnak. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>Kapcsolattípusok

Minden egyes konfiguráció esetében egy adott kapcsolattípusra van szükség. A `-ConnectionType` lehetséges Resource Manager PowerShell-értékei a következők:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

Az alábbi példában létrehozott, helyek közötti kapcsolathoz „IPsec” kapcsolattípusra van szükség.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="lng"></a>Helyi hálózati átjárók

A helyi hálózati átjáró általában a helyszínt jelenti. A klasszikus üzembe helyezési modellben a helyi hálózati átjárót a Helyi névvel illettük. A következőkkel fogja ellátni a helyi hálózati átjárót: név, a helyszíni VPN-eszköz nyilvános IP-címe és a helyszínen található címelőtagok. Az Azure megvizsgálja a hálózati adatforgalom célcím-előtagjait, valamint a helyi hálózati átjáróhoz megadott konfigurációt, és a csomagok útválasztását ennek megfelelően alakítja ki. Ezen címelőtagok szükség szerint módosíthatóak.



### Címelőtagok módosítása – Resource Manager

A címelőtagok módosításának folyamata attól függően eltérő, hogy a VPN-átjáró létre lett-e már hozva. Lásd a következő cikkszakaszt: [Helyi hálózati átjáró címelőtagjainak módosítása](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

Az alábbi példában a MyOnPremiseWest nevű helyi hálózati átjáró megadása történik, amely két IP-címelőtagot fog tartalmazni.

    New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24') 

### Címelőtagok módosítása – Klasszikus üzembe helyezés

Ha a helyeket a klasszikus üzembe helyezési modell alkalmazásával kell módosítania, ehhez a klasszikus portál Helyi hálózatok konfigurációs lapját használhatja, vagy közvetlenül is módosíthatja a NETCFG.XML hálózati konfigurációs fájlt.


##  <a name="devices"></a> VPN-eszközök

Meg kell győződnie arról, hogy a használni kívánt VPN-eszköz támogatja az adott konfigurációhoz szükséges VPN-típust. További információk a kompatibilis VPN-eszközökről: [Tudnivalók a VPN-eszközökről](vpn-gateway-about-vpn-devices.md).

##  <a name="requirements"></a>Az átjáróra vonatkozó követelmények


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## Következő lépések

A konfiguráció megtervezése és kialakítása előtt tekintse át a [VPN Gateway – Gyakori kérdések](vpn-gateway-vpn-faq.md) cikkben foglalt részletes információkat.





 



<!--HONumber=jun16_HO2-->



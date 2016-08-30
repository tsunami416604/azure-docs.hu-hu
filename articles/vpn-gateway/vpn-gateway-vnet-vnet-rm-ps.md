<properties
   pageTitle="Virtuális hálózatok közötti VPN Gateway-kapcsolat létrehozása az Azure Resource Manager és a virtuális hálózatokhoz készült PowerShell használatával | Microsoft Azure"
   description="Ez a cikk lépésről lépésre bemutatja, hogyan csatlakoztathatók a virtuális hálózatok egymáshoz az Azure Resource Manager és a PowerShell használatával."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/02/2016"
   ms.author="cherylmc"/>

# Virtuális hálózatok közötti kapcsolat létrehozása az Azure Resource Manager és a PowerShell használatával

> [AZURE.SELECTOR]
- [Klasszikus Azure portál](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell – Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Ez a cikk lépésről lépésre bemutatja, hogyan hozható létre virtuális hálózatok közötti kapcsolat a Resource Manager-alapú üzemi modell és a PowerShell használatával. A virtuális hálózatok lehetnek azonos vagy eltérő régiókban, és azonos vagy eltérő előfizetésekben.


**Tudnivalók az Azure üzembehelyezési modellekről**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Üzemi modellek és eszközök a virtuális hálózatok közötti kapcsolatokhoz**

A virtuális hálózatok közötti kapcsolat mindkét üzemi modellben konfigurálható, számos különféle eszközzel. További információkért tekintse meg az alábbi táblázatot. A táblázatot frissítjük, ahogy új cikkek, üzemi modellek és további eszközök válnak elérhetővé ehhez a konfigurációhoz. Amint új cikk érhető el, egy arra mutató közvetlen hivatkozás szerepel majd a táblázatban.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)] 


## Tudnivalók a virtuális hálózatok közötti kapcsolatokról

Egy virtuális hálózat egy másikkal való összekapcsolása (virtuális hálózatok közötti kapcsolat) egy virtuális hálózat egy helyszíni helyhez való csatlakoztatásához hasonló. Mindkét kapcsolattípus Azure VPN-átjárót használ biztonságos alagút kialakításához IPsec/IKE használatával. Az összekapcsolt virtuális hálózatok lehetnek eltérő régiókban vagy eltérő előfizetésekben is. A virtuális hálózatok közötti kommunikációt kombinálhatja többhelyes konfigurációkkal is. Így létrehozhat olyan hálózati topológiákat, amelyek a létesítmények közötti kapcsolatokat a virtuális hálózatok közötti kapcsolatokkal egyesítik, ahogyan azt az alábbi diagram mutatja. 


![Tudnivalók a kapcsolatokról](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Miért érdemes összekapcsolni a virtuális hálózatokat?

A virtuális hálózatokat a következő okokból érdemes összekapcsolni:

- **Georedundancia és földrajzi jelenlét több régióban**
    - Beállíthatja a saját georeplikációját vagy szinkronizálását biztonságos kapcsolaton át, internetes végpontok használata nélkül.
    - Az Azure Traffic Manager és a Load Balancer segítségével magas rendelkezésre állású munkaterhelést állíthat be georedundanciával több Azure-régióban. Például beállíthat folyamatosan működő SQL-t több Azure-régióban található rendelkezésre állási csoportokkal.

- **Regionális többrétegű alkalmazások elkülönítéssel vagy felügyeleti határral**
    - Egy régión belül beállíthat többrétegű alkalmazásokat több, elkülönítéssel vagy felügyeleti követelményekkel összekapcsolt virtuális hálózatokkal.


### Virtuális hálózatok közötti kapcsolat – gyakori kérdések

- A virtuális hálózatok lehetnek azonos vagy eltérő Azure-régiókban (helyeken).

- A felhőszolgáltatás és a terheléselosztási végpont NEM ívelhet át több virtuális hálózaton, akkor sem, ha ezek össze vannak kapcsolva.

- Az Azure Virtual Networkök összekapcsolása nem igényel helyszíni VPN-átjárókat, hacsak nem szükséges a létesítmények közötti kapcsolat.

- A virtuális hálózatok közötti kapcsolat támogatja a virtuális hálózatok csatlakoztatását, azonban nem támogatja a NEM virtuális hálózatban lévő virtuális gépek és felhőszolgáltatások csatlakoztatását.

- A virtuális hálózatok közötti kapcsolathoz útvonalalapú (korábbi nevén dinamikus útválasztású) VPN-típussal rendelkező Azure VPN Gateway átjárók szükségesek. 

- A virtuális hálózati kapcsolat használható egyidejűleg többhelyes VPN-kapcsolatokkal, legfeljebb 10 (alapértelmezett/általános átjárók) vagy 30 (nagy teljesítményű átjárók) VPN-átjáróval olyan virtuális hálózati VPN-átjáró esetében, amely más virtuális hálózatokhoz vagy helyszíni helyekhez csatlakozik.

- A virtuális hálózatok címterei és a helyszíni helyi hálózati helyek nem lehetnek egymással átfedésben. Ha a címterek átfedik egymást, a virtuális hálózatok közötti kapcsolat létrehozása meghiúsul.

- A virtuális hálózatok párjai közötti redundáns alagutak nem támogatottak.

- A virtuális hálózat minden VPN-alagútja az Azure VPN Gateway átjárón elérhető sávszélességet használja, és azonos VPN-átjáró üzemidőre vonatkozó SLA-t az Azure-ban.

- A virtuális hálózatok közötti adatforgalom a Microsoft hálózatán halad át, nem az interneten.

- Az azonos régión belüli virtuális hálózatok közötti adatforgalom ingyenes mindkét irányban. A régiókon átívelő virtuális hálózatok közötti kimenő forgalom díjának megállapítása a virtuális hálózatok közötti kimenő adatátviteli tarifák alapján történik a forrásrégiók alapján. A részletekért tekintse meg a [díjszabást ismertető oldalt](https://azure.microsoft.com/pricing/details/vpn-gateway/).


## Melyik eljárást használjam?

Ebben a cikkben különböző eljárások találhatók, amelyek a Resource Manager-alapú üzemi modellel létrehozott virtuális hálózatokra vonatkoznak. A virtuális hálózatok közötti kapcsolat konfigurációs lépései attól függnek, hogy a virtuális hálózatok azonos vagy eltérő előfizetésben találhatók-e. 

![Mindkét kapcsolat](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


A konfigurációs lépések szempontjából a két lehetőség közötti fő különbség az, hogy lehetséges-e egyetlen PowerShell-munkameneten belül konfigurálni az összes virtuális hálózati és átjáró-erőforrást. Ez a dokumentum leírja mindkét eset lépéseit. A fenti ábrán látható egy azonos előfizetésben lévő virtuális hálózatok közötti kapcsolat, és egy előfizetéseken átívelő virtuális hálózatok közötti kapcsolat is. 


- [Azonos előfizetésben található virtuális hálózatok](#samesub)
- [Eltérő előfizetésben található virtuális hálózatok](#difsub)


## <a name="samesub"></a>Azonos előfizetésben található virtuális hálózatok összekapcsolása

Ez a konfiguráció az azonos előfizetésben található virtuális hálózatokra vonatkozik, ahogyan az az alábbi diagramon látható:

![Azonos előfizetésben található virtuális hálózatok közötti kapcsolat](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Előkészületek

- Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
    
- Előfordulhat, hogy telepítenie kell az Azure Resource Manager PowerShell-parancsmagjait. A PowerShell-parancsmagok telepítésével kapcsolatban további információ: [Az Azure PowerShell telepítése és konfigurálása](../powershell-install-configure.md).

### <a name="Step1"></a>1. lépés – Az IP-címtartományok megtervezése


Fontos eldönteni, milyen tartományokat fog használni a hálózat konfigurálásához. Ügyeljen arra, hogy a virtuális hálózata tartományai és a helyi hálózata tartományai ne legyenek átfedésben egymással.

Az alábbi lépések során két virtuális hálózatot fogunk létrehozni az azokhoz tartozó átjáróalhálózatokkal és konfigurációkkal. Ezután létrehozunk egy VPN-átjárókapcsolatot a két virtuális hálózat között.

A gyakorlatban a következő értékeket használja a virtuális hálózatokhoz:

**Értékek a TestVNet1-hez:**

- Virtuális hálózat neve: TestVNet1
- Erőforráscsoport: TestRG1
- Hely: East US
- TestVNet1: 10.11.0.0/16 és 10.12.0.0/16
- Előtér: 10.11.0.0/24
- Háttér: 10.12.0.0/24
- Átjáróalhálózat: 10.12.255.0/27
- DNS-kiszolgáló: 8.8.8.8
- Átjáró neve: VNet1GW
- Nyilvános IP-cím: VNet1GWIP
- VPN típusa: RouteBased
- Kapcsolat (1–4): VNet1toVNet4
- Kapcsolat (1–5): VNet1toVNet5
- Kapcsolat típusa: VNet2VNet


**Értékek a TestVNet4-hez:**

- Virtuális hálózat neve: TestVNet4
- TestVNet2: 10.41.0.0/16 és 10.42.0.0/16
- Előtér: 10.41.0.0/24
- Háttér: 10.42.0.0/24
- Átjáróalhálózat: 10.42.255.0.0/27
- Erőforráscsoport: TestRG4
- Hely: West US
- DNS-kiszolgáló: 8.8.8.8
- Átjáró neve: VNet4GW
- Nyilvános IP-cím: VNet4GWIP
- VPN típusa: RouteBased
- Kapcsolat: VNet4toVNet1
- Kapcsolat típusa: VNet2VNet



### <a name="Step2"></a>2. lépés – A TestVNet1 létrehozása és konfigurálása

1. A változók deklarálása

    Ezt a gyakorlatot a változók deklarálásával kezdjük. Az alábbi példa a gyakorlathoz használt értékekkel deklarálja a változókat. Az éles konfigurációhoz ne felejtse el ezeket az értékeket a saját értékeire cserélni. Ezeket a változókat akkor használhatja, ha azért hajtja végre a lépéseket, hogy megismerje ezt a konfigurációtípust. Módosítsa a változókat, majd másolja és illessze be őket a PowerShell-konzolra.

        $Sub1          = "Replace_With_Your_Subcription_Name"
        $RG1           = "TestRG1"
        $Location1     = "East US"
        $VNetName1     = "TestVNet1"
        $FESubName1    = "FrontEnd"
        $BESubName1    = "Backend"
        $GWSubName1    = "GatewaySubnet"
        $VNetPrefix11  = "10.11.0.0/16"
        $VNetPrefix12  = "10.12.0.0/16"
        $FESubPrefix1  = "10.11.0.0/24"
        $BESubPrefix1  = "10.12.0.0/24"
        $GWSubPrefix1  = "10.12.255.0/27"
        $DNS1          = "8.8.8.8"
        $GWName1       = "VNet1GW"
        $GWIPName1     = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14  = "VNet1toVNet4"
        $Connection15  = "VNet1toVNet5"

2. Kapcsolódás az 1. előfizetéshez

    A Resource Manager parancsmagjainak használatához váltson át PowerShell módba. További információ: [A Windows PowerShell használata a Resource Managerrel](../powershell-azure-resource-manager.md).

    Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

        Login-AzureRmAccount

    Keresse meg a fiókot az előfizetésekben.

        Get-AzureRmSubscription 

    Válassza ki a használni kívánt előfizetést.

        Select-AzureRmSubscription -SubscriptionName $Sub1

3. Új erőforráscsoport létrehozása

        New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. A TestVNet1 alhálózatkonfigurációinak létrehozása

    Az alábbi minta létrehoz egy TestVNet1 nevű virtuális hálózatot és három alhálózatot, amelyek neve a következő: GatewaySubnet, FrontEnd és Backend. Az értékek behelyettesítésekor fontos, hogy az átjáróalhálózat neve mindenképp GatewaySubnet legyen. Ha ezt másként nevezi el, az átjáró létrehozása meghiúsul. 

    Az alábbi példában az átjáróalhálózat a /27-et használja. Ugyan technikailag létrehozható átjáróalhálózat kisebb, akár /29-es alhálózat használatával is, ez nem ajánlott. Ajánlott ennél nagyobb alhálózat használata, például /27-es vagy /26-os, arra az esetre, ha olyan meglévő vagy jövőbeli konfigurációkat szeretne használni, amelyek nagyobb átjáróalhálózatot igényelnek. 


        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. A TestVNet1 létrehozása

        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Nyilvános IP-cím kérése

    Ezután egy nyilvános IP-cím kiosztását kéri majd a virtuális hálózathoz létrehozandó átjáróhoz. A használni kívánt IP-címet nem adhatja meg. Ez dinamikusan van kiosztva az átjárójához. Ezt az IP-címet a következő konfigurációs szakaszban kell majd használni. Használja az alábbi példát. A Kiosztási módszernek ehhez a címhez Dinamikusnak kell lennie. 

        $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Az átjáró konfigurációjának létrehozása

    Az átjáró konfigurációja meghatározza az alhálózatot és a használandó nyilvános IP-címet. Az alábbi minta használatával hozza létre az átjáró konfigurációját. 

        $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. A TestVNet1 átjárójának létrehozása

    Ebben a lépésben a TestVNet1 virtuális hálózati átjáróját fogja létrehozni. A virtuális hálózatok közötti konfigurációkhoz a VpnType paraméternek RouteBased értékűnek kell lennie. Az átjáró létrehozása akár 30 percet vagy hosszabb időt is igénybe vehet.

        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 3. lépés – A TestVNet4 létrehozása és konfigurálása

A TestVNet1 konfigurálása után a lépések megismétlésével létre fogja hozni a TestVNet4 virtuális hálózatot. Kövesse az alábbi lépéseket, az értékeket a saját értékeire cserélve. Ez a lépés elvégezhető ugyanebben a PowerShell-munkamenetben, mert a virtuális hálózat azonos előfizetésben található.

1. A változók deklarálása

    Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

        $RG4           = "TestRG4"
        $Location4     = "West US"
        $VnetName4     = "TestVNet4"
        $FESubName4    = "FrontEnd"
        $BESubName4    = "Backend"
        $GWSubName4    = "GatewaySubnet"
        $VnetPrefix41  = "10.41.0.0/16"
        $VnetPrefix42  = "10.42.0.0/16"
        $FESubPrefix4  = "10.41.0.0/24"
        $BESubPrefix4  = "10.42.0.0/24"
        $GWSubPrefix4  = "10.42.255.0/27"
        $DNS4          = "8.8.8.8"
        $GWName4       = "VNet4GW"
        $GWIPName4     = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41  = "VNet4toVNet1"

    Mielőtt folytatja, győződjön meg arról, hogy továbbra is csatlakozik az 1. előfizetéshez.

2. Új erőforráscsoport létrehozása

        New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. A TestVNet4 alhálózatkonfigurációinak létrehozása

        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. A TestVNet4 létrehozása

        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Nyilvános IP-cím kérése

        $gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Az átjáró konfigurációjának létrehozása

        $vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. A TestVNet4 átjárójának létrehozása

        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 4. lépés – Az átjárók csatlakoztatása

1. A virtuális hálózati átjárók létrehozása

    Ebben a példában a lépés elvégezhető ugyanebben a PowerShell-munkamenetben, mert az átjárók azonos előfizetésben találhatóak.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. A TestVNet1–TestVNet4 kapcsolat létrehozása

    Ebben a lépésben létrehozza a TestVNet1 felől a TestVNet4 felé irányuló kapcsolatot. A példák egy megosztott kulcsra is hivatkoznak. A megosztott kulcshoz használhatja a saját értékeit. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. A TestVNet4–TestVNet1 kapcsolat létrehozása

    Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet4 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

    A kapcsolat néhány perc alatt létrejön.

## <a name="Verify"></a>A virtuális hálózatok közötti kapcsolat ellenőrzése

Az alábbi példák bemutatják, hogyan ellenőrizhető a kapcsolat. Az értékeket módosítsa úgy, hogy azok megfeleljenek a saját környezetének.

### A kapcsolat ellenőrzése az Azure Portal használatával

Ha ellenőrizni kíván egy VPN-kapcsolatot az Azure Portalon, akkor lépjen a **Virtuális hálózati átjárók** elemre,  -> ** kattintson az átjárója nevére, majd a** -> **Beállítások** -> **Kapcsolatok** elemre. A kapcsolat nevét kiválasztva további információkat jeleníthet meg a **Kapcsolat** panelen. 


### A kapcsolat ellenőrzése a PowerShell használatával

A *Get-AzureRmVirtualNetworkGatewayConnection –Debug* parancs használatával is ellenőrizheti, hogy sikeresen létrejött-e a kapcsolat. Használja a következő példát, és módosítsa az értékeket a sajátjaival megegyezőkre. Ha a rendszer arra kéri, válassza az „A” lehetőséget az összes futtatásához.

    Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

A parancsmag futtatása után görgessen végig az értékeken és tekintse meg őket. Az alábbi PowerShell-kimeneti példában a kapcsolati állapot *Csatlakoztatva*, és láthatja a bemenő és kimenő bájtokat.



    AuthorizationKey           :
    VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
    LocalNetworkGateway2       :
    Peer                       :
    ConnectionType             : Vnet2Vnet
    RoutingWeight              : 0
    SharedKey                  : AzureA1b2C3
    ConnectionStatus           : Connected
    EgressBytesTransferred     : 1376
    IngressBytesTransferred    : 1232
    ProvisioningState          : Succeeded
    VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
    VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
    LocalNetworkGateway2Text   :
    PeerText                   :
    ResourceGroupName          : TestRG1
    Location                   : eastus
    ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
    Tag                        : {}
    TagsTable                  :
    Name                       : VNet1toVNet4
    Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name="difsub"></a>Különböző előfizetésben található virtuális hálózatok összekapcsolása

Az alábbi konfigurációs lépések létrehoznak egy további, virtuális hálózatok közötti kapcsolatot a TestVNet1 és az eltérő előfizetésben található TestVNet5 összekapcsolásához. A különbség itt az, hogy a konfigurációs lépések egy részét külön PowerShell-munkamenetben kell elvégezni a második előfizetés környezetében, különösen akkor, ha a két előfizetés különböző szervezetekhez tartozik. Az alábbi lépések elvégzésének eredményeként létrejövő konfigurációt az alábbi ábra mutatja:

![Különböző előfizetésekben található VNet2VNet kapcsolat](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

Az alábbi útmutató a fent leírt lépések folytatása. Az [1. lépés](#Step1) és a [2. lépés](#Step2) elvégzésével hozza létre és konfigurálja a TestVNet1-et, valamint a TestVNet1 VPN-átjáróját. Ha kizárólag különböző előfizetésekben található virtuális hálózatok csatlakoztatását tervezi, az előző gyakorlat 3. és 4. lépésével nem kell foglalkoznia, és folytathatja az alábbi 5. lépéssel. 

### 5. lépés – A további IP-címtartományok ellenőrzése

Fontos ügyelni arra, hogy az új virtuális hálózat (TestVNet5) IP-címtere ne legyen átfedésben a többi virtuális hálózati vagy hálózati átjárói tartománnyal. 

Ebben a példában a virtuális hálózatok különböző szervezetekhez is tartozhatnak. A gyakorlatban a TestVNet5 hálózathoz a következő értékeket használhatja:

**Értékek a TestVNet5-höz:**

- Virtuális hálózat neve: TestVNet5
- Erőforráscsoport: TestRG5
- Hely: Japan East
- TestVNet5: 10.51.0.0/16 és 10.52.0.0/16
- Előtér: 10.51.0.0/24
- Háttér: 10.52.0.0/24
- Átjáróalhálózat: 10.52.255.0.0/27
- DNS-kiszolgáló: 8.8.8.8
- Átjáró neve: VNet5GW
- Nyilvános IP-cím: VNet5GWIP
- VPN típusa: RouteBased
- Kapcsolat: VNet5toVNet1
- Kapcsolat típusa: VNet2VNet

**További értékek a TestVNet1-hez:**

- Kapcsolat: VNet1toVNet5


### 6. lépés – A TestVNet5 létrehozása és konfigurálása

Ezt a lépést az új előfizetés környezetében kell elvégezni. Ezt a részt azon másik szervezet rendszergazdájának kell elvégeznie, amely az előfizetés tulajdonosa.

1. A változók deklarálása

    Ne felejtse el az értékeket olyanokra cserélni, amelyeket a saját konfigurációjához kíván használni.

        $Sub5          = "Replace_With_the_New_Subcription_Name"
        $RG5           = "TestRG5"
        $Location5     = "Japan East"
        $VnetName5     = "TestVNet5"
        $FESubName5    = "FrontEnd"
        $BESubName5    = "Backend"
        $GWSubName5    = "GatewaySubnet"
        $VnetPrefix51  = "10.51.0.0/16"
        $VnetPrefix52  = "10.52.0.0/16"
        $FESubPrefix5  = "10.51.0.0/24"
        $BESubPrefix5  = "10.52.0.0/24"
        $GWSubPrefix5  = "10.52.255.0/27"
        $DNS5          = "8.8.8.8"
        $GWName5       = "VNet5GW"
        $GWIPName5     = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51  = "VNet5toVNet1"

2. Kapcsolódás az 5. előfizetéshez

    Nyissa meg a PowerShell konzolt, és csatlakozzon a fiókjához. A következő minta segíthet a kapcsolódásban:

        Login-AzureRmAccount

    Keresse meg a fiókot az előfizetésekben.

        Get-AzureRmSubscription 

    Válassza ki a használni kívánt előfizetést.

        Select-AzureRmSubscription -SubscriptionName $Sub5

3. Új erőforráscsoport létrehozása

        New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. A TestVNet4 alhálózatkonfigurációinak létrehozása
    
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. A TestVNet5 létrehozása

        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Nyilvános IP-cím kérése

        $gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Az átjáró konfigurációjának létrehozása

        $vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. A TestVNet5 átjárójának létrehozása

        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### 7. lépés – Az átjárók csatlakoztatása

Ebben a példában, mivel az átjárók különböző előfizetésekben találhatóak, a lépést felosztottuk két PowerShell-munkamenetre, amelyek jelölése [1. előfizetés] és [5. előfizetés].

1. **[1. előfizetés]** Az 1. előfizetés virtuális hálózati átjárójának beszerzése

    Jelentkezzen be és kapcsolódjon az 1. előfizetéshez.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

    Másolja a következő elemek kimenetét, és küldje el a másolatokat az 5. előfizetés rendszergazdájának e-mailben vagy egyéb módon.

        $vnet1gw.Name
        $vnet1gw.Id

    Ez a két elem hasonló értékekkel fog rendelkezni, mint a következő kimeneti példában látható értékek:

        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[5. előfizetés]** Az 5. előfizetés virtuális hálózati átjárójának beszerzése

    Jelentkezzen be és kapcsolódjon az 5. előfizetéshez.

        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

    Másolja a következő elemek kimenetét, és küldje el a másolatokat az 1. előfizetés rendszergazdájának e-mailben vagy egyéb módon.

        $vnet5gw.Name
        $vnet5gw.Id

    Ez a két elem hasonló értékekkel fog rendelkezni, mint a következő kimeneti példában látható értékek:

        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[1. előfizetés]** A TestVNet1–TestVNet5 kapcsolat létrehozása

    Ebben a lépésben TestVNet1 felől a TestVNet5 felé irányuló kapcsolatot fogja létrehozni. A különbség itt az, hogy a $vnet5gw nem szerezhető be közvetlenül, mert másik előfizetésben található. Ehhez létre kell hoznia egy új PowerShell-objektumot a fenti lépésekben az 1. előfizetésből átküldött értékekkel. Cserélje le a nevet, az azonosítót és a megosztott kulcsot a saját értékeire. Fontos, hogy a megosztott kulcs azonos legyen mindkét kapcsolathoz. A kapcsolat létrehozása egy kis időt vehet igénybe.

    Kapcsolódjon az 1. előfizetéshez. 
    
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[5. előfizetés]** A TestVNet5–TestVNet1 kapcsolat létrehozása

    Ez a lépés az előzőhöz hasonló. A különbség annyi, hogy ezúttal a TestVNet5 felől a TestVNet1 felé irányuló kapcsolatot hozza létre. Itt ugyanúgy egy PowerShell-objektumot kell létrehozni az 1. előfizetésből szerzett értékek alapján. Ügyeljen arra, hogy a megosztott kulcsok megegyezzenek ebben a lépésben.

    Kapcsolódjon az 5. előfizetéshez.

        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. A kapcsolat ellenőrzése

    A fenti lépések elvégzése után ellenőrizheti a kapcsolatot a [Virtuális hálózatok közötti kapcsolat ellenőrzése](#Verify) című cikkben található módszerekkel.

## Következő lépések

- Miután a kapcsolat létrejött, hozzáadhat virtuális gépeket a virtuális hálózataihoz. A lépésekért lásd: [Virtuális gép létrehozása](../virtual-machines/virtual-machines-windows-hero-tutorial.md).
- Információk a BGP-ről: [A BGP áttekintése](vpn-gateway-bgp-overview.md) és [A BGP konfigurálása](vpn-gateway-bgp-resource-manager-ps.md). 




<!--HONumber=jun16_HO2-->



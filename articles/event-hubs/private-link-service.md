---
title: Az Azure Event Hubs integrálása az Azure Private link Service szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Event Hubs az Azure Private link Service használatával
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: fb8fc93174345d0bdb09e4308a4206a65ed2270a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82148200"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Az Azure Event Hubs integrálása az Azure Private Linktel (előzetes verzió)
Az Azure Private link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Event Hubs, az Azure Storage és a Azure Cosmos DB) és az Azure által üzemeltetett ügyfél/partner szolgáltatások elérését a virtuális hálózat **privát végpontján** keresztül.

A privát végpontok olyan hálózati adapterek, amelyek az Azure Private-kapcsolaton keresztül csatlakoznak a szolgáltatáshoz. A privát végpont egy magánhálózati IP-címet használ a VNet, és hatékonyan hozza a szolgáltatást a VNet. A szolgáltatás felé irányuló összes forgalom a privát végponton keresztül irányítható, így nincs szükség átjáróra, NAT-eszközre, ExpressRoute vagy VPN-kapcsolatra, vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Kapcsolódhat egy Azure-erőforrás egy példányához, amely a legmagasabb szintű részletességet nyújtja a hozzáférés-vezérlésben.

További információ: [Mi az az Azure Private link?](../private-link/private-link-overview.md)

> [!IMPORTANT]
> Ez a funkció csak a **dedikált** szinten támogatott. További információ a dedikált platformról: [dedikált Event Hubs áttekintése](event-hubs-dedicated-overview.md). 
>
> Ez a funkció jelenleg **előzetes**verzióban érhető el. 

>[!WARNING]
> A privát végpontok engedélyezése megakadályozhatja, hogy más Azure-szolgáltatások a Event Hubs használatával kommunikálnak.
>
> A megbízható Microsoft-szolgáltatások virtuális hálózatok használata esetén nem támogatottak.
>
> Olyan általános Azure-forgatókönyvek, amelyek nem működnek a virtuális hálózatokkal (vegye figyelembe, hogy a lista **nem** teljes) –
> - Azure Monitor (diagnosztikai beállítás)
> - Azure Stream Analytics
> - Integráció a Azure Event Grid
> - Azure IoT Hub útvonalak
> - Azure IoT Device Explorer
>
> A következő Microsoft-szolgáltatások szükségesek virtuális hálózaton
> - Azure Web Apps
> - Azure Functions

## <a name="add-a-private-endpoint-using-azure-portal"></a>Privát végpont hozzáadása a Azure Portal használatával

### <a name="prerequisites"></a>Előfeltételek

Event Hubs névtér Azure Private-hivatkozással való integrálásához a következő entitásokra vagy engedélyekre lesz szüksége:

- Egy Event Hubs névtér.
- Egy Azure-beli virtuális hálózat.
- Egy alhálózat a virtuális hálózaton.
- A névtérhez és a virtuális hálózathoz tartozó tulajdonosi vagy közreműködői engedélyek.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Ha a portál használatával kiválaszt egy régiót a privát végponthoz, akkor az automatikusan csak az adott régióban lévő virtuális hálózatokat fogja szűrni. A névtér lehet egy másik régióban is.

A privát végpont egy magánhálózati IP-címet használ a virtuális hálózaton.

### <a name="steps"></a>Lépések
Ha már rendelkezik Event Hubs névtérrel, a következő lépések végrehajtásával hozhat létre privát kapcsolati kapcsolatot:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. A keresősáv mezőbe írja be az **Event hubok**kifejezést.
3. Válassza ki a listából azt a **névteret** , amelyhez privát végpontot szeretne hozzáadni.
4. Válassza a **hálózatkezelés** fület a **Beállítások**területen.
5. Válassza ki a **privát Endpoint Connections (előzetes verzió)** fület az oldal tetején. Ha nem a Event Hubs dedikált szintjét használja, egy üzenet jelenik meg: **a Event Hubson a magánhálózati végponti kapcsolatok csak a dedikált fürtben létrehozott névterek által támogatottak**.
6. A lap tetején kattintson a **+ privát végpont** gombra.

    ![Kép](./media/private-link-service/private-link-service-3.png)
7. Az **alapvető beállítások** lapon kövesse az alábbi lépéseket: 
    1. Válassza ki azt az **Azure-előfizetést** , amelyben létre szeretné hozni a privát végpontot. 
    2. Válassza ki a privát végpont erőforráshoz tartozó **erőforráscsoportot** .
    3. Adja meg a magánhálózati végpont **nevét** . 
    5. Válasszon ki egy **régiót** a privát végpont számára. A privát végpontnak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, de a privát kapcsolati erőforrástól eltérő régióban lehet, amelyhez csatlakozik. 
    6. Válassza a **Tovább: erőforrás >** gombot az oldal alján.

        ![Privát végpont létrehozása – alapismeretek lap](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Az **erőforrás** oldalon hajtsa végre az alábbi lépéseket:
    1. A kapcsolódási módszer esetén, ha a **címtárban a kapcsolódás Azure-erőforráshoz**lehetőséget választja, kövesse az alábbi lépéseket: 
        1. Válassza ki azt az **Azure-előfizetést** , amelyben a **Event Hubs névtere** létezik. 
        2. Az **erőforrástípus**mezőben válassza ki a **Microsoft. EventHub/Namespaces** elemet az **erőforrás típushoz**.
        3. Az **erőforrás**mezőben válasszon ki egy Event Hubs névteret a legördülő listából. 
        4. Győződjön meg arról, hogy a **cél alerőforrás** a **névtérre**van beállítva.
        5. Válassza a **Tovább: konfigurációs >** gombot az oldal alján. 
        
            ![Privát végpont létrehozása – Erőforrás lap](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Ha a **Kapcsolódás Azure-erőforráshoz erőforrás-azonosító vagy alias alapján**lehetőséget választja, kövesse az alábbi lépéseket:
        1. Adja meg az **erőforrás-azonosítót** vagy az **aliast**. Ez lehet az az erőforrás-azonosító vagy alias, amelyet valaki megosztott Önnel. Az erőforrás-azonosító beszerzésének legegyszerűbb módja, ha a Azure Portal Event Hubs névterére navigál, és az URI részét másolja a-től `/subscriptions/`kezdődően. Példaként tekintse meg az alábbi ábrát. 
        2. A **cél alerőforrásnál**adja meg a **névteret**. Ez a saját végpont által elérhető alerőforrás típusa.
        3. választható Adja meg a **kérelem üzenetét**. Az erőforrás tulajdonosa látja ezt az üzenetet a privát végponti kapcsolatok kezelése során.
        4. Ezután válassza a **Tovább: konfigurációs >** gombot az oldal alján.

            ![Privát végpont létrehozása – kapcsolat az erőforrás-AZONOSÍTÓval](./media/private-link-service/connect-resource-id.png)
9. A **konfiguráció** lapon válassza ki azt az alhálózatot egy virtuális hálózaton, ahová a magánhálózati végpontot telepíteni szeretné. 
    1. Válasszon ki egy **virtuális hálózatot**. A legördülő listában csak a jelenleg kijelölt előfizetésben és helyen lévő virtuális hálózatok jelennek meg. 
    2. Válasszon egy **alhálózatot** a kiválasztott virtuális hálózatban. 
    3. Válassza a **Tovább: címkék >** gombot az oldal alján. 

        ![Privát végpont létrehozása – konfigurációs lap](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. A **címkék** lapon hozzon létre minden olyan címkét (nevet és értéket), amelyet hozzá szeretne rendelni a privát végpont-erőforráshoz. Ezután kattintson az oldal alján található **felülvizsgálat + létrehozás** gombra. 
11. A **felülvizsgálat + létrehozás**lapon tekintse át az összes beállítást, majd kattintson a **Létrehozás** elemre a privát végpont létrehozásához.
    
    ![Privát végpont létrehozása – oldal áttekintése és létrehozása](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Ellenőrizze, hogy megjelenik-e a létrehozott privát végponti kapcsolatok a végpontok listájában. Ebben a példában a privát végpontot automatikusan jóváhagyjuk, mert egy Azure-erőforráshoz kapcsolódott a címtárban, és rendelkezik a megfelelő engedélyekkel. 

    ![Saját végpont létrehozva](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Privát végpont hozzáadása a PowerShell használatával
Az alábbi példa bemutatja, hogyan használható a Azure PowerShell egy privát végponti kapcsolatok létrehozásához. Nem hoz létre dedikált fürtöt. A [cikkben](event-hubs-dedicated-cluster-create-portal.md) ismertetett lépéseket követve hozzon létre egy dedikált Event Hubs-fürtöt. 

```azurepowershell-interactive
# create resource group

$rgName = "<RESOURCE GROUP NAME>"
$vnetlocation = "<VIRTUAL NETWORK LOCATION>"
$vnetName = "<VIRTUAL NETWORK NAME>"
$subnetName = "<SUBNET NAME>"
$namespaceLocation = "<NAMESPACE LOCATION>"
$namespaceName = "<NAMESPACE NAME>"
$peConnectionName = "<PRIVATE ENDPOINT CONNECTION NAME>"

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName $rgName `
                    -Location $vnetlocation `
                    -Name $vnetName `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name $subnetName `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork

# create an event hubs namespace in a dedicated cluster
$namespaceResource = New-AzResource -Location $namespaceLocation `
                                    -ResourceName $namespaceName `
                                    -ResourceGroupName $rgName `
                                    -Sku @{name = "Standard"; capacity = 1} `
                                    -Properties @{clusterArmId = "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/clusters/<EVENT HUBS CLUSTER NAME>"} `
                                    -ResourceType "Microsoft.EventHub/namespaces" -ApiVersion "2018-01-01-preview"

# create private endpoint connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name $peConnectionName `
                                -PrivateLinkServiceId $namespaceResource.ResourceId `
                                -GroupId "namespace"

# get subnet object that you will use later
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  $rgName -Name $vnetName
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                                | Where-Object  {$_.Name -eq $subnetName}  
   
# create a private endpoint   
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName  `
                                -Name $vnetName   `
                                -Location $vnetlocation `
                                -Subnet  $subnet   `
                                -PrivateLinkServiceConnection $privateEndpointConnection

(Get-AzResource -ResourceId $namespaceResource.ResourceId -ExpandProperties).Properties


```

### <a name="configure-the-private-dns-zone"></a>A magánhálózati DNS-zóna konfigurálása
Hozzon létre egy magánhálózati DNS-zónát Event Hubs tartományhoz, és hozzon létre egy társítási hivatkozást a virtuális hálózattal:

```azurepowershell-interactive
$zone = New-AzPrivateDnsZone -ResourceGroupName $rgName `
                            -Name "privatelink.servicebus.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rgName `
                                            -ZoneName "privatelink.servicebus.windows.net" `
                                            -Name "mylink" `
                                            -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
    foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
        Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
        $recordName = $fqdn.split('.',2)[0] 
        $dnsZone = $fqdn.split('.',2)[1] 
        New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.servicebus.windows.net"  `
                                -ResourceGroupName $rgName -Ttl 600 `
                                -PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
    } 
}
```

## <a name="manage-private-endpoints-using-azure-portal"></a>Privát végpontok kezelése Azure Portal használatával

Privát végpont létrehozásakor jóvá kell hagyni a kapcsolódást. Ha az erőforrás, amelyhez privát végpontot hoz létre, akkor jóváhagyhatja a megfelelő engedélyekkel rendelkező kapcsolatkérelem-kérést. Ha egy másik címtárban található Azure-erőforráshoz csatlakozik, meg kell várnia, hogy az erőforrás tulajdonosa jóváhagyja a kapcsolódási kérelmet.

Négy kiépítési állapot létezik:

| Szolgáltatási művelet | A szolgáltatás fogyasztói magánhálózati végpontjának állapota | Leírás |
|--|--|--|
| None | Függőben | A kapcsolat manuálisan lett létrehozva, és jóváhagyásra vár a Private link erőforrás-tulajdonostól. |
| Jóváhagyás | Approved | A kapcsolódás automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A magánhálózati kapcsolat erőforrásának tulajdonosa elutasította a kapcsolatot. |
| Eltávolítás | Leválasztott | A kapcsolatot a privát kapcsolat erőforrás-tulajdonosa eltávolította, a magánhálózati végpont informatív lesz, és törölni kell a tisztításhoz. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Privát végponti kapcsolatok jóváhagyása, elutasítása vagy eltávolítása

1. Jelentkezzen be az Azure portálra.
2. A keresősáv mezőbe írja be az **Event hubok**kifejezést.
3. Válassza ki a kezelni kívánt **névteret** .
4. Válassza a **hálózatkezelés** lapot.
5. Nyissa meg az alábbi megfelelő szakaszt a kívánt művelet alapján: jóváhagyás, elutasítás vagy eltávolítás.

### <a name="approve-a-private-endpoint-connection"></a>Privát végponti kapcsolatok jóváhagyása
1. Ha van függőben lévő kapcsolat, a kiépítési állapotban **függőben lévő** kapcsolat jelenik meg. 
2. Válassza ki a jóváhagyni kívánt **privát végpontot**
3. Kattintson a **jóváhagyás** gombra.

    ![Kép](./media/private-link-service/approve-private-endpoint.png)
4. A **kapcsolatok jóváhagyása** lapon adjon hozzá egy megjegyzést (nem kötelező), majd válassza az **Igen**lehetőséget. Ha a **nem**lehetőséget választja, semmi nem történik. 
5. A privát végponti kapcsolatok állapota a listában a **jóváhagyott**értékre módosult. 

### <a name="reject-a-private-endpoint-connection"></a>Privát végponti kapcsolatok elutasítása

1. Ha van olyan privát végponti kapcsolat, amelyet el szeretne utasítani, legyen az egy függőben lévő kérelem vagy létező kapcsolat, válassza ki a kapcsolatot, és kattintson az **elutasítás** gombra.

    ![Kép](./media/private-link-service/private-endpoint-reject-button.png)
2. A **kapcsolatok elutasítása** lapon írjon be egy megjegyzést (nem kötelező), majd válassza az **Igen**lehetőséget. Ha a **nem**lehetőséget választja, semmi nem történik. 
3. A privát végponti kapcsolatok állapota a listában **visszautasította**értékre módosult. 

### <a name="remove-a-private-endpoint-connection"></a>Privát végponti kapcsolatok eltávolítása

1. Privát végponti kapcsolatok eltávolításához jelölje ki azt a listában, majd az eszköztáron kattintson az **Eltávolítás** elemre.
2. A **Kapcsolódás törlése** lapon válassza az **Igen** lehetőséget a privát végpont törlésének megerősítéséhez. Ha a **nem**lehetőséget választja, semmi nem történik.
3. A **kapcsolat megszakadt**állapotra módosult. Ezután megjelenik a végpont a listából.

## <a name="validate-that-the-private-link-connection-works"></a>Annak ellenőrzése, hogy a magánhálózati kapcsolat működik-e

Győződjön meg arról, hogy a magánhálózati végpont erőforrásának ugyanazon alhálózatán belüli erőforrásai csatlakoznak a Event Hubs névtérhez egy magánhálózati IP-címen keresztül, valamint a saját DNS-zónák megfelelő integrálását.

Először hozzon létre egy virtuális gépet a [Windows rendszerű virtuális gép létrehozása a Azure Portalban](../virtual-machines/windows/quick-create-portal.md) című témakör lépéseit követve.

A **hálózatkezelés** lapon:

1. Határozza meg a **virtuális hálózatot** és az **alhálózatot**. Létrehozhat egy új virtuális hálózatot, vagy kijelölhet egy meglévőt is. Ha egy meglévőt választ ki, győződjön meg arról, hogy a régió megfelel.
1. **Nyilvános IP-** erőforrást kell megadni.
1. A **NIC hálózati biztonsági csoportban**válassza a **nincs**lehetőséget.
1. A **terheléselosztás**területen válassza a **nem**lehetőséget.

Nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Ha az NS lookup parancs futtatásával oldja fel egy Event Hubs névtér IP-címét egy nyilvános végponton keresztül, a következőhöz hasonló eredményt fog látni:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Ha az NS lookup parancs futtatásával oldja fel egy Event Hubs névtér IP-címét egy privát végponton keresztül, a következőhöz hasonló eredményt fog látni:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Korlátozások és kialakítási szempontok

**Díjszabás**: díjszabási információkért tekintse meg az [Azure Private link díjszabását](https://azure.microsoft.com/pricing/details/private-link/).

**Korlátozások**: az Azure Event Hubs magánhálózati végpontja nyilvános előzetes verzióban érhető el. Ez a funkció az összes Azure-beli nyilvános régióban elérhető.

**Privát végpontok maximális száma Event Hubs névtérben**: 120.

További információ [: Azure Private link Service: korlátozások](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>További lépések

- További információ az [Azure Private linkről](../private-link/private-link-service-overview.md)
- További információ az [Azure Event Hubs](event-hubs-about.md)

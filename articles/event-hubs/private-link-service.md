---
title: Az Azure Event Hubs integrálása az Azure Private Link szolgáltatással
description: Ismerje meg, hogyan integrálható az Azure Event Hubs az Azure Private Link szolgáltatással
services: event-hubs
author: spelluru
ms.author: spelluru
ms.date: 03/12/2020
ms.service: event-hubs
ms.topic: article
ms.openlocfilehash: cff1b3b79b34d3f0bed27a2ea50799185958a8ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477849"
---
# <a name="integrate-azure-event-hubs-with-azure-private-link-preview"></a>Az Azure Event Hubs integrálása az Azure Private Link (előzetes verzió) szolgáltatással
Az Azure Private Link Service lehetővé teszi az Azure-szolgáltatások (például az Azure Event Hubs, az Azure Storage és az Azure Cosmos DB) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások elérését a virtuális hálózat **egy privát végpontján** keresztül.

A privát végpont egy hálózati adapter, amely privát és biztonságos kapcsolatot biztosít az Azure Private Link által működtetett szolgáltatással. A privát végpont egy privát IP-címet használ a virtuális hálózatból, hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatáshoz irányuló összes forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Csatlakozhat egy Azure-erőforrás egy példányához, így a legmagasabb szintű részletességet biztosíthatja a hozzáférés-vezérlésben.

További információ: [Mi az Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Ez a funkció csak a **dedikált** szint támogatott. A dedikált szinttel kapcsolatos további információkért [lásd: Dedikált eseményközpontok áttekintése.](event-hubs-dedicated-overview.md) 
>
> Ez a funkció jelenleg **előzetes verzióban érhető el.** 


## <a name="add-a-private-endpoint-using-azure-portal"></a>Privát végpont hozzáadása az Azure Portalhasználatával

### <a name="prerequisites"></a>Előfeltételek

Ha integrálni szeretne egy Event Hubs-névteret az Azure Private Linkkel, a következő entitásokra vagy engedélyekre van szüksége:

- Eseményközpontok névtere.
- Egy Azure virtuális hálózat.
- A virtuális hálózat alhálózata.
- Tulajdonosi vagy közreműködői engedélyek a névtérhez és a virtuális hálózathoz egyaránt.

A privát végpontnak és a virtuális hálózatnak ugyanabban a régióban kell lennie. Amikor kiválaszt egy régiót a privát végponthoz a portál használatával, automatikusan csak az adott régióban lévő virtuális hálózatokat szűri. A névtér lehet egy másik régióban.

A privát végpont egy privát IP-címet használ a virtuális hálózatban.

### <a name="steps"></a>Lépések
Ha már rendelkezik Eseményközpont-névtérrel, az alábbi lépésekkel hozhat létre privát kapcsolatkapcsolatot:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
2. A keresősávba írja be az **eseményközpontokat**.
3. Jelölje ki azt a **névteret** a listából, amelyhez saját végpontot szeretne hozzáadni.
4. Válassza a **Hálózat** lapot a **Beállítások csoportban.**
5. Válassza a lap tetején található **Privát végpontkapcsolatok (előnézet)** lapot. Ha nem az Event Hubs dedikált rétegét használja, megjelenik egy üzenet: **Az Event Hubs privát végpontkapcsolatait csak egy dedikált fürt ben létrehozott névterek támogatják.**
6. A lap tetején kattintson a **+ Privát végpont** gombra.

    ![Kép](./media/private-link-service/private-link-service-3.png)
7. Az Alapok lapon **hajtsa** végre az alábbi lépéseket: 
    1. Válassza ki azt az **Azure-előfizetést,** amelyben létre szeretné hozni a privát végpontot. 
    2. Válassza ki a magánvégpont-erőforrás **erőforráscsoportját.**
    3. Adja meg a magánvégpont **nevét.** 
    5. Válasszon ki egy **régiót** a privát végponthoz. A privát végpontnak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, de lehet egy másik régióban, amelyben a magánkapcsolati erőforrás, amelyhez csatlakozik. 
    6. Válassza a **Tovább: Erőforrás >** gombot a lap alján.

        ![Saját végpont létrehozása – Alapok lap](./media/private-link-service/create-private-endpoint-basics-page.png)
8. Az **Erőforrás** lapon hajtsa végre az alábbi lépéseket:
    1. A csatlakozási módszer esetében, ha a **Csatlakozás egy Azure-erőforráshoz lehetőséget választja a címtárban,** kövesse az alábbi lépéseket: 
        1. Válassza ki azt az **Azure-előfizetést,** amelyben az **Event Hubs névtér** létezik. 
        2. Az **Erőforrás típus esetén**válassza a **Microsoft.EventHub/namespaces** elemet az **Erőforrás típushoz.**
        3. Az **Erőforrás listában**válasszon ki egy Eseményközpontok névteret a legördülő listából. 
        4. Ellenőrizze, hogy a **Cél alerőforrás** **névtérre van-e**állítva.
        5. Válassza a **Tovább: A >** gomb a lap alján lehetőséget. 
        
            ![Saját végpont létrehozása – Erőforrás lap](./media/private-link-service/create-private-endpoint-resource-page.png)    
    2. Ha a **Csatlakozás egy Azure-erőforráshoz erőforrás-azonosító vagy alias szerint lehetőséget választja,** kövesse az alábbi lépéseket:
        1. Adja meg az **erőforrás-azonosítót** vagy **az aliast.** Ez lehet az erőforrás-azonosító vagy alias, amelyet néhány megosztott Önnel.
        2. A **Cél alerőforrás**mezőbe írja be a **névteret.** Ez az al-erőforrás típusa, amely a privát végpont férhet hozzá.
        3. (nem kötelező) Adjon meg egy **kérésüzenetet**. Az erőforrás-tulajdonos látja ezt az üzenetet a privát végpontkapcsolat kezelése közben.
        4. Ezután válassza a **Tovább: Konfigurációs >** gombot a lap alján.

            ![Saját végpont létrehozása – Csatlakozás erőforrás-azonosító használatával](./media/private-link-service/connect-resource-id.png)
9. A **Konfiguráció lapon** kijelöli az alhálózatot egy virtuális hálózatban oda, ahol telepíteni szeretné a saját végpontot. 
    1. Válasszon **egy virtuális hálózatot**. Csak az aktuálisan kiválasztott előfizetésben és helyen lévő virtuális hálózatok jelennek meg a legördülő listában. 
    2. Jelöljön ki egy **alhálózatot** a kiválasztott virtuális hálózatban. 
    3. Válassza a **Tovább: Címkék >** gomb baloldalán. 

        ![Saját végpont létrehozása – Konfiguráció lap](./media/private-link-service/create-private-endpoint-configuration-page.png)
10. A **Címkék** lapon hozzon létre olyan címkéket (neveket és értékeket), amelyeket a magánvégpont-erőforráshoz társítani szeretne. Ezután válassza **a Véleményezés + létrehozás** gombot az oldal alján. 
11. A **Véleményezés + létrehozás**párbeszédpanelen tekintse át az összes beállítást, és válassza a **Létrehozás** gombot a saját végpont létrehozásához.
    
    ![Saját végpont létrehozása – Véleményezés és létrehozás lap](./media/private-link-service/create-private-endpoint-review-create-page.png)
12. Ellenőrizze, hogy a létrehozott privát végpont-kapcsolat megjelenik-e a végpontok listájában. Ebben a példában a privát végpont automatikusan jóváhagyott, mert csatlakozik egy Azure-erőforrás a címtárban, és rendelkezik a megfelelő engedélyekkel. 

    ![Létrehozott privát végpont](./media/private-link-service/private-endpoint-created.png)

## <a name="add-a-private-endpoint-using-powershell"></a>Privát végpont hozzáadása a PowerShell használatával
A következő példa bemutatja, hogyan azure PowerShell segítségével hozzon létre egy privát végpont-kapcsolat. Nem hoz létre dedikált fürtöt az Ön számára. A [cikk](event-hubs-dedicated-cluster-create-portal.md) lépéseit követve hozzon létre egy dedikált Eseményközpontok-fürtöt. 

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

## <a name="manage-private-endpoints-using-azure-portal"></a>Privát végpontok kezelése az Azure Portal használatával

Privát végpont létrehozásakor a kapcsolatot jóvá kell hagyni. Ha az erőforrás, amelyhez privát végpontot hoz létre, a címtárban található, jóváhagyhatja a csatlakozási kérelmet, feltéve, hogy rendelkezik a megfelelő engedélyekkel. Ha egy másik címtárban csatlakozik egy Azure-erőforráshoz, meg kell várnia, amíg az erőforrás tulajdonosa jóváhagyja a csatlakozási kérelmet.

Négy kiépítési állapot létezik:

| Szolgáltatási művelet | Szolgáltatás fogyasztói privát végpont állapota | Leírás |
|--|--|--|
| None | Függőben | A kapcsolat manuálisan jön létre, és a Magánkapcsolat erőforrás tulajdonosának jóváhagyása vár. |
| Jóváhagyás | Approved | A kapcsolat automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A kapcsolatot a magánkapcsolati erőforrás tulajdonosa elutasította. |
| Eltávolítás | Elválasztott | A kapcsolatot a magánkapcsolati erőforrás tulajdonosa eltávolította, a privát végpont informatívsá válik, és törlésre kell törölni. |
 
###  <a name="approve-reject-or-remove-a-private-endpoint-connection"></a>Privát végpontkapcsolat jóváhagyása, elutasítása vagy eltávolítása

1. Jelentkezzen be az Azure portálra.
2. A keresősávba írja be az **eseményközpontokat**.
3. Jelölje ki a kezelni kívánt **névteret.**
4. Válassza a **Hálózat** lapot.
5. Nyissa meg az alábbi megfelelő szakaszt a jóváhagyni, elutasítani vagy eltávolítani kívánt művelet alapján.

### <a name="approve-a-private-endpoint-connection"></a>Privát végpontkapcsolat jóváhagyása
1. Ha vannak függőben lévő kapcsolatok, a **létesítési** állapotban a Függő ben felsorolt kapcsolat jelenik meg. 
2. Válassza ki a jóváhagyni kívánt **privát végpontot.**
3. Válassza a **Jóváhagyás** gombot.

    ![Kép](./media/private-link-service/approve-private-endpoint.png)
4. A **Kapcsolat jóváhagyása** lapon adjon hozzá megjegyzést (nem kötelező), és válassza az **Igen**lehetőséget. Ha a **Nem**lehetőséget választja, semmi sem történik. 
5. A privát végpontkapcsolat állapotát a listában jóvá változtatva kell **látnia.** 

### <a name="reject-a-private-endpoint-connection"></a>Privát végpontkapcsolat elutasítása

1. Ha vannak olyan privát végpontkapcsolatok, amelyeket el szeretne utasítani, függetlenül attól, hogy függőben lévő kérelemről vagy meglévő kapcsolatról van-e szó, jelölje ki a kapcsolatot, és kattintson az **Elutasítás** gombra.

    ![Kép](./media/private-link-service/private-endpoint-reject-button.png)
2. A **Kapcsolat elvetése** lapon írjon be egy megjegyzést (nem kötelező), és válassza az **Igen**lehetőséget. Ha a **Nem**lehetőséget választja, semmi sem történik. 
3. A privát végpontkapcsolat állapotát az **Elutasított**névre módosított listában kell látnia. 

### <a name="remove-a-private-endpoint-connection"></a>Privát végpontkapcsolat eltávolítása

1. Ha el szeretne távolítani egy privát végpontkapcsolatot, jelölje ki azt a listában, és válassza az **eszköztár Eltávolítás parancsát.**
2. A **Kapcsolat törlése** lapon válassza az **Igen** lehetőséget a privát végpont törlésének megerősítéséhez. Ha a **Nem**lehetőséget választja, semmi sem történik.
3. Az állapot nak leválasztva állapotra kell **változotta.** Ezután látni fogja, hogy a végpont eltűnik a listáról.

## <a name="validate-that-the-private-link-connection-works"></a>Annak ellenőrzése, hogy a privát kapcsolat kapcsolata működik-e

Ellenőrizze, hogy a magánvégpont-erőforrás ugyanazon alhálózatán belüli erőforrások privát IP-címen keresztül csatlakoznak-e az Event Hubs névtérhez, és hogy rendelkeznek-e a megfelelő privát DNS-zóna-integrációval.

Először hozzon létre egy virtuális gépet a Windows virtuális gép létrehozása az [Azure Portalon](../virtual-machines/windows/quick-create-portal.md) című csoportban leírt lépések végrehajtásával

A **Hálózat** lapon:

1. Adja meg **a virtuális hálózatot** és **az alhálózatot.** Létrehozhat egy új virtuális hálózatot, vagy kiválaszthat egy meglévőt. Ha egy meglévőt választ, győződjön meg arról, hogy a régió egyezik.If selecting an existing one, make sure the region matches.
1. Adjon meg egy **nyilvános IP-erőforrást.**
1. A **hálózati adapter hálózati biztonsági csoportjában**válassza a **Nincs**lehetőséget.
1. A **Terheléselosztás**ban válassza a **Nem**lehetőséget.

Nyissa meg a parancssort, és futtassa a következő parancsot:

```console
nslookup <your-event-hubs-namespace-name>.servicebus.windows.net
```

Ha az ns-lookup paranccsal oldja fel egy Event Hubs névtér IP-címét egy nyilvános végponton keresztül, a következő höz hasonló eredmény jelenik meg:

```console
c:\ >nslookup <your-event-hubs-namespae-name>.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  (public IP address)
Aliases:  <your-event-hubs-namespace-name>.servicebus.windows.net
```

Ha az ns-lookup paranccsal oldja fel egy Event Hubs névtér IP-címét egy privát végponton keresztül, a következő höz hasonló eredmény jelenik meg:

```console
c:\ >nslookup your_event-hubs-namespace-name.servicebus.windows.net

Non-authoritative answer:
Name:    
Address:  10.1.0.5 (private IP address)
Aliases:  <your-event-hub-name>.servicebus.windows.net
```

## <a name="limitations-and-design-considerations"></a>Korlátozások és tervezési szempontok

**Díjszabás:** A díjszabási információkért lásd: [Azure Private Link díjszabása.](https://azure.microsoft.com/pricing/details/private-link/)

**Korlátozások:** Az Azure Event Hubs privát végpontja nyilvános előzetes verzióban érhető el. Ez a funkció az Azure összes nyilvános régiójában elérhető.

**A privát végpontok maximális száma eseményközpontok névterében:** 120.

További információkért lásd: [Azure Private Link szolgáltatás: Korlátozások](../private-link/private-link-service-overview.md#limitations)

## <a name="next-steps"></a>További lépések

- További információ az [Azure Private Linkről](../private-link/private-link-service-overview.md)
- További információ az [Azure Event Hubs-ról](event-hubs-about.md)

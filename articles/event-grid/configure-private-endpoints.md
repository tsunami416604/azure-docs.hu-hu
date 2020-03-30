---
title: Privát végpontok konfigurálása Azure Event Grid-témakörökhöz vagy tartományokhoz
description: Ez a cikk ismerteti, hogyan konfigurálhatja a privát végpontok az Azure Event Grid-témakörök vagy a tartomány.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: d08afe00c13a3f96b9526c3cb29804cfad688ddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299906"
---
# <a name="configure-private-endpoints-for-azure-event-grid-topics-or-domains-preview"></a>Privát végpontok konfigurálása Azure Event Grid-témakörökhöz vagy tartományokhoz (előzetes verzió)
[A privát végpontok](../private-link/private-endpoint-overview.md) használatával engedélyezheti az események közvetlenül a virtuális hálózatról a témakörökbe és tartományokba biztonságosan egy [privát kapcsolaton](../private-link/private-link-overview.md) keresztül, anélkül, hogy a nyilvános interneten keresztül haladna. A privát végpont a témakör vagy tartomány virtuális hálózat címterületéről származó IP-címet használ. További általános tudnivalókért lásd: [Hálózatbiztonság](network-security.md).

Ez a cikk ismerteti, hogyan konfigurálhatja a magánvégpontok témakörökhöz vagy tartományokhoz.

> [!IMPORTANT]
> A privát végpontok szolgáltatás csak a prémium szintű témakörökhöz és tartományokhoz érhető el. Az alapszintű szintről a prémium szintre való frissítéshez tekintse meg a [Tarifacsomag frissítése](update-tier.md) cikket. 

## <a name="use-azure-portal"></a>Az Azure Portal használata 
Ez a szakasz bemutatja, hogyan használhatja az Azure Portalon egy témakör vagy egy tartomány privát végpontjának létrehozásához.

> [!NOTE]
> Az ebben a szakaszban látható lépések többnyire témakörök. Hasonló lépésekkel privát végpontokat hozhat létre a **tartományokhoz.** 

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg a témakört vagy tartományt.
2. Váltson a témakörlap **Hálózat lapjára.** Válassza a **+ Privát végpont lehetőséget** az eszköztáron.

    ![Privát végpont hozzáadása](./media/configure-private-endpoints/add-button.png)
2. Az **alapok** lap az alábbi lépésekkel: 
    1. Válasszon ki egy **Azure-előfizetést,** amelyben létre szeretné hozni a saját végpontot. 
    2. Válasszon ki egy **Azure-erőforráscsoportot** a privát végponthoz. 
    3. Adja meg a végpont **nevét.** 
    4. Válassza ki a végpont **régióját.** A privát végpontnak ugyanabban a régióban kell lennie, mint a virtuális hálózatnak, de a magánkapcsolati erőforrástól eltérő régióban (ebben a példában egy eseményrács-témakörben). 
    5. Ezután válassza a **Tovább: Erőforrás >** gombot a lap alján. 

      ![Saját végpont - alapok lap](./media/configure-private-endpoints/basics-page.png)
3. Az **Erőforrás** lapon hajtsa végre az alábbi lépéseket: 
    1. A csatlakozási módszer, ha a **Csatlakozás egy Azure-erőforráshoz a címtárban,** kövesse az alábbi lépéseket. Ez a példa bemutatja, hogyan csatlakozhat egy Azure-erőforrása a címtárban. 
        1. Válassza ki azt az **Azure-előfizetést,** amelyben a **témakör/tartomány** létezik. 
        1. Az **Erőforrás típus esetén**válassza a **Microsoft.EventGrid/topics** vagy a **Microsoft.EventGrid/domains** lehetőséget az **Erőforrás típushoz.**
        2. Az **Erőforrás**listában válasszon ki egy témakört/tartományt a legördülő listából. 
        3. Ellenőrizze, hogy a **Cél alerőforrás** **témakörre** vagy **tartományra** van-e állítva (a kiválasztott erőforrástípus alapján).    
        4. Válassza a **Tovább: A >** gomb a lap alján lehetőséget. 

            ![Saját végpont - erőforráslap](./media/configure-private-endpoints/resource-page.png)
    2. Ha a **Csatlakozás erőforráshoz erőforrásazonosítóval vagy aliassal lehetőséget választja,** kövesse az alábbi lépéseket:
        1. Adja meg az erőforrás azonosítóját. Például: `/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>`.  
        2. Az **Erőforrás**mezőbe írja be a **témakört** vagy **a tartományt.** 
        3. (nem kötelező) Kérésüzenet hozzáadása. 
        4. Válassza a **Tovább: A >** gomb a lap alján lehetőséget. 

            ![Saját végpont - erőforráslap](./media/configure-private-endpoints/connect-azure-resource-id.png)
4. A **Konfiguráció lapon** kijelöli az alhálózatot egy virtuális hálózatban oda, ahol telepíteni szeretné a saját végpontot. 
    1. Válasszon **egy virtuális hálózatot**. Csak az aktuálisan kiválasztott előfizetésben és helyen lévő virtuális hálózatok jelennek meg a legördülő listában. 
    2. Jelöljön ki egy **alhálózatot** a kiválasztott virtuális hálózatban. 
    3. Válassza a **Tovább: Címkék >** gomb baloldalán. 

    ![Saját végpont - konfigurációs lap](./media/configure-private-endpoints/configuration-page.png)
5. A **Címkék** lapon hozzon létre olyan címkéket (neveket és értékeket), amelyeket a magánvégpont-erőforráshoz társítani szeretne. Ezután válassza **a Véleményezés + létrehozás** gombot az oldal alján. 
6. A **Véleményezés + létrehozás**párbeszédpanelen tekintse át az összes beállítást, és válassza a **Létrehozás** gombot a saját végpont létrehozásához. 

    ![Saját végpont – tekintse át & hozzon létre lapot](./media/configure-private-endpoints/review-create-page.png)
    

## <a name="manage-private-link-connection"></a>Privát kapcsolat kezelése

Privát végpont létrehozásakor a kapcsolatot jóvá kell hagyni. Ha az erőforrás, amelyhez privát végpontot hoz létre, a címtárban található, jóváhagyhatja a csatlakozási kérelmet, feltéve, hogy rendelkezik a megfelelő engedélyekkel. Ha egy másik címtárban csatlakozik egy Azure-erőforráshoz, meg kell várnia, amíg az erőforrás tulajdonosa jóváhagyja a csatlakozási kérelmet.

Négy kiépítési állapot létezik:

| Szolgáltatási művelet | Szolgáltatás fogyasztói privát végpont állapota | Leírás |
|--|--|--|
| None | Függőben | A kapcsolat manuálisan jön létre, és a személyes hivatkozás erőforrás tulajdonosának jóváhagyása vár. |
| Jóváhagyás | Approved | A kapcsolat automatikusan vagy manuálisan lett jóváhagyva, és készen áll a használatra. |
| Elutasítás | Elutasítva | A kapcsolatot a magánkapcsolati erőforrás tulajdonosa elutasította. |
| Eltávolítás | Elválasztott | A kapcsolatot a magánkapcsolati erőforrás tulajdonosa eltávolította, a privát végpont informatívsá válik, és törlésre kell törölni. |
 
###  <a name="how-to-manage-a-private-endpoint-connection"></a>Privát végpontkapcsolat kezelése
A következő szakaszok bemutatják, hogyan lehet jóváhagyni vagy elutasítani egy privát végponti kapcsolatot. 

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
1. A keresősávba írja be az **Eseményrács témaköreit** vagy **az Eseményrács tartományait.**
1. Jelölje ki a kezelni kívánt **témakört** vagy **tartományt.**
1. Válassza a **Hálózat** lapot.
1. Ha vannak függőben lévő kapcsolatok, a létesítési állapotban a **Függő** ben felsorolt kapcsolat jelenik meg. 

### <a name="to-approve-a-private-endpoint"></a>Privát végpont jóváhagyása
Jóváhagyhat egy privát végpontot, amely a függőállapotban van. A jóváhagyáshoz kövesse az alábbi lépéseket: 

> [!NOTE]
> Az ebben a szakaszban látható lépések többnyire témakörök. Hasonló lépésekkel jóváhagyhatja a tartományok privát **végpontjait.** 

1. Jelölje ki a jóváhagyni kívánt **privát végpontot,** és az eszköztáron válassza a **Jóváhagyás** lehetőséget.

    ![Privát végpont - függőállapot](./media/configure-private-endpoints/pending.png)
1. A **Kapcsolat jóváhagyása** párbeszédpanelen írjon be egy megjegyzést (nem kötelező), és válassza az **Igen**lehetőséget. 

    ![Privát végpont - jóváhagyás](./media/configure-private-endpoints/approve.png)
1. Ellenőrizze, hogy a végpont állapota **Jóváhagyva**állapotú.Confirm that you see the status of the endpoint as Approved . 

    ![Privát végpont - jóváhagyott állapot](./media/configure-private-endpoints/approved-status.png)

### <a name="to-reject-a-private-endpoint"></a>Privát végpont elutasítása
Elutasíthatja a függőben lévő vagy jóváhagyott állapotban lévő privát végpontot. Az elutasításhoz hajtsa végre az alábbi lépéseket: 

> [!NOTE]
> Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel elutasíthatja a tartományok privát **végpontjait.** 

1. Jelölje ki az elutasítandó **magánvégpontot,** és az eszköztáron válassza az **Elutasítás** gombot.

    ![Privát végpont - elutasítás](./media/configure-private-endpoints/reject-button.png)
1. A **Kapcsolat elvetése** párbeszédpanelen írjon be egy megjegyzést (nem kötelező), és válassza az **Igen**lehetőséget. 

    ![Privát végpont - elutasítás](./media/configure-private-endpoints/reject.png)
1. Ellenőrizze, hogy a végpont állapota Elutasított állapotú.Confirm that you see the status of the endpoint as **Rejected**. 

    ![Privát végpont - elutasított állapot](./media/configure-private-endpoints/rejected-status.png)

    > [!NOTE]
    > Az Azure Portalon nem hagyhat jóvá egy privát végpontot, miután elutasították. 


## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Privát végpont létrehozásához használja az [az hálózati magánvégpont-létrehozási](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) módszert az alábbi példában látható módon:

```azurecli-interactive
az network private-endpoint create \
    --resource-group <RESOURECE GROUP NAME> \
    --name <PRIVATE ENDPOINT NAME> \
    --vnet-name <VIRTUAL NETWORK NAME> \
    --subnet <SUBNET NAME> \
    --private-connection-resource-id "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME> \
    --connection-name <PRIVATE LINK SERVICE CONNECTION NAME> \
    --location <LOCATION> \
    --group-ids topic
```

A példában használt paraméterek leírását lásd az [az hálózati magánvégpont létrehozásának dokumentációjában.](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) Néhány megjegyzés ebben a példában a következő: 

- A `private-connection-resource-id`esetén adja meg a **témakör** vagy **tartomány**erőforrásazonosítóját. Az előző példa a következő típust használja: témakör.
- a `group-ids`, `topic` `domain`adja meg vagy. Az előző példában a használatban `topic` van. 

Privát végpont törléséhez használja az [az hálózati magánvégpont-törlési](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-delete) módszert a következő példában látható módon:

```azurecli-interactive
az network private-endpoint delete --resource-group <RESOURECE GROUP NAME> --name <PRIVATE ENDPOINT NAME>
```

> [!NOTE]
> Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel privát végpontokat hozhat létre a **tartományokhoz.** 

### <a name="create-a-private-endpoint"></a>Privát végpont létrehozása
Az alábbi Azure-erőforrásokat hozó mintaparancsfájl:

- Erőforráscsoport
- Virtuális hálózat
- Alhálózat a virtuális hálózatban
- Az Azure Event Grid témaköre (prémium szint)
- A témakör privát végpontja

> [!NOTE]
> Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel hozhat létre privát végpontokat a tartományokhoz.

```azurecli-interactive
subscriptionID="<AZURE SUBSCRIPTION ID>"
resourceGroupName="<RESOURCE GROUP NAME>"
location="<LOCATION>"
vNetName="<VIRTUAL NETWORK NAME>"
subNetName="<SUBNET NAME>"
topicName = "<TOPIC NAME>"
connectionName="<ENDPOINT CONNECTION NAME>"
endpointName=<ENDPOINT NAME>

# URI for the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicUri="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>?api-version=2020-04-01-preview"

# resource ID of the topic. replace <SUBSCRIPTION ID>, <RESOURCE GROUP NAME>, and <TOPIC NAME>
topicResourceID="/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<TOPIC NAME>"

# select subscription
az account set --subscription $subscriptionID

# create resource group
az group create --name $resourceGroupName --location $location

# create vnet 
az network vnet create \
    --resource-group $resourceGroupName \
    --name $vNetName \
    --address-prefix 10.0.0.0/16

# create subnet
az network vnet subnet create \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --address-prefixes 10.0.0.0/24

# disable private endpoint network policies for the subnet
az network vnet subnet update \
    --resource-group $resourceGroupName \
    --vnet-name $vNetName \
    --name $subNetName \
    --disable-private-endpoint-network-policies true

# create event grid topic. update <LOCATION>
az rest --method put \
    --uri $topicUri \
    --body "{\""location\"":\""LOCATION\"", \""sku\"": {\""name\"": \""premium\""}, \""properties\"": {\""publicNetworkAccess\"":\""Disabled\""}}"

# verify that the topic was created.
az rest --method get \
    --uri $topicUri

# create private endpoint for the topic you created
az network private-endpoint create 
    --resource-group $resourceGroupName \
    --name $endpointName \
    --vnet-name $vNetName \
    --subnet $subNetName \
    --private-connection-resource-id $topicResourceID \
    --connection-name $connectionName \
    --location $location \
    --group-ids topic

# get topic 
az rest --method get \
    --uri $topicUri

```

### <a name="approve-a-private-endpoint-connection"></a>Privát végpontkapcsolat jóváhagyása
A következő minta CLI-kódrészlet bemutatja, hogyan lehet jóváhagyni egy privát végponti kapcsolatot. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""approved\"",\""description\"":\""connection approved\"", \""actionsRequired\"": \""none\""}}}"
```


### <a name="reject-a-private-endpoint-connection"></a>Privát végpontkapcsolat elutasítása
A következő minta CLI-kódrészlet bemutatja, hogyan utasíthatja el a privát végponti kapcsolatot. 

```azurecli-interactive
az rest --method put --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" --body "{\""properties\"":{\""privateLinkServiceConnectionState\"": {\""status\"":\""rejected\"",\""description\"":\""connection rejected\"", \""actionsRequired\"": \""none\""}}}"
```


## <a name="use-powershell"></a>A PowerShell használata
Ez a szakasz bemutatja, hogyan hozhat létre egy privát végpontot egy témakörhöz vagy tartományhoz a PowerShell használatával. 

### <a name="prerequisite"></a>Előfeltétel
Kövesse [az Útmutató: A portál használatával hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást, amely erőforrásokhoz férhet hozzá](../active-directory/develop/howto-create-service-principal-portal.md) egy Azure Active Directory-alkalmazás létrehozásához, és jegyezze fel a **címtár (bérlői) azonosító,** **az alkalmazás (ügyfél) azonosító**és az alkalmazás **(ügyfél) titkos**értékeinek értékét. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Token és fejlécek előkészítése REST API-hívásokhoz 
Futtassa a következő előfeltételek parancsokat a REST API-hívásokhoz és engedélyezéshez és egyéb fejlécadatokhoz használható hitelesítési jogkivonat lekéréséhez. 

```azurepowershell-interactive
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get authentication token
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token  `
    -Body $body  `
    -ContentType 'application/x-www-form-urlencoded' 

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
```

### <a name="create-a-subnet-with-endpoint-network-policies-disabled"></a>Alhálózat létrehozása letiltott végponthálózati házirendekkel

```azurepowershell-interactive

# create resource group
New-AzResourceGroup -ResourceGroupName <RESOURCE GROUP NAME>  -Location <LOCATION>

# create virtual network
$virtualNetwork = New-AzVirtualNetwork `
                    -ResourceGroupName <RESOURCE GROUP NAME> `
                    -Location <LOCATION> `
                    -Name <VIRTUAL NETWORK NAME> `
                    -AddressPrefix 10.0.0.0/16

# create subnet with endpoint network policy disabled
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
                    -Name example-privatelinksubnet `
                    -AddressPrefix 10.0.0.0/24 `
                    -PrivateEndpointNetworkPoliciesFlag "Disabled" `
                    -VirtualNetwork $virtualNetwork

# update virtual network
$virtualNetwork | Set-AzVirtualNetwork
```

### <a name="create-an-event-grid-topic-with-a-private-endpoint"></a>Eseményrács-témakör létrehozása saját végponttal

> [!NOTE]
> Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel privát végpontokat hozhat létre a **tartományokhoz.** 


```azurepowershell-interactive
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="premium"}; "properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json

# create topic
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $body

# verify that the topic was created
$topic=Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview"   `
    -Headers $Headers  

# create private link service connection
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
                                -Name "<PRIVATE LINK SERVICE CONNECTION NAME>" `
                                -PrivateLinkServiceId $topic.Id `
                                -GroupId "topic"

# get virtual network info 
$virtualNetwork = Get-AzVirtualNetwork  `
                    -ResourceGroupName  <RESOURCE GROUP NAME> `
                    -Name <VIRTUAL NETWORK NAME>

# get subnet info
$subnet = $virtualNetwork | Select -ExpandProperty subnets `
                             | Where-Object  {$_.Name -eq <SUBNET NAME>}  

# now, you are ready to create a private endpoint 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName <RESOURCE GROUP NAME>  `
                                        -Name <PRIVATE ENDPOINT NAME>   `
                                        -Location <LOCATION> `
                                        -Subnet  $subnet   `
                                        -PrivateLinkServiceConnection $privateEndpointConnection

# verify that the endpoint was created
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections?api-version=2020-04-01-preview"   `
    -Headers $Headers   `
    | ConvertTo-Json -Depth 5

```

A végpont létrehozásának ellenőrzésekor az alábbihoz hasonló eredményt kell látnia:

```json

{
  "value": [
    {
      "properties": {
        "privateEndpoint": {
          "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Network/privateEndpoints/<PRIVATE ENDPOINT NAME>"
        },
        "groupIds": [
          "topic"
        ],
        "privateLinkServiceConnectionState": {
          "status": "Approved",
          "description": "Auto-approved",
          "actionsRequired": "None"
        },
        "provisioningState": "Succeeded"
      },
      "id": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>",
      "name": "myConnection",
      "type": "Microsoft.EventGrid/topics/privateEndpointConnections"
    }
  ]
}
```

### <a name="approve-a-private-endpoint-connection"></a>Privát végpontkapcsolat jóváhagyása
A következő minta PowerShell-kódrészlet bemutatja, hogyan lehet jóváhagyni egy privát végpontot. 

> [!NOTE]
> Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel jóváhagyhatja a tartományok privát **végpontjait.** 

```azurepowershell-interactive
$approvedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="approved";"description"="connection approved";"actionsRequired"="none"}}} | ConvertTo-Json

# approve endpoint connection
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AzuRE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $approvedBody

# confirm that the endpoint connection was approved
Invoke-RestMethod -Method 'Get'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers

```

### <a name="reject-a-private-endpoint-connection"></a>Privát végpontkapcsolat elutasítása
A következő példa bemutatja, hogyan utasíthatja el a privát végpont powershell használatával. A privát végpont GUID-ja az előző GET parancs eredményéből szerezhető be. 

> [!NOTE]
> Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel elutasíthatja a tartományok privát **végpontjait.** 


```azurepowershell-interactive
$rejectedBody = @{"properties"=@{"privateLinkServiceConnectionState"=@{"status"="rejected";"description"="connection rejected";"actionsRequired"="none"}}} | ConvertTo-Json

# reject private endpoint
Invoke-RestMethod -Method 'Put'  `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview"  `
    -Headers $Headers  `
    -Body $rejectedBody

# confirm that endpoint was rejected
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>/privateEndpointConnections/<PRIVATE ENDPOINT NAME>.<GUID>?api-version=2020-04-01-preview" ` 
    -Headers $Headers
```

A kapcsolatot akkor is jóváhagyhatja, ha az API-n keresztül elutasították. Ha az Azure Portal, nem hagyhatja jóvá a végpont, amely elutasított. 

## <a name="next-steps"></a>További lépések
Az IP-tűzfal beállításainak konfigurálásáról az [IP-tűzfal konfigurálása az Azure Event Grid témaköreihez vagy tartományaihoz](configure-firewall.md)című témakörben olvashat.
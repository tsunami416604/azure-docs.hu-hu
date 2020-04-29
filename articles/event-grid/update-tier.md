---
title: Azure Event Grid témakör vagy tartomány frissítésének díjszabási szintje
description: Ez a cikk azt ismerteti, hogyan frissíthető egy Azure Event Grid témakör vagy tartomány (alapszintű, prémium és alapszintű) árképzési szintje a Azure Portal, az Azure CLI és a Azure PowerShell használatával.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: caea8d515964510fce432eb0497e3af19ecc1369
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82101637"
---
# <a name="update-pricing-tier"></a>Tarifacsomag frissítése 
Ez a cikk bemutatja, hogyan frissíthet egy Azure Event Grid témakör vagy tartomány díjszabási szintjét Azure Portal, az Azure CLI és a Azure PowerShell használatával. 

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan módosíthatja egy témakör vagy tartomány díjszabási szintjét a Azure Portal. 

### <a name="overview-page"></a>Áttekintő lap
Az **Áttekintés** oldalon módosíthatja egy témakör vagy tartomány díjszabási szintjét. Az alábbi példa bemutatja, hogyan frissíthet egy témakört az alapszintű csomagról a prémium szintre. A prémium szintről az alapszintre való visszalépésre vonatkozó lépések hasonlóak.

1. A [Azure Portal](https://portal.azure.com)navigáljon a témakör vagy a tartomány oldalra. 
2. Az **Áttekintés** lapon válassza ki a jelenlegi árképzési szintet (a következő példában ez az **alapszintű**).
    
    ![Válassza ki a jelenlegi díjszabási szintet](./media/update-tier/select-tier.png)
3. A **díjszabási** csomag lapon módosítsa a szintet, majd kattintson **az OK gombra**. 

    ![A díjszabási csomag frissítése](./media/update-tier/change-tier.png)
4. Az **értesítések** lapon keresse meg a művelet állapotát.

    ![Frissítés állapota](./media/update-tier/status.png)    
5. Győződjön meg arról, hogy a frissített szintet látja az **Áttekintés** oldalon. 

    ![Frissítés állapota](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Hálózatkezelés lap
Az alapszintű csomagról a **hálózati** oldalon található prémium szintre is **frissíthet** . A prémium szintről a lap alapszintű szintjére nem lehet visszaminősíteni. 

1. A [Azure Portal](https://portal.azure.com)navigáljon a témakör vagy a tartomány oldalra. 
2. A **hálózatkezelés** lapon váltson a **Private Endpoint Connections (előzetes verzió)** lapra. 
3. Ha az aktuális árképzési szint **alapszintű**, akkor a következő üzenet jelenik meg. Válassza ki. 

    ![A saját végponti kapcsolatok lapjának frissítési szintje](./media/update-tier/private-endpoint-connections-page.png)
4. A **Premium díjszabási csomag frissítése** lapon válassza az **Igen**lehetőséget. 
    
    ![Frissítés megerősítése](./media/update-tier/message-private-endpoint-connection.png)
5. Az **értesítések** lapon keresse meg a művelet állapotát.

    ![Frissítés állapota](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Ebből a szakaszból megtudhatja, hogyan használhatja az Azure CLI-parancsokat egy témakör árképzési szintjeinek megváltoztatásához. Egy tartomány díjszabási szintjeinek frissítéséhez használja a `az eventgrid domain update` parancsot hasonló módon.

### <a name="prerequisites"></a>Előfeltételek
Frissítse a CLI Azure Event Grid bővítményét a következő parancs futtatásával: 

```azurecli-interactive
az extension update -n eventgrid
```

Ha a bővítmény nincs telepítve, futtassa a következő parancsot a telepítéséhez: 

```azurecli-interactive
az extension add -n eventgrid
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Témakör frissítése alapszintről prémium szintre

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Premium" 
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Témakör visszavonása a prémiumról az alapszintű verzióra

```azurecli-interactive
az eventgrid topic update \
    --resource-group $resourceGroupName \
    --name $topicName \
    --location $location \
    --sku "Basic" 
```


## <a name="use-azure-powershell"></a>Azure PowerShell használatával
Ebből a szakaszból megtudhatja, hogyan módosíthatja a témakör vagy tartomány díjszabási szintjét a PowerShell-parancsok használatával. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Jogkivonatok és fejlécek előkészítése REST API-hívásokhoz 
A következő előfeltétel-utasítások futtatásával szerezzen be egy hitelesítési tokent REST API hívásokkal, valamint az engedélyezési és egyéb fejléc-információkkal való használatra. 

```azurepowershell-interactive
# replace <CLIENT ID> and <CLIENT SECRET>
$body = "grant_type=client_credentials&client_id=<CLIENT ID>&client_secret=<CLIENT SECRET>&resource=https://management.core.windows.net"

# get the authentication token. Replace <TENANT ID>
$Token = Invoke-RestMethod -Method Post `
    -Uri https://login.microsoftonline.com/<TENANT ID>/oauth2/token `
    -Body $body `
    -ContentType 'application/x-www-form-urlencoded'

# set authorization and content-type headers
$Headers = @{}
$Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
$Headers.Add("Content-Type","application/json")
```

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Témakör frissítése alapszintről prémium szintre

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Témakör visszavonása a prémiumról az alapszintű verzióra

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Tartomány frissítése alapszintről prémium szintre

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Tartomány visszaminősítése prémiumról alapra

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>További lépések
A Premium szintű témakörök és tartományok esetében úgy is beállíthatja a privát végpontokat, hogy csak a kiválasztott virtuális hálózatokról korlátozza a hozzáférést. Részletes útmutatásért lásd: [privát végpontok konfigurálása](configure-private-endpoints.md).

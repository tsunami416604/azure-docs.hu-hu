---
title: Azure Event Grid-témakör vagy -tartomány tarifacsomagjának frissítése
description: Ez a cikk ismerteti, hogyan frissítheti az Azure Event Grid-témakör vagy tartomány (alap-prémium, prémium alapszintű) díjszabási csomagjának frissítését az Azure Portal, az Azure CLI és az Azure PowerShell használatával.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: 1e92a9f0b9ba007b289c634995b04f4f1575310a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300140"
---
# <a name="update-pricing-tier"></a>Tarifacsomag frissítése 
Ez a cikk bemutatja, hogyan frissítheti egy Azure Event Grid-témakör vagy tartomány tarifacsomagját az Azure Portal, az Azure CLI és az Azure PowerShell használatával. 

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan módosíthatja egy témakör vagy egy tartomány tarifacsomagját az Azure Portalon. 

### <a name="overview-page"></a>Áttekintő lap
Módosíthatja egy témakör vagy tartomány tarifacsomagját az **Áttekintés** lapon. A következő példa bemutatja, hogyan frissíthet egy témakört az alapszintű szintről a prémium szintre. A prémium szintről az alapszintre való visszaminősítés lépései hasonlóak.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a témakört vagy a tartománylapot. 
2. Az **Áttekintés** lapon válassza ki az aktuális tarifacsomagot (a következő példában **ez az alap.)**
    
    ![Az aktuális tarifacsomag kiválasztása](./media/update-tier/select-tier.png)
3. A **Tarifacsomag** lapon módosítsa a szintet, és válassza az **OK gombot.** 

    ![A tarifacsomag frissítése](./media/update-tier/change-tier.png)
4. Ellenőrizze a művelet állapotát az **Értesítések** lapon.

    ![Frissítés állapota](./media/update-tier/status.png)    
5. Ellenőrizze, hogy a frissített szint megjelenik-e az **Áttekintés** lapon. 

    ![Frissítés állapota](./media/update-tier/changed-tier.png)

### <a name="networking-page"></a>Hálózat lap
**Frissíthet** az alapszintről a prémium szintre a **Hálózati** oldalon. Ezen az oldalon azonban nem lehet leváltani a prémium szintről az alapszintre. 

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a témakört vagy a tartománylapot. 
2. A **Hálózat** lapon váltson a **Privát végpontkapcsolatok (előnézet)** lapra. 
3. Ha az aktuális tarifacsomag **alapvető,** a következő üzenet jelenik meg. Válassza ki. 

    ![Szint frissítése a magánvégfelhasználói kapcsolatok lapon](./media/update-tier/private-endpoint-connections-page.png)
4. A **Prémium tarifacsomag frissítése** lapon válassza az **Igen**lehetőséget. 
    
    ![Frissítés megerősítése](./media/update-tier/message-private-endpoint-connection.png)
5. Ellenőrizze a művelet állapotát az **Értesítések** lapon.

    ![Frissítés állapota](./media/update-tier/status.png)



## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Ez a szakasz bemutatja, hogyan használhatja az Azure CLI-parancsokat egy témakör vagy tartomány tarifacsomagjának módosításához. 

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Téma frissítése alapról prémiumra

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Téma leminősítése prémiumról alapszintűre

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Tartomány frissítése alapszintűről prémiumra

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""premium\""}}"
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Tartomány leminősítése prémiumról alapszintűre

```azurecli-interactive
az rest --method patch \
        --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" \
        --body "{\""sku\"": {\""name\"": \""basic\""}}"
```



## <a name="use-azure-powershell"></a>Azure PowerShell használatával
Ez a szakasz bemutatja, hogyan használhatja a PowerShell-parancsokat egy témakör vagy tartomány tarifacsomagjának módosításához. 

### <a name="prepare-token-and-headers-for-rest-api-calls"></a>Token és fejlécek előkészítése REST API-hívásokhoz 
Futtassa a következő előfeltételek parancsokat a REST API-hívásokhoz, valamint az engedélyezési és egyéb fejlécadatokhoz használható hitelesítési jogkivonat lekéréséhez. 

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

### <a name="upgrade-a-topic-from-basic-to-premium"></a>Téma frissítése alapról prémiumra

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-topic-from-premium-to-basic"></a>Téma leminősítése prémiumról alapszintűre

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="upgrade-a-domain-from-basic-to-premium"></a>Tartomány frissítése alapszintűről prémiumra

```azurepowershell-interactive
$body = @{"sku"=@{"name"="premium"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="downgrade-a-domain-from-premium-to-basic"></a>Tartomány leminősítése prémiumról alapszintűre

```azurepowershell-interactive
$body = @{"sku"=@{"name"="basic"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/domains/<EVENT GRID DOMAIN NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

## <a name="next-steps"></a>További lépések
Prémium szintű témakörök és tartományok esetén beállíthatja a privát végpontokat, hogy csak a kiválasztott virtuális hálózatok hozzáférését korlátozzák. A privát [végpontok konfigurálása](configure-private-endpoints.md)című témakörben talál részletes útmutatást.

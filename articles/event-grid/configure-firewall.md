---
title: IP-tűzfal konfigurálása az Azure Event Grid témaköreihez vagy tartományaihoz (előzetes verzió)
description: Ez a cikk azt ismerteti, hogy miként konfigurálható az Event Grid témaköreihez vagy tartományaihoz.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: b195872ca1002970fa96ae133d5eb47a9267796d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299867"
---
# <a name="configure-ip-firewall-for-azure-event-grid-topics-or-domains-preview"></a>IP-tűzfal konfigurálása az Azure Event Grid témaköreihez vagy tartományaihoz (előzetes verzió)
Alapértelmezés szerint a témakör és a tartomány elérhető az internetről, amíg a kérelem érvényes hitelesítéssel és engedélyezéssel érkezik. Az IP-tűzfal lal tovább korlátozhatja a [CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) jelölésben lévő IPv4-címek vagy IPv4-címtartományok készletére. Bármely más IP-címről származó megjelenítők elutasításra kerülnek, és 403 (Tiltott) választ kapnak. Az Event Grid által támogatott hálózati biztonsági szolgáltatásokról az [Eseményrács hálózatbiztonsága](network-security.md)című témakörben talál további információt.

Ez a cikk ismerteti, hogyan konfigurálhatja az IP-tűzfal beállításait az Azure Event Grid-témakörökhöz vagy tartományokhoz.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Ez a szakasz bemutatja, hogyan használhatja az Azure Portalbejövő IP-tűzfal szabályok létrehozása. Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel hozhat létre bejövő IP-szabályokat a **tartományokhoz.** 

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az eseményrács témakörét vagy tartományát, és váltson a **Hálózat** lapra.
2. Válassza a **Nyilvános hálózatok** lehetőséget, ha azt szeretné, hogy az összes hálózat, beleértve az internetet is, hozzáférjen az erőforráshoz. 

    A forgalmat IP-alapú tűzfalszabályok kal korlátozhatja. Adjon meg egyetlen IPv4-címet vagy IP-címtartományt az osztály nélküli tartományok közötti útválasztás (CIDR) jelölésben. 

    ![Nyilvános hálózatok lap](./media/configure-firewall/public-networks-page.png)
3. Válassza csak **a Privát végpontok** lehetőséget, ha csak a magánhálózati végpontkapcsolatok számára szeretné engedélyezni az erőforrás elérését. A kapcsolatok kezeléséhez használja a lap **Privát végpontkapcsolatok** lapját. 

    ![Nyilvános hálózatok lap](./media/configure-firewall/private-endpoints-page.png)
4. Válassza az eszköztár **Save** (Mentés) elemét. 



## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával
Ez a szakasz bemutatja, hogyan azure CLI-parancsok at a bejövő IP-szabályokkal rendelkező témakörök létrehozásához. Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel hozhat létre bejövő IP-szabályokat a **tartományokhoz.** 


### <a name="enable-public-network-access-for-an-existing-topic"></a>Nyilvános hálózati hozzáférés engedélyezése meglévő témakörhöz
Alapértelmezés szerint a nyilvános hálózati hozzáférés engedélyezve van a témakörökés tartományok számára. A bejövő IP-tűzfalszabályok beállításával korlátozhatja a forgalmat. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Enabled\""}}"
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Nyilvános hálózati hozzáférés letiltása meglévő témakörhöz
Ha egy témakör vagy tartomány nyilvános hálózati hozzáférése le van tiltva, a nyilvános interneten keresztüli forgalom nem engedélyezett. Csak a privát végpontkapcsolatok férhetnek hozzá ezekhez az erőforrásokhoz. 

```azurecli-interactive
az rest --method patch --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" --body "{\""properties\"": {\""publicNetworkAccess\"": \""Disabled\""}}"
```

### <a name="create-topic-with-inbound-ip-rules"></a>Témakör létrehozása bejövő IP-szabályokkal
A következő minta CLI parancs létrehoz egy eseményrács-témakört bejövő IP-szabályokkal egy lépésben. 

```azurecli-interactive
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"",\""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```

### <a name="create-topic-first-and-then-add-inbound-ip-rules"></a>Először hozzon létre témakört, majd adjon hozzá bejövő IP-szabályokat
Ez a példa először létrehoz egy eseményrács-témakört, majd egy külön parancsban hozzáadja a témakör bejövő IP-szabályait. Frissíti a második parancsban beállított bejövő IP-szabályokat is. 

```azurecli-interactive

# create the event grid topic first
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" \
    --body {\""location\"":\""<LOCATION>\""}

# add inbound IP rules
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}

# later, update topic with additional ip rules or remove them. 
az rest --method put \
    --uri "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" 
    --body {\""location\"":\""<LOCATION>\", \""properties\"" :{\""publicNetworkAccess\"":\""enabled\"", \""InboundIpRules\"": [ {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""}, {\""ipMask\"": \""<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>\"", \""action\"": \""allow\""} ]}}
```


## <a name="use-powershell"></a>A PowerShell használata
Ez a szakasz bemutatja, hogyan azure PowerShell-parancsokkal hozhat létre Azure Event Grid-témaköröket bejövő IP-tűzfalszabályokkal. Az ebben a szakaszban látható lépések témakörökre vonatkozóak. Hasonló lépésekkel hozhat létre bejövő IP-szabályokat a **tartományokhoz.** 

### <a name="prerequisite"></a>Előfeltétel
Kövesse [az Útmutató: A portál használatával hozzon létre egy Azure AD-alkalmazást és egyszerű szolgáltatást, amely erőforrásokhoz férhet hozzá](../active-directory/develop/howto-create-service-principal-portal.md) egy Azure Active Directory-alkalmazás létrehozásához, és a következő értékeket jegyezze fel:

- Címtár (bérlő) azonosítója
- Alkalmazás (ügyfél) azonosítója
- Alkalmazás (ügyfél) titkos

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

### <a name="enable-public-network-access-for-an-existing-topic"></a>Nyilvános hálózati hozzáférés engedélyezése meglévő témakörhöz
Alapértelmezés szerint a nyilvános hálózati hozzáférés engedélyezve van a témakörökés tartományok számára. A bejövő IP-tűzfalszabályok beállításával korlátozhatja a forgalmat. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="enabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="disable-public-network-access-for-an-existing-topic"></a>Nyilvános hálózati hozzáférés letiltása meglévő témakörhöz
Ha egy témakör vagy tartomány nyilvános hálózati hozzáférése le van tiltva, a nyilvános interneten keresztüli forgalom nem engedélyezett. Csak a privát végpontkapcsolatok férhetnek hozzá ezekhez az erőforrásokhoz. 

```azurepowershell-interactive
$body = @{"properties"=@{"publicNetworkAccess"="disabled"}} | ConvertTo-Json -Depth 5

Invoke-RestMethod -Method 'Patch' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body `
    | ConvertTo-Json -Depth 5
```

### <a name="create-an-event-grid-topic-with-inbound-rules-in-one-step"></a>Eseményrács-témakör létrehozása bejövő szabályokkal egy lépésben

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that inbound IP rules are included. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# create the event grid topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5
```


### <a name="create-event-grid-topic-first-and-then-add-inbound-ip-rules"></a>Először hozzon létre eseményrács-témakört, majd adjon hozzá bejövő IP-szabályokat

```azurepowershell-interactive

# prepare the body for the REST PUT method. Notice that no inbound IP rules are specified. 
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled";}} | ConvertTo-Json -Depth 5

# create the Event Grid topic
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body`

# verify that the topic was created
Invoke-RestMethod -Method 'Get' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

# prepare the body for REST PUT method. Notice that it includes inbound IP rules now. This feature available in both basic and premium tiers.
$body = @{"location"="<LOCATION>"; "sku"= @{"name"="basic"}; "properties"=@{"publicNetworkAccess"="enabled"; "inboundIpRules"=@(@{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"}, @{"ipmask"="<IP ADDRESS or IP ADDRESS RANGE in CIDR notation>";"action"="allow"})}} | ConvertTo-Json -Depth 5

# update the topic with inbound IP rules
Invoke-RestMethod -Method 'Put' `
    -Uri "https://management.azure.com/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    -Body $body

# verify that the topic was updated
Invoke-RestMethod -Method 'Get' 
    -Uri "https://management.azure.com/subscriptions/<AzURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventGrid/topics/<EVENT GRID TOPIC NAME>?api-version=2020-04-01-preview" `
    -Headers $Headers `
    | ConvertTo-Json -Depth 5

```

## <a name="next-steps"></a>További lépések

* Az eseménykézbesítésfigyelésről az [Eseményrács üzenetkézbesítésének figyelése](monitor-event-delivery.md)című témakörben talál további információt.
* A hitelesítési kulcsról további információt az [Event Grid biztonsága és hitelesítése](security-authentication.md)című témakörben talál.
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.

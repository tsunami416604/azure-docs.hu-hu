---
title: Azure CLI-vel az Azure Blockchain szolgáltatás kezelése
description: Hogyan hozhat létre és kezelhet az Azure Blockchain-szolgáltatás az Azure CLI-vel
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154441"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Az Azure CLI-vel az Azure Blockchain-szolgáltatás kezelése

Az Azure Portalon kívül az Azure CLI használatával hozhat létre és kezelhet a blockchain-tagok és a tranzakciós csomópontok az Azure Blockchain-szolgáltatás.

Győződjön meg arról, hogy telepítette-e a legújabb [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli) és hogy bejelentkezett egy Azure-fiókjába a következővel `az login`.

A következő példákban cserélje le a példában `<parameter names>` a saját értékeire.

## <a name="create-blockchain-member"></a>Blockchain-tag létrehozása

Példa az Azure Blockchain Service-ben, amely a kvórum Főkönyv protokoll egy új consortium hoz létre a blockchain-tag.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Erőforráscsoport neve, az Azure Blockchain-szolgáltatási erőforrások jönnek létre. |
| **name** | Egyedi név, amely azonosítja az Azure Blockchain-szolgáltatás blockchain tag. A név szolgál a nyilvános végpont címe. Például: `myblockchainmember.blockchain.azure.com`. |
| **hely** | Az Azure régióban, amelyben létrehozza a blockchain tag. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. |
| **jelszó** | A tag fiók jelszavát. A tag fiók jelszavát az alapszintű hitelesítést használ, a blockchain tag nyilvános végpont hitelesítéséhez használatos. A jelszónak meg kell felelnie a következő négy követelmények három: hossza kell esnie: 12 & 72 karakter, 1 kisbetűt, 1 nagybetű, 1 szám és 1 különleges karaktert, amely nem szám sign(#), percent(%), vessző, star(*), biztonsági ajánlat (\`), quote("), egyetlen quote('), csal és semicolumn(;) duplán|
| **protokoll** | Nyilvános előzetes verziója támogatja a kvórum. |
| **consortium** | A csatlakozás, vagy hozzon létre consortium neve. |
| **consortiumManagementAccountPassword** | Consortium felügyeleti jelszava. A jelszó konzorcium való csatlakozásra szolgál. |
| **ruleName** | IP-címtartomány engedélyezési szabály neve. Nem kötelező paraméter a tűzfalszabályok esetében.|
| **startIpAddress** | Az engedélyezési listára helyezési IP-címtartomány kezdete. Nem kötelező paraméter a tűzfalszabályok esetében. |
| **endIpAddress** | Az engedélyezési listára helyezési IP-címtartomány vége. Nem kötelező paraméter a tűzfalszabályok esetében. |
| **skuName** | Csomag típusa. S0 Standard és B0 használata alapszintű. |

## <a name="change-blockchain-member-password"></a>Blockchain-tag jelszó módosítása

Példa módosítja a blockchain-tagok jelszót.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Erőforráscsoport neve, az Azure Blockchain-szolgáltatási erőforrások jönnek létre. |
| **name** | Az Azure Blockchain-szolgáltatás tag azonosító név. |
| **jelszó** | A tag fiók jelszavát. A jelszónak meg kell felelnie a következő négy követelmények három: hossza kell esnie: 12 & 72 karakter, 1 kisbetűt, 1 nagybetű, 1 szám és 1 különleges karaktert, amely nem szám sign(#), percent(%), vessző, star(*), biztonsági ajánlat (\`), quote("), egyetlen quote('), csal és semicolon(;) duplán. |


## <a name="create-transaction-node"></a>Tranzakció csomópont létrehozása

Egy meglévő blockchain tag belül tranzakció csomópontot hozhat létre. Tranzakció csomópontok hozzáadásával növelheti a biztonsági elkülönítés-terhelés. Például lehet egy másik ügyfélalkalmazások tranzakció csomópont végpontot.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Erőforráscsoport neve, az Azure Blockchain-szolgáltatási erőforrások jönnek létre. |
| **name** | Az Azure Blockchain-szolgáltatás, amely a tranzakció új csomópont nevét is tartalmazza blockchain tag nevét. |
| **hely** | Az Azure régióban, amelyben létrehozza a blockchain tag. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. |
| **jelszó** | A tranzakció csomópont jelszót. A jelszónak meg kell felelnie a következő négy követelmények három: hossza kell esnie: 12 & 72 karakter, 1 kisbetűt, 1 nagybetű, 1 szám és 1 különleges karaktert, amely nem szám sign(#), percent(%), vessző, star(*), biztonsági ajánlat (\`), quote("), egyetlen quote('), csal és semicolon(;) duplán. |
| **ruleName** | IP-címtartomány engedélyezési szabály neve. Nem kötelező paraméter a tűzfalszabályok esetében. |
| **startIpAddress** | Az engedélyezési listára helyezési IP-címtartomány kezdete. Nem kötelező paraméter a tűzfalszabályok esetében. |
| **endIpAddress** | Az engedélyezési listára helyezési IP-címtartomány vége. Nem kötelező paraméter a tűzfalszabályok esetében.|

## <a name="change-transaction-node-password"></a>Tranzakció-csomópont jelszó módosítása

A példában a tranzakció csomópont jelszót módosít.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Ha az Azure Blockchain-szolgáltatási erőforrások léteznek erőforráscsoport nevét. |
| **name** | Az Azure Blockchain-szolgáltatás, amely a tranzakció új csomópont nevét is tartalmazza blockchain tag nevét. |
| **jelszó** | A tranzakció csomópont jelszót. A jelszónak meg kell felelnie a következő négy követelmények három: hossza kell esnie: 12 & 72 karakter, 1 kisbetűt, 1 nagybetű, 1 szám és 1 különleges karaktert, amely nem szám sign(#), percent(%), vessző, star(*), biztonsági ajánlat (\`), quote("), egyetlen quote('), csal és semicolon(;) duplán. |

## <a name="change-consortium-management-account-password"></a>Consortium felügyeleti fiók jelszó módosítása

A consortium felügyeleti fiókja consortium tagsági felügyeleti szolgál. Minden tag egyedileg azonosítja a consortium felügyeleti fiókját, és módosíthatja az a következő parancsot a fiókhoz tartozó jelszót.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Erőforráscsoport neve, az Azure Blockchain-szolgáltatási erőforrások jönnek létre. |
| **name** | Az Azure Blockchain-szolgáltatás tag azonosító név. |
| **consortiumManagementAccountPassword** | A consortium felügyeleti fiók jelszavát. A jelszónak meg kell felelnie a következő négy követelmények három: hossza kell esnie: 12 & 72 karakter, 1 kisbetűt, 1 nagybetű, 1 szám és 1 különleges karaktert, amely nem szám sign(#), percent(%), vessző, star(*), biztonsági ajánlat (\`), quote("), egyetlen quote('), csal és semicolon(;) duplán. |
  
## <a name="update-firewall-rules"></a>Tűzfalszabályok frissítése

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Ha az Azure Blockchain-szolgáltatási erőforrások léteznek erőforráscsoport nevét. |
| **name** | Az Azure Blockchain-szolgáltatás blockchain tag nevét. |
| **ruleName** | IP-címtartomány engedélyezési szabály neve. Nem kötelező paraméter a tűzfalszabályok esetében.|
| **startIpAddress** | Az engedélyezési listára helyezési IP-címtartomány kezdete. Nem kötelező paraméter a tűzfalszabályok esetében.|
| **endIpAddress** | Az engedélyezési listára helyezési IP-címtartomány vége. Nem kötelező paraméter a tűzfalszabályok esetében.|

## <a name="list-api-keys"></a>Kulcsok listázása API

API-kulcsok csomópont elérése felhasználónévvel és jelszóval hasonló használható. Nincsenek két API-kulcs kulcsrotálás támogatása. A következő paranccsal listázhatja az API-kulcsokat.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Ha az Azure Blockchain-szolgáltatási erőforrások léteznek erőforráscsoport nevét. |
| **name** | Az Azure Blockchain-szolgáltatás, amely a tranzakció új csomópont nevét is tartalmazza blockchain tag nevét. |

## <a name="regenerate-api-keys"></a>API-kulcsok újragenerálása

Az alábbi parancs segítségével az API-kulcsok újragenerálása.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Ha az Azure Blockchain-szolgáltatási erőforrások léteznek erőforráscsoport nevét. |
| **name** | Az Azure Blockchain-szolgáltatás, amely a tranzakció új csomópont nevét is tartalmazza blockchain tag nevét. |
| **keyName** | Cserélje le \<párokat tartalmazó\> key1 és key2. |

## <a name="delete-a-transaction-node"></a>Egy tranzakció csomópont törlése

Példa a blockchain tag tranzakció csomópont törli.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Ha az Azure Blockchain-szolgáltatási erőforrások léteznek erőforráscsoport nevét. |
| **name** | Az Azure Blockchain-szolgáltatás az új csomópont tranzakciónév törölni is tartalmazó blockchain tag nevét. |

## <a name="delete-a-blockchain-member"></a>Blockchain tag törlése

Példa törli a blockchain tagja.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **resource-group** | Ha az Azure Blockchain-szolgáltatási erőforrások léteznek erőforráscsoport nevét. |
| **name** | A törlendő tag blockchain Azure Blockchain-szolgáltatás neve. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Az Azure AD-felhasználó számára

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Paraméter | Leírás |
|---------|-------------|
| **Szerepkör** | Neve az Azure AD-szerepkörhöz. |
| **assignee** | Az Azure AD felhasználói azonosítóját. Például: `user@contoso.com` |
| **Hatókör** | A szerepkör-hozzárendelés hatókörét. Blockchain tagjának tranzakció csomópont lehet. |

**Példa**

Csomópont hozzáférést biztosít az Azure AD-felhasználót a blockchain **tag**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Példa**

Csomópont hozzáférést biztosít az Azure AD-felhasználót a blockchain **tranzakció csomópont**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Csomópont hozzáférést biztosít az Azure AD-csoport vagy alkalmazás szerepkör

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Paraméter | Leírás |
|---------|-------------|
| **Szerepkör** | Neve az Azure AD-szerepkörhöz. |
| **assignee-object-id** | Az Azure AD-Csoportazonosító vagy az alkalmazás azonosítóját. |
| **Hatókör** | A szerepkör-hozzárendelés hatókörét. Blockchain tagjának tranzakció csomópont lehet. |

**Példa**

A csomópont hozzáférést **alkalmazás-szerepkör**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Távolítsa el az Azure AD-csomópont elérése

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Paraméter | Leírás |
|---------|-------------|
| **Szerepkör** | Neve az Azure AD-szerepkörhöz. |
| **assignee** | Az Azure AD felhasználói azonosítóját. Például: `user@contoso.com` |
| **Hatókör** | A szerepkör-hozzárendelés hatókörét. Blockchain tagjának tranzakció csomópont lehet. |

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Blockchain-szolgáltatás tranzakció csomópontok konfigurálása az Azure portal használatával](configure-transaction-nodes.md)

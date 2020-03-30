---
title: Az Azure Blockchain szolgáltatás kezelése az Azure CLI használatával
description: Az Azure Blockchain szolgáltatás kezelése az Azure CLI-vel
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455584"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Az Azure Blockchain szolgáltatás kezelése az Azure CLI használatával

Az Azure Portalon kívül az Azure CLI segítségével kezelheti a blokklánc-tagokat és a tranzakciócsomópontokat az Azure Blockchain szolgáltatáshoz.

Győződjön meg arról, hogy telepítette a legújabb Azure `az login` [CLI-t,](https://docs.microsoft.com/cli/azure/install-azure-cli) és bejelentkezett egy Azure-fiókba a segítségével.

A következő példákban `<parameter names>` cserélje le a példát a saját értékeire.

## <a name="create-blockchain-member"></a>Blockchain tag létrehozása

Példa létrehoz egy blockchain tagot az Azure Blockchain Szolgáltatásban, amely a Kvórum főkönyvi protokollt futtatja egy új konzorciumban.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások jönnek létre. |
| **név** | Egy egyedi név, amely azonosítja az Azure Blockchain Service blockchain tag. A nyilvános végpontcím neve a neve. Például: `myblockchainmember.blockchain.azure.com`. |
| **Helyen** | Az Azure-régió, ahol a blokklánc-tag jön létre. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. |
| **alaphelyzetbe állítása** | A tagfiók jelszava. A tagfiók-jelszó a blokklánc-tag nyilvános végpontjának hitelesítésére szolgál egyszerű hitelesítéssel. A jelszónak a következő négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetűs, 1 kisbetűs, 1 nagybetűs és 1 speciális\`karakter között kell lennie, amely nem számjel(#), százalék(%), vessző(,), visszaidézet( ), dupla idézőjel(), egyidézőjel('), kötőjel(-) és féloszlop(;)|
| **Protokoll** | A Nyilvános előzetes verzió támogatja a Quorum szolgáltatást. |
| **Konzorcium** | A csatlakozni vagy létrehozni hozandó konzorcium neve. |
| **consortiumManagementAccountPassword** | A konzorciumkezelési jelszó. A jelszó a konzorciumhoz való csatlakozáshoz használatos. |
| **szabálynév** | Ip-címtartomány engedélyezési listájának szabályneve. Nem kötelező paraméter a tűzfalszabályokhoz.|
| **startIpAddress cím** | Az engedélyezési lista IP-címtartományának kezdete. Nem kötelező paraméter a tűzfalszabályokhoz. |
| **endIpAddress cím** | Az engedélyezési lista IP-címtartományának vége. Nem kötelező paraméter a tűzfalszabályokhoz. |
| **skuName** | Szint típusa. Használja az S0-t a Normál és a B0 alapszintűhez. |

## <a name="change-blockchain-member-password"></a>A blockchain tag jelszavának módosítása

Példa megváltoztatja a blockchain tag jelszavát.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások jönnek létre. |
| **név** | Az Azure Blockchain szolgáltatás tagot azonosító név. |
| **alaphelyzetbe állítása** | A tagfiók jelszava. A jelszónak a következő négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetűs, 1 kisbetűs, 1 nagybetűs és 1 speciális\`karakter között kell lennie, amely nem számjel(#), százalék(%), vessző(,), csillag(*), visszaidézet( ), dupla idézőjel("), egyidézet(), kötőjel(-) és pontosvessző(;). |

## <a name="create-transaction-node"></a>Tranzakciócsomópont létrehozása

Hozzon létre egy tranzakciócsomópontot egy meglévő blokklánc-tagon belül. Tranzakciócsomópontok hozzáadásával növelheti a biztonsági elkülönítést, és terjesztheti a terhelést. Például lehet egy tranzakciós csomópont végpontja a különböző ügyfélalkalmazásokhoz.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások jönnek létre. |
| **név** | Az Azure Blockchain Service blockchain tag neve, amely tartalmazza az új tranzakciócsomópont nevét is. |
| **Helyen** | Az Azure-régió, ahol a blokklánc-tag jön létre. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. |
| **alaphelyzetbe állítása** | A tranzakciós csomópont jelszava. A jelszónak a következő négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetűs, 1 kisbetűs, 1 nagybetűs és 1 speciális\`karakter között kell lennie, amely nem számjel(#), százalék(%), vessző(,), csillag(*), visszaidézet( ), dupla idézőjel("), egyidézet(), kötőjel(-) és pontosvessző(;). |
| **szabálynév** | Ip-címtartomány engedélyezési listájának szabályneve. Nem kötelező paraméter a tűzfalszabályokhoz. |
| **startIpAddress cím** | Az engedélyezési lista IP-címtartományának kezdete. Nem kötelező paraméter a tűzfalszabályokhoz. |
| **endIpAddress cím** | Az engedélyezési lista IP-címtartományának vége. Nem kötelező paraméter a tűzfalszabályokhoz.|

## <a name="change-transaction-node-password"></a>Tranzakciócsomópont jelszavának módosítása

Példa a tranzakciócsomópont jelszavának módosítására.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások léteznek. |
| **név** | Az Azure Blockchain Service blockchain tag neve, amely tartalmazza az új tranzakciócsomópont nevét is. |
| **alaphelyzetbe állítása** | A tranzakciós csomópont jelszava. A jelszónak a következő négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetűs, 1 kisbetűs, 1 nagybetűs és 1 speciális\`karakter között kell lennie, amely nem számjel(#), százalék(%), vessző(,), csillag(*), visszaidézet( ), dupla idézőjel("), egyidézet(), kötőjel(-) és pontosvessző(;). |

## <a name="change-consortium-management-account-password"></a>Konzorciumkezelési fiók jelszavának módosítása

A konzorciumkezelési számla a konzorciumi tagság kezeléséhez használatos. Minden tagot egyedileg azonosít egy konzorciumkezelő fiók, és a következő paranccsal módosíthatja a fiók jelszavát.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások jönnek létre. |
| **név** | Az Azure Blockchain szolgáltatás tagot azonosító név. |
| **consortiumManagementAccountPassword** | A konzorciumi felügyeleti fiók jelszava. A jelszónak a következő négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetűs, 1 kisbetűs, 1 nagybetűs és 1 speciális\`karakter között kell lennie, amely nem számjel(#), százalék(%), vessző(,), csillag(*), visszaidézet( ), dupla idézőjel("), egyidézet(), kötőjel(-) és pontosvessző(;). |
  
## <a name="update-firewall-rules"></a>Tűzfalszabályok frissítése

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások léteznek. |
| **név** | Az Azure Blockchain Service blockchain tag neve. |
| **szabálynév** | Ip-címtartomány engedélyezési listájának szabályneve. Nem kötelező paraméter a tűzfalszabályokhoz.|
| **startIpAddress cím** | Az engedélyezési lista IP-címtartományának kezdete. Nem kötelező paraméter a tűzfalszabályokhoz.|
| **endIpAddress cím** | Az engedélyezési lista IP-címtartományának vége. Nem kötelező paraméter a tűzfalszabályokhoz.|

## <a name="list-api-keys"></a>API-kulcsok listázása

Az API-kulcsok a felhasználónévhez és a jelszóhoz hasonló csomópont-hozzáféréshez használhatók. Két API-kulcs támogatja a kulcsrotációt. Az alábbi paranccsal listázhatja az API-kulcsokat.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások léteznek. |
| **név** | Az Azure Blockchain Service blockchain tag neve, amely tartalmazza az új tranzakciócsomópont nevét is. |

## <a name="regenerate-api-keys"></a>API-kulcsok újragenerálása

Az API-kulcsok újragenerálása a következő paranccsal.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások léteznek. |
| **név** | Az Azure Blockchain Service blockchain tag neve, amely tartalmazza az új tranzakciócsomópont nevét is. |
| **Kulcsnév** | Cserélje \<le\> a keyValue értéket a key1 vagy a key2 billentyűre. |

## <a name="delete-a-transaction-node"></a>Tranzakciócsomópont törlése

Példa egy blokklánc tag tranzakciócsomópont törlése.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások léteznek. |
| **név** | Az Azure Blockchain Service blockchain tag neve, amely tartalmazza a tranzakciócsomópont nevét is törölni. |

## <a name="delete-a-blockchain-member"></a>Blokklánc-tag törlése

Példa törli a blockchain tag.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Erőforráscsoport neve, ahol az Azure Blockchain Service-erőforrások léteznek. |
| **név** | A törölni aandó Azure Blockchain Service blockchain tag neve. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Hozzáférés megadása az Azure AD-felhasználó számára

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Paraméter | Leírás |
|---------|-------------|
| **Szerepet** | Az Azure AD-szerepkör neve. |
| **Megbízott** | Az Azure AD felhasználói azonosítója. Például: `user@contoso.com` |
| **Hatókör** | A szerepkör-hozzárendelés hatóköre. Lehet blokklánc-tag vagy tranzakciós csomópont. |

**Példa:**

Csomópont-hozzáférés megadása az Azure AD-felhasználó számára a blockchain **tagszámára:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Példa:**

Csomópont-hozzáférés megadása az Azure AD-felhasználó számára a **blokklánc-tranzakciócsomóponthoz:**

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Csomópont-hozzáférés megadása az Azure AD-csoporthoz vagy alkalmazásszerepkörhöz

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Paraméter | Leírás |
|---------|-------------|
| **Szerepet** | Az Azure AD-szerepkör neve. |
| **assignee-object-id** | Azure AD-csoport azonosítója vagy alkalmazásazonosítója. |
| **Hatókör** | A szerepkör-hozzárendelés hatóköre. Lehet blokklánc-tag vagy tranzakciós csomópont. |

**Példa:**

Csomópont-hozzáférés megadása **az alkalmazásszerepkörhöz**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Az Azure AD-csomópont-hozzáférés eltávolítása

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Paraméter | Leírás |
|---------|-------------|
| **Szerepet** | Az Azure AD-szerepkör neve. |
| **Megbízott** | Az Azure AD felhasználói azonosítója. Például: `user@contoso.com` |
| **Hatókör** | A szerepkör-hozzárendelés hatóköre. Lehet blokklánc-tag vagy tranzakciós csomópont. |

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan konfigurálhatja az [Azure Blockchain szolgáltatás tranzakciós csomópontjait az Azure Portalon.](configure-transaction-nodes.md)

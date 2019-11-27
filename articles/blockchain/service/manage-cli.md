---
title: Az Azure Blockchain szolgáltatás kezelése az Azure CLI-vel
description: Az Azure Blockchain szolgáltatás kezelése az Azure CLI-vel
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455584"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Az Azure Blockchain szolgáltatás kezelése az Azure CLI-vel

A Azure Portalon kívül az Azure CLI használatával is kezelheti az Azure Blockchain szolgáltatás blockchain-tagjait és tranzakciós csomópontjait.

Győződjön meg arról, hogy telepítette a legújabb [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -t, és bejelentkezett egy Azure-fiókba a `az login`használatával.

Az alábbi példákban cserélje le például a saját értékeire `<parameter names>`.

## <a name="create-blockchain-member"></a>Blockchain-tag létrehozása

Példa létrehoz egy blockchain tagot az Azure Blockchain szolgáltatásban, amely egy új konzorciumban futtatja a kvórum Ledger protokollt.

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
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. |
| **név** | Egy egyedi név, amely az Azure Blockchain szolgáltatás Blockchain-tagját azonosítja. A nevet a rendszer a nyilvános végpont címeként használja. Például: `myblockchainmember.blockchain.azure.com`. |
| **hely** | Az az Azure-régió, ahol a blockchain-tag létrejött. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. |
| **jelszó** | A tag fiók jelszava. A tag fiók jelszava a blockchain-tag nyilvános végpontján történő hitelesítésre szolgál alapszintű hitelesítés használatával. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem szám típusú jel (#), százalék (%), vessző (,), csillag (*), vissza idézőjel (\`), idézőjel ("), szimpla idézőjel ("), kötőjel (-) és semicolumn (;)|
| **protokoll** | A nyilvános előzetes verzió támogatja a kvórumot. |
| **Consortium** | A csatlakozáshoz vagy létrehozáshoz használandó konzorcium neve. |
| **consortiumManagementAccountPassword** | A konzorcium felügyeleti jelszava. A rendszer a jelszót használja a konzorciumhoz való csatlakozáshoz. |
| **ruleName** | Az IP-címtartomány engedélyezési szabályának neve. A tűzfalszabályok választható paramétere.|
| **startIpAddress** | Az engedélyezési listán szereplő IP-címtartomány kezdete. A tűzfalszabályok választható paramétere. |
| **endIpAddress** | Az engedélyezési listán szereplő IP-címtartomány vége. A tűzfalszabályok választható paramétere. |
| **skuName** | A rétegek típusa. A standard és a B0 S0 használata alapszintű. |

## <a name="change-blockchain-member-password"></a>Blockchain-tag jelszavának módosítása

Példa egy blockchain-tag jelszavának módosítására.

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
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. |
| **név** | Az Azure Blockchain-szolgáltatás tagját azonosító név. |
| **jelszó** | A tag fiók jelszava. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem a Number Sign (#), a százalék (%), a vessző (,), a csillag (*), a back quota (\`), a Double quota (";) |

## <a name="create-transaction-node"></a>Tranzakciós csomópont létrehozása

Hozzon létre egy tranzakciós csomópontot egy meglévő blockchain-tag belsejében. A tranzakciós csomópontok hozzáadásával növelheti a biztonsági elkülönítést és terjesztheti a terhelést. Rendelkezhet például egy tranzakciós csomópont végponttal különböző ügyfélalkalmazások számára.

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
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. |
| **név** | Az Azure Blockchain szolgáltatás Blockchain azon tagja, amely az új tranzakciós csomópont nevét is tartalmazza. |
| **hely** | Az az Azure-régió, ahol a blockchain-tag létrejött. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. |
| **jelszó** | A tranzakciós csomópont jelszava. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem a Number Sign (#), a százalék (%), a vessző (,), a csillag (*), a back quota (\`), a Double quota (";) |
| **ruleName** | Az IP-címtartomány engedélyezési szabályának neve. A tűzfalszabályok választható paramétere. |
| **startIpAddress** | Az engedélyezési listán szereplő IP-címtartomány kezdete. A tűzfalszabályok választható paramétere. |
| **endIpAddress** | Az engedélyezési listán szereplő IP-címtartomány vége. A tűzfalszabályok választható paramétere.|

## <a name="change-transaction-node-password"></a>Tranzakciós csomópont jelszavának módosítása

Példa a tranzakciós csomópont jelszavának módosítására.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | Az Azure Blockchain szolgáltatás Blockchain azon tagja, amely az új tranzakciós csomópont nevét is tartalmazza. |
| **jelszó** | A tranzakciós csomópont jelszava. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem a Number Sign (#), a százalék (%), a vessző (,), a csillag (*), a back quota (\`), a Double quota (";) |

## <a name="change-consortium-management-account-password"></a>A konzorcium-felügyeleti fiók jelszavának módosítása

A konzorcium-felügyeleti fiók a konzorcium tagságának felügyeletére szolgál. Az egyes tagokat a konzorciumi felügyeleti fiókok egyedileg azonosítják, és a fiók jelszavát a következő paranccsal módosíthatja.

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
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. |
| **név** | Az Azure Blockchain-szolgáltatás tagját azonosító név. |
| **consortiumManagementAccountPassword** | A konzorcium felügyeleti fiókjának jelszava. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem a Number Sign (#), a százalék (%), a vessző (,), a csillag (*), a back quota (\`), a Double quota (";) |
  
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
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | Az Azure Blockchain Service Blockchain-tag neve. |
| **ruleName** | Az IP-címtartomány engedélyezési szabályának neve. A tűzfalszabályok választható paramétere.|
| **startIpAddress** | Az engedélyezési listán szereplő IP-címtartomány kezdete. A tűzfalszabályok választható paramétere.|
| **endIpAddress** | Az engedélyezési listán szereplő IP-címtartomány vége. A tűzfalszabályok választható paramétere.|

## <a name="list-api-keys"></a>API-kulcsok listázása

Az API-kulcsok a felhasználónévhez és a jelszóhoz hasonló csomópont-hozzáféréshez használhatók. A kulcs rotációjának támogatásához két API-kulcs szükséges. Használja az alábbi parancsot az API-kulcsok listázásához.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | Az Azure Blockchain szolgáltatás Blockchain azon tagja, amely az új tranzakciós csomópont nevét is tartalmazza. |

## <a name="regenerate-api-keys"></a>API-kulcsok újragenerálása

Használja az alábbi parancsot az API-kulcsok újragenerálása érdekében.

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
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | Az Azure Blockchain szolgáltatás Blockchain azon tagja, amely az új tranzakciós csomópont nevét is tartalmazza. |
| **Kulcsnév** | Cserélje le a \<\> a key1 vagy a key2 értékre. |

## <a name="delete-a-transaction-node"></a>Tranzakciós csomópont törlése

Példa töröl egy blockchain-tag tranzakciós csomópontot.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | Az Azure Blockchain szolgáltatás Blockchain azon tagja, amely tartalmazza a törölni kívánt tranzakciós csomópont nevét is. |

## <a name="delete-a-blockchain-member"></a>Blockchain-tag törlése

Példa egy blockchain-tag törlésére.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | A törlendő Azure Blockchain Service Blockchain-tag neve. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Hozzáférés biztosítása az Azure AD-felhasználó számára

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Paraméter | Leírás |
|---------|-------------|
| **szerepkör** | Az Azure AD-szerepkör neve. |
| **megbízott** | Azure AD-felhasználói azonosító. Például: `user@contoso.com` |
| **hatókör** | A szerepkör-hozzárendelés hatóköre. Blockchain-tag vagy tranzakciós csomópont lehet. |

**Példa**

Csomópont-hozzáférés biztosítása az Azure AD-felhasználó számára a blockchain- **tag**számára:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Példa**

Csomópont-hozzáférés biztosítása az Azure AD-felhasználó számára a blockchain **tranzakciós csomópontja**számára:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Csomópont-hozzáférés biztosítása Azure AD-csoport vagy alkalmazás-szerepkör számára

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Paraméter | Leírás |
|---------|-------------|
| **szerepkör** | Az Azure AD-szerepkör neve. |
| **megbízott – objektum-azonosító** | Azure AD-csoport azonosítója vagy az alkalmazás azonosítója. |
| **hatókör** | A szerepkör-hozzárendelés hatóköre. Blockchain-tag vagy tranzakciós csomópont lehet. |

**Példa**

Csomópont-hozzáférés megadása az **alkalmazási szerepkörhöz**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD-csomópont-hozzáférés eltávolítása

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Paraméter | Leírás |
|---------|-------------|
| **szerepkör** | Az Azure AD-szerepkör neve. |
| **megbízott** | Azure AD-felhasználói azonosító. Például: `user@contoso.com` |
| **hatókör** | A szerepkör-hozzárendelés hatóköre. Blockchain-tag vagy tranzakciós csomópont lehet. |

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [konfigurálhatja az Azure Blockchain szolgáltatás tranzakciós csomópontjait a Azure Portal](configure-transaction-nodes.md).

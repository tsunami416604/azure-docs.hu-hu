---
title: A felhasználói hozzáférés korlátozása az adatműveletekhez csak az Azure Cosmos DB segítségével
description: Megtudhatja, hogyan korlátozhatja az adatműveletekhez való hozzáférést csak az Azure Cosmos DB segítségével
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980373"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Felhasználói hozzáférés korlátozása az adatműveletekre

Az Azure Cosmos DB-ben kétféleképpen hitelesítheti az adatbázis-szolgáltatással folytatott interakciókat:
- az Azure Active Directory-identitás használata az Azure Portalon való interakció során,
- Az Azure Cosmos [DB-kulcsok](secure-access-to-data.md#master-keys) vagy [erőforrás-jogkivonatok](secure-access-to-data.md#resource-tokens) használata API-k és SDK-k hívásainak kiadása kor.

Minden hitelesítési módszer hozzáférést biztosít a különböző műveletkészletekhez, némi átfedéssel: ![A műveletek felosztása hitelesítési típusonként](./media/how-to-restrict-user-data/operations.png)

Bizonyos esetekben előfordulhat, hogy a szervezet egyes felhasználóit csak adatműveletek (azaz CRUD-kérelmek és lekérdezések) végrehajtására szeretné korlátozni. Ez általában a fejlesztők, akik nem kell létrehozni vagy törölni az erőforrásokat, vagy módosítsa a kiosztott átviteli a tárolók dolgoznak.

A hozzáférést a következő lépések alkalmazásával korlátozhatja:
1. Egyéni Azure Active Directory-szerepkör létrehozása azoknak a felhasználóknak, akiknek korlátozni szeretné a hozzáférést. Az egyéni Active Directory-szerepkörnek részletes hozzáférési szinttel kell rendelkeznie az Azure Cosmos DB [részletes műveleteit](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)használó műveletekhez.
1. A nem adatműveletek kulcsokkal történő végrehajtásának lehetővé. Ezt úgy érheti el, hogy ezeket a műveleteket csak az Azure Resource Manager-hívásokra korlátozza.

A cikk következő szakaszai bemutatják, hogyan hajthatja végre ezeket a lépéseket.

> [!NOTE]
> A következő szakaszokban a parancsok végrehajtásához telepítenie kell az Azure PowerShell 3.0.0-s vagy újabb modulját, valamint az [Azure-tulajdonosi szerepkört](../role-based-access-control/built-in-roles.md#owner) a módosítani kívánt előfizetésen.

A powershell-parancsfájlok a következő szakaszokban, cserélje ki a következő helyőrzők a környezetre jellemző értékeket:
- `$MySubscriptionId`- Az előfizetés-azonosító, amely tartalmazza az Azure Cosmos-fiókot, ahol korlátozni szeretné az engedélyeket. Például: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- Az Azure Cosmos-fiókot tartalmazó erőforráscsoport. Például: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- Az Azure Cosmos-fiók neve. Például: `mycosmosdbsaccount`.
- `$MyUserName`- Annak ausername@domainfelhasználónak a bejelentkezési ( ) bejelentkezése, akinek korlátozni szeretné a hozzáférést. Például: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Válassza ki az Azure-előfizetését

Az Azure PowerShell-parancsok megkövetelik, hogy jelentkezzen be, és válassza ki az előfizetést a parancsok végrehajtásához:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Egyéni Azure Active Directory-szerepkör létrehozása

A következő parancsfájl létrehoz egy Azure Active Directory-szerepkör-hozzárendelést az Azure Cosmos-fiókok "Csak kulcs" hozzáféréssel. A szerepkör az [Azure-erőforrások egyéni szerepkörein](../role-based-access-control/custom-roles.md) és [az Azure Cosmos DB részletes műveleteken](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)alapul. Ezek a szerepkörök és `Microsoft.DocumentDB` műveletek az Azure Active Directory névtér részét képezik.

1. Először hozzon létre egy `AzureCosmosKeyOnlyAccess.json` JSON-dokumentumot, amelynek neve a következő tartalommal:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Futtassa a következő parancsokat a szerepkör-hozzárendelés létrehozásához és a felhasználóhoz rendeléséhez:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>A nem adatjellegű műveletek végrehajtásának lehetővé tévő lehetővé tévő lehetővé tévő lehetővé

A következő parancsok eltávolítják a billentyűk használatának lehetőségét:
- erőforrások létrehozása, módosítása vagy törlése
- tárolóbeállítások frissítése (beleértve az indexelési házirendeket, átviteli- és átviteli- és átviteli beállításokat).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>További lépések

- További információ a [Cosmos DB szerepköralapú hozzáférés-vezérléséről](role-based-access-control.md)
- Az adatokhoz való biztonságos hozzáférés áttekintése [a Cosmos DB-ben](secure-access-to-data.md)

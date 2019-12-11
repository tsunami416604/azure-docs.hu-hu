---
title: Az adatműveletekhez való felhasználói hozzáférés korlátozása kizárólag Azure Cosmos DB
description: Megtudhatja, hogyan korlátozhatja az adatműveletekhez való hozzáférést Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980373"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Csak az adatműveletek felhasználói hozzáférésének korlátozása

Azure Cosmos DB két módon hitelesítheti az interakciókat az adatbázis-szolgáltatással:
- a Azure Active Directory identitás használata a Azure Portal való interakció során
- Azure Cosmos DB [kulcsok](secure-access-to-data.md#master-keys) vagy [erőforrás-tokenek](secure-access-to-data.md#resource-tokens) használata az API-k és SDK-k által kezdeményezett hívások kibocsátásakor.

Az egyes hitelesítési módszerek különböző műveletekhez biztosítanak hozzáférést, néhány átfedéssel: ![műveletek felosztása hitelesítési típusok alapján](./media/how-to-restrict-user-data/operations.png)

Bizonyos esetekben előfordulhat, hogy korlátozni szeretné a szervezet egyes felhasználói számára az adatműveletek (azaz a szifilisz-kérelmek és-lekérdezések) elvégzését. Ez általában olyan fejlesztőknek szól, akik nem szükségesek erőforrásokat létrehozni vagy törölni, vagy módosítani az általuk használt tárolók kiépített átviteli sebességét.

A hozzáférést a következő lépések alkalmazásával korlátozhatja:
1. Egyéni Azure Active Directory szerepkör létrehozása azon felhasználók számára, akik számára korlátozni kívánja a hozzáférést. Az egyéni Active Directory szerepkörnek részletes hozzáférési szintűnek kell lennie a műveletekhez Azure Cosmos DB [szemcsés](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)műveleteinek használatával.
1. A nem adatműveletek végrehajtásának letiltása a kulcsokkal. Ezt úgy érheti el, ha korlátozza ezeket a műveleteket csak a hívások Azure Resource Manager.

A cikk következő fejezetei bemutatják, hogyan hajthatja végre ezeket a lépéseket.

> [!NOTE]
> A következő szakaszokban szereplő parancsok végrehajtásához telepítenie kell Azure PowerShell modul 3.0.0 vagy újabb verzióját, valamint az [Azure tulajdonosi szerepkört](../role-based-access-control/built-in-roles.md#owner) a módosítani kívánt előfizetéshez.

A következő szakaszban található PowerShell-parancsfájlokban cserélje le a következő helyőrzőket a környezetre jellemző értékekre:
- `$MySubscriptionId` – az az előfizetés-azonosító, amely tartalmazza azt az Azure Cosmos-fiókot, amelyben korlátozni szeretné az engedélyeket. Például: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` – az Azure Cosmos-fiókot tartalmazó erőforráscsoport. Például: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` – az Azure Cosmos-fiók neve. Például: `mycosmosdbsaccount`.
- `$MyUserName` – annak a felhasználónak a felhasználóneve (username@domain), akivel korlátozni kívánja a hozzáférést. Például: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Válassza ki az Azure-előfizetését

Azure PowerShell parancsok használatához be kell jelentkeznie, és ki kell választania az előfizetést a parancsok végrehajtásához:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Az egyéni Azure Active Directory szerepkör létrehozása

Az alábbi szkript létrehoz egy Azure Active Directory szerepkör-hozzárendelést az Azure Cosmos-fiókok "kulcs csak" hozzáférésével. A szerepkör az Azure- [erőforrások egyéni szerepkörein](../role-based-access-control/custom-roles.md) és a [Azure Cosmos db részletes műveletein](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)alapul. Ezek a szerepkörök és műveletek a `Microsoft.DocumentDB` Azure Active Directory névtér részét képezik.

1. Először hozzon létre egy `AzureCosmosKeyOnlyAccess.json` nevű JSON-dokumentumot a következő tartalommal:

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

1. Futtassa a következő parancsokat a szerepkör-hozzárendelés létrehozásához és a felhasználóhoz való hozzárendeléséhez:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Nem adatműveletek végrehajtásának letiltása

A következő parancsokkal eltávolítható a kulcsok használatának lehetősége a következőkhöz:
- erőforrások létrehozása, módosítása vagy törlése
- a tároló beállításainak frissítése (beleértve az indexelési házirendeket, az átviteli sebességet stb.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Következő lépések

- További információ a [Cosmos db szerepköralapú hozzáférés-vezérléséről](role-based-access-control.md)
- Áttekintés a [Cosmos db lévő adathozzáférések biztonságos eléréséről](secure-access-to-data.md)

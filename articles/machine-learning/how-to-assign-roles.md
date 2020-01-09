---
title: Szerepkörök kezelése a munkaterületen
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan férhet hozzá egy Azure Machine Learning munkaterülethez szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 11/06/2019
ms.custom: seodec18
ms.openlocfilehash: d43e04e0ef347ec46a8aad12aac380f68106fc2f
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541607"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning munkaterület elérésének kezelése
[!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan kezelheti az Azure Machine Learning-munkaterülethez való hozzáférést. A [szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview) az Azure-erőforrásokhoz való hozzáférés kezelésére szolgál. A Azure Active Directory lévő felhasználók meghatározott szerepköröket kapnak, amelyek hozzáférést biztosítanak az erőforrásokhoz. Az Azure beépített szerepköröket és egyéni szerepkörök létrehozását is lehetővé teszi.

## <a name="default-roles"></a>Alapértelmezett szerepkörök

Az Azure Machine Learning munkaterület egy Azure-erőforrás. Más Azure-erőforrásokhoz hasonlóan, amikor létrejön egy új Azure Machine Learning munkaterület, három alapértelmezett szerepkör jön létre. Felhasználókat adhat hozzá a munkaterülethez, és hozzárendelheti őket a beépített szerepkörök valamelyikéhez.

| Szerepkör | Hozzáférési szint |
| --- | --- |
| **Olvasó** | Csak olvasási műveletek a munkaterületen. Az olvasók megtekinthetik és megtekinthetik a munkaterületen lévő objektumokat, de nem hozhatnak létre és nem frissíthetik ezeket az eszközöket. |
| **Közreműködő** | Munkaterületen lévő eszközök megtekintése, létrehozása, szerkesztése vagy törlése (ha van ilyen). A közreműködők például létrehozhatnak egy kísérletet, létrehozhatnak vagy csatolhatnak egy számítási fürtöt, futtathatnak és telepíthetnek egy webszolgáltatást. |
| **Tulajdonos** | Teljes hozzáférés a munkaterülethez, beleértve a munkaterületen lévő eszközök megtekintését, létrehozását, szerkesztését vagy törlését. Emellett módosíthatja a szerepkör-hozzárendeléseket is. |

> [!IMPORTANT]
> A szerepkör-hozzáférés az Azure több szintjére is kiterjed. Előfordulhat például, hogy valaki tulajdonosi hozzáféréssel rendelkezik a munkaterülethez, és nem rendelkezik tulajdonosi hozzáféréssel a munkaterületet tartalmazó erőforráscsoporthoz. További információ: [how RBAC Works](/azure/role-based-access-control/overview#how-rbac-works).

Az adott beépített szerepkörökkel kapcsolatos további információkért lásd: [beépített szerepkörök az Azure-](/azure/role-based-access-control/built-in-roles)hoz.

## <a name="manage-workspace-access"></a>Munkaterület-hozzáférés kezelése

Ha Ön a munkaterület tulajdonosa, szerepköröket adhat hozzá és távolíthat el a munkaterülethez. Szerepköröket is hozzárendelhet a felhasználókhoz. A következő hivatkozások segítségével megismerheti a hozzáférés kezelését:
- [Azure Portal felhasználói felület](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager sablonok](/azure/role-based-access-control/role-assignments-template)

Ha telepítette a [Azure Machine learning CLI](reference-azure-machine-learning-cli.md)-t, a parancssori felület parancs használatával is hozzárendelhet szerepköröket a felhasználókhoz.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

A `user` mező egy meglévő felhasználó e-mail-címe azon Azure Active Directory-példányban, ahol a munkaterület szülő-előfizetése él. Az alábbi példa bemutatja, hogyan használhatja ezt a parancsot:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Egyéni szerepkör létrehozása

Ha a beépített szerepkörök nem elégségesek, létrehozhat egyéni szerepköröket. Előfordulhat, hogy az egyéni szerepkörök olvasási, írási, törlési és számítási erőforrás-jogosultságokkal rendelkeznek az adott munkaterületen. A szerepkört egy adott munkaterület-szinten, egy adott erőforráscsoport-szinten vagy egy adott előfizetési szinten is elérhetővé teheti.

> [!NOTE]
> Ezen a szinten az erőforrás tulajdonosának kell lennie ahhoz, hogy egyéni szerepköröket hozzon létre az adott erőforráson belül.

Egyéni szerepkör létrehozásához először létre kell hoznia egy szerepkör-definíciós JSON-fájlt, amely meghatározza a szerepkör engedélyeit és hatókörét. Az alábbi példa egy "adattudós" nevű egyéni szerepkört definiál egy adott munkaterület-szinten:

`data_scientist_role.json`:
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

A `AssignableScopes` mező módosításával beállíthatja az egyéni szerepkör hatókörét az előfizetés szintjén, az erőforráscsoport szintjén vagy egy adott munkaterület szintjén.

Ez az egyéni szerepkör a munkaterületen mindent megtehet a következő műveletek kivételével:

- Nem hozható létre vagy nem frissíthető számítási erőforrás.
- Nem törölhet számítási erőforrást.
- Szerepkör-hozzárendeléseket nem lehet hozzáadni, törölni vagy módosítani.
- A munkaterület nem törölhető.

Az egyéni szerepkör üzembe helyezéséhez használja az alábbi Azure CLI-parancsot:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Az üzembe helyezés után ez a szerepkör elérhetővé válik a megadott munkaterületen. Most hozzáadhatja és hozzárendelheti ezt a szerepkört a Azure Portal. A szerepkört hozzárendelheti egy felhasználóhoz a `az ml workspace share` CLI parancs használatával:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Az egyéni szerepkörökkel kapcsolatos további információkért lásd: [Egyéni szerepkörök az Azure-erőforrásokhoz](/azure/role-based-access-control/custom-roles).

További információ az egyéni szerepkörökkel használható műveletekről (műveletekről): [erőforrás-szolgáltatói műveletek](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="next-steps"></a>Következő lépések

- [A nagyvállalati biztonság áttekintése](concept-enterprise-security.md)
- [A kísérletek és következtetések/pontszámok biztonságos futtatása virtuális hálózaton belül](how-to-enable-virtual-network.md)
- [Oktatóanyag: modellek betanítása](tutorial-train-models-with-aml.md)
- [Erőforrás-szolgáltatói műveletek](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
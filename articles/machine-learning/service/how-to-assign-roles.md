---
title: Szerepkörök az Azure Machine Learning-munkaterület kezelése
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan érhető el az Azure Machine Learning szolgáltatás munkaterületén szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: b40edf705ba61713f4b695dd55a6a20028936c82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993818"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Az Azure Machine Learning-munkaterület-hozzáférés kezelése

Ebből a cikkből megismerheti, hogyan kezelheti az Azure Machine Learning-munkaterület elérését. [Szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/role-based-access-control/overview) Azure-erőforrásokhoz való hozzáférés kezelésére szolgál. Az Azure Active Directory felhasználók vannak hozzárendelve adott szerepkörök, amelyek az erőforrásokhoz való hozzáférést. Az Azure biztosít a beépített szerepkörök és az egyéni szerepkörök létrehozása.

## <a name="default-roles"></a>Alapértelmezett szerepkörök

Az Azure Machine Learning-munkaterület egy Azure-erőforrás. Egyéb Azure-erőforrások, például egy új Azure Machine Learning-munkaterület létrehozásakor azt együttműködik a három alapértelmezett szerepkör. Felhasználók hozzáadása a munkaterületet, és hozzárendelheti azokat a beépített szerepkörök egyike.

| Szerepkör | Hozzáférési szint |
| --- | --- |
| **Olvasó** | A munkaterület csak olvasási műveleteket. Olvasói is listázása és megtekintése eszközök a munkaterületen, de nem létrehozása vagy frissítése ezeknek az eszközöknek. |
| **Közreműködő** | Megtekintése, létrehozása, szerkesztése vagy törlése (ha vannak ilyenek) eszközök a munkaterületen. Például közreműködők is kísérlet létrehozása, létrehozása vagy csatolása egy számítási fürt, küldje el a futtató és egy webszolgáltatás üzembe helyezése. |
| **Tulajdonos** | Teljes hozzáférés a munkaterület, beleértve a megtekintése, létrehozása, szerkesztése vagy törlése (ha vannak ilyenek) lehetővé teszi az eszközök a munkaterületen. Emellett módosíthatja a szerepkör-hozzárendeléseket. |

> [!IMPORTANT]
> Hozzáférés a szerepkör hatóköre beállítható több szintre az Azure-ban. Egy munkacsoporthoz tulajdonosi hozzáféréssel rendelkező felhasználó például nem lehet a munkacsoport tartalmazó erőforráscsoport tulajdonosi hozzáféréssel. További információkért lásd: [hogyan RBAC működik](/azure/role-based-access-control/overview#how-rbac-works).

Adott beépített szerepkörökkel kapcsolatos további információkért lásd: [Azure a beépített szerepkörök](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Munkaterület-hozzáférés kezelése

Ha Ön a tulajdonosa, egy munkaterületet, akkor hozzáadhat és eltávolíthat a munkaterülethez tartozó szerepkörök. Szerepkörök hozzárendelheti a felhasználók számára is. Az alábbi hivatkozások segítségével arról, hogy miképpen való hozzáférést kezelheti:
- [Az Azure portal felhasználói felületén](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Az Azure Resource Manager-sablonok](/azure/role-based-access-control/role-assignments-template)

Ha már telepítette a [Azure Machine Learning parancssori](reference-azure-machine-learning-cli.md), szerepkörök hozzárendelése a felhasználókhoz a CLI-parancsot is használhatja.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

A `user` mező, ahol a munkaterület szülő előfizetés él Azure Active Directory-példány egy meglévő felhasználó e-mail-címét. A következő példa bemutatja, hogyan használja a következő parancsot:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Egyéni szerepkör létrehozása

Ha a beépített szerepkörök nem elegendő, létrehozhat egyéni szerepköröket is. Egyéni szerepkörök előfordulhat, hogy rendelkezik olvasási, írási, törlése és számítási erőforrásokra vonatkozó engedélyek, munkaterület. Elérhetővé teheti a szerepkör egy adott munkaterület szintjén, egy adott erőforráscsoport szintjén vagy egy adott előfizetés szintjén.

> [!NOTE]
> Ennek az erőforrásnak belül egyéni szerepkörök létrehozása ezen a szinten az erőforrás tulajdonosának kell lennie.

Egyéni szerepkör létrehozása hozhatnak létre egy szerepkör definíciójának JSON-fájlt, amelyben megadja az engedélyt, a szerepkör hatóköre. Az alábbi példa meghatározza az egy adott munkaterület szintjén kötődő "adatelemzési szakértő" nevű egyéni szerepkört:

`data_scientist_role.json` :
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

Módosíthatja a `AssignableScopes` mezőt állítsa be az egyéni szerepkör hatóköre az előfizetés szintjén, az erőforráscsoport szintjén vagy egy adott munkaterület szintjén.

Az egyéni szerepkör mindent a munkaterületen, kivéve a következő műveleteket hajthatja végre:

- Nem hozható létre vagy számítási erőforrás frissítése.
- Azt nem lehet törölni olyan számítási erőforrás.
- Azt nem hozzáadásához, törléséhez vagy alter szerepkör-hozzárendelések.
- Nem tudja törölni a munkaterületen.

Az egyéni szerepkör üzembe helyezéséhez használja az Azure CLI-parancsot:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Üzembe helyezés után ez a szerepkör elérhetővé válik az adott munkaterületen. Most adja hozzá, és rendelje hozzá ezt a szerepkört az Azure Portalon. Vagy a felhasználó ezt a szerepkört a segítségével rendelhet a `az ml workspace share` parancssori felületi parancsot:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


További információkért lásd: [egyéni szerepkörök az Azure-erőforrások](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>További lépések

- [Vállalati biztonsági áttekintése](concept-enterprise-security.md)
- [Biztonságos futtatására kísérletek vagy következtetési egy virtuális hálózaton belül](how-to-enable-virtual-network.md)
- [Oktatóanyag: Modellek](tutorial-train-models-with-aml.md)

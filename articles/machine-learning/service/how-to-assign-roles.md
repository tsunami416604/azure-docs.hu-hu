---
title: Felhasználók és szerepkörök az Azure Machine Learning-munkaterület kezelése
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan kezelheti a felhasználók és szerepkörök az Azure Machine Learning szolgáltatás munkaterületen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hning86
ms.author: haining
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: 623aaff3cba86e8e523a86e4adcb0a359c339c45
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336458"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Felhasználók és szerepkörök az Azure Machine Learning-munkaterület kezelése

Ebből a cikkből megismerheti, hogyan adhat hozzá felhasználókat az Azure Machine Learning-munkaterület. Azt is megtudhatja, hogyan felhasználók hozzárendelése a különböző szerepkörökhöz, és egyéni szerepkörök létrehozása.

## <a name="built-in-roles"></a>Beépített szerepkörök
Az Azure Machine Learning-munkaterület egy Azure-erőforrás. Egyéb Azure-erőforrások, például egy új Azure Machine Learning-munkaterület létrehozásakor azt együttműködik a három alapértelmezett szerepkör. Felhasználók hozzáadása a munkaterületet, és hozzárendelheti azokat a beépített szerepkörök egyike.

- **Olvasó**
    
    Ez a szerepkör lehetővé teszi, hogy a munkaterület csak olvasási műveleteket. Olvasói is listázása és megtekintése eszközök a munkaterületen, de nem létrehozása vagy frissítése ezeknek az eszközöknek.

- **Közreműködő**
    
    Ez a szerepkör lehetővé teszi a felhasználóknak a megtekintése, létrehozása, szerkesztése vagy törlése (ha vannak ilyenek) eszközök a munkaterületen. Például közreműködők is kísérlet létrehozása, létrehozása vagy csatolása egy számítási fürt, küldje el a futtató és egy webszolgáltatás üzembe helyezése.

- **Tulajdonos**
    
    Ehhez a szerepkörhöz felhasználók teljes hozzáférést biztosít a munkaterületet, beleértve a megtekintése, létrehozása, szerkesztése vagy törlése (ha vannak ilyenek) eszközök a munkaterületen. Emellett adja hozzá, vagy távolítsa el a felhasználókat, és szerepkör-hozzárendelések módosítása.

## <a name="add-or-remove-users"></a>Felhasználók hozzáadása vagy eltávolítása
Ha Ön a tulajdonosa, egy munkaterületet, adja hozzá, és távolítsa el a felhasználókat a munkaterületen válassza ki a következő módszerek egyikét:
- [Az Azure portal felhasználói felületén](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Az Azure Resource Manager-sablonok](/azure/role-based-access-control/role-assignments-template)

Ha már telepítette a [Azure Machine Learning parancssori](reference-azure-machine-learning-cli.md), használhatja a CLI-paranccsal adhat hozzá felhasználókat a munkaterületen.

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

Hozzon létre egy egyéni szerepkört, elsőként hozza létre a szerepkör-definíciós JSON fájlt, amely megadja a engedély és a egy hatókört a szerepkörhöz. Ha például itt látható egy adott munkaterület szintjén kötődő "adatelemzési szakértő" nevű egyéni szerepkör egy szerepkör-definíciós fájljának:

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

Üzembe helyezés után ez a szerepkör elérhetővé válik az adott munkaterületen. Most adja hozzá, és rendelje hozzá ezt a szerepkört az Azure Portalon. Vagy a szerepkörrel rendelkező felhasználók használatával adhat hozzá a `az ml workspace share` parancssori felületi parancsot:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Az Azure-beli egyéni szerepkörökkel kapcsolatos további információkért lásd: [ebben a dokumentumban](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>További lépések

A részletes oktatóanyag megtudhatja, hogyan hozhat létre, betanítását és üzembe helyezése az Azure Machine Learning szolgáltatással modellek munkaterület használatával.

> [!div class="nextstepaction"]
> [Oktatóanyag: Modellek](tutorial-train-models-with-aml.md)

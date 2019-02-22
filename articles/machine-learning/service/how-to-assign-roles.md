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
ms.openlocfilehash: f6c74da2e9189c5dddb88cb80f04422f49cfaee2
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594253"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Felhasználók és szerepkörök az Azure Machine Learning-munkaterület kezelése

Ebből a cikkből megismerheti, hogyan felhasználók hozzáadása az Azure Machine Learning-munkaterület, és hozzárendelheti azokat a különböző szerepkörökkel. Azt is megtudhatja, hogyan hozhat létre egyéni szerepköröket.

## <a name="built-in-roles"></a>Beépített szerepkörök
Az Azure Machine Learning-munkaterületet egy Azure-erőforrás. És bármely Azure-erőforrások, például egy új Azure Machine Learning-munkaterület létrehozásakor azt három alapértelmezett szerepkör. Felhasználók hozzáadása a munkaterületet, és hozzárendelheti azokat a beépített szerepkörök egyike.

- **Olvasó**
    
    Ez a szerepkör lehetővé teszi, hogy a munkaterület csak olvasási műveleteket. Ezzel a szerepkörrel listázhatja, és a munkaterületen kísérletek, fut, számítás, modelleket, webszolgáltatásokat, például nézet eszközeinek közzétett folyamatok és így tovább. De létrehozásához vagy frissítéséhez ezeknek az eszközöknek nem engedélyezett.

- **Közreműködő**
    
    Ez a szerepkör lehetővé teszi a felhasználóknak a megtekintése, létrehozása, szerkesztése vagy törlése (ha vannak ilyenek) eszközök a munkaterületen. Az ehhez a szerepkörhöz a kísérlet létrehozásának, létrehozhat vagy csatolja a számítási fürt, például küldje el a futtatási, regisztráljon egy modellt, és a egy webszolgáltatás üzembe helyezése.

- **Tulajdonos**
    
    Ez a szerepkör teljes hozzáférést biztosít a munkaterületet, és lehetővé teszi, hogy a megtekintése, létrehozása, szerkesztése vagy törlése (ha vannak ilyenek) eszközök a munkaterületen. Ezenkívül azt is hozzáadása vagy távolítsa el a felhasználókat, és a szerepkör-hozzárendelések módosítása.

## <a name="add-or-remove-users"></a>Felhasználók hozzáadása vagy eltávolítása
Ha a munkaterület tulajdonosa, adja hozzá a felhasználók számára, vagy távolítsa el a felhasználókat a munkaterületen válassza ki a művelet végrehajtásához az alábbi módszerek bármelyikét:
- [Az Azure portal felhasználói felületén](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Az Azure Resource Management-sablonok](/azure/role-based-access-control/role-assignments-template).

Azt is megteheti Ha már telepítette [Azure Machine Learning parancssori](reference-azure-machine-learning-cli.md), használhatja a kényelmes CLI-parancsot, adja hozzá a felhasználó a munkaterületre.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Vegye figyelembe, hogy a `user` mező egy meglévő felhasználót, ahol az Azure-előfizetést a munkaterület szülő él azonos Azure Active Directory az e-mail-címét. Alább a következő példa bemutatja, hogyan használja a következő parancsot:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Egyéni szerepkör létrehozása
Ha a beépített szerepkörök az igényeinek megfelelő, létrehozhat egyéni szerepköröket is. Az egyéni szerepkör előfordulhat, hogy rendelkezik olvasási, írási és engedélyek a munkaterülethez és a számítási erőforrás munkaterület törlése. És elérhetővé teheti a szerepkör egy adott munkaterület szintjén, egy adott erőforráscsoport szintjén vagy egy adott előfizetés szintjén. 

> [!NOTE]
> Annak érdekében, hogy ennek az erőforrásnak belül egyéni szerepkörök létrehozása ezen a szinten az erőforrás tulajdonosának kell lennie.

Egyéni szerepkör létrehozása hozhatnak létre egy szerepkör definíciójának JSON-fájlt adja meg az engedélyt és azt szeretné, hogy a szerepkör hatóköre. Például alább az egy adott munkaterület szintjén kötődő "adatelemzési szakértő" nevű egyéni szerepkör egy szerepkör-definíciós fájljának.

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

Ez a szerepkör lehetővé teszi, hogy minden a munkaterületen, kivéve a következő műveleteket:
- Nem hozható létre vagy számítási erőforrás frissítése.
- Azt nem lehet törölni olyan számítási erőforrás.
- Ez nem adható hozzá, törölni a felhasználót, vagy módosíthatja bármely felhasználói szerepkör-hozzárendelések.
- Nem tudja törölni a munkaterületen.

Az egyéni szerepkör üzembe helyezéséhez használja az Azure-CLI-parancsot:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Miután üzembe helyezte ezt a szerepkört elérhetővé válik az adott munkaterületen. Most felhasználók hozzáadása és hozzárendelése ehhez a szerepkörhöz őket az Azure Portalon. Vagy a felhasználó a szerepkör használatával adhat hozzá a `az ml workspace share` parancssori felületi parancsot:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Módosíthatja is a `AssignableScopes` mezőt állítsa az egyéni szerepkör hatóköre az előfizetés szintjén, az erőforráscsoport szintjén, vagy egy adott munkaterület szintjén (szerint).

További információ az egyéni szerepkörök az Azure-ban működik, tekintse meg [ebben a dokumentumban](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>További lépések

Kövesse a részletes oktatóanyag munkaterület használatával hozhat létre, betanítását és üzembe helyezése a modellek Azure Machine Learning szolgáltatással.

> [!div class="nextstepaction"]
> [Oktatóanyag: Modellek](tutorial-train-models-with-aml.md)

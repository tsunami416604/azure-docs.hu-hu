---
title: Szerepkörök kezelése a munkaterületen
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan érheti el az Azure Machine Learning-munkaterületet szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270094"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterülethez való hozzáférés kezelése
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben a cikkben megtudhatja, hogyan kezelheti az Azure Machine Learning-munkaterülethez való hozzáférést. [Szerepköralapú hozzáférés-vezérlés (RBAC) azure-erőforrásokhoz](/azure/role-based-access-control/overview) való hozzáférés kezelésére szolgál. Az Azure Active Directoryban lévő felhasználók meghatározott szerepkörökhöz vannak rendelve, amelyek hozzáférést biztosítanak az erőforrásokhoz. Az Azure beépített szerepköröket és egyéni szerepkörök létrehozásának lehetővé teszi.

## <a name="default-roles"></a>Alapértelmezett szerepkörök

Az Azure Machine Learning-munkaterület egy Azure-erőforrás. Más Azure-erőforrásokhoz hasonlóan egy új Azure Machine Learning-munkaterület létrehozásakor három alapértelmezett szerepkörrel érkezik. Felhasználókat adhat hozzá a munkaterülethez, és hozzárendelheti őket a beépített szerepkörök egyikéhez.

| Szerepkör | Hozzáférési szint |
| --- | --- |
| **Olvasó** | Írásvédett műveletek a munkaterületen. Az olvasók felsorolhatják és megtekinthetik az eszközöket egy munkaterületen, de nem hozhatják létre és nem frissíthetik ezeket az eszközöket. |
| **Közreműködő** | A munkaterületen lévő (adott esetben) eszközök megtekintése, létrehozása, szerkesztése vagy törlése. A közreműködők például létrehozhatnak egy kísérletet, létrehozhatnak vagy csatolhatnak egy számítási fürtöt, futtathatnak, és webszolgáltatást telepíthetnek. |
| **Tulajdonos** | Teljes hozzáférés a munkaterülethez, beleértve az eszközök megtekintését, létrehozását, szerkesztését vagy törlését (adott esetben) egy munkaterületen. Ezenkívül módosíthatja a szerepkör-hozzárendeléseket is. |

> [!IMPORTANT]
> A szerepkör-hozzáférés az Azure-ban több szintre is kiterjedhet. Előfordulhat például, hogy egy munkaterülethez tulajdonosi hozzáféréssel rendelkező személy nem rendelkezik tulajdonosi hozzáféréssel a munkaterületet tartalmazó erőforráscsoporthoz. További információ: [Hogyan működik az RBAC.](/azure/role-based-access-control/overview#how-rbac-works)

Az adott beépített szerepkörökről további információt az [Azure beépített szerepkörei című témakörben talál.](/azure/role-based-access-control/built-in-roles)

## <a name="manage-workspace-access"></a>Munkaterületi hozzáférés kezelése

Ha ön egy munkaterület tulajdonosa, hozzáadhat és eltávolíthat szerepköröket a munkaterülethez. Szerepköröket is rendelhet a felhasználókhoz. Az alábbi hivatkozásoksegítségével megtudhatja, hogyan kezelheti a hozzáférést:
- [Az Azure Portal felhasználói felülete](/azure/role-based-access-control/role-assignments-portal)
- [Powershell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager-sablonok](/azure/role-based-access-control/role-assignments-template)

Ha telepítette az [Azure Machine Learning CLI,](reference-azure-machine-learning-cli.md)is használhatja a CLI parancsot szerepkörök hozzárendelése a felhasználókhoz.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

A `user` mező egy meglévő felhasználó e-mail címe az Azure Active Directory azon példányában, ahol a munkaterület szülő előfizetése él. Íme egy példa a parancs használatára:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Egyéni szerepkör létrehozása

Ha a beépített szerepkörök nem elegendőek, egyéni szerepköröket hozhat létre. Előfordulhat, hogy az egyéni szerepkörök olvasási, írási, törlési és számítási erőforrás-engedélyeket rendelkeznek az adott munkaterületen. A szerepkört elérhetővé teheti egy adott munkaterületi szinten, egy adott erőforráscsoport szintjén vagy egy adott előfizetési szinten.

> [!NOTE]
> Egyéni szerepkörök létrehozásához ezen a szinten az erőforrás tulajdonosának kell lennie.

Egyéni szerepkör létrehozásához először hozzon létre egy szerepkör-definíciós JSON-fájlt, amely megadja a szerepkör engedélyét és hatókörét. A következő példa egy "Adattudós" nevű egyéni szerepkört határoz meg egy adott munkaterületi szinten:

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

A `AssignableScopes` mező módosításával beállíthatja az egyéni szerepkör hatókörét az előfizetés, az erőforráscsoport szintjén vagy egy adott munkaterületi szinten.

Ez az egyéni szerepkör mindent megtehet a munkaterületen, kivéve a következő műveleteket:

- Számítási erőforrást nem hozhat létre és nem frissíthet.
- Számítási erőforrás nem törölhető.
- Nem adhat hozzá, nem törölhet és nem módosíthat szerepkör-hozzárendeléseket.
- Nem tudja törölni a munkaterületet.

Az egyéni szerepkör üzembe helyezéséhez használja a következő Azure CLI parancsot:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

A telepítés után ez a szerepkör elérhetővé válik a megadott munkaterületen. Most már hozzáadhatja és hozzárendelheti ezt a szerepkört az Azure Portalon. Ezt a szerepkört a `az ml workspace share` CLI paranccsal is hozzárendelheti egy felhasználóhoz:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Az egyéni szerepkörökről az [Egyéni szerepkörök az Azure-erőforrásokhoz](/azure/role-based-access-control/custom-roles)című témakörben talál további információt.

Az egyéni szerepkörökkel használható műveletekről (műveletekről) az [Erőforrás-szolgáltató műveletei című témakörben talál](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)további információt.


## <a name="frequently-asked-questions"></a>Gyakori kérdések


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>K. Melyek az Azure Machine Learning szolgáltatás különböző műveletek végrehajtásához szükséges engedélyek?

Az alábbi táblázat az Azure Machine Learning-tevékenységek és a legalább hatókörhöz szükséges engedélyek összegzését találja. Például, ha egy tevékenység munkaterülethatókörrel (4. oszlop) is elvégezhető, akkor az ezzel az engedéllyel rendelkező összes magasabb hatókör is automatikusan működni fog. A táblázatban szereplő összes **elérési út a relatív elérési út.** `Microsoft.MachineLearningServices/`

| Tevékenység | Előfizetési szintű hatókör | Erőforráscsoport-szintű hatókör | Munkaterület-szintű hatókör |
|---|---|---|---|
| Új munkaterület létrehozása | Nem kötelező | Tulajdonos vagy közreműködő | N/A (tulajdonossá válik, vagy a létrehozás után nagyobb hatókörszerepkört örököl) |
| Új számítási fürt létrehozása | Nem kötelező | Nem kötelező | Tulajdonos, közreműködő vagy egyéni szerepkör engedélyezése:`workspaces/computes/write` |
| Új notebook virtuális gép létrehozása | Nem kötelező | Tulajdonos vagy közreműködő | Nem lehetséges |
| Új számítási példány létrehozása | Nem kötelező | Nem kötelező | Tulajdonos, közreműködő vagy egyéni szerepkör engedélyezése:`workspaces/computes/write` |
| Adatsík-tevékenység, például futtatás i. futtatás, adatok elérése, modell telepítése vagy közzétételi folyamat | Nem kötelező | Nem kötelező | Tulajdonos, közreműködő vagy egyéni szerepkör engedélyezése:`workspaces/*/write` <br/> Vegye figyelembe, hogy a munkaterületre regisztrált adattárra is szüksége van ahhoz, hogy az MSI hozzáférhessen a tárfiókban lévő adatokhoz. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>K. Hogyan sorolhatom fel az összes egyéni szerepkört az előfizetésemben?

Az Azure CLI-ben futtassa a következő parancsot.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>K. Hogyan találhatom meg egy szerepkör szerepkör-definícióját az előfizetésemben?

Az Azure CLI-ben futtassa a következő parancsot. Ne `<role-name>` feledje, hogy a fenti parancs által visszaadott formátumnak azonos formátumban kell lennie.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>K. Hogyan frissíthetem a szerepkör-definíciót?

Az Azure CLI-ben futtassa a következő parancsot.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Vegye figyelembe, hogy az új szerepkör-definíció teljes hatóköréhez engedélyekkel kell rendelkeznie. Ha például ez az új szerepkör három előfizetéshatókörrel rendelkezik, mindhárom előfizetéshez engedélyekkel kell rendelkeznie. 

> [!NOTE]
> A szerepkörfrissítések alkalmazása 15 percet vagy egy órát is igénybe vehet, hogy az adott hatókör összes szerepkör-hozzárendelésére vonatkozzon.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>K. Definiálhatok olyan szerepkört, amely megakadályozza a munkaterület-kiadás frissítését? 

Igen, megadhat egy szerepkört, amely megakadályozza a munkaterületi kiadás frissítését. Mivel a munkaterület frissítése patch-hívás a munkaterület-objektumon, ezt úgy kell `"NotActions"` megtennie, hogy a Következő műveletet helyezi a JSON-definíció tömbjében: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>K. Milyen engedélyek szükségesek a kvótaműveletek munkaterületen történő végrehajtásához? 

A kvótával kapcsolatos műveletek végrehajtásához a munkaterülethez előfizetési szintű engedélyekre van szükség. Ez azt jelenti, hogy a felügyelt számítási erőforrások előfizetési szintű kvótájának vagy munkaterületi szintű kvótájának beállítása csak akkor történhet meg, ha írási engedélyekkel rendelkezik az előfizetéshatókörben. 


## <a name="next-steps"></a>További lépések

- [Vállalati biztonság áttekintése](concept-enterprise-security.md)
- [Biztonságosan futtatja a kísérleteket és következtetéseket/pontokat egy virtuális hálózaton belül](how-to-enable-virtual-network.md)
- [Oktatóanyag: Vonatmodellek](tutorial-train-models-with-aml.md)
- [Erőforrás-szolgáltatói műveletek](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

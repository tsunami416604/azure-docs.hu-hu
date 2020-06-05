---
title: Szerepkörök kezelése a munkaterületen
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan férhet hozzá egy Azure Machine Learning munkaterülethez szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 9763cc0d93e6731bb42bcc55f9d8bf9463e2b0dd
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2020
ms.locfileid: "84434675"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning munkaterület elérésének kezelése
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

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
- [Azure Resource Manager-sablonok](/azure/role-based-access-control/role-assignments-template)

Ha telepítette a [Azure Machine learning CLI](reference-azure-machine-learning-cli.md)-t, a parancssori felület parancs használatával is hozzárendelhet szerepköröket a felhasználókhoz.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

A `user` mező egy meglévő felhasználó e-mail-címe a Azure Active Directory példányában, ahol a munkaterület szülő-előfizetése él. Az alábbi példa bemutatja, hogyan használhatja ezt a parancsot:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Egyéni szerepkör létrehozása

Ha a beépített szerepkörök nem elégségesek, létrehozhat egyéni szerepköröket. Előfordulhat, hogy az egyéni szerepkörök olvasási, írási, törlési és számítási erőforrás-jogosultságokkal rendelkeznek az adott munkaterületen. A szerepkört egy adott munkaterület-szinten, egy adott erőforráscsoport-szinten vagy egy adott előfizetési szinten is elérhetővé teheti.

> [!NOTE]
> Ezen a szinten az erőforrás tulajdonosának kell lennie ahhoz, hogy egyéni szerepköröket hozzon létre az adott erőforráson belül.

Egyéni szerepkör létrehozásához először létre kell hoznia egy szerepkör-definíciós JSON-fájlt, amely meghatározza a szerepkör engedélyeit és hatókörét. Az alábbi példa egy "adattudós" nevű egyéni szerepkört definiál egy adott munkaterület-szinten:

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

A mező módosításával `AssignableScopes` beállíthatja az egyéni szerepkör hatókörét az előfizetés szintjén, az erőforráscsoport szintjén vagy egy adott munkaterület szintjén.

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


## <a name="frequently-asked-questions"></a>Gyakori kérdések


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>K. Milyen engedélyekre van szükség a különböző műveletek végrehajtásához a Azure Machine Learning szolgáltatásban?

A következő táblázat a Azure Machine Learning tevékenységek összegzését, valamint a minimális hatókörön belüli végrehajtáshoz szükséges engedélyeket tartalmazza. Például ha egy tevékenység egy munkaterület hatókörével (4. oszlop) hajtható végre, akkor az ezzel az engedéllyel rendelkező összes magasabb hatókör automatikusan is működni fog. A táblázat összes elérési útja **relatív elérési út** `Microsoft.MachineLearningServices/` .

| Tevékenység | Előfizetés szintű hatókör | Erőforráscsoport-szintű hatókör | Munkaterület-szintű hatókör |
|---|---|---|---|
| Új munkaterület létrehozása | Nem kötelező | Tulajdonos vagy közreműködő | N/A (A létrehozása után válik a tulajdonos vagy A magasabb hatókörű szerepkör örökli) |
| Új számítási fürt létrehozása | Nem kötelező | Nem kötelező | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`workspaces/computes/write` |
| Új notebook virtuális gép létrehozása | Nem kötelező | Tulajdonos vagy közreműködő | Nem lehetséges |
| Új számítási példány létrehozása | Nem kötelező | Nem kötelező | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`workspaces/computes/write` |
| Adatsík tevékenység, például a Futtatás elküldése, adatok elérése, modell vagy közzétételi folyamat üzembe helyezése | Nem kötelező | Nem kötelező | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`workspaces/*/write` <br/> Vegye figyelembe, hogy a munkaterülethez regisztrált adattárral is rendelkeznie kell, hogy az MSI hozzáférjen a Storage-fiókban tárolt adataihoz. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>K. Hogyan a saját előfizetés összes egyéni szerepkörét listázom?

Futtassa az alábbi parancsot az Azure CLI-ben.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>K. Hogyan megkeresni az előfizetésben szereplő szerepkörhöz tartozó szerepkör-definíciót?

Futtassa az alábbi parancsot az Azure CLI-ben. Vegye figyelembe, hogy a `<role-name>` fenti parancs által visszaadott formátumban kell lennie.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>K. Hogyan frissíteni a szerepkör-definíciót?

Futtassa az alábbi parancsot az Azure CLI-ben.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Vegye figyelembe, hogy az új szerepkör-definíciók teljes hatókörére vonatkozó engedélyekkel kell rendelkeznie. Ha például az új szerepkör hatóköre három előfizetésben található, akkor mindhárom előfizetéshez engedélyekkel kell rendelkeznie. 

> [!NOTE]
> A szerepkör frissítései 15 percet is igénybe vehetnek, hogy az adott hatókörben lévő összes szerepkör-hozzárendelésre érvényesek legyenek.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>K. Megadhatok olyan szerepkört, amely megakadályozza a munkaterület kiadásának frissítését? 

Igen, megadhat egy olyan szerepkört, amely megakadályozza a munkaterület kiadásának frissítését. Mivel a munkaterület frissítése egy javítási hívás a munkaterület-objektumon, ezt a következő művelet végrehajtásával teheti meg a `"NotActions"` JSON-definícióban található tömbben: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>K. Milyen engedélyekre van szükség a kvóta-műveletek végrehajtásához egy munkaterületen? 

A munkaterületen a kvótával kapcsolatos műveletek elvégzéséhez előfizetési szintű engedélyekre van szükség. Ez azt jelenti, hogy az előfizetési szint kvótájának vagy a munkaterületnek a felügyelt számítási erőforrásokra vonatkozó kvótájának beállítása csak akkor fordulhat elő, ha az előfizetés hatókörében írási engedélyekkel rendelkezik. 


## <a name="next-steps"></a>Következő lépések

- [Vállalati biztonság áttekintése](concept-enterprise-security.md)
- [A kísérletek és következtetések/pontszámok biztonságos futtatása virtuális hálózaton belül](how-to-enable-virtual-network.md)
- [Oktatóanyag: modellek betanítása](tutorial-train-models-with-aml.md)
- [Erőforrás-szolgáltatói műveletek](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

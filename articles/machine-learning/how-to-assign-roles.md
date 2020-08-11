---
title: Szerepkörök kezelése a munkaterületen
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan férhet hozzá egy Azure Machine Learning munkaterülethez szerepköralapú hozzáférés-vezérlés (RBAC) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 07/24/2020
ms.custom: how-to, seodec18
ms.openlocfilehash: afffdd0267cde8ffc841587748e51dd27e021369
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88079586"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning munkaterület elérésének kezelése
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan kezelheti az Azure Machine Learning-munkaterülethez való hozzáférést. Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](/azure/role-based-access-control/overview) az Azure-erőforrásokhoz való hozzáférés kezelésére szolgál. A Azure Active Directory lévő felhasználók meghatározott szerepköröket kapnak, amelyek hozzáférést biztosítanak az erőforrásokhoz. Az Azure beépített szerepköröket és egyéni szerepkörök létrehozását is lehetővé teszi.

## <a name="default-roles"></a>Alapértelmezett szerepkörök

Az Azure Machine Learning-munkaterület egy Azure-erőforrás. A többi Azure-erőforráshoz hasonlóan az új Azure Machine Learning-munkaterületek is három alapértelmezett szerepkörrel rendelkeznek a létrehozásukkor. Felhasználókat adhat hozzá a munkaterülethez, és hozzárendelheti őket a beépített szerepkörök valamelyikéhez.

| Szerepkör | Hozzáférési szint |
| --- | --- |
| **Olvasó** | Csak olvasási műveletek a munkaterületen. Az olvasók listázhatja és megtekintheti az eszközöket, beleértve az [adattár](how-to-access-data.md) hitelesítő adatait is egy munkaterületen. Az olvasók nem tudják létrehozni vagy frissíteni ezeket az eszközöket. |
| **Közreműködő** | Munkaterületen lévő eszközök megtekintése, létrehozása, szerkesztése vagy törlése (ha van ilyen). A közreműködők például létrehozhatnak egy kísérletet, létrehozhatnak vagy csatolhatnak egy számítási fürtöt, futtatást végezhetnek és webszolgáltatásokat helyezhetnek üzembe. |
| **Tulajdonos** | Teljes hozzáférés a munkaterülethez, beleértve a munkaterületen lévő eszközök megtekintését, létrehozását, szerkesztését vagy törlését. Emellett módosíthatja a szerepkör-hozzárendeléseket. |
| **Egyéni szerepkör** | Lehetővé teszi a munkaterületen belüli adott vezérlési vagy adatsík-műveletekhez való hozzáférés testreszabását. Például elküldheti egy futtatást, létrehozhat egy számítási modellt, üzembe helyezheti a modelleket, vagy regisztrálhat egy adatkészletet. |

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

Ha telepítette a [Azure Machine learning CLI](reference-azure-machine-learning-cli.md)-t, a parancssori felület parancsaival rendelhet hozzá szerepköröket a felhasználókhoz:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

A `user` mező egy meglévő felhasználó e-mail-címe a Azure Active Directory példányában, ahol a munkaterület szülő-előfizetése él. Az alábbi példa bemutatja, hogyan használhatja ezt a parancsot:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> "az ml Workspace Share" parancs nem működik összevont fiók esetén Azure Active Directory B2B-vel. A parancs helyett használja az Azure UI Portalt.


## <a name="azure-machine-learning-operations"></a>Azure Machine Learning műveletek

Számos művelethez és feladathoz Azure Machine Learning beépített műveleteket. A teljes listát lásd: az [Azure erőforrás-szolgáltató műveletei](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="create-custom-role"></a>Egyéni szerepkör létrehozása

Ha a beépített szerepkörök nem elegendőek, egyéni szerepköröket is létrehozhat. Előfordulhat, hogy az egyéni szerepkörök olvasási, írási, törlési és számítási erőforrás-jogosultságokkal rendelkeznek az adott munkaterületen. A szerepkört egy adott munkaterület-szinten, egy adott erőforráscsoport-szinten vagy egy adott előfizetési szinten is elérhetővé teheti.

> [!NOTE]
> Ezen a szinten az erőforrás tulajdonosának kell lennie ahhoz, hogy egyéni szerepköröket hozzon létre az adott erőforráson belül.

Egyéni szerepkör létrehozásához először létre kell hoznia egy szerepkör-definíciós JSON-fájlt, amely meghatározza a szerepkör engedélyeit és hatókörét. Az alábbi példa az "adattudós egyéni" nevű egyéni szerepkört definiálja egy adott munkaterület szintjén:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> A mező módosításával `AssignableScopes` beállíthatja az egyéni szerepkör hatókörét az előfizetés szintjén, az erőforráscsoport szintjén vagy egy adott munkaterület szintjén.
> A fenti egyéni szerepkör csak példaként szolgál [a Azure Machine learning szolgáltatáshoz javasolt egyéni szerepkörökkel](#customroles)kapcsolatban.

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

További információ az egyéni szerepkörökről: [Egyéni Azure-szerepkörök](/azure/role-based-access-control/custom-roles). Az egyéni szerepkörökkel használható műveletekkel (műveletekkel és nem műveletekkel) kapcsolatos további információkért lásd: [erőforrás-szolgáltatói műveletek](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).

## <a name="frequently-asked-questions"></a>Gyakori kérdések


### <a name="q-what-are-the-permissions-needed-to-perform-some-common-scenarios-in-the-azure-machine-learning-service"></a>K. Milyen engedélyek szükségesek néhány gyakori forgatókönyv végrehajtásához a Azure Machine Learning szolgáltatásban?

A következő táblázat a Azure Machine Learning tevékenységek összegzését, valamint a minimális hatókörön belüli végrehajtáshoz szükséges engedélyeket tartalmazza. Ha például egy tevékenység a munkaterület hatókörével (4. oszlop) végezhető el, akkor az ezzel az engedéllyel rendelkező összes magasabb hatókör automatikusan is működni fog:

> [!IMPORTANT]
> Az ebben a táblázatban szereplő összes elérési `/` út **relatív elérési utak** a következőhöz `Microsoft.MachineLearningServices/` :

| Tevékenység | Előfizetés szintű hatókör | Erőforráscsoport-szintű hatókör | Munkaterület-szintű hatókör |
| ----- | ----- | ----- | ----- |
| Új munkaterület létrehozása | Nem szükséges | Tulajdonos vagy közreműködő | N/A (A létrehozása után válik a tulajdonos vagy A magasabb hatókörű szerepkör örökli) |
| A munkaterület kiadásának frissítése | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`/workspaces/write` |
| Kérelem előfizetési szintjének Amlcompute-kvótája vagy a munkaterület szintjének beállítása kvóta | Tulajdonos vagy közreműködő, vagy egyéni szerepkör </br>így`/locations/updateQuotas/action`</br> az előfizetés hatókörében | Nincs engedélyezve | Nincs engedélyezve |
| Új számítási fürt létrehozása | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`/workspaces/computes/write` |
| Új számítási példány létrehozása | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`/workspaces/computes/write` |
| Bármilyen típusú Futtatás elküldése | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Folyamat végpontjának közzététele | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`"/workspaces/pipelines/write", "/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Regisztrált modell üzembe helyezése AK/ACI-erőforráson | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Egy üzembe helyezett AK-végponton keresztüli pontozás | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` a következőket: (ha nem használja Azure Active Directory auth) vagy `"/workspaces/read"` (jogkivonat-hitelesítés használatakor) |
| A tárterület elérése interaktív jegyzetfüzetekkel | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*"` |
| Új egyéni szerepkör létrehozása | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi`Microsoft.Authorization/roleDefinitions/write` | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket:`/workspaces/computes/write` |

> [!TIP]
> Ha a munkaterület első alkalommal történő létrehozásakor hiba lép fel, győződjön meg arról, hogy a szerepköre engedélyezi `Microsoft.MachineLearningServices/register/action` . Ez a művelet lehetővé teszi, hogy regisztrálja az Azure Machine Learning erőforrás-szolgáltatót az Azure-előfizetésében.

### <a name="q-are-we-publishing-azure-built-in-roles-for-the-machine-learning-service"></a>K. Az Azure beépített szerepköreit tesszük közzé a Machine Learning szolgáltatáshoz?

Jelenleg nem tesszük közzé az [Azure beépített szerepköreit](/azure/role-based-access-control/built-in-roles) a Machine learning szolgáltatáshoz. Nem lehet frissíteni egy beépített szerepkört a közzététel után, és az ügyfél-forgatókönyvek és a visszajelzések alapján továbbra is megerősítjük a szerepkör-definíciókat. 

<a id="customroles"></a>

### <a name="q-are-there-some-custom-role-templates-for-the-most-common-scenarios-in-machine-learning-service"></a>K. Létezik-e egyéni szerepkörű sablon a Machine Learning szolgáltatás leggyakoribb forgatókönyvei számára?

Igen, íme néhány gyakori forgatókönyv az egyéni javasolt szerepkör-definíciókkal együtt, amelyeket a saját egyéni szerepköreinek definiálására használhat alapszintű:

* __Adattudós egyéni__: lehetővé teszi, hogy az adattudós a munkaterületen belül minden műveletet elvégezzen, **kivéve**a következőket:

    * Számítás létrehozása
    * Modellek üzembe helyezése üzemi AK-fürtön
    * Folyamat-végpont üzembe helyezése éles környezetben

    `data_scientist_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.MachineLearningServices/workspaces/*/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/write",
            "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/services/aks/write",
            "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Adattudós által korlátozott egyéni__: a korlátozott szerepkör-definíciók nem helyettesítő karakterek az engedélyezett műveletekben. A munkaterületen belül minden műveletet elvégezhet, **kivéve**a következőket:

    * Számítás létrehozása
    * Modellek üzembe helyezése üzemi AK-fürtön
    * Folyamat-végpont üzembe helyezése éles környezetben

    `data_scientist_restricted_custom_role.json` :
    ```json
    {
        "Name": "Data Scientist Restricted Custom",
        "IsCustom": true,
        "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/computes/start/action",
            "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
            "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
            "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
            "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
            "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
            "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/models/write",
            "Microsoft.MachineLearningServices/workspaces/modules/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
            "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/write",
            "Microsoft.MachineLearningServices/workspaces/datastores/delete"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Egyéni MLOps__: lehetővé teszi, hogy egy szerepkört rendeljen egy egyszerű szolgáltatáshoz, és ezzel automatizálja az MLOps-folyamatokat. Például egy már közzétett folyamaton való futtatáshoz:

    `mlops_custom_role.json` :
    ```json
    {
        "Name": "MLOps Custom",
        "IsCustom": true,
        "Description": "Can run pipelines against a published pipeline endpoint",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
            "Microsoft.MachineLearningServices/workspaces/environments/read",    
            "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
            "Microsoft.MachineLearningServices/workspaces/modules/read",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
            "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
            "Microsoft.MachineLearningServices/workspaces/datastores/read",
            "Microsoft.MachineLearningServices/workspaces/environments/write",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
            "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
            "Microsoft.MachineLearningServices/workspaces/environments/build/action",
            "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/computes/write",
            "Microsoft.MachineLearningServices/workspaces/write",
            "Microsoft.MachineLearningServices/workspaces/computes/delete",
            "Microsoft.MachineLearningServices/workspaces/delete",
            "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
            "Microsoft.MachineLearningServices/workspaces/listKeys/action",
            "Microsoft.Authorization/*"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

* __Munkaterület rendszergazdája__: lehetővé teszi az összes művelet végrehajtását egy munkaterület hatókörén belül, **kivéve**a következőket:

    * Új munkaterület létrehozása
    * Előfizetés vagy munkaterület szintű kvóták kiosztása
    * A munkaterület kiadásának frissítése

    A munkaterület rendszergazdája szintén nem tud új szerepkört létrehozni. A meglévő beépített vagy egyéni szerepköröket csak a munkaterület hatókörén belül lehet hozzárendelni:

    `workspace_admin_custom_role.json` :
    ```json
    {
        "Name": "Workspace Admin Custom",
        "IsCustom": true,
        "Description": "Can perform all operations except quota management and upgrades",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/*/read",
            "Microsoft.MachineLearningServices/workspaces/*/action",
            "Microsoft.MachineLearningServices/workspaces/*/write",
            "Microsoft.MachineLearningServices/workspaces/*/delete",
            "Microsoft.Authorization/roleAssignments/*"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/write"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

<a name="labeler"></a>
* __Egyéni Labeler__: lehetővé teszi, hogy csak az adat címkézésére szolgáló szerepkört definiáljon:

    `labeler_custom_role.json` :
    ```json
    {
        "Name": "Labeler Custom",
        "IsCustom": true,
        "Description": "Can label data for Labeling",
        "Actions": [
            "Microsoft.MachineLearningServices/workspaces/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
            "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
        ],
        "NotActions": [
            "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
        ],
        "AssignableScopes": [
            "/subscriptions/<subscription_id>"
        ]
    }
    ```

### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>K. Hogyan a saját előfizetés összes egyéni szerepkörét listázom?

Futtassa az alábbi parancsot az Azure CLI-ben.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-operations-supported-by-the-machine-learning-service"></a>K. Hogyan megkeresni a Machine Learning szolgáltatás által támogatott műveleteket?

Futtassa az alábbi parancsot az Azure CLI-ben.

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

Ezek az [erőforrás-szolgáltatói műveletek](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)listájában is megtalálhatók.


### <a name="q-what-are-some-common-gotchas-when-using-azure-rbac"></a>K. Melyek az Azure RBAC használatának gyakori megtartása?

Íme néhány tudnivaló az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata során:

- Ha az Azure-ban hoz létre egy erőforrást, mondjuk egy munkaterületet, nem közvetlenül a munkaterület tulajdonosa. A szerepkör örökölt a legmagasabb hatókörű szerepkörtől, amelyet az adott előfizetésben engedélyez. Például ha Ön hálózati rendszergazda, és rendelkezett a Machine Learning munkaterület létrehozásához szükséges engedélyekkel, akkor a hálózati rendszergazda szerepkört a munkaterülethez kell rendelni, nem pedig a tulajdonosi szerepkörhöz.
- Ha két szerepkör-hozzárendelés van ugyanahhoz a Azure Active Directory-felhasználóhoz, amely ütközik a műveletek és a tevékenységek ütköző részeivel, akkor előfordulhat, hogy az egyik szerepkörtől való kizárásban felsorolt műveletek nem lépnek érvénybe, ha egy másik szerepkör Műveleteiként is szerepelnek. Ha többet szeretne megtudni arról, hogy az Azure hogyan elemzi a szerepkör-hozzárendeléseket, olvassa el, [hogyan határozza meg, hogy a felhasználó rendelkezik-e erőforrás-hozzáféréssel az Azure RBAC](/azure/role-based-access-control/overview#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)
- A számítási erőforrások VNet való üzembe helyezéséhez explicit módon rendelkeznie kell a következő műveletekhez szükséges engedélyekkel:
    - "Microsoft. Network/virtualNetworks/JOIN/Action" a VNet-erőforráson.
    - "Microsoft. Network/virtualNetworks/subnet/JOIN/Action" az alhálózati erőforráson.
    
    A hálózatkezeléssel való RBAC kapcsolatos további információkért tekintse meg a [hálózatkezelés beépített szerepköreit](/azure/role-based-access-control/built-in-roles#networking).

- Időnként akár 1 óráig is eltarthat, amíg az új szerepkör-hozzárendelések érvénybe lépnek a gyorsítótárban tárolt engedélyekkel szemben a veremben.

### <a name="q-what-permissions-do-i-need-to-use-a-user-assigned-managed-identity-with-my-amlcompute-clusters"></a>K. Milyen engedélyek szükségesek egy felhasználó által hozzárendelt felügyelt identitás használatához a Amlcompute-fürtökkel?

Ahhoz, hogy egy felhasználóhoz hozzárendelt identitást rendeljen a Amlcompute-fürtökhöz, az egyiknek írási engedéllyel kell rendelkeznie a számítási és [felügyelt identitás-kezelői szerepkör](/azure/role-based-access-control/built-in-roles#managed-identity-operator)létrehozásához. A felügyelt identitásokkal történő RBAC kapcsolatos további információkért olvassa el a [felhasználóhoz rendelt identitás kezelése](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) című témakört.


### <a name="q-do-we-support-role-based-access-control-on-the-studio-portal"></a>K. Támogatjuk a szerepköralapú hozzáférés-vezérlést a Studio portálon?

Azure Machine Learning Studio támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC). 

> [!IMPORTANT]
> Miután hozzárendelt egy adott engedélyekkel rendelkező egyéni szerepkört a munkaterületen lévő adattudóshoz, a megfelelő műveletek (például a számítási gomb hozzáadása) automatikusan el lesznek rejtve a felhasználók elől. Az elemek elrejtésével megakadályozhatja, hogy a rendszer ne észlelje az olyan vezérlőket, amelyek a használat során jogosulatlan hozzáférési értesítést adnak vissza a szolgáltatástól.

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>K. Hogyan megkeresni az előfizetésben szereplő szerepkörhöz tartozó szerepkör-definíciót?

Futtassa az alábbi parancsot az Azure CLI-ben. Az értéknek `<role-name>` a fenti parancs által visszaadott formátumban kell lennie.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>K. Hogyan frissíteni a szerepkör-definíciót?

Futtassa az alábbi parancsot az Azure CLI-ben.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Az új szerepkör-definíció teljes hatókörére vonatkozó engedélyekkel kell rendelkeznie. Ha például az új szerepkör hatóköre három előfizetésben található, akkor mindhárom előfizetéshez engedélyekkel kell rendelkeznie. 

> [!NOTE]
> A szerepkör frissítései 15 percet is igénybe vehetnek, hogy az adott hatókörben lévő összes szerepkör-hozzárendelésre érvényesek legyenek.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>K. Megadhatok olyan szerepkört, amely megakadályozza a munkaterület kiadásának frissítését? 

Igen, megadhat egy olyan szerepkört, amely megakadályozza a munkaterület kiadásának frissítését. Mivel a munkaterület frissítése egy javítási hívás a munkaterület-objektumon, ezt a következő művelet végrehajtásával teheti meg a `"NotActions"` JSON-definícióban található tömbben: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>K. Milyen engedélyekre van szükség a kvóta-műveletek végrehajtásához egy munkaterületen? 

Az előfizetési szintű engedélyekre van szükség a munkaterületen található kvóta-alapú műveletek elvégzéséhez. Ez azt jelenti, hogy az előfizetési szint kvótájának vagy a munkaterületnek a felügyelt számítási erőforrásokra vonatkozó kvótájának beállítása csak akkor fordulhat elő, ha az előfizetés hatókörében írási engedélyekkel rendelkezik. 


## <a name="next-steps"></a>További lépések

- [Vállalati biztonság áttekintése](concept-enterprise-security.md)
- [A kísérletek és következtetések/pontszámok biztonságos futtatása virtuális hálózaton belül](how-to-enable-virtual-network.md)
- [Oktatóanyag: modellek betanítása](tutorial-train-models-with-aml.md)
- [Erőforrás-szolgáltatói műveletek](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)

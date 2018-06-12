---
title: Az Azure RBAC szerepkör-definíciók megértése |} Microsoft Docs
description: Információ a szerepköralapú hozzáférés-vezérlést (RBAC) szerepkör-definíciók és hogyan adhat meg a részletes hozzáféréskezelést az erőforrások egyéni szerepkörök az Azure-ban.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/18/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 9bb7808f2b483fe9cd7d22c6df3fe80d4a98f1f4
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35266856"
---
# <a name="understand-role-definitions"></a>A szerepkör-definíciók ismertetése

Ha el egy szerepkört működésének vagy létrehozásakor a saját [egyéni szerepkör](custom-roles.md), hasznos lehet megérteni a szerepkörök definiálásának módját. Ez a cikk ismerteti a szerepkör-definíciók részleteit és néhány példa.

## <a name="role-definition-structure"></a>Szerepkör-definíció struktúra

A *szerepkör-definíció* engedélyek gyűjteménye. Nevezik néha csak egy *szerepkör*. Egy szerepkör-definíció végezhető, ilyen például az olvasási műveletek sorolja fel, írási és törlési. A nem hajtható végre, műveletek vagy a mögöttes adatokhoz kapcsolódó műveletek is listázhatja. Egy szerepkör-definíciót az alábbi szerkezettel rendelkezik:

```
assignableScopes []
description
id
name
permissions []
  actions []
  dataActions []
  notActions []
  notDataActions []
roleName
roleType
type
```

Műveletek vannak megadva, karakterláncok, amelyek formátuma a következő:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

A `{action}` egy művelet karakterlánc részének erőforrástípus végezhető műveletek típusát határozza meg. Például láthatja a következő karakterláncrészletek `{action}`:

| A művelet substring    | Leírás         |
| ------------------- | ------------------- |
| `*` | A helyettesítő karakter engedélyezi a hozzáférést a karakterláncnak megfelelő összes művelethez. |
| `read` | Lehetővé teszi, hogy olvasási műveletek (GET). |
| `write` | Lehetővé teszi, hogy írási műveletek (PUT, POST és javítás). |
| `delete` | Lehetővé teszi, hogy törli a műveleteket (Törlés). |

Itt a [közreműködő](built-in-roles.md#contributor) szerepkör-definíció JSON formátumban. A helyettesítő karakter (`*`) műveletet `actions` azt jelzi, hogy a rendszerbiztonsági tag ehhez a szerepkörhöz rendelt minden művelet végrehajtására, vagy ez azt jelenti, hogy mindent felügyelhetnek. Ez magában foglalja a jövőben definiált műveletek, az Azure ad hozzá az új erőforrástípusok esetében. A műveletek `notActions` vannak-e vonni `actions`. A következőket a [közreműködő](built-in-roles.md#contributor) szerepkör, `notActions` eltávolítja ezt a szerepkört képes erőforrásokhoz való hozzáférés kezelése és is hozzárendelhetők erőforrásokhoz való hozzáférést.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-and-data-operations-preview"></a>Kezelési és az operations (előzetes verzió)

Felügyeleti műveletek szerepköralapú hozzáférés-vezérlés van megadva a `actions` és `notActions` szerepkör-definíció szakaszait. Íme néhány példa a felügyeleti műveleteket az Azure-ban:

- A tárfiók való hozzáférés kezelése
- Létrehozására, frissítésére, és egy blob-tároló törlése
- Törölje az erőforráscsoportot és az összes erőforrást

A kezelési hozzáférés nem örökli az adatait. Ez az elkülönítés megakadályozza, hogy a szerepkört és a helyettesítő karakterek (`*`) az adatok korlátlan hozzáféréssel rendelkezik a. Például, ha a felhasználó rendelkezik-e egy [olvasó](built-in-roles.md#reader) szerepkört az előfizetés, majd is megtekinthetik a tárfiókot, de alapértelmezés szerint ezek a mögöttes adatokat nem lehet megtekinteni.

Szerepköralapú hozzáférés-vezérlés korábban nem volt használva műveletek. Engedélyezési műveletek változnak, erőforrás-szolgáltató között. Az azonos szerepköralapú hozzáférés-vezérlési engedélyezési modell felügyeleti műveleteihez használt kibővített adatok művelet (jelenleg előzetes verzió).

Adatok műveletek támogatásához új adatok szakaszok érhetőek el a szerepkör-definíció struktúra. Operatív adatok vannak megadva a `dataActions` és `notDataActions` szakaszok. Ezek a szakaszok adatokat ad hozzá, felügyelete és az adatok elkülönítése megmarad. Ez megakadályozza a helyettesítő karakterek aktuális szerepkör-hozzárendelések (`*`) hirtelen rendelkezik az adatokhoz való hozzáférés. Az alábbiakban néhány adatok művelet a megadott `dataActions` és `notDataActions`:

- Olvassa el a tárolóban lévő blobok listája
- A tárolási blob a tárolóban lévő írása
- A várólistában lévő üzenetek törlése

Itt a [Adatolvasó a tárolási Blob (előzetes verzió)](built-in-roles.md#storage-blob-data-reader-preview) szerepkör-definíciót, amely egyaránt tartalmazza a műveleteket a `actions` és `dataActions` szakaszok. Ez a szerepkör lehetővé teszi a blob-tároló, és az alapul szolgáló blob-adatok olvasása.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Allows for read access to Azure Storage blob containers and data.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/read"
        ],
        "additionalProperties": {},
        "dataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
        ],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Storage Blob Data Reader (Preview)",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Csak az műveleteket lehet hozzáadni a `dataActions` és `notDataActions` szakaszok. Erőforrás-szolgáltatók azonosíthatja, hogy mely műveletek legyenek az műveleteket úgy, hogy a `isDataAction` tulajdonságot `true`. A műveletek listájának megtekintéséhez ahol `isDataAction` van `true`, lásd: [erőforrás-szolgáltatói műveletekhez](resource-provider-operations.md). Szerepkörök, amelyek nem rendelkeznek az műveleteket nem kell lennie `dataActions` és `notDataActions` szakaszok belül a szerepkör-definíció.

Az összes felügyeleti műveletet API-hívásokhoz engedélyezési által Azure Resource Manager kezeli. Az adatok művelet API-hívásokhoz engedélyezési egy erőforrás-szolgáltató vagy az Azure Resource Manager kezeli.

### <a name="data-operations-example"></a>Adatok műveletek – példa

Jobb megértése, hogyan működnek a felügyeleti és műveletei, Mérlegeljük, egy adott példa. Alice lett rendelve a [tulajdonos](built-in-roles.md#owner) szerepkört az előfizetés hatókörben. Bob lett rendelve a [tárolási Blob adatok közreműködői (előzetes verzió)](built-in-roles.md#storage-blob-data-contributor-preview) szerepkör tárolási fiók hatókörre. Az alábbi ábrán ez a példa látható.

![Szerepköralapú hozzáférés-vezérlés kibővített felügyelet és a adatok műveletek támogatásához](./media/role-definitions/rbac-management-data.png)

A [tulajdonos](built-in-roles.md#owner) Alice szerepkör és a [tárolási Blob adatok közreműködői (előzetes verzió)](built-in-roles.md#storage-blob-data-contributor-preview) Bob szerepkör rendelkezik a következő műveleteket:

Tulajdonos

&nbsp;&nbsp;&nbsp;&nbsp;műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage-blobadatok közreműködője (minta)

&nbsp;&nbsp;&nbsp;&nbsp;műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Mivel Alice csak egy helyettesítő karakter (`*`) művelet előfizetés hatókörre, az engedélyek öröklése le engedélyezése rá, hogy az összes felügyeleti műveletek elvégzéséhez. Alice azonban nem végezhet adatok műveleteket. Például alapértelmezés szerint Ágnes nem tudja olvasni a blobok egy tárolóba belül, de ő olvasási, írási és törlési tárolók.

Bob engedélyek korlátozódnak csak a `actions` és `dataActions` megadott a [tárolási Blob adatok közreműködői (előzetes verzió)](built-in-roles.md#storage-blob-data-contributor-preview) szerepkör. A szerepkör alapján Bob hajthat végre felügyeleti mind az műveleteket. Például Bálint olvasási, írási és a tárolók a megadott tárfiók törlése és Phil is olvasása, írása, és a blobok törlése.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Mely eszközök támogatják az RBAC használata műveletek?

Megtekintéséhez, és az műveleteket használatához, az eszközök vagy az SDK-k megfelelő verziójával kell rendelkeznie:

| Eszköz  | Verzió  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-azurerm-ps) | 5.6.0 vagy újabb verzió |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 vagy újabb verzió |
| [.NET-keretrendszerhez készült Azure](/dotnet/azure/) | 2.8.0-Preview vagy újabb verzió |
| [Nyissa meg az Azure SDK](/go/azure/azure-sdk-go-install) | 15.0.0 vagy újabb verzió |
| [Azure Java](/java/azure/) | 1.9.0 vagy újabb verzió |
| [Python az Azure-ral](/python/azure) | 0.40.0 vagy újabb verzió |
| [Rubyhoz készült Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 vagy újabb verzió |

## <a name="actions"></a>műveletek

A `actions` engedélyt megadja a kezelési műveletek, amelyre a szerepkört engedélyezi a hozzáférést. Művelet karakterláncok, amelyek azonosítják az Azure erőforrás-szolgáltatók biztonságos műveletek gyűjteménye. Néhány példa a használt felügyeleti műveletek `actions`.

| A művelet karakterlánc    | Leírás         |
| ------------------- | ------------------- |
| `*/read` | biztosít hozzáférést az olvasási műveletek az összes Azure-erőforrás-szolgáltató minden erőforrástípus esetén.|
| `Microsoft.Compute/*` | engedélyezi a hozzáférést a Microsoft.Compute erőforrás-szolgáltató az összes erőforrástípus összes műveletet.|
| `Microsoft.Network/*/read` | Olvasási műveletek a Microsoft.Network erőforrás-szolgáltató az összes erőforrástípus biztosít hozzáférést.|
| `Microsoft.Compute/virtualMachines/*` | minden műveletet a virtuális gépek és a gyermek típusú erőforrások hozzáférést biztosít.|
| `microsoft.web/sites/restart/Action` | A webalkalmazás újraindítása biztosít hozzáférést.|

## <a name="notactions"></a>NotActions

A `notActions` engedélyt megadja a felügyeleti műveleteket, amelyek ki lettek zárva az engedélyezett a `actions`. Használja a `notActions` engedélyt, ha az engedélyezni kívánt műveletek készletét könnyebben definiált tiltott operatív kizárásával. Egy szerepkör (hatályos engedélyek) által biztosított hozzáférést számított kivonja a `notActions` műveletek a `actions` műveletek.

> [!NOTE]
> Ha egy felhasználó tartozik, amely nem tartalmazza egy műveletet a szerepkör `notActions`, és hozzá van rendelve egy második szerepkör, amely hozzáférést biztosít a műveletet, hogy a felhasználó számára engedélyezett a művelet elvégzéséhez. `notActions` Nincs megtagadási szabály – egyszerűen csak egy kényelmes módot nyújt az engedélyezett műveletek készlet létrehozása, ha az adott műveletek ki lesznek zárva.
>

## <a name="dataactions-preview"></a>dataActions (előzetes verzió)

A `dataActions` engedélyt megadja a, amelyre a szerepkört az adatok az objektumon belül hozzáférést biztosít az műveleteket. Például ha egy felhasználó rendelkezik olvasási blob adatokhoz való hozzáférést egy tárfiókot, majd követően elolvashatják a blobok a tárfiókon belül. Néhány példa a használt adatok műveletek `dataActions`.

| A művelet karakterlánc    | Leírás         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Egy blob vagy bináris objektumok listáját adja vissza. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | A blob írása eredményét adja vissza. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Egy üzenetet adja vissza. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Egy üzenet vagy írása, vagy törölje a üzenet eredményét adja vissza. |

## <a name="notdataactions-preview"></a>notDataActions (előzetes verzió)

A `notDataActions` engedélyt megadja a adatok műveleteket, amelyek ki lettek zárva az engedélyezett a `dataActions`. Egy szerepkör (hatályos engedélyek) által biztosított hozzáférést számított kivonja a `notDataActions` műveletek a `dataActions` műveletek. Mindegyik erőforrás-szolgáltató tartalmaz a megfelelő API-k a adatok műveletek végrehajtását.

> [!NOTE]
> Ha egy felhasználó társított egy szerepkör, amely nem tartalmazza az Adatműveletek `notDataActions`, és hozzá van rendelve egy második szerepkör, amely hozzáférést biztosít az azonos művelet, hogy a felhasználó számára engedélyezett adatok művelet elvégzéséhez. `notDataActions` Nincs megtagadási szabály – egyszerűen csak egy kényelmes módszert arra megengedett műveletkészlet létrehozásához, ha meghatározott ki lesznek zárva.
>

## <a name="assignablescopes"></a>AssignableScopes

A `assignableScopes` szakaszban határozza meg, hogy a szerepkör érhető el a hozzárendelési hatókör (felügyeleti csoport (jelenleg előzetes verzió), előfizetések, erőforráscsoport-sablonok vagy erőforrások). Elérhetővé teheti a szerepkör hozzárendelés csak a előfizetésekben, vagy azt, és nem a zsúfoltságát felhasználó igénylő erőforráscsoportok élményt a többi részének az előfizetések vagy erőforráscsoportok. Segítségével kell legalább egy felügyeleti csoport, az előfizetés, erőforráscsoport vagy erőforrás-azonosító.

Beépített szerepkörök `assignableScopes` értéke a gyökérszintű hatókörben (`"/"`). A gyökérszintű hatókörben azt jelzi, hogy a szerepkör az összes hatókör kiosztására használható. A saját egyéni szerepkörök nem használhatja a gyökérszintű hatókörben. Ha, egy engedélyezési hiba jelenik meg.

Érvényes hozzárendelhető hatókörök például:

| Forgatókönyv | Példa |
|----------|---------|
| Szerepkör-hozzárendelést egy-egy előfizetéshez tartozó érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Szerepkör hozzárendelés két előfizetésekben érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Szerepkör esetén csak a hálózati erőforrás csoport-hozzárendelést érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Szerepkör érhető el az összes hatókör hozzárendelése | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes és egyéni szerepkörök

A `assignableScopes` szakasz egy egyéni biztonsági szerepkört is vezérlők, akik létrehozása, törlése, módosítása vagy megtekintése az egyéni biztonsági szerepkört.

| Tevékenység | Művelet | Leírás |
| --- | --- | --- |
| Egyéni szerepkör létrehozása vagy törlése | `Microsoft.Authorization/ roleDefinition/write` | Ez a művelet az összes engedéllyel rendelkező felhasználók a `assignableScopes` az egyéni szerepkör létrehozhat (vagy törölhet) egyéni szerepkörök ezeket használható. Például [tulajdonosok](built-in-roles.md#owner) és [felhasználói rendszergazdák](built-in-roles.md#user-access-administrator) előfizetések, erőforráscsoport-sablonok és erőforrások. |
| Egyéni szerepkör módosítása | `Microsoft.Authorization/ roleDefinition/write` | Ez a művelet az összes engedéllyel rendelkező felhasználók a `assignableScopes` az egyéni szerepkör módosíthatja ezeket az egyéni szerepkörök. Például [tulajdonosok](built-in-roles.md#owner) és [felhasználói rendszergazdák](built-in-roles.md#user-access-administrator) előfizetések, erőforráscsoport-sablonok és erőforrások. |
| Egy egyéni biztonsági szerepkört megtekintése | `Microsoft.Authorization/ roleDefinition/read` | Ez a művelet egy hatókörhöz engedéllyel rendelkező felhasználók megtekinthetik az adott hatókörben kiosztására használható egyéni szerepköröket. Az összes beépített szerepkör engedélyezése egyéni szerepkörök hozzárendelés elérhető legyen. |

## <a name="role-definition-examples"></a>Szerepkör-definíció példák

Az alábbi példa azt mutatja meg a [olvasó](built-in-roles.md#reader) szerepkör-definíció jelenik meg, az Azure parancssori felület használatával:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

A következő példa bemutatja egy egyéni biztonsági szerepkört figyelési és virtuális gépek újraindításával megjelenített Azure PowerShell használatával:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
                  "Microsoft.Storage/*/read",
                  "Microsoft.Network/*/read",
                  "Microsoft.Compute/*/read",
                  "Microsoft.Compute/virtualMachines/start/action",
                  "Microsoft.Compute/virtualMachines/restart/action",
                  "Microsoft.Authorization/*/read",
                  "Microsoft.Resources/subscriptions/resourceGroups/read",
                  "Microsoft.Insights/alertRules/*",
                  "Microsoft.Insights/diagnosticSettings/*",
                  "Microsoft.Support/*"
  ],
  "NotActions":  [

                 ],
  "DataActions":  [

                  ],
  "NotDataActions":  [

                     ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Lásd még

* [Beépített szerepkörök](built-in-roles.md)
* [Egyéni szerepkörök](custom-roles.md)
* [Az Azure Resource Manager erőforrás-szolgáltatói műveletekhez](resource-provider-operations.md)

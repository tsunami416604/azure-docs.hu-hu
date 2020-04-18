---
title: Szerepkör-definíciók megismerése az Azure-erőforrásokrBAC-jában | Microsoft dokumentumok
description: Ismerje meg a szerepkör-definíciók szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-erőforrások részletes hozzáférés-kezelése.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 777ea7cc29679a3819e94d39913f167ea1cb3453
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641379"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Az Azure-erőforrások szerepkör-definícióinak ismertetése

Ha megpróbálja megérteni, hogyan működik egy szerepkör, vagy ha saját egyéni szerepkört hoz létre az [Azure-erőforrásokhoz,](custom-roles.md)hasznos megérteni, hogyan vannak definiálva a szerepkörök. Ez a cikk ismerteti a szerepkör-definíciók részleteit, és néhány példát tartalmaz.

## <a name="role-definition"></a>Szerepkör-definíció

A *szerepkör-definíció* engedélyek gyűjteménye. Szokás egyszerűen csak *szerepkörnek* is nevezni. A szerepkör-definíció a végrehajtható műveletek listáját tartalmazza (például olvasás, írás és törlés). Emellett azon műveleteket is tartalmazhatja, amelyek nem hajthatók végre, vagy amelyek a mögöttes adatokhoz kapcsolódnak. A szerepkör-definíció a következő tulajdonságokkal rendelkezik:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

| Tulajdonság | Leírás |
| --- | --- |
| `Name` | A szerepkör megjelenítendő neve. |
| `Id` | A szerepkör egyedi azonosítója. |
| `IsCustom` | Azt jelzi, hogy ez egyéni szerepkör-e. Egyéni `true` szerepkörökhöz. |
| `Description` | A szerepkör leírása. |
| `Actions` | Karakterláncok tömbje, amely megadja a szerepkör által lehetővé tesz felügyeleti műveleteket. |
| `NotActions` | Karakterláncok tömbje, amely megadja azengedélyezett rendszerből kizárt `Actions`felügyeleti műveleteket. |
| `DataActions` | Karakterláncok tömbje, amely megadja azokat az adatműveleteket, amelyeket a szerepkör lehetővé tesz az objektumon belüli adatokon. |
| `NotDataActions` | Karakterláncok tömbje, amely megadja az engedélyezett ből kizárt `DataActions`adatműveleteket. |
| `AssignableScopes` | Karakterláncok tömbje, amely megadja a szerepkör hozzárendeléshez rendelkezésre álló hatóköreit. |

### <a name="operations-format"></a>Műveletek formátuma

A műveletek a következő formátumú karakterláncokkal vannak megadva:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A `{action}` műveleti karakterlánc része határozza meg az erőforrástípuson végrehajtható műveletek típusát. A következő részkarakterláncok például a `{action}`következő ben jelennek meg:

| Művelet részkarakterlánca    | Leírás         |
| ------------------- | ------------------- |
| `*` | A helyettesítő karakter hozzáférést biztosít a karakterláncnak megfelelő összes művelethez. |
| `read` | Lehetővé teszi az olvasási műveleteket (GET). |
| `write` | Írási műveletek engedélyezése (PUT vagy PATCH). |
| `action` | Engedélyezi az egyéni műveleteket, például a virtuális gépek újraindítását (POST). |
| `delete` | Engedélyezi a törlési műveleteket (DELETE). |

### <a name="role-definition-example"></a>Példa szerepkör-definícióra

Itt a [Közreműködői](built-in-roles.md#contributor) szerepkör definíciója JSON formátumban. Az `Actions``*` helyettesítő karakterének művelete azt jelzi, hogy a szerepkörhöz rendelt szolgáltatásnév minden műveletet elvégezhet, azaz mindent kezelhet. Ez az Azure által hozzáadott új erőforrástípusokkal együtt a jövőben meghatározott műveletekre is vonatkozik. A `NotActions` műveleteit kivonjuk az `Actions` elemből. A [Contributor](built-in-roles.md#contributor) szerepkör esetében a `NotActions` eltávolítja a szerepkör erőforrások hozzáférésének kezelésére, valamint megadására vonatkozó engedélyét.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Irányítási és adatkezelési műveletek

A felügyeleti műveletek szerepköralapú hozzáférés-vezérlése `Actions` a `NotActions` szerepkör-definíció és tulajdonságai között van megadva. Íme néhány példa az Azure-beli felügyeleti műveletekre:

- Tárfiókhoz való hozzáférés kezelése
- Blob-tároló létrehozása, frissítése vagy törlése
- Erőforráscsoport és az összes erőforrás törlése

A felügyeleti hozzáférés nem öröklődik az adatokból, feltéve, hogy a tárolóhitelesítési módszer "Azure AD felhasználói fiók" és nem "Hozzáférési kulcs" lesz. Ez a szétválasztás megakadályozza,`*`hogy a helyettesítő karakterekkel ( ) rendelkező szerepkörök korlátlan hozzáféréssel rendelkezzenek az adatokhoz. Ha például egy felhasználó [rendelkezik egy előfizetésreaderi](built-in-roles.md#reader) szerepkörrel, akkor megtekintheti a tárfiókot, de alapértelmezés szerint nem tekintheti meg az alapul szolgáló adatokat.

Korábban a szerepköralapú hozzáférés-vezérlés nem volt használva az adatműveletekhez. Az adatműveletek engedélyezése erőforrás-szolgáltatók között eltérő volt. A felügyeleti műveletekhez használt szerepköralapú hozzáférés-vezérlési engedélyezési modell t ki bővült az adatműveletekre is.

Az adatműveletek támogatásához új adattulajdonságok lettek hozzáadva a szerepkör-definícióhoz. Az adatműveletek a `DataActions` és `NotDataActions` tulajdonságokban mennek végbe. Ezekkel az adattulajdonságokkal a felügyelet és az adatok elkülönítése megmarad. Az aktuális, helyettesítő karakterekkel (`*`) történő szerepkör-hozzárendelések így nem kaphatnak hirtelen hozzáférést az adatokhoz. A `DataActions` és `NotDataActions` elemekben többek között a következő adatműveletek adhatók meg:

- Egy tároló bloblistájának olvasása
- Tárolóblob írása egy tárolóban
- Egy üzenetsor üzenetének törlése

Itt a [Storage Blob Data Reader](built-in-roles.md#storage-blob-data-reader) szerepkör-definíciója, `Actions` amely `DataActions` tartalmazza a műveleteket mind a és a tulajdonságok. Ez a szerepkör lehetővé teszi a blobtároló és az alapul szolgáló blobadatok olvasását.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

A `DataActions` és `NotDataActions` tulajdonsághoz csak adatműveletek adhatók hozzá. Az erőforrás-szolgáltatók a `isDataAction` tulajdonság beállításával azonosítják, `true`hogy mely műveletek adatműveletek. A műveletek helyének megtekintéséhez `isDataAction` `true`tekintse meg az [Erőforrás-szolgáltató műveletei című témakört.](resource-provider-operations.md) Az adatműveleteket nem tartalmazó szerepköröknek `DataActions` `NotDataActions` és a szerepkör-definíción belüli tulajdonságoknak nem kell rendelkezniük.

Az összes felügyeleti művelet API-hívásának engedélyezését az Azure Resource Manager kezeli. Az adatüzemeltetési API-hívások engedélyezését erőforrás-szolgáltató vagy az Azure Resource Manager kezeli.

### <a name="data-operations-example"></a>Példa adatműveletekre

A felügyeleti és adatműveletek működésének jobb megértéséhez vegyünk egy konkrét példát. Alice az előfizetés hatókörében van [hozzárendelve](built-in-roles.md#owner) a tulajdonos szerepkör. Bob a Storage [Blob Data Contributor](built-in-roles.md#storage-blob-data-contributor) szerepkört egy tárfiók hatókörén kapta. Az alábbi ábra ezt a példát mutatja be.

![A szerepköralapú hozzáférés-vezérlés ki lett bővítve a felügyeleti és az adatműveletek támogatására](./media/role-definitions/rbac-management-data.png)

Az Alice [tulajdonosi](built-in-roles.md#owner) szerepköre és a [Zsemleárd tárolási blobadat-közreműködői](built-in-roles.md#storage-blob-data-contributor) szerepköre a következő műveletekkel rendelkezik:

Tulajdonos

&nbsp;&nbsp;&nbsp;&nbsp;Műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage Blob-adatközreműködő

&nbsp;&nbsp;&nbsp;&nbsp;Műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions (Adatműveletek)<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Mivel Az Alice rendelkezik egy helyettesítő ( )`*`művelet egy előfizetési hatókörben, az engedélyek öröklik le, hogy azok az összes felügyeleti műveletek végrehajtása. Alice tud olvasni, írni és törölni a tárolókat. Alice azonban további lépések nélkül nem hajthat végre adatműveleteket. Alapértelmezés szerint például Az Anna nem tudja olvasni a tárolón belüli blobokat. A blobok olvasásához Alice kellett volna letölteni a tárolási hozzáférési kulcsokat, és használja őket a blobok eléréséhez.

Bob engedélyei csak a `Actions` storage `DataActions` blob [adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkörben vannak megadva. A szerepkör alapján Bob felügyeleti és adatműveleteket is végrehajthat. Például Bob olvashat, írhat és törölhet tárolókat a megadott tárfiókban, és olvashatja, írhatja és törölheti a blobokat.

A felügyeleti és adatsíkok biztonságáról az [Azure Storage biztonsági útmutatójában](../storage/blobs/security-recommendations.md)talál további információt.

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Milyen eszközök támogatják az RBAC használatát az adatműveletekhez?

Az adatműveletek megtekintéséhez és kezeléséhez az eszközök vagy SDK-k megfelelő verzióival kell rendelkeznie:

| Eszköz  | Verzió  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 vagy újabb |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 vagy újabb |
| [Azure a .NET-hez](/dotnet/azure/) | 2.8.0-preview vagy újabb |
| [Azure SDK for Go](/azure/go/azure-sdk-go-install) | 15.0.0 vagy újabb |
| [Java-alapú Azure](/java/azure/) | 1.9.0 vagy újabb |
| [Azure Pythonhoz](/azure/python/) | 0.40.0 vagy újabb |
| [Rubyhoz készült Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 vagy újabb |

A REST API-ban az adatműveletek megtekintéséhez és használatához az **api-version** paramétert a következő vagy újabb verzióra kell állítania:

- 2018-07-01

## <a name="actions"></a>Műveletek

Az `Actions` engedély meghatározza azokat a felügyeleti műveleteket, amelyeket a szerepkör lehetővé tesz. Ez egy olyan műveleti karakterláncok gyűjteménye, amelyek azonosítják az Azure-erőforrás-szolgáltatók biztonságos műveleteit. Íme néhány példa a kezelési műveletekre, `Actions`amelyek a ban használhatók.

> [!div class="mx-tableFixed"]
> | Műveleti karakterlánc    | Leírás         |
> | ------------------- | ------------------- |
> | `*/read` | Hozzáférést biztosít az olvasási műveletekhez az összes Azure-erőforrás-szolgáltató összes erőforrástípusához.|
> | `Microsoft.Compute/*` | Hozzáférést biztosít a Microsoft.Compute erőforrás-szolgáltató összes erőforrástípusának összes műveletéhez.|
> | `Microsoft.Network/*/read` | Hozzáférést biztosít olvasási műveletekhez a Microsoft.Network erőforrás-szolgáltató összes erőforrástípusához.|
> | `Microsoft.Compute/virtualMachines/*` | Hozzáférést biztosít a virtuális gépek és a gyermekerőforrás-típusok összes műveletéhez.|
> | `microsoft.web/sites/restart/Action` | Hozzáférést biztosít egy webalkalmazás újraindításához.|

## <a name="notactions"></a>NotActions

Az `NotActions` engedély azokat a felügyeleti műveleteket határozza `Actions`meg, amelyek ki vannak zárva az engedélyezett ből. Használja `NotActions` az engedélyt, ha az engedélyezni kívánt műveletek készlete könnyebben definiálható a korlátozott műveletek kizárásával. A szerepkör által biztosított hozzáférés (hatályos engedélyek) a `NotActions` műveletek kivonásával számítja ki a `Actions` műveleteket.

> [!NOTE]
> Ha egy felhasználóhoz olyan szerepkör van rendelve, amely kizárja a műveletet a alkalmazásban, `NotActions`és egy második szerepkört kap, amely hozzáférést biztosít ugyanahhoz a művelethez, a felhasználó végrehajthatja a műveletet. `NotActions`nem megtagadási szabály – ez egyszerűen egy kényelmes módja annak, hogy hozzon létre egy sor engedélyezett műveletek, ha bizonyos műveleteket ki kell zárni.
>

## <a name="dataactions"></a>DataActions (Adatműveletek)

Az `DataActions` engedély meghatározza azokat az adatműveleteket, amelyeket a szerepkör lehetővé tesz az adott objektumon belüli adatokon. Például ha egy felhasználó olvasási blob adathozzáférés egy tárfiókhoz, majd olvashatják a blobok az adott tárfiókon belül. Íme néhány példa a rendszerben használható `DataActions`adatműveletekre.

> [!div class="mx-tableFixed"]
> | Műveleti karakterlánc    | Leírás         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Blobot vagy blobok listáját adja vissza. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Blob írásának eredményét adja vissza. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Üzenetet ad vissza. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Üzenetet vagy üzenet írásának vagy törlésének eredményét adja vissza. |

## <a name="notdataactions"></a>NotDataActions

Az `NotDataActions` engedély azokat az adatműveleteket határozza `DataActions`meg, amelyek ki vannak zárva az engedélyezett alkalmazásból. A szerepkör által biztosított hozzáférés (hatályos engedélyek) a `NotDataActions` műveletek kivonásával számítja ki a `DataActions` műveleteket. Minden erőforrás-szolgáltató biztosítja a megfelelő API-készletét az adatműveletek teljesítéséhez.

> [!NOTE]
> Ha egy felhasználóhoz olyan szerepkör van rendelve, amely kizárja az adatműveletet a alkalmazásban, `NotDataActions`és egy második szerepkört kap, amely hozzáférést biztosít ugyanahhoz az adatművelethez, a felhasználó végrehajthatja az adatműveletet. `NotDataActions`nem megtagadási szabály – ez egyszerűen egy kényelmes módja annak, hogy hozzon létre egy sor engedélyezett adatműveletek, ha bizonyos adatműveleteket ki kell zárni.
>

## <a name="assignablescopes"></a>Hozzárendelhető ható körök

A `AssignableScopes` tulajdonság meghatározza azokat a hatóköröket (felügyeleti csoportokat, előfizetéseket vagy erőforráscsoportokat), amelyek rendelkeznek ezzel a szerepkör-definícióval. A szerepkört csak az azt igénylő felügyeleti csoportokban, előfizetésekben vagy erőforráscsoportokban teheti elérhetővé hozzárendeléshez. Legalább egy felügyeleti csoportot, előfizetést vagy erőforráscsoportot kell használnia.

A beépített szerepkörök a gyökérhatókörre ( `AssignableScopes` `"/"`) vannak beállítva. A gyökérhatókör azt jelzi, hogy a szerepkör minden hatókörben elérhető hozzárendeléshez. Példák az érvényes hozzárendelhető hatókörökre:

> [!div class="mx-tableFixed"]
> | A szerepkör hozzárendeléshez elérhető | Példa |
> |----------|---------|
> | Egy előfizetés | `"/subscriptions/{subscriptionId1}"` |
> | Két előfizetés | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Hálózati erőforráscsoport | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Egy felügyeleti csoport | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Felügyeleti csoport és előfizetés | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Minden hatókör (csak a beépített szerepkörökre vonatkozik) | `"/"` |

Az egyéni `AssignableScopes` szerepkörökről az [Egyéni szerepkörök az Azure-erőforrásokhoz](custom-roles.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

* [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
* [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
* [Az Azure Resource Manager erőforrás-szolgáltatóműveletei](resource-provider-operations.md)

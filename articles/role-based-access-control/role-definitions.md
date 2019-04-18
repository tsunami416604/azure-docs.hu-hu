---
title: Megismerheti az Azure-erőforrások számára az RBAC szerepkör-definíciók |} A Microsoft Docs
description: További információk a szerepköralapú hozzáférés-vezérlés (RBAC) szerepkör-definíciók az Azure-erőforrások részletes hozzáférés-vezérlést.
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
ms.date: 02/09/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 7855c2bd45ba35ecb0ede5c60268e6446f37ed5a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58804530"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Megismerheti az Azure-erőforrások szerepkör-definíciók

Ha egy szerepkör működésének megismerése kívánt, vagy ha hoz létre a saját [egyéni szerepkört az Azure-erőforrások](custom-roles.md), hasznos lehet megérteni, milyen szerepkörök vannak definiálva. Ez a cikk a szerepkör-definíciók részleteit ismerteti, valamint példákat.

## <a name="role-definition-structure"></a>Szerepkör szabályzatdefiníciók struktúrája

A *szerepkör-definíció* engedélyek gyűjteménye. Szokás egyszerűen csak *szerepkörnek* is nevezni. A szerepkör-definíció a végrehajtható műveletek listáját tartalmazza (például olvasás, írás és törlés). A műveletek, amelyek nem hajtható végre vagy az alapul szolgáló adatokkal kapcsolatos műveletek is listázhatja. Egy szerepkör-definíció az alábbi struktúrával rendelkezik:

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

Műveletek formátuma a következő karakterláncokkal vannak megadva:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A `{action}` egy művelet karakterlánc részének határozza meg az erőforrástípus elvégezhető műveleteket. Például, látni fogja a következő karakterláncrész a `{action}`:

| A művelet karakterláncrészletet    | Leírás         |
| ------------------- | ------------------- |
| `*` | A helyettesítő karaktert az összes műveletet, amelyek megfelelnek a karakterlánc a hozzáférést. |
| `read` | Lehetővé teszi, hogy olvasási műveleteket (GET). |
| `write` | Lehetővé teszi, hogy írási műveletek (PUT, POST és PATCH). |
| `delete` | Lehetővé teszi, hogy törlési műveletek (Törlés). |

Íme a [közreműködői](built-in-roles.md#contributor) szerepkör-definíció JSON formátumban. A helyettesítő karakter (`*`) művelet alatt `Actions` azt jelzi, hogy az egyszerű, ehhez a szerepkörhöz rendelt minden művelet végrehajtására, vagy más szóval azt mindent felügyelhetnek. Ez magában foglalja a későbbiekben, meghatározott műveletek, az Azure ad hozzá új erőforrástípusok. A műveletek alatt `NotActions` összegből `Actions`. Abban az esetben, a [közreműködői](built-in-roles.md#contributor) szerepkör `NotActions` eltávolítja a szerepkör lehetővé teszi az erőforrásokhoz való hozzáférés kezelése és is hozzárendelhet erőforrásokhoz való hozzáférés.

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

## <a name="management-and-data-operations-preview"></a>Felügyeleti és műveletei (előzetes verzió)

Felügyeleti műveletek a szerepköralapú hozzáférés-vezérlő van megadva a `Actions` és `NotActions` egy szerepkör-definíció tulajdonságait. Íme néhány példa a felügyeleti műveleteket az Azure-ban:

- A tárfiókhoz való hozzáférés kezelése
- Létrehozásához, frissítéséhez vagy törléséhez egy blob-tárolóba
- Törölje az erőforráscsoportot, és minden erőforrását

Felügyeleti hozzáférés nem örökli az adatokhoz. Ez a fajta elkülönítés megakadályozza, hogy a szerepkörök a helyettesítő karakterek (`*`) származó kellene korlátozás nélkül hozzáférnek az adatokat. Például, ha a felhasználó rendelkezik egy [olvasó](built-in-roles.md#reader) szerepkör egy előfizetésre, majd megtekinthetik a storage-fiókot, de alapértelmezés szerint ezek nem tekintheti meg az alapul szolgáló adatokat.

Szerepköralapú hozzáférés-vezérlés korábban nem használta az üzemeltetés. Engedélyezési műveletek különböző erőforrás-szolgáltató között. Az azonos szerepköralapú hozzáférés-vezérlési engedélyezési modell felügyeleti műveleteihez használt ki van terjesztve az műveletekhez (jelenleg előzetes verzióban érhető el).

Műveletek támogatása érdekében új adattulajdonságok lettek hozzáadva a szerepkör-definíció struktúra. Adatműveletek vannak megadva a `DataActions` és `NotDataActions` tulajdonságait. Ezek a Tulajdonságok adatok hozzáadásával a felügyeleti és az adatok elkülönítése változatlan marad. Ez megakadályozza, hogy a jelenlegi szerepkör-hozzárendelések helyettesítő karakterek (`*`) hirtelen nem adatokhoz fér hozzá. Az alábbiakban néhány műveletekhez, amelyek segítségével is megadható `DataActions` és `NotDataActions`:

- Olvassa el a tárolóban lévő blobok listája
- Írási storage blob-tárolóban
- Egy üzenetsorban lévő üzenet törlése

Íme a [Storage-Blobadatok olvasója](built-in-roles.md#storage-blob-data-reader) szerepkör-definíciót, amely tartalmazza a műveleteket is a `Actions` és `DataActions` tulajdonságait. Ez a szerepkör lehetővé teszi, hogy blob-tárolóban, és az alapul szolgáló blob-adatok olvasását.

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

Csak az üzemeltetés lehet hozzáadni a `DataActions` és `NotDataActions` tulajdonságait. Erőforrás-szolgáltatók azonosítani, hogy mely műveletekre Adatműveletek, azzal a `isDataAction` tulajdonságot `true`. A műveletek listájának megtekintéséhez, ahol `isDataAction` van `true`, lásd: [erőforrás-szolgáltatói műveletek](resource-provider-operations.md). Szerepkörök, amelyek nem rendelkeznek műveletekhez nem szükséges rendelkezik `DataActions` és `NotDataActions` belül a szerepkör-definíció tulajdonságait.

Az összes felügyeleti műveletet API-hívások engedélyezési Azure Resource Manager által történik. Az adatok a művelet API-hívások engedélyezési egy erőforrás-szolgáltató vagy az Azure Resource Manager kezeli.

### <a name="data-operations-example"></a>Adatok operations példa

Segít jobban megérteni, hogyan működnek a felügyeleti és műveleteket, vegyünk egy olyan példát. Alice hozzá lett rendelve a [tulajdonosa](built-in-roles.md#owner) szerepkör az előfizetések szintjén. Bob hozzá lett rendelve a [Storage-Blobadatok Közreműködője](built-in-roles.md#storage-blob-data-contributor) szerepkör egy storage-fiók hatókörben. Az alábbi ábrán látható az ebben a példában.

![Szerepköralapú hozzáférés-vezérlés ki van terjesztve a felügyelet és a műveletek támogatásához](./media/role-definitions/rbac-management-data.png)

A [tulajdonosa](built-in-roles.md#owner) Alice szerepkör és a [Storage-Blobadatok Közreműködője](built-in-roles.md#storage-blob-data-contributor) Bob szerepkör rendelkezik a következő műveleteket:

Tulajdonos

&nbsp;&nbsp;&nbsp;&nbsp;Műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage-blobadatok közreműködője

&nbsp;&nbsp;&nbsp;&nbsp;Műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Mivel Alice szerepel egy helyettesítő karakter (`*`) műveletet egy előfizetési hatókörben, saját engedélyek öröklése le engedélyezéséhez számára a kezelési műveletek végrehajtásához. Alice olvasási, írási és törölheti a tárolókat. Azonban az Ágnes nem tudja végrehajtani az Adatműveletek anélkül, hogy további lépéseket. Például alapértelmezés szerint Ágnes nem tudja olvasni a blobok tárolóban. Olvassa el a blobokat, hogy Ágnes kellene a tárelérési kulcsok lekéréséhez, és ezek segítségével érheti el a blobokat.

Bob engedélyek korlátozódnak csupán a `Actions` és `DataActions` megadott a [Storage-Blobadatok Közreműködője](built-in-roles.md#storage-blob-data-contributor) szerepkör. A szerepkör alapján, Bob végrehajthat felügyeleti és az üzemeltetés is. Például Bálint olvasási, írási és törölheti a tárolókat a megadott tárfiók, és ő is olvasási, írási, és a blobok törlése.

További információ a felügyeleti és az adatsík adatbiztonságot Storage: a [Azure Storage biztonsági útmutatóját](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Milyen eszközöket támogatja a műveletekhez az RBAC használatával?

Megtekintheti és az üzemeltetés, az eszközök és SDK-kkal megfelelő verziójával kell rendelkeznie:

| Eszköz  | Verzió  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0-ás vagy újabb |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30-as vagy újabb |
| [.NET-hez készült Azure](/dotnet/azure/) | 2.8.0-Preview vagy újabb |
| [A Góhoz készült Azure SDK](/go/azure/azure-sdk-go-install) | 15.0.0-s vagy újabb |
| [A Javához készült Azure](/java/azure/) | 1.9.0-s és újabb verziók |
| [Pythonhoz készült Azure](/python/azure) | 0.40.0 vagy újabb |
| [Rubyhoz készült Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 vagy újabb |

Megtekintheti, és a Adatműveletek használata a REST API-ban, be kell állítani a **api-version** paraméter a következő verziójú vagy újabb:

- 2018-01-01-preview

Az Azure Portalon is lehetővé teszi a felhasználóknak megkeresheti és felügyelheti a várólisták és a Blob tartalmát felületének előzetese tárolók az Azure AD-n keresztül. Megtekintheti és kezelheti a várakozási sorban vagy Blobban tároló kattintson a tartalmát a **feltárhatja az adatait az Azure AD preview rendszert használó** a storage-fiók áttekintése.

![Ismerkedés az üzenetsorok és a Blob-tárolók használatával az Azure AD-előzetes verzió](./media/role-definitions/rbac-dataactions-browsing.png)

## <a name="actions"></a>Műveletek

A `Actions` engedélyt megadja a szerepkör lehetővé teszi, hogy a végrehajtandó felügyeleti műveleteket. Művelet karakterláncok, amelyek azonosítják az Azure-erőforrás-szolgáltatók biztonságos műveletek egy gyűjteménye. Íme néhány példa a felügyeleti műveleteket is használható `Actions`.

| A művelet karakterlánc    | Leírás         |
| ------------------- | ------------------- |
| `*/read` | Biztosít hozzáférést, az olvasási műveletek az összes Azure-erőforrás-szolgáltató minden erőforrástípus esetén.|
| `Microsoft.Compute/*` | Engedélyezi a hozzáférést a minden erőforrás esetében a Microsoft.Compute erőforrás-szolgáltató az összes műveletet.|
| `Microsoft.Network/*/read` | Engedélyezi a hozzáférést az olvasási műveletek esetében a Microsoft.Network erőforrás-szolgáltató az összes erőforrástípus.|
| `Microsoft.Compute/virtualMachines/*` | Engedélyezi a hozzáférést az összes műveletet a virtuális gépeket, és annak gyermek erőforrástípusok.|
| `microsoft.web/sites/restart/Action` | Webalkalmazást indíthat újra hozzáférést biztosít.|

## <a name="notactions"></a>Nem műveletek

A `NotActions` engedélyt megadja a felügyeleti műveleteket, amelyek ki vannak zárva az engedélyezett a `Actions`. Használja a `NotActions` engedélyt, ha engedélyezni szeretné műveletkészletet könnyebben megadva korlátozott műveletek kizárásával. A szerepkör (hatályos engedélyek) által biztosított hozzáférést számított kivonásával történik a `NotActions` műveleteket az a `Actions` műveleteket.

> [!NOTE]
> Ha egy felhasználó lesz hozzárendelve egy szerepkörhöz, amely nem tartalmazza a művelet `NotActions`, és hozzá van rendelve egy második szerepkör, amely hozzáférést biztosít a műveletben, a felhasználó számára engedélyezett a művelet végrehajtásához. `NotActions` nem megtagadási szabály – egyszerűen kényelmesen engedélyezett műveletek készletének létrehozása, ha adott műveletek kell ki lesznek zárva.
>

## <a name="dataactions-preview"></a>a dataActions (előzetes verzió)

A `DataActions` engedélyt megadja az adatműveletekre, amely a szerepkör lehetővé teszi, hogy végrehajtani ahhoz, hogy az adatok az objektumon belül. Például ha a felhasználó rendelkezik-e olvasási adatelérési blob storage-fiókba, majd elolvasása, hogy a tárfiókban található blobok. Az alábbiakban néhány példát, amely használható Adatműveletek `DataActions`.

| A művelet karakterlánc    | Leírás         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Egy blobot vagy blobok listáját adja vissza. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Blob írásának eredményét adja vissza. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Egy üzenetet ad vissza. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Üzenet vagy írásakor vagy üzenet törlésének eredményét adja vissza. |

## <a name="notdataactions-preview"></a>notDataActions (előzetes verzió)

A `NotDataActions` engedélyt megadja a kizárt Adatműveletek az engedélyezett a `DataActions`. A szerepkör (hatályos engedélyek) által biztosított hozzáférést számított kivonásával történik a `NotDataActions` műveleteket az a `DataActions` műveleteket. Mindegyik erőforrás-szolgáltató biztosítja a megfelelő API-k Adatműveletek teljesítéséhez.

> [!NOTE]
> Ha egy felhasználó lesz hozzárendelve egy szerepkörhöz, amely nem tartalmazza egy adat-művelet `NotDataActions`, és hozzá van rendelve egy második szerepkör, amely hozzáférést biztosít az ugyanazon művelet, a felhasználó számára engedélyezett a adatok művelet végrehajtásához. `NotDataActions` nem megtagadási szabály – egyszerűen csak egy kényelmes módot engedélyezett műveletek csoportját hozhatja létre, amikor a meghatározott zárható ki kell legyen.
>

## <a name="assignablescopes"></a>AssignableScopes

A `AssignableScopes` tulajdonság határozza meg, hogy a szerepkör-hozzárendelés érhető hatókörök (előfizetések, erőforráscsoportok vagy az erőforrások). Elérhetővé teheti a szerepkör-hozzárendelés csak az előfizetések alatt vagy erőforráscsoportok, és nem a legyen zsúfolt felhasználó igénylő többi része az előfizetések vagy erőforráscsoportok esetében tapasztalható. Kell használni legalább egy előfizetés, erőforráscsoport vagy erőforrás-azonosítója.

Beépített szerepkör rendelkezik `AssignableScopes` állítsa be a legfelső szintű hatókörhöz (`"/"`). A gyökérszintű hatókörben azt jelzi, hogy a szerepkör minden hatókör-hozzárendelés érhető el. Érvényes hozzárendelhető hatókörökkel közé:

| Forgatókönyv | Példa |
|----------|---------|
| Szerepkör-hozzárendelés egy előfizetés érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Szerepkör-hozzárendelés két előfizetéssel érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Szerepkör-hozzárendelés csak a hálózati erőforrás-csoport érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Szerepkör-hozzárendelés az összes hatókör érhető el | `"/"` |

További információ `AssignableScopes` tekintse meg az egyéni szerepkörökhöz [egyéni szerepkörök az Azure-erőforrások](custom-roles.md).

## <a name="next-steps"></a>További lépések

* [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
* [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
* [Az Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md)

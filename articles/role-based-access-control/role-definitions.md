---
title: A RBAC for Azure-erőforrások szerepkör-definícióinak megismerése | Microsoft Docs
description: A szerepköralapú hozzáférés-vezérlés (RBAC) szerepkör-definícióinak megismerése az Azure-erőforrások részletes hozzáférés-kezeléséhez.
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
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 3ff4b2cb6a59a35dc6da4748a7c7fbb4758a4fcf
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980999"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Az Azure-erőforrások szerepkör-definícióinak megismerése

Ha meg szeretné ismerni, hogyan működik a szerepkör, vagy ha saját [Egyéni szerepkört hoz létre az Azure-erőforrásokhoz](custom-roles.md), hasznos lehet megérteni a szerepkörök definiálásának módját. Ez a cikk a szerepkör-definíciók részleteit ismerteti, és példákat is tartalmaz.

## <a name="role-definition-structure"></a>Szerepkör-definíciós struktúra

A *szerepkör-definíció* engedélyek gyűjteménye. Szokás egyszerűen csak *szerepkörnek* is nevezni. A szerepkör-definíció a végrehajtható műveletek listáját tartalmazza (például olvasás, írás és törlés). Emellett azon műveleteket is tartalmazhatja, amelyek nem hajthatók végre, vagy amelyek a mögöttes adatokhoz kapcsolódnak. A szerepkör-definíció a következő szerkezettel rendelkezik:

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

A műveletek a következő formátumú karakterláncokkal vannak megadva:

- `{Company}.{ProviderName}/{resourceType}/{action}`

A műveleti karakterlánc `{action}` szakasza határozza meg az erőforrástípus által elvégezhető műveletek típusát. Például a következő alsztringek jelennek meg a `{action}`ban:

| Művelet alkarakterlánca    | Leírás         |
| ------------------- | ------------------- |
| `*` | A helyettesítő karakter a sztringnek megfelelő összes művelethez hozzáférést biztosít. |
| `read` | Olvasási műveletek (GET) engedélyezése. |
| `write` | Írási műveletek (PUT vagy javítás) engedélyezése. |
| `action` | Engedélyezi az olyan egyéni műveleteket, mint például a virtuális gépek újraindítása (POST). |
| `delete` | Engedélyezi a törlési műveleteket (Törlés). |

Itt látható a [közreműködő](built-in-roles.md#contributor) szerepkör definíciója JSON formátumban. Az `Actions``*` helyettesítő karakterének művelete azt jelzi, hogy a szerepkörhöz rendelt szolgáltatásnév minden műveletet elvégezhet, azaz mindent kezelhet. Ez az Azure által hozzáadott új erőforrástípusokkal együtt a jövőben meghatározott műveletekre is vonatkozik. A `NotActions` műveleteit kivonjuk az `Actions` elemből. A [Contributor](built-in-roles.md#contributor) szerepkör esetében a `NotActions` eltávolítja a szerepkör erőforrások hozzáférésének kezelésére, valamint megadására vonatkozó engedélyét.

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

## <a name="management-and-data-operations"></a>Kezelési és adatműveletek

A felügyeleti műveletek szerepköralapú hozzáférés-vezérlése a szerepkör-definíciók `Actions` és `NotActions` tulajdonságaiban van megadva. Íme néhány példa az Azure-beli felügyeleti műveletekre:

- Storage-fiókhoz való hozzáférés kezelése
- BLOB-tároló létrehozása, frissítése vagy törlése
- Erőforráscsoport és az összes erőforrás törlése

A felügyeleti hozzáférés nem örökli az adatait, ha a tároló hitelesítési módszere "Azure AD felhasználói fiók", és nem "hozzáférési kulcs". Ez az elkülönítés megakadályozza, hogy a helyettesítő karakterrel rendelkező szerepkörök (`*`) ne férhessenek hozzá az adatokhoz. Ha például egy felhasználó rendelkezik egy előfizetéshez tartozó [olvasó](built-in-roles.md#reader) szerepkörrel, akkor megtekintheti a Storage-fiókot, de alapértelmezés szerint nem tekinthetik meg az alapul szolgáló adatokat.

Korábban a szerepköralapú hozzáférés-vezérlést nem használták adatműveletekhez. Az adatműveletek hitelesítése az erőforrás-szolgáltatókon keresztül változhat. A felügyeleti műveletekhez használt szerepköralapú hozzáférés-vezérlési hitelesítési modell kibővült az adatműveletekre.

Az adatműveletek támogatásához új Adattulajdonságok lettek hozzáadva a szerepkör-definíciós struktúrához. Az adatműveletek a `DataActions` és `NotDataActions` tulajdonságokban mennek végbe. Ezen Adattulajdonságok hozzáadásával a rendszer megőrzi a felügyelet és az adatkezelés közötti elkülönítést. Az aktuális, helyettesítő karakterekkel (`*`) történő szerepkör-hozzárendelések így nem kaphatnak hirtelen hozzáférést az adatokhoz. A `DataActions` és `NotDataActions` elemekben többek között a következő adatműveletek adhatók meg:

- Egy tároló bloblistájának olvasása
- Tárolóblob írása egy tárolóban
- Egy üzenetsor üzenetének törlése

Itt látható a [Storage blob Adatolvasó](built-in-roles.md#storage-blob-data-reader) szerepkör-definíciója, amely a `Actions` és az `DataActions` tulajdonságaiban is tartalmaz műveleteket. Ez a szerepkör lehetővé teszi a blob-tároló és a mögöttes blob-adat olvasását.

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

A `DataActions` és `NotDataActions` tulajdonsághoz csak adatműveletek adhatók hozzá. Az erőforrás-szolgáltatók határozzák meg, hogy mely műveletek legyenek adatműveletek, ha a `isDataAction` tulajdonságot `true`értékre állítja. Ha meg szeretné tekinteni a `isDataAction` `true`műveletek listáját, tekintse meg az [erőforrás-szolgáltatói műveletek](resource-provider-operations.md)című témakört. Azok a szerepkörök, amelyek nem rendelkeznek adatműveletekkel, nem szükségesek ahhoz, hogy a szerepkör-definíción belül `DataActions` és `NotDataActions` tulajdonságokkal rendelkezzenek.

Az összes felügyeleti művelet API-hívásának hitelesítését Azure Resource Manager kezeli. Az adatműveleti API-hívások engedélyezését vagy erőforrás-szolgáltató vagy Azure Resource Manager kezeli.

### <a name="data-operations-example"></a>Adatműveletek – példa

A kezelési és az adatműveletek működésének jobb megismeréséhez Vegyünk egy konkrét példát. Alice hozzá lett rendelve a [tulajdonosi](built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. Bob hozzárendelte a [Storage blob adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkört egy Storage-fiók hatókörében. Az alábbi ábrán látható példa.

![A szerepköralapú hozzáférés-vezérlés ki van terjesztve a felügyeleti és az adatműveletek támogatására is](./media/role-definitions/rbac-management-data.png)

Az Alice [tulajdonosi](built-in-roles.md#owner) szerepköre és a [Storage blob adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepköre a következő műveletekkel rendelkezik:

Tulajdonos

&nbsp;&nbsp;&nbsp;&nbsp;műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Storage blob adatközreműködői

&nbsp;&nbsp;&nbsp;&nbsp;műveletek<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Mivel Alice egy helyettesítő karakteres (`*`) művelettel rendelkezik az előfizetések hatókörében, az engedélyeik öröklik az összes felügyeleti művelet végrehajtásának engedélyezését. Alice képes a tárolók olvasására, írására és törlésére. Alice azonban nem végezheti el az adatműveleteket további lépések végrehajtása nélkül. Alapértelmezés szerint például Alice nem tudja beolvasni a blobokat egy tárolón belül. A Blobok olvasásához Alice-nek le kell kérnie a tároló-hozzáférési kulcsokat, és a Blobok eléréséhez használnia kell őket.

A Bob engedélyei csak az `Actions`re korlátozódnak, és `DataActions` a [Storage blob-adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkörben megadott értékre. A Bob szerepkör alapján a felügyeleti és az adatműveletek is elvégezhetők. Például Bob elolvashatja, írhatja és törölheti a tárolókat a megadott Storage-fiókban, valamint elolvashatja, írhatja és törölheti is a blobokat.

A tárolással kapcsolatos felügyelettel és adatsíkokkal kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Milyen eszközök támogatják a RBAC használatát az adatműveletekhez?

Az adatműveletek megtekintéséhez és használatához az eszközök vagy SDK-k megfelelő verzióját kell megadnia:

| Eszköz  | Verzió  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 vagy újabb |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 vagy újabb |
| [Azure .NET-hez](/dotnet/azure/) | 2.8.0 – előzetes verzió vagy újabb |
| [Góhoz készült Azure SDK](/azure/go/azure-sdk-go-install) | 15.0.0 vagy újabb |
| [Azure Java-hoz](/java/azure/) | 1.9.0 vagy újabb |
| [Azure Pythonhoz](/azure/python/) | 0.40.0 vagy újabb |
| [Rubyhoz készült Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 vagy újabb |

Az REST APIban lévő adatműveletek megtekintéséhez és használatához az **API-Version** paramétert az alábbi vagy újabb verzióra kell beállítani:

- 2018-07-01

## <a name="actions"></a>Műveletek

A `Actions` engedély meghatározza azokat a felügyeleti műveleteket, amelyeket a szerepkör lehetővé tesz. Ez a műveleti karakterláncok gyűjteménye, amelyek az Azure-erőforrás-szolgáltatók biztonságos műveleteit azonosítják. Az alábbiakban néhány példát láthat a `Actions`használható felügyeleti műveletekre.

| Műveleti sztring    | Leírás         |
| ------------------- | ------------------- |
| `*/read` | Olvasási műveletekhez való hozzáférést biztosít az összes Azure-erőforrás-szolgáltató összes erőforrás-típusához.|
| `Microsoft.Compute/*` | Hozzáférést biztosít minden művelethez a Microsoft. számítási erőforrás-szolgáltató összes erőforrás-típusához.|
| `Microsoft.Network/*/read` | Olvasási műveletekhez való hozzáférést biztosít a Microsoft. Network erőforrás-szolgáltató összes erőforrás-típusához.|
| `Microsoft.Compute/virtualMachines/*` | Hozzáférést biztosít a virtuális gépek összes műveletéhez és annak alárendelt erőforrásaihoz.|
| `microsoft.web/sites/restart/Action` | Hozzáférést biztosít egy webalkalmazás újraindításához.|

## <a name="notactions"></a>NotActions

Az `NotActions` engedély határozza meg az engedélyezett `Actions`ból kizárt felügyeleti műveleteket. Használja a `NotActions` engedélyt, ha az engedélyezni kívánt műveletek csoportja a korlátozott műveletek kizárásával könnyebben definiálható. A szerepkör (érvényes engedélyek) által biztosított hozzáférés kiszámítása a `NotActions` műveletek `Actions` műveletből való kivonásával történik.

> [!NOTE]
> Ha a felhasználó olyan szerepkört rendel hozzá, amely kizárja a `NotActions`ban lévő műveletet, és olyan második szerepkörhöz van rendelve, amely hozzáférést biztosít ugyanahhoz a művelethez, a felhasználó számára engedélyezett a művelet végrehajtása. `NotActions` nem megtagadási szabály – egyszerűen egy kényelmes módszer, amely lehetővé teszi, hogy az egyes műveleteket ki kell zárni az engedélyezett műveletekből.
>

## <a name="dataactions"></a>DataActions

Az `DataActions` engedély megadja azokat az adatműveleteket, amelyeket a szerepkör engedélyez az adott objektumon belüli adatokon való végrehajtáshoz. Ha például egy felhasználó beolvasta a blob-adathozzáférést egy Storage-fiókhoz, akkor az adott Storage-fiókban található blobokat is elolvashatja. Az alábbiakban néhány példát láthat a `DataActions`használható adatműveletekre.

| Műveleti sztring    | Leírás         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Blobot vagy Blobok listáját adja vissza. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Egy blob írásának eredményét adja vissza. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Egy üzenetet ad vissza. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Egy üzenetet vagy egy üzenet írásának vagy törlésének eredményét adja vissza. |

## <a name="notdataactions"></a>NotDataActions

Az `NotDataActions` engedély az engedélyezett `DataActions`ból kizárt adatműveleteket határozza meg. A szerepkör (érvényes engedélyek) által biztosított hozzáférés kiszámítása a `NotDataActions` műveletek `DataActions` műveletből való kivonásával történik. Mindegyik erőforrás-szolgáltató biztosítja az API-k megfelelő készletét az adatműveletek teljesítéséhez.

> [!NOTE]
> Ha a felhasználó olyan szerepkört rendel hozzá, amely kizárja az adatműveleteket `NotDataActions`ban, és olyan második szerepkört rendel hozzá, amely hozzáférést biztosít ugyanahhoz az adatművelethez, a felhasználó elvégezheti az adatműveletet. `NotDataActions` nem megtagadási szabály – egyszerűen egy kényelmes módszer, amely lehetővé teszi, hogy az egyes adatműveletek kizárásakor az engedélyezett adatműveletek készletét is létrehozza.
>

## <a name="assignablescopes"></a>AssignableScopes

A `AssignableScopes` tulajdonság határozza meg azokat a hatóköröket (felügyeleti csoportok, előfizetések, erőforráscsoportok vagy erőforrások), amelyekre ez a szerepkör-definíció elérhető. A szerepkört csak azokhoz a felügyeleti csoportokhoz, előfizetésekhez vagy erőforráscsoportokhöz lehet hozzárendelni, amelyekhez szükség van. Legalább egy felügyeleti csoportot, előfizetést, erőforráscsoportot vagy erőforrás-azonosítót kell használnia.

A beépített szerepkörök `AssignableScopes` a gyökérszintű hatókörre (`"/"`) vannak beállítva. A gyökérszintű hatókör azt jelzi, hogy a szerepkör minden hatókörben elérhető a hozzárendeléshez. Érvényes hozzárendelhető hatókörök például a következők:

| A szerepkör hozzárendelésre elérhető | Példa |
|----------|---------|
| Egy előfizetés | `"/subscriptions/{subscriptionId1}"` |
| Két előfizetés | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
| Hálózati erőforráscsoport | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
| Egy felügyeleti csoport | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
| Felügyeleti csoport és előfizetés | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
| Minden hatókör (csak a beépített szerepkörökre vonatkozik) | `"/"` |

Az egyéni szerepkörök `AssignableScopes`ával kapcsolatos további információkért lásd: [Egyéni szerepkörök az Azure-erőforrásokhoz](custom-roles.md).

## <a name="next-steps"></a>Következő lépések

* [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
* [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
* [Erőforrás-szolgáltatói műveletek Azure Resource Manager](resource-provider-operations.md)

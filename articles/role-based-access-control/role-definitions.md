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
ms.date: 06/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: ece5d8f9733dde57c2990bfbb6bb90305d8bc37d
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977193"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Az Azure-erőforrások szerepkör-definícióinak megismerése

Ha meg szeretné ismerni, hogyan működik a szerepkör, vagy ha saját egyéni szerepkört hoz létre [Az Azure](custom-roles.md)-erőforrásokhoz, hasznos lehet megérteni a szerepkörök definiálásának módját. Ez a cikk a szerepkör-definíciók részleteit ismerteti, és példákat is tartalmaz.

## <a name="role-definition-structure"></a>Szerepkör-definíciós struktúra

A *szerepkör-definíció* engedélyek gyűjteménye. Szokás egyszerűen csak *szerepkörnek* is nevezni. A szerepkör-definíció a végrehajtható műveletek listáját tartalmazza (például olvasás, írás és törlés). Azokat a műveleteket is listázhatja, amelyek nem hajthatók végre, illetve az alapul szolgáló adatokhoz kapcsolódó műveleteket is. A szerepkör-definíció a következő szerkezettel rendelkezik:

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

A `{action}` műveleti karakterláncok része az erőforrástípus által elvégezhető műveletek típusát határozza meg. Például a következő alsztringek jelennek meg a ben `{action}`:

| Művelet alkarakterlánca    | Leírás         |
| ------------------- | ------------------- |
| `*` | A helyettesítő karakter a sztringnek megfelelő összes művelethez hozzáférést biztosít. |
| `read` | Olvasási műveletek (GET) engedélyezése. |
| `write` | Írási műveletek (PUT vagy javítás) engedélyezése. |
| `action` | Engedélyezi az olyan egyéni műveleteket, mint például a virtuális gépek újraindítása (POST). |
| `delete` | Engedélyezi a törlési műveleteket (Törlés). |

Itt látható a [közreműködő](built-in-roles.md#contributor) szerepkör definíciója JSON formátumban. A helyettesítő karakteres (`*`) `Actions` művelet azt jelzi, hogy az ehhez a szerepkörhöz rendelt rendszerbiztonsági tag minden műveletet végrehajthat, vagy más szóval, mindent kezelhet. Ez magában foglalja a jövőben meghatározott műveleteket is, mivel az Azure új erőforrástípusok hozzáadását teszi elérhetővé. A alatti `NotActions` műveletek kivonása a következőből `Actions`történik:. A [közreműködő](built-in-roles.md#contributor) szerepkör `NotActions` esetében eltávolítja ezt a szerepkört az erőforrásokhoz való hozzáférés kezeléséhez, valamint az erőforrásokhoz való hozzáférés kiosztásához is.

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

A felügyeleti műveletek szerepköralapú hozzáférés- `Actions` vezérlése a szerepkör-definíció és `NotActions` tulajdonságaiban van megadva. Íme néhány példa az Azure-beli felügyeleti műveletekre:

- Storage-fiókhoz való hozzáférés kezelése
- BLOB-tároló létrehozása, frissítése vagy törlése
- Erőforráscsoport és az összes erőforrás törlése

A felügyeleti hozzáférés nem örökli az adatait. Ez az elkülönítés megakadályozza, hogy a helyettesítő`*`karakterrel rendelkező szerepkörök () ne legyenek korlátozott hozzáférésük az adatokhoz. Ha például egy felhasználó rendelkezik egy előfizetéshez tartozó [olvasó](built-in-roles.md#reader) szerepkörrel, akkor megtekintheti a Storage-fiókot, de alapértelmezés szerint nem tekinthetik meg az alapul szolgáló adatokat.

Korábban a szerepköralapú hozzáférés-vezérlést nem használták adatműveletekhez. Az adatműveletek hitelesítése az erőforrás-szolgáltatókon keresztül változhat. A felügyeleti műveletekhez használt szerepköralapú hozzáférés-vezérlési hitelesítési modell kibővült az adatműveletekre.

Az adatműveletek támogatásához új Adattulajdonságok lettek hozzáadva a szerepkör-definíciós struktúrához. Az adatműveletek a és `DataActions` `NotDataActions` a tulajdonságok között vannak megadva. Ezen Adattulajdonságok hozzáadásával a rendszer megőrzi a felügyelet és az adatkezelés közötti elkülönítést. Ez megakadályozza, hogy az aktuális szerepkör-hozzárendeléseket helyettesítő karakterekkel (`*`) lehessen elérni, és az adatokhoz való hozzáférés hirtelen megtörténjen. Íme néhány olyan adatművelet, amely megadható `DataActions` a `NotDataActions`és a alkalmazásban:

- A tárolóban lévő Blobok listájának beolvasása
- Tárolási blob írása egy tárolóba
- Üzenetsor törlése

Itt látható a [Storage blob Adatolvasó](built-in-roles.md#storage-blob-data-reader) szerepkör-definíciója, amely a és `Actions` `DataActions` a tulajdonságok között is tartalmaz műveleteket. Ez a szerepkör lehetővé teszi a blob-tároló és a mögöttes blob-adat olvasását.

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

Csak adatműveletek adhatók hozzá a `DataActions` és `NotDataActions` a tulajdonsághoz. Az erőforrás-szolgáltatók határozzák meg, hogy mely műveletek legyenek adatműveletek, ha `isDataAction` `true`a tulajdonságot értékre állítja. A következő műveletek `isDataAction` `true`listájának megtekintéséhez lásd: erőforrás-szolgáltatói [műveletek](resource-provider-operations.md). Azok a szerepkörök, amelyek nem rendelkeznek adatműveletekkel, nem `DataActions` szükségesek a szerepkör-definícióban lévő és `NotDataActions` a tulajdonságok eléréséhez.

Az összes felügyeleti művelet API-hívásának hitelesítését Azure Resource Manager kezeli. Az adatműveleti API-hívások engedélyezését vagy erőforrás-szolgáltató vagy Azure Resource Manager kezeli.

### <a name="data-operations-example"></a>Adatműveletek – példa

A kezelési és az adatműveletek működésének jobb megismeréséhez Vegyünk egy konkrét példát. Alice hozzá lett rendelve a [tulajdonosi](built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. Bob hozzárendelte a [Storage blob adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkört egy Storage-fiók hatókörében. Az alábbi ábrán látható példa.

![A szerepköralapú hozzáférés-vezérlés ki van terjesztve a felügyeleti és az adatműveletek támogatására is](./media/role-definitions/rbac-management-data.png)

Az [](built-in-roles.md#owner) Alice tulajdonosi szerepköre és a [Storage blob adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepköre a következő műveletekkel rendelkezik:

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

Mivel Alice egy helyettesítő karakter (`*`) művelettel rendelkezik egy előfizetési hatókörben, az engedélyeik öröklik az összes felügyeleti művelet végrehajtását. Alice képes a tárolók olvasására, írására és törlésére. Alice azonban nem végezheti el az adatműveleteket további lépések végrehajtása nélkül. Alapértelmezés szerint például Alice nem tudja beolvasni a blobokat egy tárolón belül. A Blobok olvasásához Alice-nek le kell kérnie a tároló-hozzáférési kulcsokat, és a Blobok eléréséhez használnia kell őket.

A Bob engedélyei a [Storage blob](built-in-roles.md#storage-blob-data-contributor) -adatközreműködői `DataActions` szerepkörben csak a és a `Actions` értékre korlátozódnak. A Bob szerepkör alapján a felügyeleti és az adatműveletek is elvégezhetők. Például Bob elolvashatja, írhatja és törölheti a tárolókat a megadott Storage-fiókban, valamint elolvashatja, írhatja és törölheti is a blobokat.

A tárolással kapcsolatos felügyelettel és adatsíkokkal kapcsolatos további információkért lásd az [Azure Storage biztonsági útmutatóját](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Milyen eszközök támogatják a RBAC használatát az adatműveletekhez?

Az adatműveletek megtekintéséhez és használatához az eszközök vagy SDK-k megfelelő verzióját kell megadnia:

| Eszköz  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 vagy újabb |
| [Azure CLI](/cli/azure/install-azure-cli) | 2.0.30 vagy újabb |
| [Azure .NET-hez](/dotnet/azure/) | 2.8.0 – előzetes verzió vagy újabb |
| [Góhoz készült Azure SDK](/azure/go/azure-sdk-go-install) | 15.0.0 vagy újabb |
| [Azure Java-hoz](/java/azure/) | 1.9.0 vagy újabb |
| [Azure Pythonhoz](/python/azure) | 0.40.0 vagy újabb |
| [Rubyhoz készült Azure SDK](https://rubygems.org/gems/azure_sdk) | 0.17.1 vagy újabb |

Az REST APIban lévő adatműveletek megtekintéséhez és használatához az **API-Version** paramétert az alábbi vagy újabb verzióra kell beállítani:

- 2018-07-01

## <a name="actions"></a>Műveletek

Az `Actions` engedély meghatározza azokat a felügyeleti műveleteket, amelyeket a szerepkör engedélyez. Ez a műveleti karakterláncok gyűjteménye, amelyek az Azure-erőforrás-szolgáltatók biztonságos műveleteit azonosítják. Íme néhány példa a szolgáltatásban használható `Actions`felügyeleti műveletekre.

| Műveleti sztring    | Leírás         |
| ------------------- | ------------------- |
| `*/read` | Olvasási műveletekhez való hozzáférést biztosít az összes Azure-erőforrás-szolgáltató összes erőforrás-típusához.|
| `Microsoft.Compute/*` | Hozzáférést biztosít minden művelethez a Microsoft. számítási erőforrás-szolgáltató összes erőforrás-típusához.|
| `Microsoft.Network/*/read` | Olvasási műveletekhez való hozzáférést biztosít a Microsoft. Network erőforrás-szolgáltató összes erőforrás-típusához.|
| `Microsoft.Compute/virtualMachines/*` | Hozzáférést biztosít a virtuális gépek összes műveletéhez és annak alárendelt erőforrásaihoz.|
| `microsoft.web/sites/restart/Action` | Hozzáférést biztosít egy webalkalmazás újraindításához.|

## <a name="notactions"></a>Nem műveletek

Az `NotActions` engedély meghatározza azokat a felügyeleti műveleteket, amelyek ki vannak zárva az `Actions`engedélyezetttől. Akkor használja `NotActions` az engedélyt, ha az engedélyezni kívánt műveletek csoportja a korlátozott műveletek kizárásával könnyebben definiálható. A szerepkör (hatályos engedélyek) által biztosított hozzáférés kiszámítása a műveletek műveletből való kivonásával `NotActions` `Actions` történik.

> [!NOTE]
> Ha a felhasználó olyan szerepkört rendel hozzá, amely kizár egy műveletet `NotActions`a alkalmazásban, és olyan második szerepkörhöz van rendelve, amely hozzáférést biztosít ugyanahhoz a művelethez, a felhasználó számára engedélyezett a művelet végrehajtása. `NotActions`nem megtagadási szabály – egyszerűen egy kényelmes módszer, amely lehetővé teszi, hogy az egyes műveleteket ki kell zárni.
>

## <a name="dataactions"></a>Adatműveletek

Az `DataActions` engedély meghatározza azokat az adatműveleteket, amelyeket a szerepkör engedélyez az adott objektumon belüli adatokon való végrehajtáshoz. Ha például egy felhasználó beolvasta a blob-adathozzáférést egy Storage-fiókhoz, akkor az adott Storage-fiókban található blobokat is elolvashatja. Íme néhány példa a szolgáltatásban használható `DataActions`adatműveletekre.

| Műveleti sztring    | Leírás         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Blobot vagy Blobok listáját adja vissza. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Egy blob írásának eredményét adja vissza. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Egy üzenetet ad vissza. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Egy üzenetet vagy egy üzenet írásának vagy törlésének eredményét adja vissza. |

## <a name="notdataactions"></a>Nem adatműveletek

Az `NotDataActions` engedély meghatározza azokat az adatműveleteket, amelyek ki vannak zárva `DataActions`az engedélyezett tartományból. A szerepkör (hatályos engedélyek) által biztosított hozzáférés kiszámítása a műveletek műveletből való kivonásával `NotDataActions` `DataActions` történik. Mindegyik erőforrás-szolgáltató biztosítja az API-k megfelelő készletét az adatműveletek teljesítéséhez.

> [!NOTE]
> Ha egy felhasználó olyan szerepkört kap, amely kizár egy adatműveletet `NotDataActions`a alkalmazásban, és olyan második szerepkört rendel hozzá, amely ugyanahhoz az adatművelethez hozzáférést biztosít, akkor a felhasználó elvégezheti az adatműveletet. `NotDataActions`nem megtagadási szabály – egyszerűen csak egy kényelmes módszer, amely lehetővé teszi az adatműveletek egy készletének kizárását, ha bizonyos adatműveleteket ki kell zárni.
>

## <a name="assignablescopes"></a>AssignableScopes

A `AssignableScopes` tulajdonság meghatározza azokat a hatóköröket (előfizetéseket, erőforráscsoportokat vagy erőforrásokat), amelyeken ez a szerepkör-definíció elérhető. A szerepkört csak azokhoz az előfizetésekhez vagy erőforráscsoportokhöz lehet kijelölni, amelyeknek szükségük van rá, és ne legyenek túlterhelve a felhasználói élmény a többi előfizetés vagy erőforráscsoport esetében. Legalább egy előfizetést, erőforráscsoportot vagy erőforrás-azonosítót kell használnia.

A beépített szerepkörök a gyökérszintű `AssignableScopes` hatókörre (`"/"`) vannak beállítva. A gyökérszintű hatókör azt jelzi, hogy a szerepkör minden hatókörben elérhető a hozzárendeléshez. Érvényes hozzárendelhető hatókörök például a következők:

| Forgatókönyv | Példa |
|----------|---------|
| A szerepkör egyetlen előfizetésben használható hozzárendeléshez. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| A szerepkör két előfizetésben is elérhető a hozzárendeléshez | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| A szerepkör csak a hálózati erőforráscsoporthoz való hozzárendeléshez érhető el | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| A szerepkör minden hatókörben elérhető a hozzárendeléshez (csak a beépített szerepkörökre vonatkozik) | `"/"` |

További információ `AssignableScopes` az egyéni szerepkörökről: [Egyéni szerepkörök az Azure](custom-roles.md)-erőforrásokhoz.

## <a name="next-steps"></a>További lépések

* [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
* [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
* [Erőforrás-szolgáltatói műveletek Azure Resource Manager](resource-provider-operations.md)

---
title: "Megosztás pillanatképekkel (előzetes verzió) |} Microsoft Docs"
description: "Megosztás pillanatképet egy olyan Azure fájlok megosztási konzolán kell elvégezni egy ideje, készítsen biztonsági másolatot a megosztást is csak olvasható verziója telepítve."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 5212866bda9ff775d32ebb57874b3d58e11f1eb3
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="work-with-share-snapshots-preview"></a>Megosztás pillanatképekkel (előzetes verzió)
Egy megosztás pillanatkép (előzetes verzió) egy olyan Azure fájlok megosztási egy időben lesz végrehajtva csak olvasható verziója telepítve. Megosztás pillanatképének létrehozása után azt is kell olvasni, másolja, vagy törölni, de nem módosított. Készítsen biztonsági másolatot a megosztás, ahogyan megjelenik egy időben el módszert kínál a megosztás pillanatképet. 

Ön ebből a cikkből megtudhatja, hogyan létrehozására, kezelésére és törölje a megosztást pillanatképeket. További információkért lásd: a [áttekintése pillanatkép megosztása](storage-snapshots-files.md) vagy a [pillanatkép-gyakran ismételt kérdések](storage-files-faq.md).

## <a name="create-a-share-snapshot"></a>Megosztás pillanatkép létrehozása

Egy megosztás pillanatkép az Azure-portálon, PowerShell, az Azure parancssori felület, a REST API vagy bármely Azure Storage szolgáltatás SDK használatával hozhat létre. Az alábbi szakaszok azt ismertetik, hogyan megosztás pillanatkép létrehozása a portálon, a parancssori felület és a PowerShell segítségével. 

Fájlmegosztás megosztás pillanatképet is igénybe vehet, amíg használatban van. Megosztás pillanatképek azonban csak a már ki lett írva a fájlmegosztáshoz, amikor a megosztás Pillanatkép parancs kiadása adatokat rögzíti. Ez lehet, hogy zárja ki minden olyan alkalmazást vagy az operációs rendszer által gyorsítótárazott adatokat.

### <a name="create-a-share-snapshot-by-using-the-portal"></a>Megosztás pillanatkép létrehozása a portál használatával  
Időpontban megosztás pillanatkép létrehozásához nyissa meg a fájl megosztási a portálon, és válassza ki **pillanatkép létrehozása**.

>   ![A portál pillanatkép menü](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-a-share-snapshot-by-using-azure-cli-20"></a>Megosztás pillanatkép létrehozása az Azure CLI 2.0 használatával
Megosztás pillanatkép segítségével létrehozható a `az storage share snapshot` parancs:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Példa a kimenetre:
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-a-share-snapshot-by-using-powershell"></a>Megosztás pillanatkép létrehozása a PowerShell használatával
Megosztás pillanatkép segítségével létrehozható a `$share.Snapshot()` parancs:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```

## <a name="perform-common-share-snapshot-operations"></a>Általános fájlmegosztás pillanatkép műveletek végrehajtása

A megosztás pillanatképek használatával a fájlmegosztás társított enumerálása a **korábbi verziók** lapon a Windows, a többi, az ügyféloldali kódtár, PowerShell és a portálon keresztül. Után a fájlmegosztás csatlakoztatva van, a fájl összes korábbi verzióját használatával megtekintheti a **korábbi verziók** Windows lapján. 

Az alábbi szakaszok azt ismertetik, hogyan használhatja az Azure-portálon, a Windows és az Azure CLI 2.0 listában keresse meg, és állítson vissza egy pillanatképet a megosztást.

### <a name="share-snapshot-operations-in-the-portal"></a>Megosztás pillanatkép műveletek a portálon

Vessen egy pillantást az összes fájl a megosztás pillanatképek megosztani a portálon, és keresse meg a megosztás pillanatkép megtekinthesse annak tartalmát.

#### <a name="view-a-share-snapshot"></a>Egy megosztás pillanatkép megtekintése
A fájlmegosztáson a **pillanatkép**, jelölje be **pillanatképek megtekintéséhez**.

!["View pillanatképek" parancsot a portálon](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-to-share-snapshot-content"></a>Listáról, és keresse meg a pillanatkép megosztása
Megosztás pillanatképek listájának megtekintéséhez, és tallózással keressen meg egy pillanatkép tartalmát közvetlenül a kívánt időbélyeg kiválasztásával.

![Kijelölt pillanatkép a időbélyegeket listájában](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

Igény szerint kiválaszthatja a **Connect** gombra a lista a pillanatkép-nézet az beszerzése a `net use` parancs és egy adott pillanatkép elérési útját. Majd közvetlenül tallózással megkereshet, hogy a pillanatkép.


![Csatlakozás ablaktábla parancs mezőben](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-a-share-snapshot"></a>Töltse le, vagy szeretné visszaállítani a megosztás pillanatkép
A portálról, töltse le, vagy állítsa vissza a fájlt egy pillanatképből használatával a **letöltése** vagy **visszaállítása** gomb, illetve.

![A letöltés és visszaállítási gombok](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="share-snapshot-operations-in-windows"></a>A Windows megosztás pillanatkép műveletek
Már elvégezte a fájlmegosztás megosztás pillanatképeket, tekintheti a megosztást, könyvtár vagy egy adott fájl korábbi verzióiban a Windows, a csatlakoztatott fájlmegosztásból. Tegyük fel ez hogyan használhatja a korábbi verziók szolgáltatásának megtekintéséhez és a könyvtárat, a Windows előző verziót.

> [!Note]  
> A fájl szintjén vagy a megosztás szintű ugyanazokat a műveleteket végezheti el. Csak a verziója, amely tartalmazza az adott könyvtár vagy fájl módosításait a listája látható. Ha egy adott könyvtár vagy fájl nem változott két megosztás pillanatképek között, a megosztás pillanatkép jelenik meg, a megosztási szintű előző verzió lista, de nem az előző verzió lista a könyvtár vagy fájl.

#### <a name="mount-a-file-share"></a>Fájlmegosztás csatlakoztatása
Első lépésként használatával a fájlmegosztás csatlakoztatása a `net use` parancsot.

#### <a name="open-a-mounted-share-in-file-explorer"></a>A Fájlkezelőben csatlakoztatott megosztás megnyitása
Nyissa meg a Fájlkezelőben és a csatlakoztatott megosztást található.

![A Fájlkezelőben csatlakoztatott megosztást](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Korábbi verziók listája
Tallózással keresse meg a cikk vagy a fölérendelt elemtől kell állítani. Kattintson duplán a kívánt címtár Ugrás. Kattintson a jobb gombbal, és válassza ki **tulajdonságok** a menüből.

![Kattintson a jobb gombbal a kijelölt könyvtár menü](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Válassza ki **korábbi verziók** megosztás pillanatképek ennél a címtárnál listájának megtekintéséhez. A lista lehet, hogy néhány másodpercre betölteni, attól függően, hogy a hálózati sebesség és a címtárban megosztás pillanatképek számát.

![Előző verziók lap](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

Kiválaszthatja **nyissa meg a** adott pillanatkép megnyitásához. 

![Pillanatkép megnyitása](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Visszaállítás egy korábbi verziójáról
Válassza ki **visszaállítása** átmásolása a megosztás pillanatkép létrehozáskor a teljes címtár rekurzív módon tartalmát az eredeti helyre.
 ![Visszaállítás gomb figyelmeztető üzenet](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="share-snapshot-operations-in-azure-cli-20"></a>Az Azure CLI 2.0 pillanatkép-műveletekkel megosztása
Azure CLI 2.0 segítségével hajtsa végre a műveletek, például a listaelem megosztás pillanatképeket pillanatkép megosztása való tallózással, visszaállítása, vagy a megosztás pillanatképekkel vagy törlése a fájlok letöltése megosztási pillanatképeket.

#### <a name="list-share-snapshots"></a>Lista megosztás pillanatképek

Egy adott megosztás pillanatképek segítségével listázhatja [ `az storage share list` ](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) rendelkező `--include-snapshots`:

```azurecli-interactive 
az storage share list --include-snapshots
```

A parancs lehetővé teszi a megosztás pillanatképek, valamint a kapcsolódó tulajdonságok listáját. A következő egy példa kimenete:

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-to-a-share-snapshot"></a>Keresse meg a megosztás pillanatkép
Keresse meg az adott pillanatképet, és megtekinthesse annak tartalmát a [ `az storage file list` ](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). Adja meg a megosztás és az időbélyegzőt, amelyet szeretne keresse meg, a következő példában látható módon:

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

A kimenetben azt látja, hogy a megosztás pillanatkép tartalma megegyezik a tartalomhoz a megosztás, az időpont, hogy a megosztás pillanatkép jött létre:

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-a-share-snapshot"></a>A megosztás pillanatkép visszaállítása

Visszaállíthatja a fájl másolása, vagy letöltheti a megosztás pillanatképből. Használja a `az storage file download` parancs, a következő példában látható módon:

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

A kimenet jelenik meg, hogy a letöltött fájl és a tulajdonságait tartalma megegyezik a tartalomhoz, és tulajdonságai párbeszédpanelen az időpont, amelyek közös pillanatkép jött létre:

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-a-share-snapshot"></a>Megosztás pillanatkép törlése

Az Azure-portálon, PowerShell, CLI-t, a REST API-t vagy a Storage szolgáltatás SDK használatával törölje a megosztást pillanatképeket. Az alábbi szakaszok azt ismertetik, hogyan megosztás pillanatképek törlése az Azure-portálon, a parancssori felület és a PowerShell használatával.

Böngészhet a pillanatképek megosztásához, és bármely összehasonlító eszközzel két pillanatképek közötti különbségek megtekintése. Megadhatja, hogy mely megosztás pillanatképet törölni szeretné. 

Nem törölhető egy megosztásra, amely rendelkezik egy pillanatkép. Nem fogja tudni törli a megosztást, először törölnie kell a megosztás pillanatképek.

### <a name="delete-a-share-snapshot-by-using-the-portal"></a>A portál használatával megosztás pillanatkép törlése  
A portálon, nyissa meg a fájl megosztási paneljét és használja a **törlése** gombra egy vagy több megosztást a pillanatképek törléséhez.

>   ![Gomb törlése](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-by-using-azure-cli-20"></a>Megosztás pillanatkép törlése az Azure CLI 2.0 használatával
Egy megosztás pillanatkép használatával törölheti a `[az storage share delete]` parancsot. Használja a megosztás pillanatkép időbélyegzője a `--snapshot '2017-10-04T23:28:35.0000000Z' ` paraméter a következő példa:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Példa a kimenetre:
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-by-using-powershell"></a>Megosztás pillanatkép törlése a PowerShell használatával
Egy megosztás pillanatkép használatával törölheti a `Remove-AzureStorageShare -Share` parancs:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##Create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Következő lépések
* [Pillanatkép áttekintése](storage-snapshots-files.md)
* [Pillanatkép – gyakori kérdések](storage-files-faq.md)

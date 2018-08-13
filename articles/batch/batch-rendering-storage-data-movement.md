---
title: Az Azure Batch tárolási és adatátviteli renderelési
description: Tárolás és adatkezelés adatáthelyezési beállítások renderelési számítási feladatokhoz
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d5b006fd744e463c73ee0a32388f254017e96354
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "40034757"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Tárolás és adatkezelés adatáthelyezési beállítások eszköz- és kimeneti fájlok megjelenítése

A helyszín és az eszközintelligencia fájlokat a virtuális gépek készletet a renderelési alkalmazások elérhetővé tétele a több lehetőség áll rendelkezésre:

* [Az Azure blob storage-bA](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * Jelenetek és az eszközintelligencia fájlokat töltenek fel a blob storage egy helyi fájlrendszerből. Ha az alkalmazás egy feladat fut, majd a szükséges fájlok lesznek másolva be a virtuális Gépre a blob storage-ból a renderelési alkalmazások érhető el, így. A kimeneti fájlok a renderelési alkalmazások, a Virtuálisgép-lemez által összeállított és majd át lesznek másolva a blob storage.  Ha szükséges, a kimeneti fájlok letölthető blob storage-ból a helyi fájlrendszerbe.
  * Az Azure blob storage-bA az egyszerű és költséghatékony lehetőség kisebb projektekhez.  Az összes eszköz fájlra van szükség minden készlet virtuális Gépet, majd miután száma és az eszközintelligencia-fájlok mérete nő, óvatosan kell tenni a fájlátvitel lehető leghatékonyabb.  
* Az Azure storage-ot egy fájl rendszer használatával [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * Linux rendszerű virtuális gépek tárfiókok elérhetővé tett és alkalmazni, mint egy fájlrendszer a blobfuse virtuális fájlrendszer illesztőprogramja szolgál.
  * Ez a beállítás az előnnyel jár, hogy rendkívül költséghatékony, nincsenek olyan virtuális gépek szükségesek a fájlrendszerhez, valamint az ismétlődő letöltések több feladatokat és tevékenységeket azonos fájlok blobfuse virtuális gépein gyorsítótár elkerülhető.  A fájlok, blobok egyszerűen és szabványos API-k és eszközök, például az azcopy segítségével másolja a fájlt egy helyi fájlrendszer és az Azure storage között az adatmozgatás is egyszerű.
* Fájlrendszer vagy fájlmegosztás:
  * Virtuális gép operációs rendszer és a teljesítmény és méretezési igényeknek, majd lehetőségek a következők [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), használatával egy virtuális Géphez csatlakoztatott adatlemezekkel rendelkező NFS, több virtuális gép használata a csatlakoztatott adatlemezekkel rendelkező GlusterFS, például az elosztott fájlrendszer vagy a külső ajánlat használatával.
  * [Avere rendszerek](http://www.averesystems.com/) részeként a Microsoft és a nagyméretű, nagy teljesítményű renderelési ideális megoldások a közeljövőben fog rendelkezni.  A Avere megoldás lehetővé teszi az Azure-alapú NFS, vagy az SMB-gyorsítótár létrehozása, amely együttműködik a blob storage- vagy a helyszíni NAS-eszközökön.
  * A fájlrendszer, a fájlok olvasott és írt közvetlenül a fájlrendszert vagy a fájlrendszer és a készlet virtuális gépek között lehet másolni.
  * Egy megosztott fájlrendszert lehetővé teszi, hogy nagy számú projektek és a feladatok között megosztott eszközök használhatók fel a renderelési feladatokat csak a szükséges eléréséhez.

## <a name="using-azure-blob-storage"></a>Az Azure blob storage-dzsal

Blob storage-fiók vagy általános célú v2 tárfiók kell használni.  E két storage fióktípus esetében is konfigurálhatók, általános célú v1 tárfiók, ahogy az az képest jelentősen magasabb korlátok [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Ha konfigurálva, a magasabb korlátok lehetővé teszi sokkal jobb teljesítményt és méretezhetőséget, különösen, ha több készlet virtuális gépet, a storage-fiók elérése során.

### <a name="copying-files-between-client-and-blob-storage"></a>Ügyfél- és blob storage között a fájlok másolása

Fájlok másolása az Azure storage szolgáltatásba vagy onnan, különböző mechanizmusokat használható a storage blob API-t, beleértve a [Azure Storage adatátviteli könyvtár](https://github.com/Azure/azure-storage-net-data-movement), az azcopy parancssori eszközt [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) vagy [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [az Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), és [az Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Például az azcopy használata esetén egy mappában található összes eszköz vihetők át a következő:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Csak a módosított fájlokat másolni, a /XO paraméter használható:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti objektuma fájlok másolása a blob storage-ból a Batch-készlet virtuális gépek

Van néhány különböző megközelítések a legjobb módja a feladat eszközök mérete határozza meg a fájlok másolásához.
A legegyszerűbb megközelítés, hogy az adategység-fájlok másolása a virtuális gépek készletet az egyes feladatokhoz:

* Ha vannak egy feladathoz egyedi fájlok, de szükség a feladat összes tevékenységében akkor [feladat-előkészítési tevékenység](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) összes fájl másolása adható meg.  A feladat-előkészítési tevékenység egyszer fut, amikor az első feladat a feladat végrehajtása a virtuális gép, de nem fut újra a későbbi feldolgozás feladatokat.
* A [feladatkiadási tevékenység](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) a Feladatonkénti fájlok eltávolítása, a feladat befejezése után adható meg; így elkerülhető, a feladat objektumfájlok kitöltő első Virtuálisgép-lemez.
* Ha több feladat ugyanazokat az eszközöket, használata csak a növekményes változásokat az eszközök az egyes feladatokhoz, majd adategység-fájlok vannak továbbra is másolja, még akkor is, ha csak egy részhalmazát frissítése.  Ez akkor lehet hatékony, kivéve, ha nagy mennyiségű nagy méretű adategység-fájlok.

Adategység-fájlok között a feladatok, a feladatok között csak a növekményes változásokat újra felhasználhatók majd egy valamivel több jelentőséggel bír, de hatékonyabb megközelítés esetén az eszközöket a megosztott mappa a virtuális gépen tárolja, és szinkronizálja a megváltozott fájlokat.

* A feladat-előkészítési tevékenységet kell elvégeznie a példány, az azcopy használata a /XO paramétert a virtuális gép AZ_BATCH_NODE_SHARED_DIR környezeti változó által megadott megosztott mappához.  Ez csak másolja a megváltozott fájlokat minden egyes virtuális géphez.
* Gondolkodási kell annak érdekében, hogy illeszkedjenek a készlet virtuális gépek az ideiglenes meghajtón fordítani az összes erőforrás méretét.

Az Azure Batch egy storage-fiók és a Batch-készlet virtuális gépek között a fájlok másolása beépített támogatással rendelkezik.  A feladat [erőforrásfájlok](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) másolás tárolási készlet virtuális gépek fájljainak és lehetett adni a feladat-előkészítési tevékenység.  Sajnos azonban nincsenek fájlok több száz esetén lehetséges érni egy korlátot, és a feladatok sikertelenek lesznek.  Ha az eszközök nagy mennyiségű a feladat-előkészítési tevékenység, amely helyettesítő karaktereket is használhat, és nincs korlátozás az azcopy parancssori használata ajánlott.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kimeneti fájlok másolása a blob storage a Batch-készlet virtuális gépek

[Kimeneti fájlok](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) használt fájlok másolása a virtuális gép készletből tároló lehet.  Egy vagy több fájl is átmásolja a virtuális gép a megadott tárfiókhoz a feladat befejezése után.  A renderelt kimenet másolásának, de az is lehet, a naplófájlok tárolásához.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Linux rendszerű virtuális gép készletek blobfuse virtuális fájlrendszer használatával

Blobfuse a virtuális fájlrendszer illesztőprogramja az Azure Blob Storage, amely lehetővé teszi a Storage-fiókban a Linux fájlrendszeren keresztül blobként tárolt fájlok elérését.

Készlet csomópontjainak is csatlakoztathatja a fájlrendszert, amikor elindult, vagy a csatlakoztatási fordulhat elő, a feladat-előkészítési tevékenység – csak akkor fut, amikor egy csomóponton futnak első lépése egy feladatot a feladat részeként.  Blobfuse beállítható úgy, hogy a ramdisk és a gyorsítótárazáshoz, fájlok, ami növeli teljesítménye jelentősen Ha több tevékenység egy csomóponton hozzáférhessen ugyanazokat a fájlokat a virtuális gépek helyi SSD.

[Mintasablonok érhetők el](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) futtatása a önálló V-Ray rendereli blobfuse fájl rendszert használ, és az egyéb alkalmazásokra vonatkozóan sablonok alapjaként használható.

### <a name="accessing-files"></a>Fájlok elérése

Feladatok bemeneti fájlok és a csatlakoztatott fájlrendszer használatával kimeneti fájlok elérési útját adja meg.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti objektuma fájlok másolása a blob storage-ból a Batch-készlet virtuális gépek

Mivel a fájlok egyszerűen az Azure Storage-blobok, majd szokásos blob API-kkal, eszközökkel és előkészíthetik használható fájlok másolása egy helyszíni fájl rendszer- és blob storage; között Ha például az azcopy Storage Explorer, a Batch Explorer, stb.

## <a name="using-azure-files-with-windows-vms"></a>Windows-alapú virtuális gépekhez az Azure Files használatával

[Az Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) teljes körűen felügyelt fájlmegosztást kínáló a felhőben, amelyek az SMB protokollon keresztül érhető el.  Az Azure Files alapján az Azure blob storage; Ez [költséghatékony](https://azure.microsoft.com/pricing/details/storage/files/) és az adatok replikálása egy másik régióban, ezért globálisan redundáns adatokkal.  [Tárolók skálázása](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) meghatározni, ha az Azure Files kell használni az előrejelzési példánykészlet méretét és az adategység-fájlok adott meg kell vizsgálni.

Van egy [blogbejegyzés](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) és [dokumentáció](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) kiterjedő, hogyan csatlakoztathat Azure-fájlmegosztás.

### <a name="mounting-an-azure-files-share"></a>Az Azure Files-megosztás csatlakoztatásával

A Batch szolgáltatásban használandó csatlakoztatási művelet kell hajtható végre, minden alkalommal, amikor egy feladat futtatása, mert nem lehetséges a feladatok közötti kapcsolat megőrzéséhez.  Ennek legegyszerűbb módja, hogy használja a cmdkey megőrzéséhez a hitelesítő adatok az indítási tevékenység a készlet konfigurációjában, majd minden egyes tevékenység előtt a megosztás csatlakoztatásához.

Például használja a cmdkey készlet sablonban (escape-karakterrel használt JSON-fájl) – vegye figyelembe, hogy a cmdkey hívás az hálózathasználati hívás szétválasztása a felhasználói környezet esetében az indítási tevékenység azonosnak kell lennie a feladatok futtatásához használt:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Példa feladat feladat parancssorából:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Fájlok elérése

Feldolgozás feladatokat adja meg az elérési utat a bemeneti fájlok és a kimeneti fájlokat a csatlakoztatott fájlrendszer, a csatlakoztatott meghajtó vagy UNC elérési út használatával.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti objektuma fájlok másolása a blob storage-ból a Batch-készlet virtuális gépek

Az összes a fő API-k és eszközök, amelyeken az Azure Storage-támogatás; által támogatott Azure-fájlok például az azcopy, Azure CLI-vel, a Storage Explorer, a Azure PowerShell, a Batch Explorer, stb.

[Az Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) érhető el automatikusan a fájlok egy helyi fájlrendszer és a egy Azure-fájlmegosztás közötti szinkronizálása.

## <a name="next-steps"></a>További lépések

További információ a tárolási lehetőség a részletes dokumentációjában talál:

* [Az Azure blob storage-bA](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Az Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)

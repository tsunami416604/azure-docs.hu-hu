---
title: Tárolás és adatmozgás rendereléshez - Azure Batch
description: Ismerje meg a különböző tárolási és adatáthelyezési lehetőségeket az eszköz- és kimeneti fájlszámítási feladatok rendereléséhez.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 1076fc152ddf2c3a2d4f2346262ca90215d68ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75390386"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Az eszköz- és kimeneti fájlok renderelésének tárolási és adatmozgatási lehetőségei

A jelenet- és eszközfájlok nak a készlet virtuális gépein lévő renderelési alkalmazások számára való elérhetővé tételére több lehetőség is rendelkezésre áll:

* [Azure blob tároló:](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
  * A jelenet- és eszközfájlok feltöltése a helyi fájlrendszerből a blobstorage-ba. Amikor az alkalmazást egy feladat futtatja, majd a szükséges fájlokat a blob storage-ból a virtuális gépre másolja, így a renderelési alkalmazás által elérhetők. A kimeneti fájlokat a renderelési alkalmazás írja a virtuális gép lemezére, majd másolja a blob storage.  Szükség esetén a kimeneti fájlok letölthetők a blob storage-ból egy helyi fájlrendszerbe.
  * Az Azure blob storage egy egyszerű és költséghatékony lehetőség a kisebb projektek.  Mivel minden eszközfájlok szükségesek az egyes készlet virtuális gépek, majd ha az eszköz fájlok száma és mérete növeli, gondossággal kell elvégezni annak érdekében, hogy a fájlátvitelek a lehető leghatékonyabbak legyenek.  
* Az Azure storage fájlrendszerként használja [blobfuse:](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
  * Linuxos virtuális gépek esetén a tárfiók elérhetővé tehető, és fájlrendszerként használható, ha a blobfuse virtuális fájlrendszer-illesztőprogramot használja.
  * Ez a beállítás azzal az előnnyel jár, hogy nagyon költséghatékony, mivel nincs szükség virtuális gépekre a fájlrendszerhez, valamint a virtuális gépekblobok blobfuse gyorsítótárazása elkerüli az ugyanazon fájlok ismételt letöltését több feladathoz és feladathoz.  Az adatok áthelyezése is egyszerű, mivel a fájlok egyszerűen blobok és szabványos API-k és eszközök, például az azcopy, fájl másolására használható egy helyszíni fájlrendszer és az Azure storage között.
* Fájlrendszer vagy fájlmegosztás:
  * A virtuális gép operációs rendszerétől és a teljesítmény-/méretezési követelményektől függően a lehetőségek közé tartozik [az Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), az NFS-hez csatlakoztatott lemezekkel rendelkező virtuális gép használata, az elosztott fájlrendszerhez (glusterFS) csatlakoztatott lemezekkel rendelkező virtuális gépek használata, vagy egy harmadik fél től származó ajánlat használata.
  * [Az Avere Systems](https://www.averesystems.com/) ma már a Microsoft része, és a közeljövőben olyan megoldásokkal rendelkezik, amelyek ideálisak a nagy méretű, nagy teljesítményű rendereléshez.  Az Avere-megoldás lehetővé teszi egy Azure-alapú NFS- vagy SMB-gyorsítótár létrehozását, amely a blobstorage-szal vagy a helyszíni NAS-eszközökkel együtt működik.
  * A fájlrendszer segítségével a fájlok közvetlenül a fájlrendszerbe olvashatók vagy írhatók, vagy a fájlrendszer és a készletvirtuális gépek között másolhatók.
  * A megosztott fájlrendszer lehetővé teszi a projektek és feladatok között megosztott nagyszámú eszköz kihasználását, és a renderelési feladatok csak a szükséges mértékben érhetők el.

## <a name="using-azure-blob-storage"></a>Az Azure blob storage használata

Blob storage-fiók vagy egy általános célú v2 tárfiók kell használni.  Ez a két tárfiók-típus lényegesen magasabb korlátokkal konfigurálható, mint egy általános célú v1 tárfiók, [ahogy azt ez](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)a blogbejegyzés részletezi.  Konfigurálása kor a magasabb korlátok sokkal jobb teljesítményt és méretezhetőséget tesznek lehetővé, különösen akkor, ha sok készlet virtuális gép fér hozzá a tárfiókhoz.

### <a name="copying-files-between-client-and-blob-storage"></a>Fájlok másolása az ügyfél- és blobtároló között

Fájlok Azure-tárhelyre történő másolásához és onnan történő másolásához különböző mechanizmusok használhatók, többek között a storage blob API, az [Azure Storage Data Movement Library,](https://github.com/Azure/azure-storage-net-data-movement)a [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) vagy [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux)azcopy parancssori eszköze, az Azure [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)és az Azure [Batch Explorer](https://azure.github.io/BatchExplorer/).

Az azcopy használatával például a mappában lévő összes eszköz a következőképpen vihető át:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Ha csak a módosított fájlokat szeretné másolni, a /XO paraméter a következő tetszés szerint használható:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti eszközfájlok másolása a blobstorageból a batch készlet virtuális gépeibe

Vannak néhány különböző megközelítések fájlok másolása a legjobb megközelítés a feladat eszközök mérete határozza meg.
A legegyszerűbb megközelítés az, hogy másolja az összes eszközfájlokat a készlet virtuális gépek minden feladat:

* Ha egy feladathoz egyedi fájlok vannak, de a feladat összes feladatához szükségesek, akkor az összes fájl másolásához [feladat-előkészítési feladat](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) adható meg.  A feladat-előkészítési feladat egyszer fut, amikor az első feladat feladat végrehajtása a virtuális gép, de nem fut újra a következő feladat feladatok.
* Meg kell adni [egy feladatkiadási feladatot,](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) hogy a feladat befejezése után eltávolítsa a feladatonkénti fájlokat; ezzel elkerülhető, hogy a virtuális gép lemezét az összes feladateszköz-fájl kitölti.
* Ha több feladat használja ugyanazt az eszközöket, és csak növekményes változások az eszközök minden feladat, majd az összes eszköz fájloktovábbra is másolja, akkor is, ha csak egy részhalmaza lett frissítve.  Ez nem lenne hatékony, ha sok nagy eszköz fájlokat.

Amikor az eszközfájlok at újra felhasználják a feladatok között, csak a feladatok közötti növekményes változások, majd egy hatékonyabb, de valamivel nagyobb részt megközelítés az eszközök tárolása a megosztott mappában a virtuális gép és a módosított fájlok szinkronizálása.

* A feladat-előkészítési feladat végrehajtaná a másolatot az azcopy használatával a /XO paraméterrel a AZ_BATCH_NODE_SHARED_DIR környezeti változó által megadott virtuális gép megosztott mappájába.  Ez csak a módosított fájlokat másolja az egyes virtuális gépekre.
* Úgy kell gondolni, hogy a méret az összes eszköz annak biztosítása érdekében, hogy elférjen a készlet virtuális gépek ideiglenes meghajtóján.

Az Azure Batch beépített támogatást nyújt a tárfiók és a batch készlet virtuális gépei közötti fájlok másolásához.  A [feladat-erőforrás fájlok](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) fájlokat másolnak a tárolóból a készletbe, és megadhatók a feladat-előkészítési feladathoz.  Sajnos, ha több száz fájl lehetséges, hogy elérje a határértéket, és a feladatok nem.  Ha nagy számú eszköz van, ajánlott az azcopy parancssort használni a feladat-előkészítési feladatban, amely helyettesítő karaktereket használhat, és nincs korlátja.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Kimeneti fájlok másolása blobstorage-ba a batch készlet virtuális gépeiből

[A kimeneti fájlok](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) fájlokat másolhatnak a készletvirtuális gépről a tárolóba.  Egy vagy több fájl másolható a virtuális gépről egy adott tárfiókba, miután a feladat befejeződött.  A renderelt kimenetet másolni kell, de a naplófájlok tárolása is kívánatos lehet.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Blobfuse virtuális fájlrendszer használata Linux os virtuálisgép-készletekhez

A Blobfuse az Azure Blob Storage virtuális fájlrendszer-illesztőprogramja, amely lehetővé teszi a blobként tárolt fájlok elérését egy Storage-fiókban a Linux fájlrendszeren keresztül.

A készletcsomópontok indításkor csatlakoztathatják a fájlrendszert, vagy a csatlakoztatás egy feladat-előkészítési feladat részeként történhet meg – ez a feladat csak akkor fut, ha a feladat első feladata egy csomóponton fut.  A Blobfuse beállítható úgy, hogy a ramdisk és a virtuális gépek helyi SSD-je egyaránt használható a fájlok gyorsítótárazásához, ami jelentősen növeli a teljesítményt, ha egy csomóponton több feladat is hozzáfér ugyanazon fájlok egy részéhez.

[Mintasablonok érhetők el](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) önálló V-Ray renderelések futtatásához egy blobfuse fájlrendszer használatával, és más alkalmazások sablonjainak alapjaként használhatók.

### <a name="accessing-files"></a>Fájlok elérése

A feladatfeladatok a bemeneti és kimeneti fájlok elérési útját határozzák meg a csatlakoztatott fájlrendszer használatával.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti eszközfájlok másolása a blobstorageból a batch készlet virtuális gépeibe

Mivel a fájlok egyszerűen blobok az Azure Storage-ban, majd a szabványos blob API-k, eszközök és felhasználói felület használható fájlok másolása között egy helyszíni fájlrendszer és blob storage; például azcopy, Storage Explorer, Batch Explorer stb.

## <a name="using-azure-files-with-windows-vms"></a>Az Azure Files használata Windows virtuális gépekkel

[Az Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) teljes körűen felügyelt fájlmegosztásokat kínál a felhőben, amelyek az SMB protokollon keresztül érhetők el.  Az Azure Files az Azure blob storage-on alapul; [költséghatékony,](https://azure.microsoft.com/pricing/details/storage/files/) és konfigurálható egy másik régióba irányuló adatreplikációval, így globálisan redundáns.  [A méretezési célokat](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) felül kell vizsgálni annak meghatározásához, hogy az Azure Files kell használni, mivel az előrejelzési készlet mérete és az eszközfájlok száma.

Van egy [blogbejegyzést](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) és [dokumentációt,](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) amely bemutatja, hogyan csatlakoztathatja az Azure-fájlmegosztást.

### <a name="mounting-an-azure-files-share"></a>Azure-fájlok megosztásának csatlakoztatása

A Batch-ben való használathoz a csatlakoztatási műveletet minden futtatáskor el kell végezni, mivel a feladatok közötti kapcsolat nem tartható fenn.  Ennek legegyszerűbb módja a cmdkey használata a hitelesítő adatok megőrzéséhez a készletkonfigurációban lévő indítási feladat használatával, majd a megosztás csatlakoztatása az egyes feladatok előtt.

Példa a cmdkey használata készletsablonban (a JSON-fájlban való használatra kiváltva) – vegye figyelembe, hogy a cmdkey hívás nak a hálózati használati hívástól való elválasztásakor a kezdő feladat felhasználói környezetének meg kell egyeznie a feladatok futtatásához használt környezettel:

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

Példa feladatfeladat parancssora:
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

A feladatfeladatok a bemeneti és kimeneti fájlok elérési útját határozzák meg a csatlakoztatott fájlrendszer használatával, leképezett meghajtó vagy UNC elérési út használatával.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Bemeneti eszközfájlok másolása a blobstorageból a batch készlet virtuális gépeibe

Az Azure Files támogatja az összes fő API-k és eszközök, amelyek az Azure Storage-támogatás; pl. azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer stb.

[Az Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) automatikusan szinkronizálhatja a fájlokat egy helyszíni fájlrendszer és egy Azure-fájlmegosztás között.

## <a name="next-steps"></a>További lépések

A tárolási lehetőségekről további információt a részletes dokumentációban talál:

* [Azure blob-tároló](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse (Blobfuse)](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)

---
title: Virtuális fájlrendszer csatlakoztatása készletre – Azure Batch | Microsoft dokumentumok
description: Ismerje meg, hogyan csatlakoztathat virtuális fájlrendszert kötegkészlethez.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: labrenne
ms.openlocfilehash: bdf0b3bfc955d8a2e2ce1b363c8699ca719b957c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919005"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>Virtuális fájlrendszer csatlakoztatása kötegkészletre

Az Azure Batch mostantól támogatja a felhőalapú tárolás vagy a külső fájlrendszer csatlakoztatása Windows vagy Linux számítási csomópontok on a Batch készletek. Amikor egy számítási csomópont csatlakozik egy készlethez, a virtuális fájlrendszer csatlakoztatva lesz, és helyi meghajtóként lesz kezelve az adott csomóponton. Csatlakoztathatja a fájlrendszereket, például az Azure Files, az Azure Blob storage, a Network File System (NFS), beleértve az [Avere vFXT gyorsítótárat](../avere-vfxt/avere-vfxt-overview.md)vagy a Common Internet File System (CIFS) rendszert.

Ebből a cikkből megtudhatja, hogyan csatlakoztathat virtuális fájlrendszert számítási csomópontok készletéhez a [.NET Kötegkezelési könyvtárával.](https://docs.microsoft.com/dotnet/api/overview/azure/batch?view=azure-dotnet)

> [!NOTE]
> A virtuális fájlrendszer csatlakoztatása a 2019-08-19-en vagy azt követően létrehozott batch készletekben támogatott. A 2019-08-19 előtt létrehozott kötegkészletek nem támogatják ezt a funkciót.
> 
> A számítási csomóponton lévő fájlrendszerek csatlakoztatásához szükséges API-k a [Batch .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch?view=azure-dotnet) könyvtár részét képezik.

## <a name="benefits-of-mounting-on-a-pool"></a>A medencére való felszerelés előnyei

A fájlrendszer készlethez való csatlakoztatása ahelyett, hogy a feladatok nak a saját adataikat egy nagy adatkészletből szeretné lekérni, megkönnyíti és hatékonyabbá teszi a feladatok elérését a szükséges adatokhoz.

Fontolja meg egy olyan forgatókönyv, amelyben több feladat igényel hozzáférést egy közös adathalmazhoz, például egy film rendereléséhez. Minden feladat egyszerre egy vagy több képkockát jelenít meg a jelenetfájlokból. A jelenetfájlokat tartalmazó meghajtó csatlakoztatásával a számítási csomópontok könnyebben hozzáférhetnek a megosztott adatokhoz. Emellett az alapul szolgáló fájlrendszer az adatokhoz egyidejűleg hozzáférő számítási csomópontok száma által igényelt teljesítmény és méretezés (átviteli és IOPS) alapján egymástól függetlenül választható ki és méretezhető. Például egy [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) elosztott memóriában gyorsítótár használható, hogy támogassa a nagy mozgókép-méretű renderelések több ezer egyidejű rendereléscsomópontok, a helyszíni forrásadatok eléréséhez. Másik lehetőségként a felhőalapú Blob storage-ban már található adatok esetében [a blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) használható az adatok helyi fájlrendszerként való csatlakoztatására. A Blobfuse csak Linux-csomópontokon érhető el, azonban az [Azure Files](https://azure.microsoft.com/blog/a-new-era-for-azure-files-bigger-faster-better/) hasonló munkafolyamatot biztosít, és Windows és Linux rendszeren is elérhető.

## <a name="mount-a-virtual-file-system-on-a-pool"></a>Virtuális fájlrendszer csatlakoztatása készletre  

A virtuális fájlrendszer készletre történő csatlakoztatása a készlet minden számítási csomópontja számára elérhetővé teszi a fájlrendszert. A fájlrendszer akkor van konfigurálva, amikor egy számítási csomópont csatlakozik egy készlethez, vagy amikor a csomópontot újraindítják vagy újraképlik.

Ha fájlrendszert szeretne csatlakoztatni egy `MountConfiguration` készlethez, hozzon létre egy objektumot. Válassza ki a virtuális fájlrendszernek `AzureFileShareConfiguration` `NfsMountConfiguration`megfelelő `CifsMountConfiguration`objektumot: `AzureBlobFileSystemConfiguration`, , , vagy .

Minden csatlakoztatási konfigurációs objektumnak a következő alapparaméterekre van szüksége. Egyes csatlakoztatási konfigurációk a használt fájlrendszerre jellemző paraméterekkel rendelkeznek, amelyeket a kódpéldák részletesebben tárgyalnak.

- **Fióknév vagy -forrás:** Virtuális fájlmegosztás csatlakoztatásához a tárfiók nevére vagy forrására van szükség.
- **Relatív csatlakoztatási útvonal vagy forrás**: A számítási csomópontra csatlakoztatott fájlrendszer helye a csomóponton keresztül `fsmounts` `AZ_BATCH_NODE_MOUNTS_DIR`elérhető szabványos könyvtárhoz viszonyítva. A pontos hely a csomóponton használt operációs rendszertől függ. Például egy Ubuntu csomópont fizikai helye le van `mnt\batch\tasks\fsmounts`képezve a rendszerre, és egy `mnt\resources\batch\tasks\fsmounts`CentOS csomóponton a.
- **Csatlakoztatási beállítások vagy blobfuse-beállítások**: Ezek a beállítások a fájlrendszer csatlakoztatásának konkrét paramétereit írják le.

Az `MountConfiguration` objektum létrehozása után rendelje hozzá `MountConfigurationList` az objektumot a tulajdonsághoz a készlet létrehozásakor. A fájlrendszer csatlakoztatva van, ha egy csomópont csatlakozik egy készlethez, vagy amikor a csomópontújraindul, vagy újraképezi.

A fájlrendszer csatlakoztatásakor létrejön egy környezeti változó, `AZ_BATCH_NODE_MOUNTS_DIR` amely a csatlakoztatott fájlrendszerek helyére, valamint a naplófájlokra mutat, amelyek hasznosak a hibaelhárításhoz és a hibakereséshez. A naplófájlokat a [csatlakoztatási hibák diagnosztizálása](#diagnose-mount-errors) című szakaszban ismertetjük részletesebben.  

> [!IMPORTANT]
> A készlethez csatlakoztatott fájlrendszerek maximális száma 10. A részletekért és egyéb korlátokért tekintse meg a [Batch szolgáltatási kvótákat és korlátokat.](batch-quota-limit.md#other-limits)

## <a name="examples"></a>Példák

Az alábbi kódpéldák bemutatják a különböző fájlmegosztások számítási csomópontok készletbe való csatlakoztatását.

### <a name="azure-files-share"></a>Azure-fájlok megosztása

Az Azure Files a szabványos Azure felhőalapú fájlrendszer-ajánlat. Ha többet szeretne megtudni arról, hogyan szerezheti be a paramétereket a csatlakoztatási konfigurációs kód mintában, olvassa el [az Azure-fájlok megosztásának használata című témakört.](../storage/files/storage-how-to-use-files-windows.md)

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "AccountName",
                AzureFileUrl = "AzureFileShareUrl",
                AccountKey = "StorageAccountKey",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-file-system"></a>Azure Blob fájlrendszer

Egy másik lehetőség az Azure Blob storage használata [a blobfuse-on keresztül.](../storage/blobs/storage-how-to-mount-container-linux.md) Blob fájlrendszer csatlakoztatásához `AccountKey` `SasKey` vagy a tárfiókhoz. A kulcsok beszerzése a [Tárfiók hozzáférési kulcsainak kezelése](../storage/common/storage-account-keys-manage.md)vagy [a Megosztott hozzáférésű aláírások (SAS) használata című témakörben](../storage/common/storage-dotnet-shared-access-signature-part-1.md)található. A blobfuse használatáról további információt a blobfuse [hibaelhárítási – gyakori kérdések című témakörben talál.](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ) A blobfuse-hoz csatlakoztatott könyvtár alapértelmezett eléréséhez futtassa a feladatot **rendszergazdaként.** A Blobfuse a könyvtárat a felhasználói térbe csatlakoztatja, és a készlet létrehozásakor gyökérként van csatlakoztatva. Linux alatt minden **rendszergazdai** feladat gyökér. A FUSE modul összes beállítását a [FUSE referenciaoldal](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)ismerteti.

A hibaelhárítási útmutató mellett a GitHub-problémák a blobfuse-tárházban hasznos módja az aktuális blobfuse-problémák és -megoldások ellenőrzésének. További információt a [blobfuse-problémák című témakörben talál.](https://github.com/Azure/azure-storage-fuse/issues)

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "",
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

### <a name="network-file-system"></a>Hálózati fájlrendszer

A Hálózati fájlrendszerek (NFS) készletcsomópontokhoz is csatlakoztathatók, így a hagyományos fájlrendszerek könnyen elérhetők az Azure Batch-csomópontok számára. Ez lehet a felhőben telepített egyetlen NFS-kiszolgáló, vagy egy virtuális hálózaton keresztül elérhető helyszíni NFS-kiszolgáló. Másik lehetőségként kihasználhatja az [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) elosztott memórián belüli gyorsítótár-megoldását, amely zökkenőmentes kapcsolatot biztosít a helyszíni tároláshoz, az adatok igény szerinti olvasását a gyorsítótárba, és nagy teljesítményt és felhőalapú számítási csomópontokra skálázható.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=1.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>Közös internetes fájlrendszer

Közös internet fájlrendszer (CIFS) is csatlakoztatható a készlet csomópontok, amely lehetővé teszi a hagyományos fájlrendszerek könnyen elérhető az Azure Batch-csomópontok. A CIFS egy fájlmegosztó protokoll, amely nyílt és platformfüggetlen mechanizmust biztosít a hálózati kiszolgálófájlok és -szolgáltatások igényléséhez. A CIFS a Microsoft Server Message Block (SMB) protokolljának továbbfejlesztett verzióján alapul az internet és az intranetes fájlmegosztáshoz, és külső fájlrendszerek windowsos csomópontokra való csatlakoztatására szolgál. Az SMB-ről a [Fájlkiszolgáló és az SMB](https://docs.microsoft.com/windows-server/storage/file-server/file-server-smb-overview)témakörben olvashat bővebben.

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>Csatlakoztatási hibák diagnosztizálása

Ha egy csatlakoztatási konfiguráció sikertelen, a készlet ben lévő számítási csomópont sikertelen lesz, és a csomópont állapota használhatatlanná válik. A csatlakoztatási konfigurációs [`ComputeNodeError`](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) hiba diagnosztizálásához vizsgálja meg a tulajdonságot a hibával kapcsolatos részletekért.

A naplófájlok hibakereséshez való lekérnie, `*.log` használja a [Kimenetfájlok](batch-task-output-files.md) at a fájlok feltöltéséhez. A `*.log` fájlok a fájlrendszer-csatlakoztatásról tartalmaznak információkat a `AZ_BATCH_NODE_MOUNTS_DIR` helyen. Mount naplófájlok formátuma: `<type>-<mountDirOrDrive>.log` minden mount. Például egy `cifs` csatlakoztatott könyvtárban `test` elnevezett csatlakoztatási könyvtárban a következő nevű csatlakoztatási naplófájl lesz: `cifs-test.log`.

## <a name="supported-skus"></a>Támogatott SKUs

| Közzétevő | Ajánlat | SKU | Azure-fájlok megosztása | Blobfuse (Blobfuse) | NFS-csatlakoztatás | CIFS-tartó |
|---|---|---|---|---|---|---|
| kötegelt | renderelés-centos73 | Renderelés | :heavy_check_mark: <br>Megjegyzés: Kompatibilis a CentOS 7.7-tel</br>| :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Canonical | UbuntuServer | 16.04-LTS, 18.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ között | Debian | 8| :heavy_check_mark: | :x: | :heavy_check_mark: | :heavy_check_mark: |
| Credativ között | Debian | 9 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-hirdetések | linux-adat-tudomány-vm | linuxdsvm | :heavy_check_mark: <br>Megjegyzés: Kompatibilis a CentOS 7.4-tel. </br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-köteg | centos-konténer | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-köteg | centos-konténer-rdma | 7.4 | :heavy_check_mark: <br>Megjegyzés: Támogatja a A_8 vagy 9 tárhelyet</br> | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-azure-köteg | ubuntu-szerver-konténer | 16.04-LTS | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| microsoft-dsvm | linux-data-science-vm-ubuntu | linuxdsvmubuntu | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS | 7.6 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| OpenLogic | CentOS-HPC | 7.4, 7.3, 7.1 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oracle | Oracle-Linux | 7.6 | :x: | :x: | :x: | :x: |
| Windows | WindowsServer | 2012, 2016, 2019 | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="next-steps"></a>További lépések

- További információ az Azure Files [windowsos](../storage/files/storage-how-to-use-files-windows.md) vagy [Linuxos](../storage/files/storage-how-to-use-files-linux.md)megosztásának csatlakoztatásáról.
- Ismerje meg [a blobfuse](https://github.com/Azure/azure-storage-fuse) virtuális fájlrendszerek használatát és csatlakoztatását.
- Az NFS-ről és annak alkalmazásairól a [Hálózati fájlrendszer áttekintése](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview) című témakörben olvashat.
- A CIFS protokollról a Microsoft SMB protokoll és a [CIFS protokoll áttekintése](https://docs.microsoft.com/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) című témakörben olvashat bővebben.

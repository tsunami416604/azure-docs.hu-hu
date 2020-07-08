---
title: Adatok másolása az Amazon S3-ból az Azure Storage-ba a AzCopy használatával | Microsoft Docs
description: Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: e917c261392da6044391efc98a81c8f90b619514
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513759"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Adatok másolása az Amazon S3-ból az Azure Storage-ba a AzCopy használatával

Az AzCopy egy parancssori segédprogram, amellyel blobokat és fájlokat másolhat a tárfiókok között. Ebből a cikkből megtudhatja, hogyan másolhat objektumokat, címtárakat és gyűjtőket Amazon Web Services (AWS) S3-ról az Azure Blob Storage-ba az AzCopy használatával.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Adja meg, hogyan adja meg az engedélyezési hitelesítő adatokat

* Az Azure Storage-ban való engedélyezéshez használja a Azure Active Directory (AD) vagy egy közös hozzáférésű aláírás (SAS) tokent.

* Az AWS S3 engedélyezéséhez használjon AWS-hozzáférési kulcsot és egy titkos hozzáférési kulcsot.

### <a name="authorize-with-azure-storage"></a>Engedélyezés az Azure Storage-ban

A AzCopy letöltéséhez tekintse meg az [első lépések a AzCopy](storage-use-azcopy-v10.md) című cikket, és válassza ki, hogyan adja meg az engedélyezési hitelesítő adatokat a Storage szolgáltatás számára.

> [!NOTE]
> A cikkben szereplő példák azt feltételezik, hogy a parancs használatával hitelesítette a személyazonosságát `AzCopy login` . A AzCopy ezután az Azure AD-fiók használatával engedélyezi a blob Storage-beli adathozzáférését.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban.
>
> Példa: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Engedélyezés az AWS S3-vel

Gyűjtse össze az AWS-hozzáférési kulcsot és a titkos hozzáférési kulcsot, majd állítsa be az alábbi környezeti változókat:

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objektumok, könyvtárak és gyűjtők másolása

A AzCopy a [put blokkot használja az URL API-ból](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , így az adatok közvetlenül az AWS S3 és a Storage kiszolgálók között másolódnak át. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Ha úgy dönt, hogy egy másolási művelet után eltávolítja az S3 gyűjtők adatait, ügyeljen arra, hogy az adatok eltávolítása előtt ellenőrizze, hogy az adatok megfelelően lettek-e átmásolva a Storage-fiókba.

> [!TIP]
> Az ebben a szakaszban szereplő példák egyetlen idézőjelekkel (' ') rendelkeznek a Path argumentumokkal. Használjon egy idézőjelet az összes parancs-rendszerhéjon, kivéve a Windows parancs-rendszerhéjt (cmd.exe). Ha Windows parancs-rendszerhéjt (cmd.exe) használ, az idézőjelek ("") helyett idézőjelek ("") közé foglalja a Path argumentumokat ("").

 Ezek a példák olyan fiókokkal is működnek, amelyek hierarchikus névtérrel rendelkeznek. A [több protokollon keresztüli hozzáférés Data Lake Storage](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé teszi, hogy ugyanazt az URL-szintaxist () használja a `blob.core.windows.net` fiókokon. 

### <a name="copy-an-object"></a>Objektum másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> A jelen cikkben szereplő példák az AWS S3-gyűjtők elérési útja stílusú URL-címeket használják (például: `http://s3.amazonaws.com/<bucket-name>` ). 
>
> Emellett használhatja a virtuálisan üzemeltetett stílusú URL-címeket is (például: `http://bucket.s3.amazonaws.com` ). 
>
> Ha többet szeretne megtudni a gyűjtők virtuális üzemeltetéséről, tekintse meg a következőt: [a gyűjtők virtuális üzemeltetése]] ( https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) .

### <a name="copy-a-directory"></a>Könyvtár másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Gyűjtő másolása

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Összes gyűjtő másolása minden régióban

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Az összes gyűjtő másolása egy adott S3 régióban

Használja ugyanazt az URL-szintaxist ( `blob.core.windows.net` ) olyan fiókokhoz, amelyek hierarchikus névtérrel rendelkeznek.

|    |     |
|--------|-----------|
| **Syntax** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Az objektumok elnevezési szabályai közötti különbségek kezelése

Az AWS S3 az Azure Blob-tárolókkal összehasonlítva különböző elnevezési konvenciókat tartalmaz a gyűjtők neveihez. [Itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)olvashat róluk. Ha a gyűjtők egy csoportját egy Azure Storage-fiókba másolja, a másolási művelet a névadási különbségek miatt sikertelen lehet.

A AzCopy két leggyakoribb problémát okoz, amelyek felmerülhetnek; az egymást követő kötőjeleket tartalmazó pontokat és gyűjtőket tartalmazó gyűjtők. Az AWS S3-gyűjtők neve tartalmazhat pontokat és egymást követő kötőjeleket, de az Azure-beli tárolók nem. A AzCopy lecseréli a kötőjelekkel és egymást követő kötőjelekkel rendelkező időszakokat egy számmal, amely az egymást követő kötőjelek számát jelöli (például: egy nevű gyűjtő `my----bucket` `my-4-bucket` . 

Továbbá, mivel a AzCopy fájlokba másolja a fájlokat, ellenőrzi a névadási ütközéseket, és megkísérli a megoldását. Ha például vannak olyan gyűjtők, amelyeknek a neve `bucket-name` és a `bucket.name` , a AzCopy először a nevű gyűjtőt oldja `bucket.name` fel, majd a (z `bucket-name` ) értékre `bucket-name-2` .

## <a name="handle-differences-in-object-metadata"></a>Az objektum metaadatai közötti különbségek kezelése

Az AWS S3 és az Azure különböző karakterkészleteket tesz lehetővé az objektumok kulcsainak neveiben. [Itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)olvashat azokról a karakterekről, amelyeket az AWS S3 használ. Az Azure oldalon a blob-objektumok kulcsai megfelelnek a [C# azonosítók](https://docs.microsoft.com/dotnet/csharp/language-reference/)elnevezési szabályainak.

Egy AzCopy parancs részeként megadhat `copy` egy értéket a nem kötelezően megadandó `s2s-invalid-metadata-handle` jelzőhöz, amely meghatározza, hogyan szeretné kezelni azokat a fájlokat, amelyekben a fájl metaadatai a nem kompatibilis kulcsok nevét tartalmazzák. A következő táblázat ismerteti az egyes jelző értékeket.

| Jelölő értéke | Description  |
|--------|-----------|
| **ExcludeIfInvalid** | (Alapértelmezett beállítás) A metaadatok nem szerepelnek az átvitt objektumban. A AzCopy egy figyelmeztetést naplóz. |
| **FailIfInvalid** | Az objektumok nem másolhatók. A AzCopy naplóz egy hibát, és tartalmazza azt a hibás darabszámot, amely megjelenik az átvitel összegzésében.  |
| **RenameIfInvalid**  | A AzCopy feloldja az érvénytelen metaadat-kulcsot, és átmásolja az objektumot az Azure-ba a megoldott metaadat-kulcs értéke pár használatával. Ha pontosan szeretné megtudni, hogy milyen lépések szükségesek az AzCopy átnevezéséhez, tekintse meg az alábbi [AzCopy átnevezése az objektumok kulcsairól](#rename-logic) című szakaszt. Ha a AzCopy nem tudja átnevezni a kulcsot, az objektum nem lesz átmásolva. |

<a id="rename-logic"></a>

### <a name="how-azcopy-renames-object-keys"></a>A AzCopy átnevezi az objektumok kulcsait

A AzCopy a következő lépéseket hajtja végre:

1. Az érvénytelen karaktereket az "_" karakter váltja fel.

2. Hozzáadja a karakterláncot `rename_` egy új érvényes kulcs elejéhez.

   Ezt a kulcsot fogja használni a rendszer az eredeti metaadat **értékének**mentéséhez.

3. Hozzáadja a karakterláncot `rename_key_` egy új érvényes kulcs elejéhez.
   Ezt a kulcsot fogja használni a rendszer az eredeti metaadatok érvénytelen **kulcsának**mentéséhez.
   A kulcs használatával kipróbálhatja és helyreállíthatja a metaadatokat az Azure-ban, mivel a metaadat-kulcs megmarad a blob Storage szolgáltatásban.

## <a name="next-steps"></a>További lépések

További példákat a következő cikkekben talál:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)

- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
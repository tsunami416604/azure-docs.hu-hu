---
title: Adatok átvitele az Azure Storage-ba az Amazon S3-gyűjtők számára a AzCopy v10 használatával | Microsoft Docs
description: Adatok átvitele a AzCopy és az Amazon S3 gyűjtővel
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: d3c0943ceeadd725ac0e0fb2624d37bc9fb8c3bf
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68844778"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Adatok másolása az Amazon S3-gyűjtőből a AzCopy használatával

A AzCopy olyan parancssori segédprogram, amellyel blobokat vagy fájlokat másolhat a Storage-fiókba, vagy átmásolhatja azokat. Ebből a cikkből megtudhatja, hogyan másolhat objektumokat, címtárakat és gyűjtőket Amazon Web Services (AWS) S3-ról az Azure Blob Storage-ba az AzCopy használatával.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Adja meg, hogyan adja meg az engedélyezési hitelesítő adatokat

* Az Azure Storage-ban való engedélyezéshez használja a Azure Active Directory (AD) vagy egy közös hozzáférésű aláírás (SAS) tokent.

* Az AWS S3 engedélyezéséhez használjon AWS-hozzáférési kulcsot és egy titkos hozzáférési kulcsot.

### <a name="authorize-with-azure-storage"></a>Engedélyezés az Azure Storage-ban

A AzCopy letöltéséhez tekintse meg az [első lépések a AzCopy](storage-use-azcopy-v10.md) című cikket, és válassza ki, hogyan adja meg az engedélyezési hitelesítő adatokat a Storage szolgáltatás számára.

> [!NOTE]
> A cikkben szereplő példák azt feltételezik, hogy a `AzCopy login` parancs használatával hitelesítette a személyazonosságát. A AzCopy ezután az Azure AD-fiók használatával engedélyezi a blob Storage-beli adathozzáférését.
>
> Ha inkább SAS-tokent használ a blob-adathozzáférés engedélyezéséhez, akkor a tokent az erőforrás URL-címéhez is hozzáfűzheti az egyes AzCopy-parancsokban.
>
> Például: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

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

### <a name="copy-an-object"></a>Objektum másolása

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Példa** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> A jelen cikkben szereplő példák az AWS S3-gyűjtők elérési útja stílusú URL-címeket `http://s3.amazonaws.com/<bucket-name>`használják (például:). 
>
> Emellett használhatja a virtuálisan üzemeltetett stílusú URL-címeket is (például: `http://bucket.s3.amazonaws.com`). 
>
> Ha többet szeretne megtudni a gyűjtők virtuális üzemeltetéséről, tekintse meg a következőt: [a https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html) gyűjtők virtuális üzemeltetése]] (.

### <a name="copy-a-directory"></a>Könyvtár másolása

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Példa** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Gyűjtő másolása

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Példa** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Összes gyűjtő másolása minden régióban

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Példa** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Az összes gyűjtő másolása egy adott S3 régióban

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Példa** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Az objektumok elnevezési szabályai közötti különbségek kezelése

Az AWS S3 az Azure Blob-tárolókkal összehasonlítva különböző elnevezési konvenciókat tartalmaz a gyűjtők neveihez. [Itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules)olvashat róluk. Ha a gyűjtők egy csoportját egy Azure Storage-fiókba másolja, a másolási művelet a névadási különbségek miatt sikertelen lehet.

A AzCopy két leggyakoribb problémát okoz, amelyek felmerülhetnek; az egymást követő kötőjeleket tartalmazó pontokat és gyűjtőket tartalmazó gyűjtők. Az AWS S3-gyűjtők neve tartalmazhat pontokat és egymást követő kötőjeleket, de az Azure-beli tárolók nem. A AzCopy lecseréli a kötőjelekkel és egymást követő kötőjelekkel rendelkező időszakokat egy számmal, amely az egymást követő kötőjelek `my----bucket` számát `my-4-bucket`jelöli (például: egy nevű gyűjtő. 

Továbbá, mivel a AzCopy fájlokba másolja a fájlokat, ellenőrzi a névadási ütközéseket, és megkísérli a megoldását. Ha például vannak olyan gyűjtők, amelyeknek a neve `bucket-name` és `bucket.name`a, a AzCopy először a nevű `bucket.name` gyűjtőt oldja `bucket-name` fel, majd `bucket-name-2`a (z) értékre.

## <a name="handle-differences-in-object-metadata"></a>Az objektum metaadatai közötti különbségek kezelése

Az AWS S3 és az Azure különböző karakterkészleteket tesz lehetővé az objektumok kulcsainak neveiben. [Itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)olvashat azokról a karakterekről, amelyeket az AWS S3 használ. Az Azure-oldalon a blob Object-kulcsok megfelelnek az [ C# azonosítók](https://docs.microsoft.com/dotnet/csharp/language-reference/)elnevezési szabályainak.

Egy AzCopy `copy` parancs részeként megadhat egy értéket a nem `s2s-invalid-metadata-handle` kötelezően megadandó jelzőhöz, amely meghatározza, hogyan szeretné kezelni azokat a fájlokat, amelyekben a fájl metaadatai a nem kompatibilis kulcsok nevét tartalmazzák. A következő táblázat ismerteti az egyes jelző értékeket.

| Jelölő értéke | Leírás  |
|--------|-----------|
| **ExcludeIfInvalid** | (Alapértelmezett beállítás) A metaadatok nem szerepelnek az átvitt objektumban. A AzCopy egy figyelmeztetést naplóz. |
| **FailIfInvalid** | Az objektumok nem másolhatók. A AzCopy naplóz egy hibát, és tartalmazza azt a hibás darabszámot, amely megjelenik az átvitel összegzésében.  |
| **RenameIfInvalid**  | A AzCopy feloldja az érvénytelen metaadat-kulcsot, és átmásolja az objektumot az Azure-ba a megoldott metaadat-kulcs értéke pár használatával. Ha pontosan szeretné megtudni, hogy milyen lépések szükségesek az AzCopy átnevezéséhez, tekintse meg az alábbi [AzCopy átnevezése az objektumok kulcsairól](#rename-logic) című szakaszt. Ha a AzCopy nem tudja átnevezni a kulcsot, az objektum nem lesz átmásolva. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>A AzCopy átnevezi az objektumok kulcsait

A AzCopy a következő lépéseket hajtja végre:

1. Az érvénytelen karaktereket az "_" karakter váltja fel.

2. Hozzáadja a karakterláncot `rename_` egy új érvényes kulcs elejéhez.

   Ezt a kulcsot fogja használni a rendszer az eredeti metaadat **értékének**mentéséhez.

3. Hozzáadja a karakterláncot `rename_key_` egy új érvényes kulcs elejéhez.
   Ezt a kulcsot fogja használni a rendszer az eredeti metaadatokérvénytelen kulcsának mentéséhez.
   A kulcs használatával kipróbálhatja és helyreállíthatja a metaadatokat az Azure-ban, mivel a metaadat-kulcs megmarad a blob Storage szolgáltatásban.

## <a name="next-steps"></a>További lépések

További példákat a következő cikkekben talál:

- [Ismerkedés a AzCopy](storage-use-azcopy-v10.md)

- [Adatok átvitele a AzCopy és a blob Storage szolgáltatással](storage-use-azcopy-blobs.md)

- [Adatok átvitele a AzCopy és a file Storage szolgáltatással](storage-use-azcopy-files.md)

- [AzCopy konfigurálása, optimalizálása és megoldása](storage-use-azcopy-configure.md)
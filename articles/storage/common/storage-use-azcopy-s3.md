---
title: Adatok átvitele az Azure Storage a Amazon S3 gyűjtő az AzCopy v10 |} A Microsoft Docs
description: Adatok áthelyezése az AzCopy és az Amazon S3 gyűjtő
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 04/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: b18c4c039b615c7c88268b6e668df9f7fec9fabf
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66687925"
---
# <a name="copy-data-from-amazon-s3-buckets-by-using-azcopy"></a>Adatok másolása az Amazon S3 gyűjtő AzCopy használatával

Az AzCopy parancssori segédprogram, amely a blobok és a fájlok másolása, illetve a storage-fiók használatával. Ez a cikk másolása objektumok, könyvtárak és gyűjtők Amazon Web Services (AWS) S3-ból az Azure blob storage-AzCopy használatával.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Válassza ki, hogyan fogja engedélyezési hitelesítő adatok megadása

* Ahhoz, hogy az Azure Storage, használja az Azure Active Directory (AD) vagy egy közös hozzáférésű Jogosultságkód (SAS) tokent.

* Hitelesítés az AWS S3, használja az AWS hozzáférési kulcs és titkos hívóbetűje.

### <a name="authorize-with-azure-storage"></a>Az Azure Storage engedélyezése

Tekintse meg a [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md) article AzCopy letöltéséhez, és válassza a hogyan ehhez meg kell adnia engedélyezési hitelesítő adatok a storage szolgáltatásra.

> [!NOTE]
> Ebben a cikkben szereplő példák feltételezik, hogy Ön már hitelesítve az identitás használatával a `AzCopy login` parancsot. Az AzCopy ezután a Blob Storage-adatokhoz való hozzáférés engedélyezéséhez használja az Azure AD-fiókot.
>
> Ha szeretné inkább használni egy SAS-token blobadatokhoz való hozzáférés engedélyezése, majd fűzze hozzá a ezt a jogkivonatot az erőforrás URL-címet minden egyes AzCopy-parancs.
>
> Például: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Az AWS S3 engedélyezése

Az AWS-hozzáférési kulcsot és titkos hívóbetűje gyűjthet, és állítsa az ezeket a környezeti változók:

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objektumok, könyvtárak és gyűjtők másolása

Az AzCopy használja a [URL blokk Put](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API-t, így közvetlenül az AWS S3 és a tároló kiszolgálók közötti adatokat másolja. Ezek a másolási műveletek ne használja a hálózati sávszélesség számítógép.

### <a name="copy-an-object"></a>Egy objektum másolása

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<object-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>"` |
| **Példa** | `azcopy cp "https://s3.amazonaws.com/mybucket/myobject" "https://mystorageaccount.blob.core.windows.net/mycontainer/myblob"` |

> [!NOTE]
> Ebben a cikkben szereplő példák elérési út stílusú URL-címeket használja, az AWS S3 időtartamgyűjtők (például: `http://s3.amazonaws.com/<bucket-name>`). 
>
> Virtuális üzemeltetett stílusú URL-címeket is használhatja (például: `http://bucket.s3.amazonaws.com`). 
>
> Gyűjtők virtuális üzemeltető kapcsolatos további információkért lásd: [virtuális üzemeltető a gyűjtők]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Másolja egy könyvtár

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>/<directory-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>" --recursive=true` |
| **Példa** | `azcopy cp "https://s3.amazonaws.com/mybucket/mydirectory" "https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory" --recursive=true` |

### <a name="copy-a-bucket"></a>Másolja a gyűjtőhöz

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/<bucket-name>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true` |
| **Példa** | `azcopy cp "https://s3.amazonaws.com/mybucket" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Az összes gyűjtők másolja az összes régióban

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Példa** | `azcopy cp "https://s3.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Másolja ki az összes gyűjtők egy adott régióban S3

|    |     |
|--------|-----------|
| **Syntax** | `azcopy cp "https://s3-<region-name>.amazonaws.com/" "https://<storage-account-name>.blob.core.windows.net" --recursive=true` |
| **Példa** | `azcopy cp "https://s3-rds.eu-north-1.amazonaws.com" "https://mystorageaccount.blob.core.windows.net" --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Elnevezési szabályok közötti különbségek kezelésére

Az AWS S3 rendelkezik egy másik gyűjtőbe nevek szemben az Azure blob-tárolók elnevezési szabályai. Itt olvashat őket [Itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Ha egy csoport azon gyűjtők másolása az Azure storage-fiók lehetőséget választja, akkor a másolási művelet elnevezési eltérések miatt meghiúsulhat.

Az AzCopy kezeli a két, esetlegesen felmerülő; a leggyakoribb problémák és a gyűjtőket, amely tartalmazhat egymást követő kötőjelet tartalmazó gyűjtőkbe. Az AWS S3 gyűjtő nevek időszakok és egymást követő kötőjelet tartalmazhat, de nem egy tárolót az Azure-ban. Az AzCopy időszakok lecseréli az kötőjeleket tartalmazhat, és egymást követő kötőjelet számát jelölő több egymást követő kötőjelet (például: nevű kérelemegységeket `my----bucket` válik `my-4-bucket`. 

Is az AzCopy átmásolja a fájlokat, azt ellenőrzi, hogy elnevezési ütközések és megkísérli feloldani őket. Például, ha nincsenek a gyűjtők nevű `bucket-name` és `bucket.name`, nevű kérelemegységeket oldja fel az AzCopy `bucket.name` first `bucket-name` majd `bucket-name-2`.

## <a name="handle-differences-in-object-metadata"></a>Objektum-metaadatait különbségek kezelésére

Az AWS S3 és az Azure más-más részhalmazához karakterek engedélyezése objektum kulcsok nevei. Itt olvashat a karakterek, hogy használja-e az AWS S3 [Itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Az Azure részéről blob objektum kulcsok formátumhoz elnevezési szabályait [ C# azonosítók](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Az AzCopy részeként `copy` parancsot, megadhat egy értéket nem kötelező a `s2s-invalid-metadata-handle` jelzőt, amely meghatározza, hogyan szeretné kezelni a fájlokat, ahol a fájl metaadatait tartalmazza-e a kulcsnevek nem kompatibilis. A következő táblázat ismerteti az egyes jelző érték.

| Jelző érték | Leírás  |
|--------|-----------|
| **ExcludeIfInvalid** | (Alapértelmezett beállítás) A metaadatok az átadott objektum nem található. Az AzCopy egy figyelmeztetést naplóz. |
| **FailIfInvalid** | Objektumokat a rendszer nem másolja. Az AzCopy naplózza a hibát, és a sikertelen száma, az adatátviteli összefoglalás jelenik meg a hibát tartalmaz.  |
| **RenameIfInvalid**  | Az AzCopy oldja fel az érvénytelen metaadat-kulcsot, és másolja át az objektumot az Azure-ban a feloldott metaadatok kulcs-érték pár. Pontosan milyen lépések AzCopy veszi átnevezése objektumhoz kulcsok kapcsolatban lásd: a [hogyan AzCopy átnevezi objektum kulcsok](#rename-logic) szakaszt. Ha az AzCopy nem nevezhető át a kulcsot, majd az objektum nem másolhatók. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Hogyan átnevezi az AzCopy a objektum kulcsok

Az AzCopy hajtja végre a következő lépéseket:

1. Cserél érvénytelen karaktereket (_).

2. Hozzáadja a karakterlánc `rename_` új érvényes kulcs elejére.

   Ezt a kulcsot fogja menteni az eredeti metaadatait **érték**.

3. Hozzáadja a karakterlánc `rename_key_` új érvényes kulcs elejére.
   Ezt a kulcsot fogja menteni eredeti metaadatait érvénytelen **kulcs**.
   Ez a kulcs segítségével próbálja ki, és helyreállítani a metaadatokat az Azure oldaláról, mivel értékként a Blob storage szolgáltatás metaadataihoz megadott kulcs megőrződik.

## <a name="next-steps"></a>További lépések

További példa található a következő cikkeket:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatok áthelyezése az AzCopy és a blob storage](storage-use-azcopy-blobs.md)

- [Adatok áthelyezése az AzCopy és a file storage](storage-use-azcopy-files.md)

- [Konfigurálja, optimalizálhat és AzCopy hibaelhárítása](storage-use-azcopy-configure.md)
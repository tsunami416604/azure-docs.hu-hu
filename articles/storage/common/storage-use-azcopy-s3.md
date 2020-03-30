---
title: Adatok másolása az Amazon S3-ról az Azure Storage szolgáltatásba az AzCopy használatával | Microsoft dokumentumok
description: Adatátvitel az AzCopy és az Amazon S3 gyűjtőkkel
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941501"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Adatok másolása az Amazon S3-ról az Azure Storage-ba az AzCopy használatával

Az AzCopy egy parancssori segédprogram, amely segítségével blobok vagy fájlok másolása egy tárfiókba. Ez a cikk segít az Amazon Web Services (AWS) S3 objektumainak, könyvtárainak és gyűjtőinek az AzCopy használatával az Azure blob storage-ba történő másolásában.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Adja meg, hogyan adja meg az engedélyezési hitelesítő adatokat

* Az Azure Storage-sal való engedélyezéshez használja az Azure Active Directoryt (AD) vagy a megosztott hozzáférésű aláírás (SAS) jogkivonatot.

* Az AWS S3-mal való engedélyezéshez használjon AWS hozzáférési kulcsot és titkos hozzáférési kulcsot.

### <a name="authorize-with-azure-storage"></a>Engedélyezés az Azure Storage szolgáltatással

Tekintse meg az AzCopy letöltéséhez az [AzCopy](storage-use-azcopy-v10.md) letöltéséhez az Első lépések című cikket, és válassza ki, hogyan adja meg az engedélyezési hitelesítő adatokat a storage-szolgáltatásnak.

> [!NOTE]
> A cikkben szereplő példák feltételezik, hogy a `AzCopy login` parancs használatával hitelesítette az identitását. AzAzCopy ezután az Azure AD-fiók használatával engedélyezi az adatokhoz való hozzáférést a Blob storage-ban.
>
> Ha inkább egy SAS-jogkivonatot használ a blobadatokhoz való hozzáférés engedélyezéséhez, akkor hozzáfűzheti a jogkivonatot az erőforrás URL-címéhez az egyes AzCopy-parancsokban.
>
> Például: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Engedélyezés az AWS S3-mal

Gyűjtse össze az AWS hozzáférési kulcsot és a titkos hozzáférési kulcsot, majd állítsa be a következő környezeti változókat:

| Operációs rendszer | Parancs  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Macos** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Objektumok, könyvtárak és gyűjtők másolása

Az AzCopy a [Put Block From URL API-t](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) használja, így az adatok másolása közvetlenül az AWS S3 és a tárolókiszolgálók között történik. Ezek a másolási műveletek nem használják a számítógép hálózati sávszélességét.

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Ha úgy dönt, hogy egy másolási művelet után eltávolítja az adatokat az S3-gyűjtőkből, győződjön meg arról, hogy az adatok megfelelő másolása volt-e a tárfiókba, mielőtt eltávolítana az adatokat.

> [!TIP]
> Az ebben a szakaszban szereplő példák az elérési út argumentumait egyszeres idézőjelekkel ('') mellékelik. A Windows parancshéj (cmd.exe) kivételével minden parancshéjban használjon egyszeres idézőjeleket. Ha Windows parancshéjat (cmd.exe) használ, az elérési út argumentumait idézőjelek ("") helyett idézőjelek (') közé kell mellékelni.

 Ezek a példák hierarchikus névtérrel rendelkező fiókokkal is működnek. [A Data Lake Storage többprotokollos hozzáférése](../blobs/data-lake-storage-multi-protocol-access.md) lehetővé`blob.core.windows.net`teszi, hogy ugyanazokat az URL-szintaxist ( ) használja ezeken a fiókokon. 

### <a name="copy-an-object"></a>Objektum másolása

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Példa** (hierarchikus névtér) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Ebben a cikkben például az AWS S3 gyűjtők elérési `http://s3.amazonaws.com/<bucket-name>`út stílusú URL-címeit használja (például: ). 
>
> Virtuális hosztolt stílusú URL-eket is használhat (például: `http://bucket.s3.amazonaws.com`). 
>
> Ha többet szeretne megtudni a gyűjtők virtuális üzemeltetéséről, olvassahttps://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html)el a [Virtuális vödörüzemeltetés]]) című témakört.

### <a name="copy-a-directory"></a>Könyvtár másolása

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Vödör másolása

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Az összes gyűjtő másolása az összes régióban

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Az összes gyűjtő másolása egy adott S3 régióban

Ugyanazt az URL-szintaxist (`blob.core.windows.net`) használja hierarchikus névtérrel rendelkező fiókokhoz.

|    |     |
|--------|-----------|
| **Szintaxis** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Példa** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Példa** (hierarchikus névtér)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Objektumnév-kiosztási szabályok közötti különbségek kezelése

Az AWS S3 az Azure blobtárolókhoz képest eltérő elnevezési konvenciókkal rendelkezik a gyűjtőnevekhez képest. Elolvashatja róluk [itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Ha úgy dönt, hogy egy gyűjtőcsoport egy Azure-tárfiókba másolja, a másolási művelet előfordulhat, hogy nem elnevezési különbségek miatt.

Az AzCopy kezeli a két leggyakoribb felmerülő problémák; egymást követő kötőjeleket tartalmazó gyűjtők. Az AWS S3 gyűjtőnevek tartalmazhatnak időszakokat és egymást követő kötőjeleket, de az Azure-ban egy tároló nem. Az AzCopy a időszakokat kötőjelekkel, az egymást követő kötőjeleket pedig egy egymást követő kötőjelek számát `my----bucket` `my-4-bucket`(például egy elnevezett gyűjtő lesz a . 

Továbbá, ahogy az AzCopy fájlokat másol, ellenőrzi az ütközések elnevezését, és megpróbálja megoldani őket. Ha például vannak olyan gyűjtők, `bucket.name`amelyeknek a neve `bucket.name` és `bucket-name` a neve, `bucket-name-2` `bucket-name` és az AzCopy feloldja az elsőként, majd a számára megnevezett gyűjtőt.

## <a name="handle-differences-in-object-metadata"></a>Az objektum metaadataiközötti különbségek kezelése

Az AWS S3 és az Azure különböző karakterkészleteket engedélyez az objektumkulcsok nevében. Az AWS S3 által használt karakterekről [itt](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys)olvashat. Az Azure oldalán a blobobjektum-kulcsok betartják a [C# azonosítók](https://docs.microsoft.com/dotnet/csharp/language-reference/)elnevezési szabályait.

Az AzCopy `copy` parancs részeként megadhat egy értéket `s2s-invalid-metadata-handle` a nem kötelező jelzőhöz, amely megadja, hogyan szeretné kezelni azokat a fájlokat, amelyekben a fájl metaadatai nem kompatibilis kulcsneveket tartalmaznak. Az alábbi táblázat az egyes jelzők értékét ismerteti.

| Megjelölés értéke | Leírás  |
|--------|-----------|
| **ExcludeIfInvalid** | (Alapértelmezett beállítás) A metaadatok nem szerepelnek az átvitt objektumban. AzCopy figyelmeztetést naplóz. |
| **FailIfInvalid** | Az objektumok másolása nem lesz másolva. AzCopy naplózza a hibát, és tartalmazza ezt a hibát az átviteli összegzésben megjelenő sikertelen ek száma között.  |
| **ÁtnevezésIfInvalid**  | AzCopy feloldja az érvénytelen metaadatkulcsot, és átmásolja az objektumot az Azure-ba a feloldott metaadatkulcs-értékpár használatával. Ha meg szeretné tudni, hogy az AzCopy milyen lépéseket tesz az objektumkulcsok átnevezéséhez, olvassa el az alábbi, Hogyan nevezi át az [Objektumkulcsok objektumkulcsokat.](#rename-logic) Ha az AzCopy nem tudja átnevezni a kulcsot, akkor az objektum nem lesz másolva. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Hogyan nevezi át az AzCopy az objektumkulcsokat?

Az AzCopy a következő lépéseket hajtja végre:

1. Érvénytelen karaktereket helyettesít "_" karakterre.

2. Hozzáadja `rename_` a karakterláncot egy új érvényes kulcs elejéhez.

   Ezzel a kulccsal menti az eredeti **metaadat-értéket.**

3. Hozzáadja `rename_key_` a karakterláncot egy új érvényes kulcs elejéhez.
   Ezzel a kulccsal menti az eredeti metaadatok érvénytelen **kulcsát.**
   Ezzel a kulccsal megpróbálhatja helyreállítani a metaadatokat az Azure-oldalon, mivel a metaadatkulcs a Blob storage szolgáltatás értékeként megmarad.

## <a name="next-steps"></a>További lépések

További példák a következő cikkek bármelyikében:

- [Bevezetés az AzCopy használatába](storage-use-azcopy-v10.md)

- [Adatok átvitele az AzCopy és blob tárházával](storage-use-azcopy-blobs.md)

- [Adatátvitel átvitele az AzCopy programmal és a fájltárolással](storage-use-azcopy-files.md)

- [Az AzCopy konfigurálása, optimalizálása és hibaelhárítása](storage-use-azcopy-configure.md)
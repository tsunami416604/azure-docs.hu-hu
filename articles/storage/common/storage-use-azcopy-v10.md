---
title: Másolja, vagy az Azure Storage-adatok áthelyezése az AzCopy v10 (előzetes verzió) |} A Microsoft Docs
description: Az AzCopy v10 használata (előzetes verzió) segédprogram áthelyezése vagy másolhat blob, a data lake és a fájl tartalmát. Adatok másolása az Azure Storage a helyi fájlokból vagy adatmásolás belül vagy tárfiókok között. Egyszerűen migrálhatja az adatokat az Azure Storage.
services: storage
author: artemuwka
ms.service: storage
ms.topic: article
ms.date: 10/09/2018
ms.author: artemuwka
ms.subservice: common
ms.openlocfilehash: a4e115194d7e903edae4b4713c4f65eef9895cbf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467118"
---
# <a name="transfer-data-with-the-azcopy-v10-preview"></a>Adatátvitel az AzCopy v10 (előzetes verzió)

Az AzCopy v10 (előzetes verzió) a következő generációs parancssori segédprogram és-tárolókról a Microsoft Azure Blob- és File storage, amely újratervezett parancssori felület és az új architektúra kínál a nagy teljesítményű, megbízható adatátviteli céljából. Az AzCopy használatával másolhat adatokat egy fájlrendszer és a egy tárfiókot, vagy tárfiókok között.

## <a name="whats-new-in-azcopy-v10"></a>Az AzCopy v10 újdonságai

- Az Azure-Blobba, vagy fordítva rendszer szinkronizálni. Használat `azcopy sync <source> <destination>`. A növekményes másolási forgatókönyvek esetén ideális választás.
- Az Azure Data Lake Storage Gen2 API-kat támogatja. Használat `myaccount.dfs.core.windows.net` , az ADLS Gen2 API-k hívása egy URI-t.
- Támogatja a teljes fiókra (csak Blob szolgáltatás) másolása egy másik fiókba.
- Fiók másolási fiókot most már használja az új [URL-címről Put](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API-k. Az ügyfélnek nincs adatátvitel van szükség, ami lehetővé teszi az átvitel gyorsabban!
- Lista/eltávolítása fájlok és a egy adott elérési úton lévő blobokat.
- Támogatja a helyettesítő karakterek mintái az elérési utat, valamint--belefoglalása vagy--jelzők kizárása.
- Jobb rugalmasság: minden AzCopy példányt hoz létre, a feladat rendelés és a egy kapcsolódó naplófájlt. Megtekintheti és a korábbi feladatok újraindítása és folytatása sikertelen feladatokat. Az AzCopy is automatikusan megkísérli egy átviteli hiba után.
- Általános teljesítménnyel kapcsolatos fejlesztések.

## <a name="download-and-install-azcopy"></a>Töltse le és telepítse az Azcopyval

### <a name="latest-preview-version-v10"></a>Legújabb előzetes verziójával (v10)

Töltse le az AzCopy legújabb előzetes verzióját:
- [Windows](https://aka.ms/downloadazcopy-v10-windows)
- [Linux](https://aka.ms/downloadazcopy-v10-linux)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac)

### <a name="latest-production-version-v81"></a>Éles legújabb (v8.1)

Töltse le a [AzCopy for Windows legújabb üzemi verziójával](https://aka.ms/downloadazcopy).

### <a name="azcopy-supporting-table-storage-service-v73"></a>Az AzCopy Table storage szolgáltatás (v7.3) támogatása

Töltse le a [másolhatja az adatokat és- tárolókról a Microsoft Azure Table storage szolgáltatás támogatása az AzCopy v7.3](https://aka.ms/downloadazcopynet).

## <a name="post-installation-steps"></a>Telepítés utáni lépések

Az AzCopy v10 nem igényel a telepítés. Nyisson meg egy előnyben részesített parancssori alkalmazás, és lépjen abba a mappába, amelyben a `azcopy.exe` végrehajtható fájl található. Ha szükséges, az AzCopy mappa helyét is hozzáadhat a rendszerbeli elérési úton.

## <a name="authentication-options"></a>A hitelesítési beállítások

AzCopy v10 lehetővé teszi, hogy az Azure Storage hitelesítésekor a következő beállításokat:
- **[A Blob és ADLS Gen2 támogatott] az Azure Active Directory**. Használat ```.\azcopy login``` bejelentkezni az Azure Active Directory használatával.  A felhasználóknak rendelkezniük kell ahhoz ["Storage-Blobadatok Közreműködője" szerepkörrel](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac) írni a Blob storage, Azure Active Directory-hitelesítéssel.
- **SAS-tokeneket [támogatott a Blobok és fájlok szolgáltatás]**. A SAS-jogkivonat hozzáfűzése a blob elérési útja a parancssorban a használatára. Az Azure-portált használja, SAS-jogkivonatot [Tártallózó](https://blogs.msdn.microsoft.com/jpsanders/2017/10/12/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer/), [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageblobsastoken), vagy más tetszőleges eszközökkel. További információkért lásd: [példák](https://docs.microsoft.com/azure/storage/blobs/storage-dotnet-shared-access-signature-part-2).

## <a name="getting-started"></a>Első lépések

Az AzCopy v10 egy egyszerű helyi dokumentált szintaxissal rendelkezik. Az általános szintaxist írja le a következőképpen néz ki az Azure Active Directoryban bejelentkezve:

```azcopy
.\azcopy <command> <arguments> --<flag-name>=<flag-value>
# Examples if you have logged into the Azure Active Directory:
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile"
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/container"

# Examples if you are using SAS tokens to authenticate:
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/container?sastoken" --recursive=true
.\azcopy cp "C:\local\path\myfile" "https://account.blob.core.windows.net/container/myfile?sastoken"
```

Itt látható, hogyan kezdheti az elérhető parancsok listáját:

```azcopy
.\azcopy -help
# Using the alias instead
.\azcopy -h
```

A súgóban talál oldal és a egy bizonyos paranccsal példáit futtassa az alábbi parancsot:

```azcopy
.\azcopy <cmd> -help
# Example:
.\azcopy cp -h
```

## <a name="create-a-blob-container-or-file-share"></a>A Blob-tárolóba vagy a fájlmegosztás létrehozása 

**Blobtároló létrehozása**

```azcopy
.\azcopy make "https://account.blob.core.windows.net/container-name"
```

**Fájlmegosztás létrehozása**

```azcopy
.\azcopy make "https://account.file.core.windows.net/share-name"
```

**Hozzon létre egy blobtárolót ADLS Gen2 használatával**

Ha engedélyezte a hierarchikus névterek a blob storage-fiókjában, az alábbi parancs segítségével hozzon létre egy új fájlrendszer (Blob tároló), így a fájlokat feltöltheti azt.

```azcopy
.\azcopy make "https://account.dfs.core.windows.net/top-level-resource-name"
```

## <a name="copy-data-to-azure-storage"></a>Adatok másolása az Azure Storage

A Másolás parancs segítségével adatátvitel a forrásból a célra. A forrás és a cél lehet v:
- Helyi rendszer
- Az Azure Blob/virtuális könyvtár/tároló URI-t
- Az Azure fájl vagy könyvtár/fájlmegosztás URI
- Az Azure Data Lake Storage Gen2 fájlrendszer/Directory/fájl URI

```azcopy
.\azcopy copy <source path> <destination path> --<flag-name>=<flag-value>
# Using alias instead
.\azcopy cp <source path> <destination path> --<flag-name>=<flag-value>
```

A következő parancs a mappában található összes fájlt tölt fel `C:\local\path` rekurzív módon a tároló `mycontainer1` létrehozása `path` könyvtárat a tárolóban:

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

A következő parancs a mappában található összes fájlt tölt fel `C:\local\path` (nélkül recursing alkönyvtárak be), a tároló `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>"
```

További példák lekéréséhez használja a következő parancsot:

```azcopy
.\azcopy cp -h
```

## <a name="copy-data-between-two-storage-accounts"></a>Másolja az adatokat két storage-fiókok között

Két storage-fiókok közötti másolást használja a [URL blokk Put](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API-t, és nem használja az ügyfél gépének hálózati sávszélesség. Két Azure Storage-kiszolgálók közvetlenül közötti adatokat másolja az AzCopy egyszerűen hangolja össze a másolási művelet során. Ez a beállítás jelenleg csak akkor használható a Blob Storage.

Két tárfiókok között az adatok másolásához használja a következő parancsot:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

> [!NOTE]
> A parancs fogja enumerálni az összes blob-tárolót, és másolja őket a cél-fiók. Jelenleg az AzCopy v10 támogatja között két storage-fiókok csak blokkblobokhoz másolása. Minden egyéb tárolási fiók objektum (hozzáfűzés a blobok, a lapblobok, fájlok, táblák és üzenetsorok) kimarad.

## <a name="copy-a-vhd-image-to-a-storage-account"></a>Másolja egy VHD-rendszerképet egy tárfiókba

Alapértelmezés szerint az AzCopy v10 adatait feltölti az a blokkblobok használatát támogatják. Azonban ha egy forrásfájl vhd-bővítmény, az AzCopy v10 alapértelmezés szerint feltölti azt egy lapblob. Ez a viselkedés jelenleg nem konfigurálható.

## <a name="sync-incremental-copy-and-delete-blob-storage-only"></a>Szinkronizálás: növekményes másolása és törlése (csak Blob storage)

> [!NOTE]
> Sync parancsot a tartalmat a forrás célhelyre szinkronizálja, és ez magában foglalja a cél fájlok törlése, ha azok nem léteznek a forrás. Ellenőrizze, hogy a célhelyen történő szinkronizálása használja.

Szinkronizálja a helyi fájlrendszerben egy tárfiókba, használja a következő parancsot:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

Ugyanúgy szinkronizálhat egy blobtárolót a helyi fájlrendszer le:

```azcopy
# If you're using Azure Active Directory authentication the sastoken is not required
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

A parancs lehetővé teszi a növekményes szinkronizálás a forrás utolsó módosítás időbélyegek alapján a célhelyre. Hozzáadásakor, vagy töröljön egy fájlt a forrás, az AzCopy v10 fog végezze el ugyanezt a célhelyen. Az AzCopy a törlés előtt kérni fogja a fájlokat a törlés megerősítéséhez.

## <a name="advanced-configuration"></a>Speciális konfiguráció

### <a name="configure-proxy-settings"></a>Proxybeállítások konfigurálása

Konfigurálja a proxybeállításokat az AzCopy v10, állítsa be a környezeti változó https_proxy, a következő paranccsal:

```cmd
# For Windows:
set https_proxy=<proxy IP>:<proxy port>
# For Linux:
export https_proxy=<proxy IP>:<proxy port>
# For MacOS
export https_proxy=<proxy IP>:<proxy port>
```

### <a name="optimize-throughput"></a>Teljesítmény optimalizálása

Állítsa be a környezeti változó konfigurálása az egyidejű kérelmek száma és az átviteli teljesítmény és erőforrás-használat AZCOPY_CONCURRENCY_VALUE. Az érték alapértelmezés szerint 300 értéke. Az érték csökkentése korlátozza a sávszélességet és CPU-használatának AzCopy v10.

```cmd
# For Windows:
set AZCOPY_CONCURRENCY_VALUE=<value>
# For Linux:
export AZCOPY_CONCURRENCY_VALUE=<value>
# For MacOS
export AZCOPY_CONCURRENCY_VALUE=<value>
```

## <a name="troubleshooting"></a>Hibaelhárítás

Az AzCopy v10 naplófájlokat és az összes feladat terv adatfájlokat hoz létre. A naplók segítségével megvizsgálhatja és az esetleges problémák elhárításában. A naplók tartalmazni fogja az állapotát, hiba (UPLOADFAILED COPYFAILED és DOWNLOADFAILED), a teljes elérési útja, és a hiba okát. A feladat-naplók és a terv fájlok találhatók, a % USERPROFILE\\.azcopy mappát.

### <a name="review-the-logs-for-errors"></a>Tekintse át a hibákat a naplók

A következő parancsot a 04dc9ca9-158f-7945-5933-564021086c79 naplóból UPLOADFAILED állapota az összes hiba jelenik meg:

```azcopy
cat 04dc9ca9-158f-7945-5933-564021086c79.log | grep -i UPLOADFAILED
```

### <a name="view-and-resume-jobs"></a>Feladatok megtekintése és folytatása

Minden adatátviteli műveletet egy AzCopy feladatot hoz létre. A következő paranccsal feladatok előzményeit tekintheti meg:

```azcopy
.\azcopy jobs list
```

A feladat statisztikai adatok megtekintéséhez használja a következő parancsot:

```azcopy
.\azcopy jobs show <job-id>
```

Szűrés állapot szerint átvitelek, használja a következő parancsot:

```azcopy
.\azcopy jobs show <job-id> --with-status=Failed
```

Sikertelen/megszakított feladatok azonosítójával együtt (Ez még nem állandó biztonsági okokból) SAS-jogkivonat folytathatja:

```azcopy
.\azcopy jobs resume <jobid> --sourcesastokenhere --destinationsastokenhere
```

### <a name="change-the-default-log-level"></a>Az alapértelmezett naplózási szint módosításához

Alapértelmezés szerint az AzCopy naplózási szint INFO értéke. Ha szeretné, hogy lemezterületet a napló részletességi csökkentésére, felülírja a beállítás használatával ``--log-level`` lehetőséget. Elérhető naplózási szintek a következők: HIBAKERESÉSI információ, figyelmeztetés, hiba, KILÉPJEN és végzetes

## <a name="next-steps"></a>További lépések

A visszajelzést mindig üdvözölte. Ha bármilyen kérdése van, problémák vagy általános visszajelzést küldheti el ezeket a következő https://github.com/Azure/azure-storage-azcopy. Köszönjük!

---
title: Az Azure Data Box, az Azure Data Box nehéz problémák elhárítása |} A Microsoft Docs
description: Az Azure Data Box és az Azure Data Box nehéz problémáinak, ha másol adatokat, ezekre az eszközökre elhárítását ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: bc0681a8ea15f736a7b253d6bd7ba2f7928d2a32
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439404"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box és az Azure Data Box nehéz kapcsolatos problémák elhárítása

Ez a cikk részletesen hibáinak elhárítása az Azure Data Box- vagy Azure Data Box nehéz használatakor jelenhet információkat. A cikk a lehetséges hibák látható, ha az adatok másolásakor a Data Box, vagy ha a Data Box data nahrávají listáját tartalmazza.

## <a name="error-classes"></a>Hiba-osztályok

A Data Box és a Data Box gyakori hibák a következőképpen lehet összefoglalni:

| Hiba kategória *        | Leírás        | Javasolt művelet    |
|----------------------------------------------|---------|--------------------------------------|
| Tároló vagy a megosztás neve | A tároló vagy a megosztás neve ne hajtsa végre az Azure elnevezési szabályainak.  |Töltse le a hibaüzenetben szerepel. <br> Nevezze át a tárolók vagy megosztásokhoz. [További információk](#container-or-share-name-errors).  |
| Tároló vagy a fájlmegosztás maximális mérete | A tárolók vagy megosztások teljes adatmennyiség meghaladja az Azure.   |Töltse le a hibaüzenetben szerepel. <br> Csökkentheti az általános adatokat a tároló vagy a megosztást. [További információk](#container-or-share-size-limit-errors).|
| Objektum- vagy fájl maximális mérete | Az objektum vagy a fájlokat a tárolók vagy megosztások meghaladja az Azure.|Töltse le a hibaüzenetben szerepel. <br> A tároló vagy a megosztás méretének csökkentése. [További információk](#object-or-file-size-limit-errors). |    
| Adatok vagy a fájl típusa | Az adatok formátumát, vagy a fájl típusa nem támogatott. |Töltse le a hibaüzenetben szerepel. <br> Lapblobok és a felügyelt lemezek adatok legyenek 512-bájt igazítva, és az előre létrehozott mappába másolja. [További információk](#data-or-file-type-errors). |
| A nem kritikus blob- vagy hibák  | A blob- vagy nem követi az Azure elnevezési szabályait, vagy a fájl típusa nem támogatott. | A blob vagy a fájlok nem másolható, vagy lehet módosítani a neveket. [Ezek a hibák elhárításával](#non-critical-blob-or-file-errors). |

\* Az első négy hibakategóriák kritikus hibák és a szállításra való folytatás előtt ki kell javítani.


## <a name="container-or-share-name-errors"></a>Tároló vagy a megosztás neve hiba

Ezek a tároló és a megosztás nevét kapcsolódó hibák.

### <a name="errorcontainerorsharenamelength"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Hiba leírása:** A tároló vagy a megosztás neve csak 3 és 63 karakter közötti hosszúságú lehet. 

**Javasolt megoldás:** A mappát a Data Box vagy a Data Box nehéz share(SMB/NFS), amelyhez adatokat másolta a tárfiókja egy Azure container válik. 

- Az a **csatlakozás és másolás** az eszköz helyi webes felületén, letöltése és tekintse át a hiba-fájlokat a mappa neve problémákkal rendelkező lapján.
- Módosítsa a Data Box vagy a Data Box nehéz megosztásban győződjön meg arról, hogy a mappa neve:

    - A név 3 és 63 karakter közötti hosszúságú rendelkezik.
    - A nevek csak betűket, számokat és kötőjeleket tartalmazhat rendelkezhet.
    - A nevek nem kezdődhet és végződhet kötőjellel.
    - A neve nem tartalmazhat egymást követő kötőjelet.
    - Példák az érvényes nevek: `my-folder-1`, `my-really-extra-long-folder-111`
    - Példák a nevét, amely nem érvényes: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    További információkért tekintse meg az Azure elnevezési szabályai [a tároló nevének](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) és [megosztási név](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).


### <a name="errorcontainerorsharenamealphanumericdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Hiba leírása:** A tároló vagy a megosztás neve csak betűket, számokat és kötőjeleket tartalmazhat.

**Javasolt megoldás:** A mappát a Data Box vagy a Data Box nehéz share(SMB/NFS), amelyhez adatokat másolta a tárfiókja egy Azure container válik. 

- Az a **csatlakozás és másolás** az eszköz helyi webes felületén, letöltése és tekintse át a hiba-fájlokat a mappa neve problémákkal rendelkező lapján.
- Módosítsa a Data Box vagy a Data Box nehéz megosztásban győződjön meg arról, hogy a mappa neve:

    - A név 3 és 63 karakter közötti hosszúságú rendelkezik.
    - A nevek csak betűket, számokat és kötőjeleket tartalmazhat rendelkezhet.
    - A nevek nem kezdődhet és végződhet kötőjellel.
    - A neve nem tartalmazhat egymást követő kötőjelet.
    - Példák az érvényes nevek: `my-folder-1`, `my-really-extra-long-folder-111`
    - Példák a nevét, amely nem érvényes: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    További információkért tekintse meg az Azure elnevezési szabályai [a tároló nevének](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) és [megosztási név](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

### <a name="errorcontainerorsharenameimproperdash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Hiba leírása:** A tároló nevének és a fájlmegosztások neve nem lehet elindítani vagy végződhet kötőjellel, és nem lehetnek egymást követő kötőjelet.

**Javasolt megoldás:** A mappát a Data Box vagy a Data Box nehéz share(SMB/NFS), amelyhez adatokat másolta a tárfiókja egy Azure container válik. 

- Az a **csatlakozás és másolás** az eszköz helyi webes felületén, letöltése és tekintse át a hiba-fájlokat a mappa neve problémákkal rendelkező lapján.
- Módosítsa a Data Box vagy a Data Box nehéz megosztásban győződjön meg arról, hogy a mappa neve:

    - A név 3 és 63 karakter közötti hosszúságú rendelkezik.
    - A nevek csak betűket, számokat és kötőjeleket tartalmazhat rendelkezhet.
    - A nevek nem kezdődhet és végződhet kötőjellel.
    - A neve nem tartalmazhat egymást követő kötőjelet.
    - Példák az érvényes nevek: `my-folder-1`, `my-really-extra-long-folder-111`
    - Példák a nevét, amely nem érvényes: `my-folder_1`, `my`, `--myfolder`, `myfolder--`, `myfolder!`

    További információkért tekintse meg az Azure elnevezési szabályai [a tároló nevének](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) és [megosztási név](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names).

## <a name="container-or-share-size-limit-errors"></a>Tároló vagy a megosztás méretének korlátja hibák

Ezek a engedélyezett egy tároló vagy a megosztott adatok mérete meghaladja a adatokkal kapcsolatos hibákat.

### <a name="errorcontainerorsharecapacityexceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Hiba leírása:** Azure-fájlmegosztás korlátozza egy 5 TB-nyi adatot-megosztást. Ezt a korlátot processzorhasználata túllépte a megosztások.

**Javasolt megoldás:** Az a **csatlakozás és másolás** oldalon, a helyi webes felhasználói felületen, töltse le és tekintse át a hiba fájlt.

Azonosítsa a mappákat, amelyek a probléma a hibanaplókat, és ügyeljen arra, hogy a fájlok abban a mappában található 5 TB-os.


## <a name="object-or-file-size-limit-errors"></a>Objektum vagy a fájl méretének korlátja hibák

Ezek olyan objektumot, vagy a fájlt, amely az Azure-ban engedélyezett maximális méretet meghaladó adatokkal kapcsolatos hibákat. 

### <a name="errorbloborfilesizelimit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Hiba leírása:** A fájl mérete meghaladja a maximális méretet a feltöltéshez.

**Javasolt megoldás:** A blob vagy a fájlméret meghaladja a maximálisan engedélyezett a feltöltés.

- Az a **csatlakozás és másolás** oldalon, a helyi webes felhasználói felületen, töltse le és tekintse át a hiba fájlt.
- Győződjön meg arról, hogy a blobok és fájlok mérete nem haladja meg az Azure objektum méretbeli korlátokat.

## <a name="data-or-file-type-errors"></a>Adatok vagy a fájl típusa hiba

Ezek a nem támogatott fájltípus vagy adattípus található a tároló vagy a megosztást a kapcsolódó hibák. 

### <a name="errorbloborfilesizealignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Hiba leírása:** A blob vagy a fájl nincs megfelelően igazítva.

**Javasolt megoldás:** A Data Box vagy a Data Box nehéz csak támogatja fájlok 512 bájt lap blob megosztás igazított (például VHD/VHDX). A lap blob megosztáshoz másolt adatokat az Azure-bA feltöltött lapblobként.

Távolítsa el a nem VHD/VHDX adatokat a lap blob megosztásból. Blokkblob vagy általános adatok az Azure files megosztások is használhatja.

További információkért lásd: [áttekintése, Page blobs](../storage/blobs/storage-blob-pageblob-overview.md).

### <a name="errorbloborfiletypeunsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Hiba leírása:** Egy nem támogatott fájltípus egy felügyelt lemez megosztás jelen. Kizárólag rögzített VHD-k engedélyezettek.

**Javasolt megoldás:**

- Ellenőrizze, hogy csak a felügyelt lemezek rögzített VHD feltöltése.
- A VHDX-fájlok vagy **dinamikus** és **különbséglemez** VHD-k nem támogatottak.

### <a name="errordirectorydisallowedfortype"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Hiba leírása:** A könyvtár nem engedélyezett egy már létező mappa a felügyelt lemezek. Ezek a mappák kizárólag rögzített VHD-k engedélyezettek.

**Javasolt megoldás:** Felügyelt lemezek, minden egyes megosztáson belüli a következő három mappák jönnek létre, amelyek megfelelnek a tárfiókban lévő tárolók: Prémium szintű SSD-, Standard HDD, és Standard SSD. Ezek a mappák felel meg a felügyelt lemez teljesítményszintjét.

- Győződjön meg arról, hogy az egyik létező mappát, másolja ki a lap Blobadatok (VHD).
- Ezeket a meglévő mappákat a nem engedélyezett a mappát vagy könyvtárat. Távolítsa el a már meglévő mappákban létrehozott minden olyan mappát.

További információkért lásd: [a felügyelt lemezek másolása](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).

### <a name="reparsepointerror"></a>REPARSE_POINT_ERROR

**Hiba leírása:** Szimbolikus hivatkozások nem engedélyezettek a Linux. 

**Javasolt megoldás:** A szimbolikus hivatkozásokat rendszerint hivatkozások csövek és egyéb fájlokat. A csatolást távolítsa el vagy feloldani a hivatkozásokat, és másolja az adatokat.


## <a name="non-critical-blob-or-file-errors"></a>A nem kritikus blob- vagy hibák

Az alábbiakban láthatók az adatmásolás során hibákat foglalja össze.

### <a name="errorbloborfilenamecharactercontrol"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Hiba leírása:** A blob- vagy neveket vezérlő nem támogatott karaktereket tartalmaz.

**Javasolt megoldás:** A blobok és a másolt fájlokat a nem támogatott karaktereket tartalmazó neveket tartalmaz.

Az a **csatlakozás és másolás** oldalon, a helyi webes felhasználói felületen, töltse le és tekintse át a hiba fájlt.
Távolítsa el vagy nevezze át a fájlokat, és távolítsa el a nem támogatott karaktereket.

További információkért tekintse meg az Azure elnevezési szabályai [blob-nevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlneveket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).

### <a name="errorbloborfilenamecharacterillegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Hiba leírása:** A blob vagy a fájl neve érvénytelen karaktereket tartalmaz.

**Javasolt megoldás:** A blobok és a másolt fájlokat a nem támogatott karaktereket tartalmazó neveket tartalmaz.

Az a **csatlakozás és másolás** oldalon, a helyi webes felhasználói felületen, töltse le és tekintse át a hiba fájlt.
Távolítsa el vagy nevezze át a fájlokat, és távolítsa el a nem támogatott karaktereket.

További információkért tekintse meg az Azure elnevezési szabályai [blob-nevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlneveket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenameending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Hiba leírása:** A blob- vagy neveket is végződő helytelen karaktereket.

**Javasolt megoldás:** A blobok és a másolt fájlokat a nem támogatott karaktereket tartalmazó neveket tartalmaz.

Az a **csatlakozás és másolás** oldalon, a helyi webes felhasználói felületen, töltse le és tekintse át a hiba fájlt.
Távolítsa el vagy nevezze át a fájlokat, és távolítsa el a nem támogatott karaktereket.

További információkért tekintse meg az Azure elnevezési szabályai [blob-nevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlneveket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names).


### <a name="errorbloborfilenamesegmentcount"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Hiba leírása:** A blob- vagy név túl sok összetevőt tartalmaz.

**Javasolt megoldás:** A blobok és a másolt fájlok haladhatja meg az elérési út szegmensek maximális számát. Egy részleges útvonalat a karakterláncot egymást követő elválasztó karakter, például a perjel /.

- Az a **csatlakozás és másolás** oldalon, a helyi webes felhasználói felületen, töltse le és tekintse át a hiba fájlt.
- Győződjön meg arról, hogy a [blob-nevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlneveket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) felelnek meg az Azure elnevezési konvencióinak.

### <a name="errorbloborfilenameaggregatelength"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Hiba leírása:** A blob- vagy fájlnév túl hosszú.

**Javasolt megoldás:** A blob vagy a fájlnevek hosszabb a maximálisan megengedett.

- Az a **csatlakozás és másolás** oldalon, a helyi webes felhasználói felületen, töltse le és tekintse át a hiba fájlt.
- A blob neve nem lehet 1024 karakternél.
- Távolítsa el vagy nevezze át a blob vagy a fájlokat, hogy a nevek nem lehet 1024 karakternél hosszabb.

További információkért tekintse meg a blob és fájl nevét az Azure elnevezési konvencióinak.

### <a name="errorbloborfilenamecomponentlength"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Hiba leírása:** A blob- vagy fájlnevében szereplő egyik szegmens túl hosszú.

**Javasolt megoldás:** A blob- vagy nevében az elérési út szegmensek egyik meghaladja a karakterek maximális számát. Egy részleges útvonalat a karakterláncot egymást követő elválasztó karakter, például a perjel /.

- Az a **csatlakozás és másolás** oldalon, a helyi webes felhasználói felületen, töltse le és tekintse át a hiba fájlt.
- Győződjön meg arról, hogy a [blob-nevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlneveket](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) felelnek meg az Azure elnevezési konvencióinak.


### <a name="errorcontainerorsharenamedisallowedfortype"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Hiba leírása:** Helytelen a tároló nevének felügyelt lemez megosztások vannak megadva.

**Javasolt megoldás:** Felügyelt lemezek, minden megosztás belül a következő mappák jönnek létre, amelyek megfelelnek a tárfiókban lévő tárolók: Prémium szintű SSD-, Standard HDD, és Standard SSD. Ezek a mappák felel meg a felügyelt lemez teljesítményszintjét.

- Győződjön meg arról, hogy az egyik létező mappát, másolja ki a lap Blobadatok (VHD). Csak a meglévő tárolók származó adatokat az Azure-bA feltöltött.
- Ha más mappát, amely prémium SSD-re, a standard szintű HDD és a Standard SSD ugyanazon a szinten jön nem felel meg egy érvényes teljesítményszinttel, és nem használható.
- Távolítsa el a fájlokat vagy mappákat, a teljesítményszintek kívül.

További információkért lásd: [a felügyelt lemezek másolása](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box).


## <a name="next-steps"></a>További lépések

- További információ a [Data Box Blob storage rendszerkövetelmények](data-box-system-requirements-rest.md).

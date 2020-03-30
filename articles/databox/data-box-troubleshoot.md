---
title: Az Azure Data Box, az Azure Data Box Heavy hibáinak elhárítása
description: Ez a témakör ismerteti, hogyan háríthatók el az Azure Data Box ban és az Azure Data Box Heavy-ban látott problémák, amikor adatokat másolezekre az eszközökre.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 51935516e26f263e44a926bf9b7d7ec24a5eeb9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560065"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-and-azure-data-box-heavy"></a>Az Azure Data Box és az Azure Data Box Heavy szolgáltatással kapcsolatos problémák elhárítása

Ez a cikk az Azure Data Box vagy az Azure Data Box Heavy használata során megjelenhet problémák elhárításának részleteit ismerteti. A cikk tartalmazza az adatok adatdobozba másolásakor vagy a Data Box-ból történő feltöltéskor észlelt lehetséges hibák listáját.

## <a name="error-classes"></a>Hibaosztályok

A Data Box és a Data Box Heavy hibái a következőképpen foglalódnak össze:

| Hibakategória*        | Leírás        | Javasolt művelet    |
|----------------------------------------------|---------|--------------------------------------|
| Tároló vagy megosztási nevek | A tároló vagy a megosztásnevek nem követik az Azure elnevezési szabályokat.  |Töltse le a hibalistákat. <br> Nevezze át a tárolókat vagy megosztásokat. [További információ](#container-or-share-name-errors).  |
| Tároló vagy megosztás méretkorlátja | A tárolókban vagy megosztásokban lévő összes adat meghaladja az Azure-korlátot.   |Töltse le a hibalistákat. <br> Csökkentse a tárolóban vagy megosztásban lévő teljes adatokat. [További információ](#container-or-share-size-limit-errors).|
| Objektum- vagy fájlméret-korlát | A tárolókban vagy megosztásokban lévő objektum vagy fájlok meghaladják az Azure-korlátot.|Töltse le a hibalistákat. <br> Csökkentse a fájlméretet a tárolóban vagy a megosztásban. [További információ](#object-or-file-size-limit-errors). |    
| Adat vagy fájltípus | Az adatformátum vagy a fájltípus nem támogatott. |Töltse le a hibalistákat. <br> Lapblobok vagy felügyelt lemezek esetén győződjön meg arról, hogy az adatok 512 bájttal vannak igazítva, és az előre létrehozott mappákba vannak másolva. [További információ](#data-or-file-type-errors). |
| Nem kritikus blob- vagy fájlhibák  | A blob- vagy fájlnevek nem követik az Azure elnevezési szabályait, vagy a fájltípus nem támogatott. | Előfordulhat, hogy ezek a blobok vagy fájlok nem másolhatók, vagy a nevek módosíthatók. [További információ a hibák kijavításáról.](#non-critical-blob-or-file-errors) |

\*Az első négy hibakategória kritikus hiba, és ki kell javítani, mielőtt folytathatja a felkészülést a szállításra.


## <a name="container-or-share-name-errors"></a>Tároló- vagy megosztási névhibák

Ezek a tároló- és megosztásnevekkel kapcsolatos hibák.

### <a name="error_container_or_share_name_length"></a>ERROR_CONTAINER_OR_SHARE_NAME_LENGTH     

**Hiba leírása:** A tároló- vagy megosztásnévnek 3 és 63 karakter között kell lennie. 

**Javasolt megoldás:** A Data Box vagy a Data Box Heavy share (SMB/NFS) mappában, amelyre adatokat másolt, a tárfiókban Azure-tárolóvá válik. 

- Az eszköz helyi webes felhasználói felületének **csatlakoztatása és másolása** lapján töltse le és tekintse át a hibafájlokat a problémákkal rendelkező mappanevek azonosításához.
- Módosítsa a mappa nevét a Data Box vagy a Data Box Heavy megosztás alatt, hogy biztosan:

    - A név 3 és 63 karakter közötti.
    - A nevekcsak betűket, számokat és kötőjeleket tartalmaznak.
    - A nevek nem kezdődhetnek és végződhetnek kötőjellel.
    - A neveknek nem lehetnek egymás után elválasztói.
    - Példák érvényes `my-folder-1`nevekre: ,`my-really-extra-long-folder-111`
    - `my-folder_1`Példák érvénytelen nevekre: , `my` `--myfolder`, `myfolder--`, ,`myfolder!`

    További információt a [tárolónevekés](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) [megosztásnevek](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)Azure-elnevezési konvenciói című témakörben talál.


### <a name="error_container_or_share_name_alpha_numeric_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_ALPHA_NUMERIC_DASH

**Hiba leírása:** A tároló- vagy megosztásnév csak betűkből, számokból vagy kötőjelekből állhat.

**Javasolt megoldás:** A Data Box vagy a Data Box Heavy share (SMB/NFS) mappában, amelyre adatokat másolt, a tárfiókban Azure-tárolóvá válik. 

- Az eszköz helyi webes felhasználói felületének **csatlakoztatása és másolása** lapján töltse le és tekintse át a hibafájlokat a problémákkal rendelkező mappanevek azonosításához.
- Módosítsa a mappa nevét a Data Box vagy a Data Box Heavy megosztás alatt, hogy biztosan:

    - A név 3 és 63 karakter közötti.
    - A nevekcsak betűket, számokat és kötőjeleket tartalmaznak.
    - A nevek nem kezdődhetnek és végződhetnek kötőjellel.
    - A neveknek nem lehetnek egymás után elválasztói.
    - Példák érvényes `my-folder-1`nevekre: ,`my-really-extra-long-folder-111`
    - `my-folder_1`Példák érvénytelen nevekre: , `my` `--myfolder`, `myfolder--`, ,`myfolder!`

    További információt a [tárolónevekés](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) [megosztásnevek](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)Azure-elnevezési konvenciói című témakörben talál.

### <a name="error_container_or_share_name_improper_dash"></a>ERROR_CONTAINER_OR_SHARE_NAME_IMPROPER_DASH

**Hiba leírása:** A tárolónevek és a megosztásnevek nem kezdődhetnek elválasztókkal, és nem lehetnek egymás után elválasztójelek.

**Javasolt megoldás:** A Data Box vagy a Data Box Heavy share (SMB/NFS) mappában, amelyre adatokat másolt, a tárfiókban Azure-tárolóvá válik. 

- Az eszköz helyi webes felhasználói felületének **csatlakoztatása és másolása** lapján töltse le és tekintse át a hibafájlokat a problémákkal rendelkező mappanevek azonosításához.
- Módosítsa a mappa nevét a Data Box vagy a Data Box Heavy megosztás alatt, hogy biztosan:

    - A név 3 és 63 karakter közötti.
    - A nevekcsak betűket, számokat és kötőjeleket tartalmaznak.
    - A nevek nem kezdődhetnek és végződhetnek kötőjellel.
    - A neveknek nem lehetnek egymás után elválasztói.
    - Példák érvényes `my-folder-1`nevekre: ,`my-really-extra-long-folder-111`
    - `my-folder_1`Példák érvénytelen nevekre: , `my` `--myfolder`, `myfolder--`, ,`myfolder!`

    További információt a [tárolónevekés](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata#container-names) [megosztásnevek](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#share-names)Azure-elnevezési konvenciói című témakörben talál.

## <a name="container-or-share-size-limit-errors"></a>Tároló- vagy megosztási méretkorlát-hibák

Ezek a hibák a tárolóban vagy megosztásban engedélyezett adatok méretét meghaladó adatokkal kapcsolatosak.

### <a name="error_container_or_share_capacity_exceeded"></a>ERROR_CONTAINER_OR_SHARE_CAPACITY_EXCEEDED

**Hiba leírása:** Az Azure fájlmegosztás egy megosztást 5 TB-ra korlátoz. Ez a korlát egyes megosztások esetében túllépte ezt a korlátot.

**Javasolt megoldás:** A helyi webes felhasználói felület **Connect and Copy** oldalán töltse le és tekintse át a hibafájlokat.

Azonosítsa a hibát okozó mappákat a hibanaplókból, és győződjön meg arról, hogy a mappában lévő fájlok 5 TB alatt vannak.


## <a name="object-or-file-size-limit-errors"></a>Objektum- vagy fájlméret-korlátozási hibák

Ezek az objektum vagy az Azure-ban engedélyezett fájl maximális méretét meghaladó adatokkal kapcsolatos hibák. 

### <a name="error_blob_or_file_size_limit"></a>ERROR_BLOB_OR_FILE_SIZE_LIMIT

**Hiba leírása:** A fájlméret meghaladja a feltöltés maximális fájlméretét.

**Javasolt megoldás:** A blob vagy a fájlméretek meghaladják a feltöltéshez engedélyezett maximális korlátot.

- A helyi webes felhasználói felület **Connect and Copy** oldalán töltse le és tekintse át a hibafájlokat.
- Győződjön meg arról, hogy a blob és a fájlméretek nem haladják meg az Azure-objektum méretkorlátait.

## <a name="data-or-file-type-errors"></a>Adat- vagy fájltípus-hibák

Ezek a tárolóban vagy megosztásban található nem támogatott fájltípussal vagy adattípussal kapcsolatos hibák. 

### <a name="error_blob_or_file_size_alignment"></a>ERROR_BLOB_OR_FILE_SIZE_ALIGNMENT

**Hiba leírása:** A blob vagy fájl helytelenül van igazítva.

**Javasolt megoldás:** A lapblob-megosztás a Data Box vagy a Data Box Heavy csak az 512 bájtos (például VHD/VHDX) fájlokat támogatja. A lapblob-megosztásra másolt adatokat a rendszer lapblobként tölti fel az Azure-ba.

Távolítsa el a nem VHD/VHDX-adatokat a lapblob-megosztásból. Általános adatokhoz használhatja a megosztásokat blokkblobhoz vagy Azure-fájlokhoz.

További információt [a Lapblobok áttekintése című témakörben talál.](../storage/blobs/storage-blob-pageblob-overview.md)

### <a name="error_blob_or_file_type_unsupported"></a>ERROR_BLOB_OR_FILE_TYPE_UNSUPPORTED

**Hiba leírása:** Nem támogatott fájltípus található a felügyelt lemezmegosztásban. Csak rögzített VHD-k engedélyezettek.

**Javasolt megoldás:**

- Győződjön meg arról, hogy csak a rögzített virtuális merevlemezeket töltötte fel felügyelt lemezek létrehozásához.
- A VHDX-fájlok vagy **a dinamikus** és **eltérő** virtuális merevlemezek nem támogatottak.

### <a name="error_directory_disallowed_for_type"></a>ERROR_DIRECTORY_DISALLOWED_FOR_TYPE

**Hiba leírása:** A felügyelt lemezek egyik már meglévő mappájában sem engedélyezett könyvtár. Ezekben a mappákban csak a rögzített Virtuális, nagygépek engedélyezettek.

**Javasolt megoldás:** Felügyelt lemezek, minden megosztáson belül a következő három mappa jön létre, amelyek megfelelnek a tárolók a tárfiókban: Prémium SSD, Standard HDD és standard SSD. Ezek a mappák megfelelnek a felügyelt lemez teljesítményszintjének.

- Győződjön meg arról, hogy másolja a lapblob-adatok (VHDs) az egyik meglévő mappákba.
- Ezekben a meglévő mappákban nem engedélyezett mappa vagy könyvtár. Távolítsa el a már meglévő mappákban létrehozott mappákat.

További információt a [Másolás felügyelt lemezekre című témakörben talál.](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)

### <a name="reparse_point_error"></a>REPARSE_POINT_ERROR

**Hiba leírása:** Szimbolikus linkek nem engedélyezettek linuxon. 

**Javasolt megoldás:** A szimbolikus hivatkozások általában hivatkozások, csövek és más hasonló fájlok. Távolítsa el a hivatkozásokat, vagy oldja fel a hivatkozásokat, és másolja az adatokat.


## <a name="non-critical-blob-or-file-errors"></a>Nem kritikus blob- vagy fájlhibák

Az adatmásolás során látható blobok, fájlok vagy tárolók nevével kapcsolatos összes nem kritikus hibát a következő szakasz foglalja össze. Ha ezek a hibák jelen vannak, majd a nevek módosulnak, hogy megfeleljenek az Azure elnevezési konvenciók. Az adatok feltöltésének megfelelő rendelési állapot **figyelmeztetésekkel lesz befejezve.**  

### <a name="error_blob_or_file_name_character_control"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_CONTROL

**Hiba leírása:** A blob- vagy fájlnevek nem támogatott vezérlőkaraktereket tartalmaznak.

**Javasolt megoldás:** A blobok vagy a másolt fájlok nem támogatott karaktereket tartalmazó neveket tartalmaznak.

A helyi webes felhasználói felület **Connect and Copy** oldalán töltse le és tekintse át a hibafájlokat.
Távolítsa el vagy nevezze át a fájlokat a nem támogatott karakterek eltávolításához.

További információt a blobnevek és [fájlnevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) Azure-elnevezési konvenciói című [témakörben talál.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)

### <a name="error_blob_or_file_name_character_illegal"></a>ERROR_BLOB_OR_FILE_NAME_CHARACTER_ILLEGAL

**Hiba leírása:** A blob- vagy fájlnevek érvénytelen karaktereket tartalmaznak.

**Javasolt megoldás:** A blobok vagy a másolt fájlok nem támogatott karaktereket tartalmazó neveket tartalmaznak.

A helyi webes felhasználói felület **Connect and Copy** oldalán töltse le és tekintse át a hibafájlokat.
Távolítsa el vagy nevezze át a fájlokat a nem támogatott karakterek eltávolításához.

További információt a blobnevek és [fájlnevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) Azure-elnevezési konvenciói című [témakörben talál.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)


### <a name="error_blob_or_file_name_ending"></a>ERROR_BLOB_OR_FILE_NAME_ENDING

**Hiba leírása:** A blob- vagy fájlnevek rossz karakterekkel végződnek.

**Javasolt megoldás:** A blobok vagy a másolt fájlok nem támogatott karaktereket tartalmazó neveket tartalmaznak.

A helyi webes felhasználói felület **Connect and Copy** oldalán töltse le és tekintse át a hibafájlokat.
Távolítsa el vagy nevezze át a fájlokat a nem támogatott karakterek eltávolításához.

További információt a blobnevek és [fájlnevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) Azure-elnevezési konvenciói című [témakörben talál.](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)


### <a name="error_blob_or_file_name_segment_count"></a>ERROR_BLOB_OR_FILE_NAME_SEGMENT_COUNT

**Hiba leírása:** A blob vagy fájlnév túl sok elérési útszegmenst tartalmaz.

**Javasolt megoldás:** A blobok vagy a másolt fájlok meghaladják az elérési út szegmenseinek maximális számát. A görbeszegmens az egymást követő határoló karakterek, például a perjel /.

- A helyi webes felhasználói felület **Connect and Copy** oldalán töltse le és tekintse át a hibafájlokat.
- Győződjön meg arról, hogy a [blob nevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlnevek](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) megfelelnek az Azure elnevezési konvenciók.

### <a name="error_blob_or_file_name_aggregate_length"></a>ERROR_BLOB_OR_FILE_NAME_AGGREGATE_LENGTH

**Hiba leírása:** A blob vagy a fájl neve túl hosszú.

**Javasolt megoldás:** A blob vagy a fájlnevek meghaladják a maximális hosszt.

- A helyi webes felhasználói felület **Connect and Copy** oldalán töltse le és tekintse át a hibafájlokat.
- A blob neve nem haladhatja meg az 1024 karaktert.
- Távolítsa el vagy nevezze át a blobot vagy fájlokat, hogy a nevek ne haladják meg az 1024 karaktert.

További információkért tekintse meg az Azure elnevezési konvenciók blob nevek és fájlnevek.

### <a name="error_blob_or_file_name_component_length"></a>ERROR_BLOB_OR_FILE_NAME_COMPONENT_LENGTH

**Hiba leírása:** A blob vagy fájlnév egyik szegmense túl hosszú.

**Javasolt megoldás:** A blob vagy a fájlnév egyik elérési útszegmense meghaladja a karakterek maximális számát. A görbeszegmens az egymást követő határoló karakterek, például a perjel /.

- A helyi webes felhasználói felület **Connect and Copy** oldalán töltse le és tekintse át a hibafájlokat.
- Győződjön meg arról, hogy a [blob nevek](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata#blob-names) és [fájlnevek](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) megfelelnek az Azure elnevezési konvenciók.


### <a name="error_container_or_share_name_disallowed_for_type"></a>ERROR_CONTAINER_OR_SHARE_NAME_DISALLOWED_FOR_TYPE

**Hiba leírása:** A felügyelt lemezmegosztások nem megfelelő tárolóneveket adnak meg.

**Javasolt megoldás:** Felügyelt lemezek, minden megosztáson belül a következő mappák jönnek létre, amelyek megfelelnek a tárolók a tárfiókban: Prémium SSD, Standard HDD és standard SSD. Ezek a mappák megfelelnek a felügyelt lemez teljesítményszintjének.

- Győződjön meg arról, hogy másolja a lapblob-adatok (VHDs) az egyik meglévő mappákba. Csak a meglévő tárolókból származó adatok töltődnek fel az Azure-ba.
- Bármely más mappa, amely a prémium szintű SSD, standard HDD és standard SSD ugyanazon a szinten jön létre, nem felel meg egy érvényes teljesítményszintnek, és nem használható.
- Távolítsa el a teljesítményszinteken kívül létrehozott fájlokat vagy mappákat.

További információt a [Másolás felügyelt lemezekre című témakörben talál.](data-box-deploy-copy-data-from-vhds.md#connect-to-data-box)


## <a name="next-steps"></a>További lépések

- További információ a [Data Box Blob tárolási rendszerkövetelményeiről.](data-box-system-requirements-rest.md)

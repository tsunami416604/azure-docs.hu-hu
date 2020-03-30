---
title: Azure Data Box Disk korlátok | Microsoft dokumentumok
description: A cikk a Microsoft Azure Data Box Disk rendszerkorlátait és ajánlott méreteit ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 11/05/2019
ms.author: alkohli
ms.openlocfilehash: 1bb8300f1e54cf03563704cf00549ce9e09a3916
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260162"
---
# <a name="azure-data-box-disk-limits"></a>Az Azure Data Box lemezkorlátai


Vegye figyelembe ezeket a korlátokat a Microsoft Azure Data Box Disk megoldás üzembe helyezéseés működtetése során.

## <a name="data-box-service-limits"></a>Data Box szolgáltatás korlátai

 - A Data Box szolgáltatás a régió [elérhetősége](data-box-disk-overview.md#region-availability)mezőben felsorolt Azure-régiókban érhető el.
 - A Data Box Disk egyetlen tárfiókot támogat.

## <a name="data-box-disk-performance"></a>Adatdoboz lemezének teljesítménye

A tesztjeink során USB 3.0-ás kapcsolat esetén a lemezeknél 430 MB/s-os adatátviteli sebességet is mértünk. A tényleges szám a használt fájl méretének függvényében eltérő lehet. Kisebb fájlok esetén gyengébb teljesítmény figyelhető meg.

## <a name="azure-storage-limits"></a>Az Azure tárolási korlátai

Ez a szakasz ismerteti az Azure Storage-szolgáltatás korlátait, valamint az Azure Files, az Azure-blokkblobok és az Azure-lapblobok szükséges elnevezési konvencióit, a Data Box szolgáltatásra vonatkozóan. Tekintse át figyelmesen a tárolási korlátokat, és kövesse az összes ajánlást.

Az Azure storage-szolgáltatás korlátaival és a megosztások, tárolók és fájlok elnevezésével kapcsolatos gyakorlati tanácsokról a következő információkat talál:

- [Tárolók elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blobok és lapblobok blokkolása konvenciók](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak olyan fájlok vagy könyvtárak, amelyek meghaladják az Azure Storage szolgáltatás korlátait, vagy nem felelnek meg az Azure Files/Blob elnevezési konvencióknak, majd ezek a fájlok vagy könyvtárak nem kerülnek be az Azure Storage-ba a Data Box szolgáltatáson keresztül.

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései

- Ne másolja az adatokat közvetlenül a lemezekre. Adatok másolása előre létrehozott *BlockBlob*,*PageBlob*és *AzureFile* mappákba.
- A *BlockBlob* és a *PageBlob* alatt egy mappa egy tároló. Például a tárolók *BlockBlob/container* és *PageBlob/container*néven jönnek létre.
- Ha van egy meglévő Azure-objektum (például egy blob) a felhőben, amelynek neve megegyezik a másolt objektum nevével, a Data Box Disk átnevezi a fájlt fájlként(1) a felhőben.
- A *BlockBlob* és *a PageBlob* megosztásokba írt összes fájl blokkblobként és lapblobként kerül feltöltésre.
- A *BlockBlob* és *a PageBlob* mappák alatt létrehozott üres könyvtárhierarchia (fájlok nélkül) nem kerül feltöltésre.
- Ha bármilyen hiba történik az adatok Azure-ba való feltöltésekénél, a céltárfiókban hibanapló jön létre. A hibanapló elérési útja elérhető a portálon, amikor a feltöltés befejeződött, és a naplót áttekintheti a korrekciós intézkedések elvégzéséhez. Ne töröljön adatokat a forrásból a feltöltött adatok ellenőrzése nélkül.
- A fájl metaadatok és az NTFS-engedélyek nem őrződnek meg, amikor az adatok at feltöltik az Azure Files-ba. Például a fájlok *Utolsó módosítás* attribútuma nem marad meg az adatok másolásakor.
- Ha a sorrendben felügyelt lemezeket adott meg, tekintse át a következő szempontokat:

    - Egy erőforráscsoport csak egy adott nevű felügyelt lemezt tartalmazhat az előre létrehozott mappákban és a teljes Data Box Disken. Ez azt is jelenti, hogy az előre létrehozott mappákba feltöltött VHD-knak egyedi névvel kell rendelkezniük. Győződjön meg arról, hogy a megadott név nem egyezik meg egy erőforráscsoport meglévő felügyelt lemez nevével sem. Ha a VHD-k azonos névvel rendelkeznek, akkor csak egy VHD lesz átalakítva egy azonos névvel rendelkező felügyelt lemezzé. A többi VHD-fájl lapblobként lesz feltöltve az előkészítési tárfiókba.
    - Mindig másolja át a VHD-kat az egyik előre létrehozott mappába. Ha a VHD-kat ezen mappákon kívülre vagy egy Ön által létrehozott mappába másolja, akkor a VHD-k nem felügyelt lemezekként, hanem lapblobként lesznek feltöltve az Azure Storage-fiókba.
    - Csak a rögzített VHD-ket lehet feltölteni felügyelt lemezek létrehozásához. A dinamikus VHD-k, a virtuális különbséglemezek és a VHDX-fájlok nem támogatottak.

## <a name="azure-storage-account-size-limits"></a>Az Azure tárfiók méretkorlátai

Az alábbiakban a tárfiókba másolt adatok méretére vonatkozó korlátokat. Győződjön meg arról, hogy a feltöltött adatok megfelelnek ezeknek a korlátoknak. A korlátokkal kapcsolatos legfrissebb információkért keresse fel az [Azure blob tárolási méretezési céljait](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage) és az [Azure Files méretezési célokat.](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account#scale-targets-for-standard-storage-accounts)

| Az Azure storage-fiókba másolt adatok mérete                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| Blob és lapblob blokkolása                                            | 500 TB tárfiókonként. <br> Ez magában foglalja az összes forrásból származó adatokat, beleértve a Data Box Disk-et is.|


## <a name="azure-object-size-limits"></a>Az Azure-objektumok méretkorlátai

Az alábbiakban az Azure-objektumok megírható méretei láthatók. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek ezeknek a korlátoknak.

| Azure-objektumtípusa | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blob blokkolása        | ~ 4,75 TiB                                                 |
| Lapblob         | 8 TiB <br> (A Lapblob formátumban feltöltött összes fájlnak 512 bájtnak kell lennie, különben a feltöltés sikertelen. <br> Mind a Virtuális merevlemez, mind a VHDX 512 bájttal van elrendezve.) |
|Azure Files        | 1 TiB <br> Legfeljebb a részesedés mérete 5 TiB     |
| Felügyelt lemezek     |4 TiB <br> A méretről és a korlátokról további információt a következő témakörben talál: <li>[Méretezhetőségi célok felügyelt lemezekhez](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Azure-blokkblob, lapblob és fájlelnevezési konvenciók

| Entitás                                       | Konvenciók                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Tárolónevek a blokkblobhoz és a lapblobhoz <br> Az Azure Files fájlmegosztási nevei | Érvényes, 3–63 karakter hosszú DNS-névnek kell lennie. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. <br> A tárolók nevében nem szerepelhetnek egymást követő kötőjelek. |
| Azure-fájlok könyvtár- és fájlnevei     |<li> A kis- és nagybetűk megőrzése, a kis- és nagybetűk megkülönböztetése, és nem haladhatja meg a 255 karakter hosszúságot. </li><li> Nem végződhet a perjellel (/). </li><li>Ha rendelkezésre áll, akkor automatikusan törlődik. </li><li> A következő karakterek nem engedélyezettek:<code>" \\ / : \| < > * ?</code></li><li> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li> Az URL-elérési út érvénytelen karakterei nem engedélyezettek. Az uE000-hez hasonló \\kódpontok nem érvényes Unicode karakterek. Egyes ASCII- vagy Unicode karakterek, például vezérlőkarakterek (0x00–0x1F, \\u0081 stb.) szintén nem engedélyezettek. A HTTP/1.1-ben található Unicode karakterláncokra vonatkozó szabályokat lásd az RFC 2616 2.2 szakasz: Alapszabályok és RFC 3987. </li><li> A következő fájlnevek nem engedélyezettek: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, dot character (.) és two dot character (..).</li>|
| Blobnevek blokkblob és lapblob esetén      | A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak <br> A blob nevének 1–1024 karakter hosszúságúnak kell lennie. <br> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. <br>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti sztringek, amelyek megegyeznek egy virtuális könyvtár nevével. |

## <a name="managed-disk-naming-conventions"></a>Felügyelt lemezelnevezési konvenciók

| Entitás | Konvenciók                                             |
|-------------------|-----------------------------------------------------------|
| Felügyelt lemeznevek       | <li> A név nek 1 és 80 karakter között kell lennie. </li><li> A névnek betűvel vagy számmal kell kezdődnie, betűvel, számmal vagy aláhúzásjellel kell végződnie. </li><li> A név csak betűket, számokat, aláhúzásjeleket, pontokat vagy kötőjeleket tartalmazhat. </li><li>   A névnek nem `/`lehet szóköze vagy .                                              |

## <a name="next-steps"></a>További lépések

- [Adatdoboz-lemez rendszerkövetelményeinek áttekintése](data-box-disk-system-requirements.md)

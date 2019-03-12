---
title: Az Azure Data Box-lemezek korlátozza |} A Microsoft Docs
description: Ismerteti a Microsoft Azure Data Box-lemezek rendszer korlátok és az ajánlott méreteket.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/19/2019
ms.author: alkohli
ms.openlocfilehash: 65a3aa41c72341aa57c8cb2ddca5d1ed10f0ec06
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530841"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box-lemezek korlátai


Vegye figyelembe ezeket a korlátokat, üzembe helyezése és működtetése megoldását a Microsoft Azure Data Box-lemezek.

## <a name="data-box-service-limits"></a>A Data Box szolgáltatás korlátai

 - A Data Box szolgáltatás érhető el az Azure-régiók szereplő [régiók rendelkezésre állása](data-box-disk-overview.md#region-availability).
 - Az egy tárfiókban Data Box-lemezek használata támogatott.

## <a name="data-box-disk-performance"></a>Data Box-lemezek teljesítménye

A tesztjeink során USB 3.0-ás kapcsolat esetén a lemezeknél 430 MB/s-os adatátviteli sebességet is mértünk. A tényleges szám a használt fájl méretének függvényében eltérő lehet. Kisebb fájlok esetén gyengébb teljesítmény figyelhető meg.

## <a name="azure-storage-limits"></a>Az Azure storage-korlátok

Ez a szakasz ismerteti az Azure Storage service korlátai, és a szükséges elnevezési konvenciók Azure Files, az Azure block blobs és oldala az Azure-blobok, a Data Box szolgáltatás teljesítjük. Gondosan tekintse át a tárfiókok korlátai, és hajtsa végre az összes javaslatot.

Az Azure storage szolgáltatási korlátai és gyakorlati tanácsok az elnevezési megosztások, a tárolók és a fájlok a legfrissebb információkért nyissa meg:

- [Elnevezése és hivatkozása tárolók](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokkblobok és a blob szabályai lap](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak, fájlok vagy könyvtárak lépik túl az Azure Storage szolgáltatás, vagy nem felelnek meg az Azure Files/Blob elnevezési konvencióinak, majd ezeket a fájlokat és könyvtárakat vannak nem betöltött a Data Box szolgáltatás használatával az Azure Storage-bA.

## <a name="data-upload-caveats"></a>Adatok feltöltése a figyelmeztetések

- Másolja az adatokat közvetlenül a lemezeket. Adatok másolása az előre létrehozott *BlockBlob*,*PageBlob*, és *AzureFile* mappákat.
- Egy mappát a *BlockBlob* és *PageBlob* tárolója. Például jönnek létre tárolók *BlockBlob/tároló* és *PageBlob/tároló*.
- Ha a felhőben, a neve megegyezik az objektum, amely másolásakor rendelkezik egy meglévő Azure objektum (pl. blobok), akkor Data Box-lemezek file(1) a felhőben, a fájl átnevezése.
- Minden fájl kerülnek *BlockBlob* és *PageBlob* megosztások töltenek fel a blokkblobok és lapblobok jelölik.
- Bármely üres könyvtár-hierarchia (nélküli fájlok) alatt létrehozott *BlockBlob* és *PageBlob* mappák feltöltés nem.
- Ha bármilyen hiba merül fel, ha az adatok feltöltése az Azure-ba, hibanaplót a célként megadott tárfiók létrejön. Ez hiba a napló elérési útja a Portalon érhető el, ha a feltöltés befejeződött, és a naplóban korrekciós műveletek végrehajtására. A feltöltött adatok ellenőrzése nélkül ne törölje adatokat a forrásból.
- Ha a felügyelt lemezek megadott sorrendben, tekintse át a következő további szempontok:

    - A megadott nevű egy felügyelt lemezt egy erőforráscsoportba tartozó legfeljebb a precreated mappákat és az összes a Data Box-lemezek között. Ez azt jelenti, hogy egyedi nevük legyen a VHD-k a precreated mappák feltöltött kell. Győződjön meg arról, hogy a megadott név nem egyezik meg a olyan már meglévő felügyelt lemezről egy erőforráscsoportban. Ha virtuális merevlemezek ugyanazokat a neveket, csak egy virtuális merevlemez konvertálva van ilyen nevű felügyelt lemez. A virtuális merevlemezeket lapblobként lesz töltve a az előkészítési tárfiókból.
    - Minden esetben másolja a VHD-k egyik precreated mappát. Másolja a VHD-ken kívüli ezeket a mappákat vagy egy mappában, amelyet Ön hozott létre, ha a virtuális merevlemezeket lapblobként töltődnek fel az Azure Storage-fiókot, és nem felügyelt lemezek.
    - Csak a rögzített méretű virtuális merevlemezeket is feltölthetők a felügyelt lemez gyors létrehozásához. Dinamikus VHD-k különbséglemez VHD-k vagy VHDX-fájlok nem támogatottak.

## <a name="azure-storage-account-size-limits"></a>Az Azure storage-fiók blobméretének korlátjai

Itt az adatok tárfiókba történő másolt mérete korlátok vonatkoznak. Győződjön meg arról, hogy a feltöltött adatok megfelel-e ezeket a korlátokat. Ezek a korlátok a legfrissebb információkért nyissa meg [az Azure blob storage méretezési célokat](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) és [Azure Files tárolók skálázása](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Az Azure storage-fiókra másolni adatok mérete                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| A Blokkblobok és a lap blob                                            | Tárfiókonként 500 TB. <br> Ez magában foglalja többek között a Data Box-lemezek forrásokból származó adatok.|


## <a name="azure-object-size-limits"></a>Az Azure objektum blobméretének korlátjai

Az alábbiakban az Azure objektumok lehet írni a méretét. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek-e ezeket a korlátokat.

| Az Azure-objektum típusa | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 4,75 Tib-ra                                                 |
| Lapblob         | 8 TiB <br> (Minden feltöltött Lapblob formátumú fájl igazítva 512 bájt kell lennie, ellenkező esetben a feltöltés sikertelen. <br> A VHD és VHDX is igazítva 512 bájt.) |
|Azure Files        | 1 TiB <br> Legfeljebb megosztás mérete 5 Tib-ra     |
| Felügyelt lemezek     |4 TiB <br> A mérete és a korlátok további információkért lásd: <li>[Felügyelt lemezek skálázási célértékei](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)
</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Az Azure blokkblob, lapblob és fájlok elnevezési szabályai

| Entitás                                       | Konvenciók                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A tároló nevének a blokkblobok és lapblobok <br> Fájlmegosztás neve az Azure Files számára | Egy érvényes DNS-nevet, amely 3 – 63 karakter hosszúságúnak kell lennie. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. <br> A tárolók nevében nem szerepelhetnek egymást követő kötőjelek. |
| Az Azure files szolgáltatáshoz a fájl és könyvtár neve     |<li> Nagybetűket, a kis-és nagybetűket, és nem haladhatja meg a 255 karakter hosszú lehet. </li><li> Nem végződhet perjellel (/). </li><li>Ha meg van adva, akkor automatikusan törlődni fog. </li><li> Nem engedélyezettek a következő karakterek: ' "\ vagy: | < > * ?`</li><li> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li> URL-cím elérési út érvénytelen karakterek nem engedélyezettek. A kódpontok \uE000 például amelyek nem érvényes Unicode-karaktereket. Bizonyos ASCII vagy Unicode-karaktereket, például a vezérlőkaraktereket (0x00 való 0x1F \u0081, stb.), emellett nem engedélyezett. Szabályok Unicode karakterláncok HTTP/1.1 RFC 2616, szakasz 2.2 lásd: Alapszintű szabályok és RFC 3987. </li><li> Következő fájlneveket nem engedélyezettek: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, pont karakter (.), és két pont karakter (.).</li>|
| Blobnevek blokkblob és lapblob esetén      | A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak <br> A blob nevének 1–1024 karakter hosszúságúnak kell lennie. <br> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. <br>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti karakterláncok, amelyek megegyeznek egy virtuális könyvtár nevével. |

## <a name="managed-disk-naming-conventions"></a>Felügyelt lemez elnevezési konvenciók

| Entitás | Konvenciók                                             |
|-------------------|-----------------------------------------------------------|
| Felügyelt lemez neve       | <li> A név 1 és 80 karakter hosszúságúnak kell lennie. </li><li> A név kell betűvel vagy számmal kezdődhet, egy betűvel, számmal vagy aláhúzásjellel végződik. </li><li> A név csak betűket, számokat, aláhúzásjeleket, pontokat és kötőjeleket tartalmazhat. </li><li>   A név nem rendelkezhet szóközöket vagy `/`.                                              |

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [Data Box rendszerre vonatkozó követelmények](data-box-system-requirements.md)

---
title: Az Azure Data Box-lemezek korlátozza |} A Microsoft Docs
description: Ismerteti a Microsoft Azure Data Box-lemezek rendszer korlátok és az ajánlott méreteket.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 02/05/2019
ms.author: alkohli
ms.openlocfilehash: 6a7f7943e9d567a953c0e21697dfe4fdedd6e8f0
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55744789"
---
# <a name="azure-data-box-disk-limits"></a>Azure Data Box-lemezek korlátai


Vegye figyelembe ezeket a korlátokat, üzembe helyezése és működtetése megoldását a Microsoft Azure Data Box-lemezek. 

## <a name="data-box-service-limits"></a>A Data Box szolgáltatás korlátai

 - A Data Box szolgáltatás csak az USA, Európa, Kanada és Ausztrália Azure-régióban összes az Azure nyilvános felhő érhető el.
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

- Másolja az adatokat közvetlenül a lemezeket. Adatok másolása az előre létrehozott *BlockBlob* és *PageBlob* mappákat.
- Egy mappát a *BlockBlob* és *PageBlob* tárolója. Például jönnek létre tárolók *BlockBlob/tároló* és *PageBlob/tároló*.
- Ha egy meglévő Azure objektum (például blob) a felhőben, a neve megegyezik az objektumot, másolja, a Data Box-lemezek felülírja a fájl a felhőben.
- Minden fájl kerülnek *BlockBlob* és *PageBlob* megosztások töltenek fel a blokkblobok és lapblobok jelölik.
- Bármely üres könyvtár-hierarchia (nélküli fájlok) alatt létrehozott *BlockBlob* és *PageBlob* mappák feltöltés nem.
- Ha bármilyen hiba merül fel, ha az adatok feltöltése az Azure-ba, hibanaplót a célként megadott tárfiók létrejön. Ez hiba a napló elérési útja a Portalon érhető el, ha a feltöltés befejeződött, és a naplóban korrekciós műveletek végrehajtására. A feltöltött adatok ellenőrzése nélkül ne törölje adatokat a forrásból.

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
| Lapblob         | 8 TiB <br> (Minden feltöltött Lapblob formátumú fájl 512 bájt igazítva (szerves több) kell lennie, ellenkező esetben a feltöltés sikertelen lesz. <br> A VHD és VHDX igazítva 512 bájt.) |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Az Azure blokkblob és elnevezési konvenciók lapblob

| Entitás                                       | Konvenciók                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A tároló nevének a blokkblobok és lapblobok | Egy érvényes DNS-nevet, amely 3 – 63 karakter hosszúságúnak kell lennie. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. <br> A tárolók nevében nem szerepelhetnek egymást követő kötőjelek. |
| Blobnevek blokkblob és lapblob esetén      | A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak <br> A blob nevének 1–1024 karakter hosszúságúnak kell lennie. <br> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. <br>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti karakterláncok, amelyek megegyeznek egy virtuális könyvtár nevével. |


## <a name="next-steps"></a>További lépések
* Felülvizsgálat [Data Box rendszerre vonatkozó követelmények](data-box-system-requirements.md)

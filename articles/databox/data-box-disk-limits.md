---
title: Az Azure Data Box-lemezek korlátozza |} A Microsoft Docs
description: Ismerteti a Microsoft Azure Data Box-lemezek rendszer korlátok és az ajánlott méreteket.
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/12/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 4db70fa93914ba0544d9beb8e523241513a2e5ce
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009300"
---
# <a name="azure-data-box-disk-limits-preview"></a>Az Azure Data Box-lemezek korlátait (előzetes verzió)


Vegye figyelembe ezeket a korlátokat, üzembe helyezése és működtetése megoldását a Microsoft Azure Data Box-lemezek. 

> [!IMPORTANT] 
> Az Azure Data Box-lemezek az előzetes verzióban. Tekintse át a [az előzetes verzió használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Ez a megoldás üzembe helyezése előtt. 


## <a name="data-box-service-limits"></a>A Data Box szolgáltatás korlátai

 - A Data Box szolgáltatás csak az USA és Európa, az Azure nyilvános felhő összes Azure régióban érhető el.
 - Az egy tárfiókban Data Box-lemezek használata támogatott.

## <a name="data-box-disk-performance"></a>Data Box-lemezek teljesítménye

Az USB 3.0-s csatlakoztatású lemezeket tesztelésekor a lemezteljesítmény volt akár 430 MB/s. A tényleges szám eltérőek lehetnek attól függően, hogy a fájl méretét használja. A kisebb fájlok esetében a kisebb teljesítményt jelenhet meg.

## <a name="azure-storage-limits"></a>Az Azure storage-korlátok

Ez a szakasz ismerteti az Azure Storage service korlátai, és a szükséges elnevezési konvenciók Azure Files, az Azure block blobs és oldala az Azure-blobok, a Data Box szolgáltatás teljesítjük. Gondosan tekintse át a tárfiókok korlátai, és hajtsa végre az összes javaslatot.

Az Azure storage szolgáltatási korlátai és gyakorlati tanácsok az elnevezési megosztások, a tárolók és a fájlok a legfrissebb információkért nyissa meg:

- [Elnevezése és hivatkozása tárolók](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Elnevezése és hivatkozása megosztások](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
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

Itt az adatok tárfiókba történő másolt mérete korlátok vonatkoznak. Győződjön meg arról, hogy a feltöltött adatok megfelel-e ezeket a korlátokat. Ezek a korlátok a legfrissebb információkért nyissa meg [az Azure blob storage méretezési célokat](https://docs.microsoft.com/en-us/azure/storage/cstorage-scalability-targets#azure-blob-storage-scale-targets) és [Azure Files tárolók skálázása](https://docs.microsoft.com/en-us/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Az Azure storage-fiókra másolni adatok mérete                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| A Blokkblobok és a lap blob                                            | Tárfiókonként 500 TB. <br> Ez magában foglalja többek között a Data Box-lemezek forrásokból származó adatok.|


## <a name="azure-object-size-limits"></a>Az Azure objektum blobméretének korlátjai

Az alábbiakban az Azure objektumok lehet írni a méretét. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek-e ezeket a korlátokat.

| Az Azure-objektum típusa | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 8 TB                                                 |
| Lapblob         | 1 TB <br> (Minden feltöltött Lapblob formátumú fájl 512 bájt igazítva (szerves több) kell lennie, ellenkező esetben a feltöltés sikertelen lesz. <br> A VHD és VHDX igazítva 512 bájt.) |


## <a name="azure-block-blob-and-page-blob-naming-conventions"></a>Az Azure blokkblob és elnevezési konvenciók lapblob

| Entitás                                       | Konvenciók                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A tároló nevének a blokkblobok és lapblobok | Egy érvényes DNS-nevet, amely 3 – 63 karakter hosszúságúnak kell lennie. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) kell lennie azonnal előtt és után betűnek vagy számnak. <br> Egymást követő kötőjelek használata nem engedélyezett a nevében. |
| BLOB-nevek a blokkblobok és lapblobok      | BLOB-nevek a kis-és nagybetűket, és bármilyen karakterkombináció tartalmazhat. <br> Egy blob nevének 1 és 1024 karakter hosszúságúnak kell lennie. <br> Foglalt URL-karaktereket megfelelően escape-karakterrel. <br>A blob nevét tartalmazó elérési út szegmensek száma legfeljebb 254. Egy részleges útvonalat a egymást követő elválasztó karakter közötti hosszúságú karakterláncot (például a perjel (/)), amelyek megfelelnek a virtuális könyvtár neve. |

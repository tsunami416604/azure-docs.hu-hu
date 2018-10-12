---
title: Az Azure Data Box-korlátozások |} A Microsoft Docs
description: Ismerteti a rendszer korlátozások és a Microsoft Azure Data Box-összetevők és a kapcsolatok számára az ajánlott méreteket.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/10/2018
ms.author: alkohli
ms.openlocfilehash: de47cae219aa457343df292bb91b6af06c4b1186
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091929"
---
# <a name="azure-data-box-limits"></a>Az Azure Data Box-korlátok

Vegye figyelembe ezeket a korlátokat, üzembe helyezése és a Microsoft Azure Data Box működnek. A következő táblázat ismerteti ezeket a korlátokat a Data Box.


## <a name="data-box-service-limits"></a>A Data Box szolgáltatás korlátai

 - A Data Box szolgáltatás áll rendelkezésre, csak az Egyesült Államok belül minden a [Azure-régiók az Azure nyilvános felhő](https://azure.microsoft.com/regions/).
 - Ha több tárfiók használata a Data Box szolgáltatás, a storage-fiókok kell csak az azonos régióba tartozik.
 - Azt javasoljuk, hogy használja-e legfeljebb három tárfiókot tartalmaz. További tárfiókok használata potenciálisan ronthatja a teljesítményt.

## <a name="data-box-limits"></a>A Data Box korlátai

- Data Box tud tárolni legfeljebb 500 millió fájl.

## <a name="azure-storage-limits"></a>Az Azure storage-korlátok

Ez a szakasz ismerteti az Azure Storage service korlátai, és a szükséges elnevezési konvenciók Azure Files, az Azure block blobs és oldala az Azure-blobok, a Data Box szolgáltatás teljesítjük. Gondosan tekintse át a tárfiókok korlátai, és hajtsa végre az összes javaslatot.

Az Azure storage szolgáltatási korlátai és gyakorlati tanácsok az elnevezési megosztások, a tárolók és a fájlok a legfrissebb információkért nyissa meg:

- [Elnevezése és hivatkozása tárolók](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokkblobok és a blob szabályai lap](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak, fájlok vagy könyvtárak lépik túl az Azure Storage szolgáltatás, vagy nem felelnek meg az Azure Files/Blob elnevezési konvencióinak, majd ezeket a fájlokat és könyvtárakat vannak nem betöltött a Data Box szolgáltatás használatával az Azure Storage-bA.

## <a name="data-upload-caveats"></a>Adatok feltöltése a figyelmeztetések

- Másolja az adatokat közvetlenül a precreated megosztások bármelyikét alatt. Hozzon létre egy mappát a megosztást, és adatokat másolja abba kell.
- Egy mappát a *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* tárolója. Például jönnek létre tárolók *StorageAccount_BlockBlob/tároló* és *StorageAccount_PageBlob/tároló*.
- Minden közvetlenül alatt létrehozott mappa *StorageAccount_AzureFiles* lefordítását egy Azure-fájlmegosztást.
- Ha egy meglévő Azure objektum (például egy blobba vagy egy fájlt) ezzel a névvel, az objektum, amely a másolásakor a felhőben, a Data Box felülírja a fájl a felhőben.
- Minden fájl kerülnek *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* megosztások töltenek fel a blokkblobok és lapblobok jelölik.
- Az Azure blob storage nem támogatja a címtárakat. Ha létrehoz egy mappát a *StorageAccount_BlockBlob* mappát, majd virtuális mappák jönnek létre a blob nevében. Az Azure Files között a tényleges könyvtárstruktúrát változatlan marad.
- Bármely üres könyvtár-hierarchia (nélküli fájlok) alatt létrehozott *StorageAccount_BlockBlob* és *StorageAccount_PageBlob* mappák feltöltés nem.
- Ha bármilyen hiba merül fel, ha az adatok feltöltése az Azure-ba, hibanaplót a célként megadott tárfiók létrejön. Az elérési útját a hibanapló érhető el, ha a feltöltés befejeződött, és a naplóban korrekciós műveletek végrehajtására. A feltöltött adatok ellenőrzése nélkül ne törölje adatokat a forrásból.

## <a name="azure-storage-account-size-limits"></a>Az Azure storage-fiók blobméretének korlátjai

Itt az adatok tárfiókba történő másolt mérete korlátok vonatkoznak. Győződjön meg arról, hogy a feltöltött adatok megfelel-e ezeket a korlátokat. Ezek a korlátok a legfrissebb információkért nyissa meg [az Azure blob storage méretezési célokat](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) és [Azure Files tárolók skálázása](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Az Azure storage-fiókra másolni adatok mérete                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| A blokkblobok és lapblobok                                            | Tárfiókonként 500 Tib-ra. <br> Ez magában foglalja többek között a Data Box forrásokból származó adatok.|
| Azure-beli                                                          | 5 Tib-ra jutó.<br> Az összes mappát *StorageAccount_AzureFiles* hajtsa végre ezt a korlátot.       |

## <a name="azure-object-size-limits"></a>Az Azure objektum blobméretének korlátjai

Az alábbiakban az Azure objektumok lehet írni a méretét. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek-e ezeket a korlátokat.

| Az Azure-objektum típusa | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 4,75 Tib-ra                                                 |
| Lapblob         | 8 TiB <br> Minden blob formátumban feltöltött fájl 512 bájt igazítva (szerves több) kell lennie, ellenkező esetben a feltöltés sikertelen lesz. <br> VHD és VHDX igazítva 512 bájt. |
| Azure-beli        | 1 TiB                                                      |

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Az Azure blokkblob, lapblob és fájlok elnevezési szabályai

| Entitás                                       | Konvenciók                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A tároló nevének a blokkblobok és lapblobok | Egy érvényes DNS-nevet, amely 3 – 63 karakter hosszúságúnak kell lennie. <br>  Betűvel vagy számmal kell kezdődnie. <br> Csak kisbetűket, számokat és kötőjelet (-) tartalmazhat. <br> Minden kötőjel (-) előtt és után közvetlenül egy betűnek vagy számnak kell állnia. <br> A tárolók nevében nem szerepelhetnek egymást követő kötőjelek. |
| Az Azure files szolgáltatáshoz a fájlmegosztások neve                  | Ugyanaz, mint a fenti                                                                                                                                                                                                                                                                                                             |
| Az Azure files szolgáltatáshoz a fájl és könyvtár neve     |<li> Nagybetűket, a kis-és nagybetűket, és nem haladhatja meg a 255 karakter hosszú lehet. </li><li> Nem végződhet perjellel (/). </li><li>Ha meg van adva, akkor automatikusan törlődni fog. </li><li> Nem engedélyezettek a következő karakterek: ' "\ vagy: | < > *? "</li><li> A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li> URL-cím elérési út érvénytelen karakterek nem engedélyezettek. A kódpontok \uE000 például amelyek nem érvényes Unicode-karaktereket. Bizonyos ASCII vagy Unicode-karaktereket, például a vezérlőkaraktereket (0x00 való 0x1F \u0081, stb.), emellett nem engedélyezett. Szabályok Unicode karakterláncok HTTP/1.1 lásd: RFC 2616, szakasz 2.2: alapszintű szabályok és RFC 3987. </li><li> Fájl a következő nevek nem engedélyezett: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, pont karakter (.), és két pont karakter (.).</li>|
| Blobnevek blokkblob és lapblob esetén      | </li><li>A blobnevek megkülönböztetik a kis- és nagybetűket, illetve bármilyen karakterkombinációt tartalmazhatnak </li><li>A blob nevének 1–1024 karakter hosszúságúnak kell lennie. </li><li>A fenntartott URL-karaktereket escape-karakterrel kell jelölni. </li><li>A blob nevét alkotó részleges útvonalak száma legfeljebb 254 lehet. A részleges útvonalak két elválasztókarakter (pl. a perjel „/”) közötti karakterláncok, amelyek megegyeznek egy virtuális könyvtár nevével.</li> |

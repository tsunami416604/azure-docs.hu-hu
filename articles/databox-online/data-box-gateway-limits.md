---
title: Az Azure Data Box Gateway korlátozza |} A Microsoft Docs
description: Ismerteti a rendszer korlátok és az ajánlott méreteket a Microsoft Azure Data Box Gateway.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/03/2018
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 946c045407b150a923d0067776bf80cbfab54c67
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268038"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Az Azure Data Box Gateway korlátainak (előzetes verzió)


Vegye figyelembe ezeket a korlátokat, üzembe helyezése és működtetése megoldását a Microsoft Azure Data Box-Gateway. 

> [!IMPORTANT] 
> A Data Box Gateway előzetes verzióban érhető el. A megoldás üzembe helyezése előtt tekintse át [az előzetes verziókra vonatkozó szolgáltatási feltételeket](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 


## <a name="data-box-gateway-service-limits"></a>Data Box Gateway szolgáltatási korlátozásai

- Ebben a kiadásban szolgáltatás érhető el csak bizonyos régiókban az USA, Európa és Ázsia Csendes-óceáni. További információt a [regionális elérhetőséget](#data-box-gateway-overview#region-availability) tárgyaló témakörben talál. A tárfiók fizikailag legközelebbi a régió, ahol az eszköz telepítve kell lennie (szolgáltatás földrajzilag eltérő is lehet).
- Egy Data Box-Gateway-erőforrás áthelyezése egy másik előfizetést vagy az erőforrást csoporthoz nem támogatott. További részletekért látogasson el [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="data-box-gateway-device-limits"></a>A Data Box-átjáró eszköz korlátai

A következő táblázat ismerteti a korlátokat a Data Box átjáróeszköz számára.

| Leírás | Érték |
|---|---|
|Nem. a fájlok eszközönként |100 millió <br> Legfeljebb ~ minden 2 TB-nyi szabad lemezterület 100 millió a maximális korlátot a 25-ös millió fájl |
|Nem. a megosztások eszközönként |24 |
|Maximális fájlméret megosztásra írt|2 TB-os virtuális eszköz a fájl maximális mérete 500 GB-os. <br> Maximális fájlméret egyenes arányban növekszik az adatlemez mérete az előző arány legfeljebb 5 TB-os eléréséig. |

## <a name="azure-storage-limits"></a>Az Azure storage-korlátok

Ez a szakasz ismerteti az Azure Storage service korlátai, és a szükséges elnevezési konvenciók Azure Files, az Azure block blobs és oldala az Azure-blobok, a Data Box átjáró/Data Box Edge szolgáltatás alkalmazandó. Gondosan tekintse át a tárfiókok korlátai, és hajtsa végre az összes javaslatot.

Az Azure storage szolgáltatási korlátai és gyakorlati tanácsok az elnevezési megosztások, a tárolók és a fájlok a legfrissebb információkért nyissa meg:

- [Elnevezése és hivatkozása tárolók](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Megosztások elnevezése és hivatkozása](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Blokkblobok és a blob szabályai lap](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Ha vannak, fájlok vagy könyvtárak lépik túl az Azure Storage szolgáltatás, vagy nem felelnek meg az Azure Files/Blob elnevezési konvencióinak, majd ezen fájlok vagy könyvtárak vannak nem elemezhető a Data Box átjáró/Data Box Edge szolgáltatáson keresztül az Azure Storage-bA.

## <a name="data-upload-caveats"></a>Adatok feltöltése a figyelmeztetések

Alábbi korlátozásokkal adatokra vonatkoznak, ahogy azt az Azure-bA helyezi át.

- Javasoljuk, hogy több eszköz nem kell írni ugyanazt a tárolót.
- Ha egy meglévő Azure objektum (például egy blobba vagy egy fájlt) ezzel a névvel, az objektum, amely a másolásakor a felhőben, az eszköz felülírja a fájl a felhőben. 
- Egy üres könyvtár-hierarchia (nélküli fájlokat) a megosztás mappák létrehozása a blob-tárolók nem van feltöltve.


## <a name="azure-storage-account-size-and-object-size-limits"></a>Az Azure storage-fiók mérete és az objektum méretkorlátozások

Itt az adatok tárfiókba történő másolt mérete korlátok vonatkoznak. Győződjön meg arról, hogy a feltöltött adatok megfelel-e ezeket a korlátokat. Ezek a korlátok a legfrissebb információkért nyissa meg [az Azure blob storage méretezési célokat](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) és [Azure Files tárolók skálázása](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Az Azure storage-fiókra másolni adatok mérete                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| A Blokkblobok és a lap blob                                            | Tárfiókonként 500 TB|


## <a name="azure-object-size-limits"></a>Az Azure objektum blobméretének korlátjai

Az alábbiakban az Azure objektumok lehet írni a méretét. Győződjön meg arról, hogy a feltöltött fájlok megfelelnek-e ezeket a korlátokat.

| Az Azure-objektum típusa | Alapértelmezett korlát                                             |
|-------------------|-----------------------------------------------------------|
| Blokkblob        | ~ 8 TB                                                 |
| Lapblob         | 1 TB <br> Minden feltöltött Lapblob formátumú fájl 512 bájt igazítva (szerves több) kell lennie, ellenkező esetben a feltöltés sikertelen lesz. <br> A VHD és VHDX igazítva 512 bájt. |


## <a name="next-steps"></a>További lépések

- [Az Azure Data Box Gateway üzembe helyezésének előkészítése](data-box-gateway-deploy-prep.md)

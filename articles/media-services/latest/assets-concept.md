---
title: Objektumok
titleSuffix: Azure Media Services
description: Ismerje meg, hogy milyen eszközök ről van szó, és hogyan használja őket az Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087910"
---
# <a name="assets-in-azure-media-services-v3"></a>Eszközök az Azure Media Services 3-as v3-as ében

Az Azure Media Servicesben az [eszköz](https://docs.microsoft.com/rest/api/media/assets) egy alapkoncepció. Ez az a hely, ahol az adathordozót (például feltöltés vagy élő betöltés) adja meg, kimeneti adathordozót (egy feladat kimenetéből), és közzéteszi az adathordozót (streameléshez). 

Az eszköz le van képezve egy blob tárolóaz [Azure Storage-fiókban,](storage-account-concept.md) és az eszköz fájlokat az eszköz blokkblobok tárolóként tárolja. Az eszközök az Azure Storage-ban tárolt digitális fájlokra vonatkozó információkat tartalmaznak (például video-, hang-, képgyűjteményeket, szövegszámokat és feliratfájlokat).

A Media Services támogatja a Blob-szinteket, ha a fiók általános célú v2 (GPv2) tárolót használ. A GPv2 segítségével áthelyezheti a fájlokat a [Cool vagy Archive tárolóba.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers) **Az archív** tároló akkor alkalmas a forrásfájlok archiválására, amikor már nincs szükség rájuk (például kódolás után).

Az **archív** tárolási szint csak nagyon nagy forrásfájlok, amelyek már kódolt, és a kódolási feladat kimeneti került egy kimeneti blob tárolóba. A kimeneti tárolóban lévő blobok, amelyeket egy eszközhöz szeretne társítani, és a tartalom streamelésére vagy elemzésére használni kell, egy **gyakori vagy** **ritka elérésű** tárolási rétegben kell létezniük.

## <a name="naming"></a>Elnevezés 

### <a name="assets"></a>Objektumok

Az eszköz nevének egyedinek kell lennie. Media Services v3 erőforrásnevek (például eszközök, feladatok, átalakítások) az Azure Resource Manager elnevezési korlátozások. További információ: [Elnevezési konvenciók](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Blobok

Az egy eszközön belüli fájlok/blobok nevének meg kell felelnie mind a [blobnév-követelményeknek,](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) mind az [NTFS-névkövetelményeknek.](https://docs.microsoft.com/windows/win32/fileio/naming-a-file) Ezeknek a követelményeknek az az oka, hogy a fájlok a blobstorage-ból feldolgozásra egy helyi NTFS-lemezre másolhatók.

## <a name="next-steps"></a>További lépések

[Eszközök kezelése a Media Services szolgáltatásban](manage-asset-concept.md)

## <a name="see-also"></a>Lásd még

[A Media Services 2-es és 3-as v3-as közötti különbségei](migrate-from-v2-to-v3.md)

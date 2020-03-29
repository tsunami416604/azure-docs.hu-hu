---
title: Az Azure Media Services kódolási hibakódjai | Microsoft dokumentumok
description: Ez a témakör azokat a hibakódokat sorolja fel, amelyek akkor adhatók vissza, ha hiba történt a kódolási feladat végrehajtása során..
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ce4e939f-5aee-41f9-859d-e4429815e9f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 5c038f0be31acea52c2ef07d43f0dbaf3434a371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64709522"
---
# <a name="encoding-error-codes"></a>Kódolási hibakódok

Az alábbi táblázat azokat a hibakódokat sorolja fel, amelyek a kódolási feladat végrehajtása során hiba esetén visszaadhatók.  A .NET-kódban a hiba részleteinek beírásához használja az [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) osztályt. A REST-kód hibarészleteinek beírásához használja az [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API-t.

| ErrorDetail.Kód | A hiba lehetséges okai |
| --- | --- |
| Ismeretlen |Ismeretlen hiba a feladat végrehajtása közben |
| HibaABemenetI-eszközHibásformátképezőtartalom letöltése |Kategória hibák, amely magában foglalja a hibák letöltését bemeneti eszköz, mint a rossz fájlnevek, nulla hosszúságú fájlokat, helytelen formátumokat és így tovább. |
| Hibaaz InputAssetServiceFailure letöltése |A szolgáltatási oldalon felmerülő problémákat lefedő hibák kategóriája – például a letöltés során a hálózati vagy tárolási hibák. |
| ErrorParsingConfiguration |A hibák azon kategóriája, ahol a feladat \<lásd cref="MediaTask.PrivateData"/> (konfiguráció) érvénytelen, például a konfiguráció nem érvényes rendszerkészlet, vagy érvénytelen XML-t tartalmaz. |
| ErrorExecutingTaskMformáltContent |A feladat végrehajtása során a hibák kategóriája, ahol a bemeneti médiafájlokon belüli problémák hibát okoznak. |
| ErrorExecutingTaskNem támogatottformátum |Olyan hibakategória, amelyben a médiaprocesszor nem tudja feldolgozni a megadott fájlokat - a médiaformátum nem támogatott, vagy nem felel meg a Konfigurációnak. Ha például csak hangkimenetet próbál létrehozni egy olyan eszközről, amely |
| HibaprocessingTask |Egyéb hibák kategóriája, amelyekkel a médiafeldolgozó a feladat feldolgozása során szembesül, és amely nem kapcsolódik a tartalomhoz. |
| HibaUploadingOutputAsset |A hibák kategóriája a kimeneti eszköz feltöltésekén |
| Hiba a Feladat törlésekor |A feladat megszakítására tett hibákat fedező hibák kategóriája |
| Átmeneti hiba |Kategória hibák fedezésére átmeneti kérdések (pl. ideiglenes hálózati problémák az Azure Storage szolgáltatással) |

Ha segítségre van szüksége a **Media Services** csapatától, nyisson meg egy [támogatási jegyet.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatok végrehajtása a Media Encoder Standard készletek testreszabásával](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/

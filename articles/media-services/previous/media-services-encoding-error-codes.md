---
title: Az Azure Media Services encoding hibakódok |} A Microsoft Docs
description: Ez a témakör felsorolja, abban az esetben, ha hiba történt a kódolási feladat a végrehajtás során visszatérő hibakódok...
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
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 7e32d0826d36b0d6f68264ba8c74aec49574b0c2
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51254553"
---
# <a name="encoding-error-codes"></a>Kódolási hibakódok

Az alábbi táblázat abban az esetben, ha hiba történt a kódolási feladat a végrehajtás során visszatérő hibakódok.  A hiba részletei az a .NET-kód, amelyet a [ErrorDetails](https://msdn.microsoft.com/library/microsoft.windowsazure.mediaservices.client.errordetail.aspx) osztály. Hiba részleteinek lekérése a REST-kódban, használja a [ErrorDetail](https://msdn.microsoft.com/library/jj853026.aspx) REST API-t.

| ErrorDetail.Code | Hiba lehetséges okai |
| --- | --- |
| Ismeretlen |A feladat végrehajtása közben ismeretlen hiba |
| ErrorDownloadingInputAssetMalformedContent |Hibák letöltése, például a rossz fájlneveket, nulla hosszúságú fájlok, a hibás bemeneti objektuma hibák kategóriáját formázza és így tovább. |
| ErrorDownloadingInputAssetServiceFailure |Kategória hibák Szolgáltatásoldali – például hálózati vagy tárolási hibák letöltése közben problémákat ismerteti. |
| ErrorParsingConfiguration |A hibák kategória ahol feladat <see cref="MediaTask.PrivateData"/> (konfiguráció) nem érvényes, például a konfiguráció nem egy érvényes rendszer készletet, vagy érvénytelen XML-kódot tartalmaz. |
| ErrorExecutingTaskMalformedContent |Hol található a bemeneti médiafájlok okozza hiba a feladat végrehajtása közben hibák kategóriáját. |
| ErrorExecutingTaskUnsupportedFormat |Kategória hibák, ahol a media processzor nem tudja feldolgozni a megadott fájlok – médiaformátum nem támogatott, vagy nem felel meg a konfigurációt. Ha például próbál egy eszköz, amely csak a videó rendelkezik a csak hangfájlt tartalmazó kimenetet |
| ErrorProcessingTask |Más hibák, amelyek a médiafeldolgozót. a feladat feldolgozása során fordul elő, amelyek kapcsolódnak a tartalom kategóriáját. |
| ErrorUploadingOutputAsset |A kimeneti adategység feltöltése közben hibák kategóriáját |
| ErrorCancelingTask |Ahhoz, hogy biztosítsák a hibák, a feladat megszakítására tett kísérlet során hibák kategóriáját |
| TransientError |Kategória hibák ahhoz, hogy biztosítsák az átmeneti hibák (például) ideiglenes hálózati problémák az Azure Storage) |

A Ha segítséget szeretne kérni a **Media Services** csapat, nyissa meg egy [támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatokat végez, és a Media Encoder Standard-beállításkészletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
